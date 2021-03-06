---
title: 使用 Azure 备份将 SAP HANA 数据库备份到 Azure
description: 本文介绍如何使用 Azure 备份服务将 SAP HANA 数据库备份到 Azure 虚拟机。
author: lingliw
manager: digimobile
ms.topic: conceptual
origin.date: 11/7/2019
ms.date: 03/12/2020
ms.author: v-lingwu
ms.openlocfilehash: 0e6f4a6453790539c4ea4020c96598a892701bd0
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79292583"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>备份 Azure VM 中的 SAP HANA 数据库

SAP HANA 数据库是关键工作负荷，要求较低的恢复点目标 (RPO) 和长期保留。 可以使用 [Azure 备份](backup-overview.md)来备份在 Azure 虚拟机 (VM) 上运行的 SAP HANA 数据库。

本文展示了如何将在 Azure VM 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。

本文介绍如何执行以下操作：
> [!div class="checklist"]
>
> * 创建并配置保管库
> * 发现数据库
> * 配置备份
> * 运行按需备份作业

>[!NOTE]
>**针对 Azure VM 中 SQL 服务器的软删除以及针对 Azure VM 工作负荷中 SAP HANA 的软删除**现已推出预览版。<br>
>若要注册预览版，请向 AskAzureBackupTeam@microsoft.com 发送邮件

## <a name="prerequisites"></a>先决条件

请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分来设置数据库备份。

### <a name="set-up-network-connectivity"></a>设置网络连接

对于所有操作，SAP HANA VM 虚拟机需要与 Azure 公共 IP 地址建立连接。 如果未连接到 Azure 公共 IP 地址，VM 操作（数据库发现、配置备份、计划备份、还原恢复点等）将失败。

使用以下选项之一建立连接：

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>允许 Azure 数据中心 IP 范围

此选项允许已下载文件中的 [IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 若要访问网络安全组 (NSG)，请使用 Set-AzureNetworkSecurityRule cmdlet。 如果安全收件人列表仅包含特定于区域的 IP，则还需更新 Azure Active Directory (Azure AD) 服务标记的安全收件人列表以启用身份验证。

#### <a name="allow-access-using-nsg-tags"></a>允许使用 NSG 标记进行访问

如果使用 NSG 来限制连接，则应使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 此外，还应允许使用 Azure AD 和 Azure 存储的[规则](/virtual-network/security-overview#service-tags)，在连接后进行身份验证和数据传输。 这可以通过 Azure 门户或 PowerShell 来完成。

若要使用门户创建规则，请执行以下操作：

  1. 在“所有服务”  中转到“网络安全组”  ，然后选择“网络安全组”。
  2. 在“设置”下选择“出站安全规则”。  
  3. 选择“添加”   。 根据[安全规则设置](/virtual-network/manage-network-security-group#security-rule-settings)中所述，输入创建新规则所需的所有详细信息。 确保选项“目标”设置为“服务标记”，“目标服务标记”设置为“AzureBackup”。    
  4. 单击“添加”  ，保存新创建的出站安全规则。

若要使用 PowerShell 创建规则，请执行以下操作：

 1. 添加 Azure 帐户凭据并更新国家/地区云<br/>
      `Add-AzureRmAccount`<br/>

 2. 选择 NSG 订阅<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. 选择 NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. 为 Azure 备份服务标记添加允许出站规则<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. 为 Azure 存储服务标记添加允许出站规则<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. 为 AzureActiveDirectory 服务标记添加允许出站规则<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. 保存 NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**允许使用 Azure 防火墙标记进行访问**。 如果使用 Azure 防火墙，请使用 AzureBackup [FQDN 标记](/firewall/fqdn-tags)创建一个应用程序规则。 此规则允许对 Azure 备份进行出站访问。

**部署用于路由流量的 HTTP 代理服务器**。 在 Azure VM 中备份 SAP HANA 数据库时，该 VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure AD 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 该扩展是为了访问公共 Internet 而配置的唯一组件。

连接选项包括以下优点和缺点：

**选项** | **优点** | **缺点**
--- | --- | ---
允许 IP 范围 | 无额外成本 | 管理起来很复杂，因为 IP 地址范围随时会更改 <br/><br/> 允许访问整个 Azure，而不只是 Azure 存储
使用 NSG 服务标记 | 由于范围更改会自动合并，因此管理变得更容易 <br/><br/> 无额外成本 <br/><br/> | 只可用于 NSG <br/><br/> 提供对整个服务的访问
使用 Azure 防火墙 FQDN 标记 | 由于可自动管理所需的 FQDN，因此管理变得更容易 | 只可用于 Azure 防火墙
使用 HTTP 代理 | 允许在代理中对存储 URL 进行精细控制 <br/><br/> 对 VM 进行单点 Internet 访问 <br/><br/> 不受 Azure IP 地址变化的影响 | 通过代理软件运行 VM 带来的额外成本

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>发现数据库

1. 在保管库中，单击“开始使用”中的“备份”   。 在“工作负荷在哪里运行?”中，选择“Azure VM 中的 SAP HANA”   。
2. 单击“启动发现”  。 这会开始在保管库区域中发现未受保护的 Linux VM。

   * 在发现后，未受保护的 VM 将显示在门户中，按名称和资源组列出。
   * 如果某个 VM 未按预期列出，请检查它是否已在保管库中备份。
   * 可能有多个 VM 同名，但属于不同的资源组。

3. 在“选择虚拟机”中，单击脚本下载链接。此脚本可为 Azure 备份服务提供访问 SAP HANA VM 的权限以进行数据库发现  。
4. 在托管要备份的 SAP HANA 数据库的每个 VM 上运行此脚本。
5. 在 VM 上运行此脚本后，在“选择虚拟机”中选择 VM  。 然后单击“发现数据库”  。
6. Azure 备份可发现该 VM 上的所有 SAP HANA 数据库。 在发现期间，Azure Backup 将 VM 注册到保管库，并在该 VM 上安装扩展。 不会在数据库中安装任何代理。

    ![发现 SAP HANA 数据库](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>配置备份  

现在启用备份。

1. 在步骤 2 中，单击“配置备份”  。

    ![配置备份](./media/backup-azure-sap-hana-database/configure-backup.png)
2. 在“选择要备份的项”  中，选择要保护的所有数据库，然后选择“确定”  。

    ![选择要备份的项](./media/backup-azure-sap-hana-database/select-items.png)
3. 在“备份策略” > “选择备份策略”中，按照下面的说明，为数据库创建一个新的备份策略。  

    ![选择备份策略](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 创建策略后，在“备份”菜单中单击“启用备份”   。

    ![启用备份](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 在门户的“通知”区域跟踪备份配置进度  。

### <a name="create-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

* 策略是在保管库级别创建的。
* 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。

按以下方式指定策略设置：

1. 在“策略名称”处输入新策略的名称  。

   ![输入策略名称](./media/backup-azure-sap-hana-database/policy-name.png)
2. 在“完整备份策略”中选择“备份频率”，然后选择“每日”或“每周”。    
   * **每日**：选择开始备份作业的小时和时区。
       * 你必须运行完整备份。 无法关闭此选项。
       * 单击“完整备份”以查看策略  。
       * 对于每日完整备份，无法创建差异备份。
   * **每周**：选择运行备份作业的星期、小时和时区。

   ![选择备份频率](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. 在“保持期”中，对完整备份配置保留设置  。
    * 默认情况下将选择所有选项。 清除你不想使用的所有保持期限制，并设置要使用的选项。
    * 任何备份类型（完整/差异/日志）的最短保持期均为七天。
    * 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
    * 根据每周保持期和设置，将会标记并保留特定日期的备份。
    * 每月和每年保留范围的行为类似。

4. 在“完整备份策略”菜单中，单击“确定”接受设置   。
5. 选择“差异备份”，以添加差异策略  。
6. 在“差异备份策略”中，选择“启用”打开频率和保留控件。  
    * 每天最多可以触发一次差异备份。
    * 差异备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份。

    ![差异备份策略](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 目前不支持增量备份。

7. 单击“确定”保存策略，并返回“备份策略”主菜单   。
8. 请选择“日志备份”，以添加事务日志备份策略  。
    * 在“日志备份”中，选择“启用”。    由于 SAP HANA 管理所有日志备份，此类备份无法被禁用。
    * 设置频率和保留期控制。

    > [!NOTE]
    > 日志备份仅在成功完成一次完整备份之后进行。

9. 单击“确定”保存策略，并返回“备份策略”主菜单   。
10. 完成定义备份策略后，单击“确定”  。

> [!NOTE]
> 每个日志备份都链接到上一个完整备份，以形成恢复链。 此完整备份将一直保留到最后一个日志备份的保留期结束为止。 这可能意味着完整备份会多保留一段时间，以确保所有日志都可以恢复。 假设用户具有每周完整备份、每日差异备份和 2 小时日志备份。 这些备份都保留 30 天。 但是，只有在下一个完整备份可用后（即 30 + 7 天后），才能真正清除/删除这个每周完整备份。 假设每周完整备份发生在 11 月 16 日。 根据保留策略，它应保留到 12 月 16 日。 该完整备份的最后一次日志备份发生在下一次计划的完整备份之前，即 11 月 22 日。 必须等到 12 月 22 日此日志备份可用后，才能删除 11 月 16 日的完整备份。 因此，11 月 16 日的完整备份会保留到 12 月 22 日。

## <a name="run-an-on-demand-backup"></a>运行按需备份

备份按照策略计划运行。 可以如下所述运行按需备份：

1. 在保管库菜单中，单击“备份项”  。
2. 在“备份项”  中，选择运行 SAP HANA 数据库的 VM，然后单击“立即备份”  。
3. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期  。  。
4. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。   创建初始备份可能需要一些时间，具体取决于你的数据库的大小。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在启用了 Azure 备份的数据库上运行 SAP HANA Studio 备份

如果要使用 Azure 备份创建正在备份的数据库的本地备份（使用 HANA Studio），请执行以下操作：

1. 等待数据库的所有完整备份或日志备份完成。 在 SAP HANA Studio / Cockpit 中检查状态。
2. 禁用日志备份，并将备份目录设置为相关数据库的文件系统。
3. 为此，请双击“systemdb” > “配置” > “选择数据库” > “筛选器(日志)”。    
4. 将 **enable_auto_log_backup** 设置为 **No**。
5. 将 **log_backup_using_backint** 设置为 **False**。
6. 创建数据库的完整备份。
7. 等待完整备份和目录备份完成。
8. 将前面的设置恢复为 Azure 的设置：
    * 将 **enable_auto_log_backup** 设置为 **Yes**。
    * 将 **log_backup_using_backint** 设置为 **True**。

## <a name="next-steps"></a>后续步骤

* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](/backup/sap-hana-db-manage)
