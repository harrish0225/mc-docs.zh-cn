---
title: 从 Azure 逻辑应用连接到 RSS 源
description: 使用 Azure 逻辑应用自动执行监视和管理 RSS 源的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
origin.date: 08/24/2018
ms.date: 03/09/2020
ms.author: v-yeche
tags: connectors
ms.openlocfilehash: 7877893f6af75cb08bf4ae377f69a1fdd2ad4723
ms.sourcegitcommit: 1ac138a9e7dc7834b5c0b62a133ca5ce2ea80054
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304669"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 RSS 源

使用 Azure 逻辑应用和 RSS 连接器，可以为任何 RSS 源创建自动化任务和工作流，例如：

* 监视何时发布 RSS 源项。
* 列出所有 RSS 源项。

RSS（极具特色的网站摘要），也称为“真正简单的整合”，是一种流行的 Web 联合格式，用于发布经常更新的内容，例如博客文章和新闻标题。 许多内容发布者都提供 RSS 源，以便用户可以订阅该内容。 

可以使用 RSS 触发器从 RSS 源获取响应，并使输出可用于其他操作。 可以在逻辑应用中使用 RSS 操作来执行 RSS 源的任务。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个 Azure 试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。 

* RSS 源的 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 RSS 源的逻辑应用。 若要从 RSS 触发器开始，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 RSS 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-an-rss-feed"></a>连接到 RSS 源

1. 登录到 [Azure 门户](https://portal.azure.cn)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 选择一个路径： 

    * 对于空白逻辑应用，请在搜索框中输入“rss”作为筛选器。 在触发器列表下，选择所需的触发器。 

        -或-

    * 对于现有逻辑应用，请在要添加操作的步骤下，选择“新建步骤”  。 在搜索框中，输入“rss”作为筛选器。 在操作列表下，选择所需的操作。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](https://docs.microsoft.com/connectors/rss/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)

<!-- Update_Description: update meta properties, wording update, update link -->