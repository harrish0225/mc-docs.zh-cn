---
title: 高可用性和可靠性
description: 了解 Azure 计划程序中的高可用性和可靠性
services: scheduler
ms.service: scheduler
author: WenJason
ms.author: v-jay
ms.reviewer: klam, estfan
ms.topic: article
origin.date: 08/16/2016
ms.date: 03/30/2020
ms.openlocfilehash: ac8295142f95804c0962fb901922c15574031bbf
ms.sourcegitcommit: 90660563b5d65731a64c099b32fb9ec0ce2c51c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80341695"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Azure 计划程序的高可用性和可靠性

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)将替换[即将停用](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)的 Azure 计划程序。 若要继续使用在计划程序中设置的作业，请尽快[迁移到 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 
>
> 计划程序在 Azure 门户中不再可用，但 [REST API](https://docs.microsoft.com/rest/api/scheduler) 和 [Azure 计划程序 PowerShell cmdlet](scheduler-powershell-reference.md) 目前仍可用，以便你可以管理作业和作业集合。

## <a name="high-availability"></a>高可用性

Azure 计划程序[高度可用]，并使用地域冗余服务部署和地理区域作业复制。

### <a name="geo-redundant-service-deployment"></a>异地冗余的服务部署

目前，Azure 计划程序可在 Azure 所支持的几乎每个地理区域中使用。 因此，如果托管区域中的 Azure 数据中心变得不可用，仍可以使用 Azure 计划程序，因为该服务的故障转移功能使计划程序可在另一个数据中心提供。

### <a name="geo-regional-job-replication"></a>地理区域作业复制

在 Azure 计划程序中，你自己的作业在 Azure 区域中复制。 因此如果一个中断，Azure 计划程序将进行故障转移，并确保配对地理区域中的另一个数据中心运行作业。

例如，如果在“中国东部”创建了一个作业，Azure 计划程序会自动在“中国北部”复制该作业。 如果在“中国东部”发生故障，则 Azure 计划程序将在“中国北部”运行作业。 

Azure 计划程序还可确保数据仍保留在同一个但更广泛的地理区域，以防 Azure 发生故障。 因此，只需要高可用性时无需重复作业。 Azure 计划程序会自动为作业提供高可用性。

## <a name="reliability"></a>可靠性

Azure 计划程序可保证其自身的高可用性，但提供不同的途径访问用户创建的作业。 例如，假设你的作业调用不可用的 HTTP 终结点。 Azure 计划程序仍尝试通过为你提供处理故障的替代方法来成功运行作业： 

* 设置重试策略。
* 设置备用终结点。

<a name="retry-policies"></a>

### <a name="retry-policies"></a>重试策略

Azure 计划程序允许你设置重试策略。 如果作业失败，默认情况下，计划程序以 30 秒的间隔继续重试作业四次。 可以放宽此重试策略，例如每 30 秒重试 10 次，或者提高限制，例如每日重试两次。

例如，假设你创建了一个调用 HTTP 终结点的每周作业。 如果在作业运行时，HTTP 终结点变得不可用并持续几个小时，你可能不希望再等一个星期再次运行该作业，这是因为默认重试策略在此情况下不起作用。 因此，你可能想要更改标准重试策略，以便进行重试，例如，每三个小时，而不是每 30 秒。 

若要了解如何设置重试策略，请参阅 [retryPolicy](scheduler-concepts-terms.md#retrypolicy)。

### <a name="alternate-endpoints"></a>备用终结点

如果 Azure 计划程序作业调用无法访问的终结点，即使已遵循重试策略，计划程序会回到可以处理此类错误的备用终结点。 因此，如果设置此终结点，计划程序就会调用该终结点，这使得你自己的作业在发生故障时高度可用。

例如，下图显示了在调用纽约的 Web 服务时，计划程序如何遵循重试策略。 如果重试失败，计划程序将检查备用终结点。 如果终结点存在，计划程序就会开始向备用终结点发送请求。 相同的重试策略适用于原始操作和备用操作。

![使用重试策略和备用终结点的计划程序行为](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

备用操作的操作类型可能不同于原始操作。 例如，虽然原始操作调用 HTTP 终结点，但备用操作可以通过使用存储队列、服务总线队列或服务总线主题操作来记录错误。

若要了解如何设置备用终结点，请参阅 [errorAction](scheduler-concepts-terms.md#error-action)。

## <a name="next-steps"></a>后续步骤

* [概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [Azure 计划程序 REST API 参考](https://docs.microsoft.com/rest/api/scheduler)
* [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)
* [限制、配额、默认值和错误代码](scheduler-limits-defaults-errors.md)
