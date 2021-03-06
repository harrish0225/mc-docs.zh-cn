---
title: 针对数据修改设计 Azure 存储表 | Microsoft Docs
description: 针对 Azure 表存储中的数据修改对表进行设计。
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 04/23/2018
ms.date: 05/27/2019
ms.author: v-jay
ms.subservice: tables
ms.openlocfilehash: f4046eb2971fd0072e5fe834dc5cc7fa6a24547f
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004318"
---
# <a name="design-for-data-modification"></a>针对数据修改的设计
本文重点介绍优化插入、更新和删除的设计注意事项。 在某些情况下，需要在针对查询优化的设计与针对数据修改优化的设计之间进行权衡，就像你在设计关系数据库时要做的那样（尽管在关系数据库中，管理设计权衡的方法是不同的）。 “表设计模式”部分介绍了一些详细的表服务设计模式，着重阐释了其中的部分权衡。 在实践中，会发现许多针对查询实体优化的设计对于修改实体也能很好地工作。  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>优化插入、更新和删除操作的性能
若要更新或删除某个实体，必须可使用 **PartitionKey** 和 **RowKey** 值确定该实体。 就此点而言，由于希望尽可能高效地标识实体，因此选用于修改实体的 **PartitionKey** 和 **RowKey** 应遵循为支持点查询而选择的类似条件。 为了发现更新/删除实体所需的 **PartitionKey** 和 **RowKey** 值，你不希望查找实体时使用效率低下的分区或表扫描方式。  

“表设计模式”部分中的以下模式解决了优化性能或插入、更新和删除操作的问题：  

* [高容量删除模式](table-storage-design-patterns.md#high-volume-delete-pattern) - 通过将要同时删除的所有实体都存储在它们自己的单独表中来实现删除大量实体；通过删除表来删除这些实体。  
* [数据系列模式](table-storage-design-patterns.md#data-series-pattern) - 将完整的数据系列存储在单个实体中，以最大限度地减少发出请求的次数。  
* [宽实体模式](table-storage-design-patterns.md#wide-entities-pattern) - 使用多个物理实体来存储具有多于 252 个属性的逻辑实体。  
* [大实体模式](table-storage-design-patterns.md#large-entities-pattern) - 使用 blob 存储来存储大属性值。  

## <a name="ensure-consistency-in-your-stored-entities"></a>确保存储实体中的一致性
影响你选择用于优化数据修改的键的其他关键因素是如何通过使用原子事务来确保一致性。 只能使用 EGT 作用于存储在同一个分区中的实体。  

[表设计模式](table-storage-design-patterns.md)一文的以下模式解决了管理一致性的问题：  

* [内分区的第二索引模式](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - 利用同一分区中的 **RowKey** 值存储每个实体的多个副本，实现快速、高效的查询，并借助不同的 **RowKey** 值替换排序顺序。  
* [内分区的第二索引模式](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 在单独分区/表格中利用不同 RowKey 值存储每个实体的多个副本，实现快速高效的查找，并借助 **RowKey** 值替换排序顺序。  
* [最终一致性事务模式](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - 通过使用 Azure 队列跨分区边界或存储系统边界启用最终一致的行为。
* [索引实体模式](table-storage-design-patterns.md#index-entities-pattern) - 维护索引实体，实现返回实体列表的高效搜索。  
* [反规范模式](table-storage-design-patterns.md#denormalization-pattern) - 将相关数据组合在一起放置在单个实体中，以便能够使用单个点查询检索所有所需的数据。  
* [数据系列模式](table-storage-design-patterns.md#data-series-pattern) - 将完整的数据系列存储在单个实体中，以最大限度地减少发出请求的次数。  

有关实体组事务的信息，请参阅[实体组事务](table-storage-design.md#entity-group-transactions)部分。  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>确保用于高效修改的设计便于高效查询
在许多情况下，用于高效查询的设计会产生高效修改的效果，但你应始终评估这是否适用于特定方案。 [表设计模式](table-storage-design-patterns.md)一文中的某些模式明确评估了查询实体和修改实体之间的折衷方案，你应该始终考虑每种操作的数量。  

[表设计模式](table-storage-design-patterns.md)一文中的以下模式针对的是设计实现高效查询和设计实现高效数据修改之间的折衷方案：  

* [复合键模式](table-storage-design-patterns.md#compound-key-pattern) - 使用复合 **RowKey** 值可让客户端使用单个点查询查找相关数据。  
* [日志结尾模式](table-storage-design-patterns.md#log-tail-pattern) - 通过使用以日期时间倒序排序的 *RowKey* 值检索最近添加到分区中的 **n** 个实体。  
