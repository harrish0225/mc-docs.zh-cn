---
title: 保护计算机和应用程序
description: 本文档介绍 Azure 安全中心内提供的，可帮助保护虚拟机和计算机以及 Web 应用和应用服务环境的建议。
services: security-center
documentationcenter: na
author: lingliw
manager: digimobile
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 03/20/2019
ms.date: 11/19/2019
ms.author: v-lingwu
ms.openlocfilehash: abfd48addbd0131b5e69fa6d622d00dd2bed0916
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79293064"
---
# <a name="protect-your-machines-and-applications"></a>保护计算机和应用程序
在 Azure 安全中心识别出潜在的安全漏洞时，它会创建建议，指导你完成配置所需控件以强化和保护资源的过程。

本文介绍安全中心的资源安全部分的“计算和应用”页。 


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>查看计算和应用资源的安全性

[![安全中心仪表板](./media/security-center-virtual-machine-recommendations/overview.png)](./media/security-center-virtual-machine-recommendations/overview.png#lightbox)

若要查看计算和应用资源的状态，请在安全中心边栏中的“资源”下选择“计算和应用”。   以下选项卡可用：

* **概览**：列出所有计算和应用资源及其当前安全状态的建议 

* [**VM 和计算机**](#vms-and-computers)：列出 VM、计算机及其当前安全状态的建议

* [**VM 规模集**](#vmscale-sets)：列出规模集的建议 

* [**云服务**](#cloud-services)：列出安全中心监视的 Web 角色和辅助角色的建议

* [**应用服务**](#app-services)：列出应用服务环境及其当前安全状态的建议

* **容器**：列出容器以及对其配置的安全评估的建议

* **计算资源**：列出针对计算资源（例如 Service Fabric 群集和事件中心）的建议

### <a name="whats-in-each-tab"></a>每个选项卡中的内容

每个选项卡都有多个部分。在每个部分中，可以通过向下钻取查看已显示项的其他详细信息。

在每个选项卡中，还会看到有关受监视环境中的相关资源的建议。 第一列列出建议，第二列显示受影响资源的总数，第三列显示问题的严重性。

每条建议附带一组操作，可在选择该建议后执行这些操作。 例如，如果选择“缺失的系统更新”，则会显示缺少修补程序的 VM 和计算机数目，以及所缺更新的严重性。 

> [!NOTE]
> 安全建议与“建议”  页上的相同，但此处会将其筛选为所选的特定资源类型。 有关如何解决建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。
>

### <a name="vms-and-computers"></a>VM 和计算机
“VM 和计算机”部分概述了有关 VM 和计算机的所有安全建议。 包括四种类型的计算机：

![非 Azure 计算机](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) 非 Azure 计算机。

![Azure 资源管理器 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure 资源管理器 VM。

![Azure 经典 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure 经典 VM。

![从工作区中识别的 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 只能通过工作区（属于所查看的订阅）标识的 VM。 其中包括其他订阅中向此订阅中的工作区报告的 VM，以及使用 Operations Manager 直接代理安装的 VM（没有资源 ID）。

每条建议下面显示的图标有助于快速识别需要关注的 VM 和计算机以及建议的类型。 也可使用筛选器，按“资源类型”和“严重性”搜索列表。  

若要向下钻取每个 VM 的安全建议，请单击相应的 VM。
此处显示 VM 或计算机的安全详细信息。 在底部可以看到建议的操作以及每个问题的严重性。

[![云服务](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)

### <a name="cloud-services"></a>云服务
对于云服务，会在操作系统版本过期时创建建议。

![云服务](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

在有建议的方案中，请按建议中的步骤更新操作系统。 如果有可用的更新，会出现警报（红色或橙色 - 取决于问题的严重性）。 有关此建议的完整说明，请在“说明”栏下单击“更新 OS 版本”。  

### <a name="app-services"></a>应用服务
若要查看应用服务信息，你必须在安全中心的“标准”定价层上，并在订阅中启用应用服务。 有关如何启用此功能的说明，请参阅[使用 Azure 安全中心保护应用服务](security-center-app-services.md)。


在“应用服务”下，可以看到应用服务环境的列表，以及安全中心执行评估后提供的运行状况摘要。 

![应用程序服务](./media/security-center-virtual-machine-recommendations/app-services.png)

显示了三种类型的应用程序服务：

![应用服务环境](./media/security-center-virtual-machine-recommendations/ase.png) 应用服务环境

![Web 应用程序](./media/security-center-virtual-machine-recommendations/web-app.png) Web 应用程序

![函数应用程序](./media/security-center-virtual-machine-recommendations/function-app.png) 函数应用程序

如果选择 Web 应用程序，则会打开摘要视图，其中包含三个选项卡：

   - **建议**：基于安全中心执行的失败的评估。
   - **已通过的评估**：安全中心执行的、已通过的评估列表。
   - **不可用的评估**：由于出错或者建议与特定的应用服务不相关，而未能运行的评估列表。

   “建议”下面是选定 Web 应用程序的建议列表，以及每条建议的严重性。 

   ![应用服务建议](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

选择一条建议即可查看其说明，以及不正常资源、正常资源和未扫描资源的列表。

   - “已通过的评估”列显示已通过的评估的列表。  这些评估的严重性始终为绿色。

   - 从评估说明列表、不正常和正常资源列表以及未扫描的资源列表中选择一个已通过的评估。 不正常的资源有一个对应的选项卡，但其中的列表始终为空，因为评估已经通过。

### <a name="vmscale-sets"></a>虚拟机规模集
安全中心会自动发现你是否有规模集，并会建议你在其上安装 Microsoft Monitoring Agent。

若要安装 Microsoft Monitoring Agent，请执行以下操作： 

1. 选择建议“在虚拟机规模集上安装监视代理”。  你会获得未收监视的规模集的列表。

1. 选择不正常的规模集。 按照说明操作，使用现有的已填充工作区或新建的工作区安装监视代理。 确保设置工作区[定价层](security-center-pricing.md)（如果尚未设置）。

   ![安装 MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

若要将新规模集设置为自动安装 Microsoft Monitoring Agent，请执行以下操作：
1. 转到 Azure Policy，单击“定义”。 

1. 搜索策略“为 Windows 虚拟机规模集部署 Log Analytics 代理”，然后单击它。 

1. 单击“分配”  。

1. 设置“范围”和“Log Analytics 工作区”，然后单击“分配”。   

若要将所有现有的规模集设置为安装 Microsoft Monitoring Agent，请在 Azure Policy 中转到“修正”，将现有的策略应用到现有的规模集。 


## <a name="next-steps"></a>后续步骤
若要详细了解适用于其他 Azure 资源类型的建议，请参阅以下文章：


若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。

* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。



