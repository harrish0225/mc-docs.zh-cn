---
title: OMS 门户即将转移到 Azure | Azure Docs
description: OMS 门户即将被淘汰，其所有功能将转移到 Azure 门户。 本文提供有关此项过渡的详细信息。
services: log-analytics
documentationcenter: ''
author: lingliw
manager: digimobile
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/21/19
ms.author: v-lingwu
ms.openlocfilehash: cd99a397d2462be51335c08f1f203f10062d84b5
ms.sourcegitcommit: 7e25a709734f03f46418ebda2c22e029e22d2c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56440793"
---
# <a name="oms-portal-moving-to-azure"></a>OMS 门户即将转移到 Azure

> [!NOTE]
> 本文适用于 Azure 公有云和政府云（除非另有说明）。

Azure 门户是所有 Azure 服务的中心，提供丰富的管理体验和功能，例如，为固定资源提供仪表板、为查找资源提供智能搜索，以及为资源管理提供标记。 用于整合和简化监视与管理工作流，我们已开始将 OMS 门户功能添加到 Azure 门户。 OMS 门户的所有功能现已并入 Azure 门户。 事实上，某些新功能（例如流量分析）只在 Azure 门户中提供。 使用 Azure 门户就能完成在 OMS 门户中所能完成的任何工作，而且 Azure 门户的功能更强大。 如果你尚未使用 Azure 门户，请立即开始使用！

对于 Azure 商业云和 Azure 美国政府云，**OMS 门户将于 2019 年 1 月 15 日正式停用**，整个 OMS 门户**将于 2019 年 3 月 30 日正式停用**。 我们很高兴能够转移到 Azure 门户，并且希望实现轻松过渡。 但我们也知道，变化会带来一定的困扰。 欢迎向 **LAUpgradeFeedback@microsoft.com** 发送任何问题、反馈或忧虑。 本文的剩余部分介绍此项过渡的关键场景和路线图。

## <a name="what-is-changing"></a>有什么变化？ 
随着 OMS 门户的弃用，我们将宣布以下变化。 其中的每项变化将在下面的相应部分做出详细描述。

- 只能在 Azure 门户中创建新[工作区](#new-workspaces)。
- 新警报管理体验将[取代警报管理解决方案](#changes-to-alerts)。
- 现在，可以使用 Azure 基于角色的访问控制在 Azure 门户中进行[用户访问管理](#user-access-and-role-migration)。
- [不再需要 Application Insights 连接器](#application-insights-connector-and-solution)，因为可通过跨工作区查询启用相同的功能。
- [OMS 移动应用](#oms-mobile-app)即将弃用。 
- [NSG 解决方案即将被取代](#azure-network-security-group-analytics)为通过流量分析解决方案提供的增强功能。
- 从 System Center Operations Manager 到 Log Analytics 的新连接需要[更新的管理包](#system-center-operations-manager)。


## <a name="what-should-i-do-now"></a>现在应该做些什么？
尽管不执行任何迁移也能继续使用大部分功能，但需要执行以下任务：

- 需[将用户权限迁移](#user-access-and-role-migration)到 Azure 门户。

参阅 [Log Analytics 用户从 OMS 门户过渡到 Azure 门户时的常见问题](oms-portal-faq.md)，了解有关如何过渡到 Azure 门户的信息。 欢迎向 LAUpgradeFeedback@microsoft.com 发送任何反馈、问题或忧虑。

## <a name="user-access-and-role-migration"></a>用户访问权限和角色迁移
与 OMS 门户中的访问权限管理相比，Azure 门户的访问权限管理功能是更丰富且更强大。 有关 Log Analytics 中访问权限管理的详细信息，请参阅[管理工作区](manage-access.md#manage-accounts-and-users)。

> [!NOTE]
> 本文的先前版本指出，权限会自动从 OMS 门户转换到 Azure 门户。 我们已不再规划这种自动转换，你必须自行执行转换。

你可能已在 Azure 门户中拥有相应的访问权限，在这种情况下，无需做出任何更改。 在某些情况下，你可能没有相应的访问权限，对于这种情况，管理员必须向你分配权限。

- 你在 OMS 门户中拥有“只读用户”权限，但在 Azure 门户中没有权限。 
- 你在 OMS 门户中拥有“参与者”权限，但在 Azure 门户中仅拥有“读取者”访问权限。
 
在这种情况下，管理员需要手动向你分配下表中所列的相应角色。 我们建议在资源组或订阅级别分配此角色。  对于这些情况，我们在不久后将会发布规范性的指导。

| OMS 门户权限 | Azure 角色 |
|:---|:---|
| ReadOnly | Log Analytics 读者 |
| 参与者 | Log Analytics 参与者 |
| 管理员 | 所有者 | 
 

## <a name="new-workspaces"></a>新工作区
不再能够使用 OMS 门户创建新工作区。 请遵照[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)中的指导，在 Azure 门户中创建新工作区。

## <a name="changes-to-alerts"></a>警报更改

### <a name="alert-extension"></a>警报扩展  

> [!NOTE]
> 针对公有云，警报现已完全扩展到 Azure 门户中。 可在 OMS 门户中查看现有警报规则，但只能在 Azure 门户中对其进行管理。 针对 Azure 政府云，将于 2019 年 2 月开始将警报扩展到 Azure 门户中。

警报已[扩展到 Azure 门户中](../../azure-monitor/platform/alerts-extend.md)。 完成此过渡后，只能在 Azure 门户中针对警报执行管理操作。 现有的警报将继续列在 OMS 门户中。 如果使用 Log Analytics 警报 REST API 或 Log Analytics 警报资源模板以编程方式访问警报，需要在 API 调用、Azure 资源管理器模板和 PowerShell 命令中使用操作组而非操作。

### <a name="alert-management-solution"></a>警报管理解决方案
作为上次公告中的一个更改，[警报管理解决方案](../../azure-monitor/platform/alert-management-solution.md)在 Azure 门户中将继续可用并完全受支持。 可以继续从 Azure 市场安装该解决方案。

尽管警报管理解决方案继续可用，但我们建议你使用 [Azure Monitor 的统一警报界面](../../azure-monitor/platform/alerts-overview.md)来可视化和管理 Azure 中的所有警报。 此新体验原生聚合来自 Azure 中多个源的警报，包括来自 Log Analytics 的日志警报。 如果你使用 Azure Monitor 的统一警报界面，则只有在将来自 System Center Operation Manager 的警报集成到 Azure 时才需要警报管理解决方案。 在 Azure Monitor 的统一警报界面中，可以查看警报的分布情况，通过智能组利用相关警报的自动分组，并在应用丰富的筛选器时查看多个订阅的警报。 警报管理方面将来的改进将主要来自此新体验。 

警报管理解决方案收集的数据（具有某种警报类型的记录）继续在 Log Analytics 中提供，前提是为工作区安装了该解决方案。 

## <a name="oms-mobile-app"></a>OMS 移动应用
OMS 移动应用将随 OMS 门户一起淘汰。 无需使用 OMS 移动应用来访问有关 IT 基础结构、仪表板和保存的查询的信息，可以直接通过移动设备中的浏览器访问 Azure 门户。 若要获取警报，应配置 [Azure 操作组](../../azure-monitor/platform/action-groups.md)来接收短信或语音呼叫形式的通知

## <a name="system-center-operations-manager"></a>System Center Operations Manager
如果已[将 Operations Manager 管理组连接到 Log Analytics](../../azure-monitor/platform/om-agents.md)，该管理组可继续工作，而无需进行任何更改。 不过，对于新连接，必须遵照[使用 Microsoft System Center Operations Manager 管理包来配置 Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/) 中的指导。

## <a name="next-steps"></a>后续步骤
- 有关从 OMS 门户转移到 Azure 门户的指导，请参阅 [Log Analytics 用户从 OMS 门户过渡到 Azure 门户时的常见问题](oms-portal-faq.md)。



