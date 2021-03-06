---
title: Query Performance Insight - Azure Database for PostgreSQL（单一服务器）
description: 本文介绍 Azure Database for PostgreSQL（单一服务器）中的 Query Performance Insight 功能。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 08/21/2019
ms.date: 03/02/2020
ms.openlocfilehash: 0c92cf0328b578261639c7ab9b2380be38af6fa2
ms.sourcegitcommit: d202f6fe068455461c8756b50e52acd4caf2d095
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155318"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**适用于：** Azure Database for PostgreSQL - 单一服务器版本 9.6、10、11

Query Performance Insight 可帮助你快速确定运行时间最长的查询、它们如何随时间的推移而变化，以及哪些等待因素会影响它们。

## <a name="permissions"></a>权限
查看 Query Performance Insight 中查询文本所需的“所有者”  或“参与者”  权限。 读者  可以查看图表和表格，但不能查看查询文本。

## <a name="prerequisites"></a>先决条件
若要运行 Query Performance Insight，数据必须存在于[查询存储](concepts-query-store.md)中。

## <a name="viewing-performance-insights"></a>查看性能见解
Azure 门户中的 [Query Performance Insight](concepts-query-performance-insight.md) 视图将显示来自查询存储的关键信息的可视化效果。 

在 Azure Database for PostgreSQL 服务器的门户页中，选择菜单栏的“智能性能”  部分下的“Query Performance Insight”  。

![Query Performance Insight 长时间运行的查询](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

“长时间运行的查询”  选项卡按每次执行平均持续时间显示前 5 个查询，每隔 15 分钟聚合一次。 可以通过从“查询数量”  下拉列表中进行选择来查看更多查询。 执行此操作时，特定查询 ID 的图表颜色可能会更改。

可以在图表中单击并拖动以缩小到特定的时间窗口。 或者，使用放大和缩小图标分别查看更短或更长的时间段。

图表下方的表提供了有关该时间窗口中长时间运行的查询的更多详细信息。

选择“等待统计信息”  选项卡以查看服务器中等待次数的相应可视化效果。

![Query Performance Insight 等待统计信息](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>注意事项
* Query Performance Insight 不可用于[只读副本](concepts-read-replicas.md)。

## <a name="next-steps"></a>后续步骤
- 了解有关如何在 Azure Database for PostgreSQL 中进行[监视和优化](concepts-monitoring.md)的详细信息。


