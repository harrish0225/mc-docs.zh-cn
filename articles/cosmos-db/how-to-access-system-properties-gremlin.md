---
title: 通过 Azure Cosmos DB 图形访问系统文档属性
description: 了解如何通过 Gremlin API 读取和写入 Cosmos DB 系统文档属性
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
origin.date: 09/10/2019
ms.date: 12/16/2019
author: rockboyfor
ms.author: v-yeche
ms.openlocfilehash: 5f531ac93355402f942f92c75a644f1cb3965e71
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75336229"
---
# <a name="system-document-properties"></a>系统文档属性

Azure Cosmos DB 具有与每个文档有关的[系统属性](https://docs.microsoft.com/rest/api/cosmos-db/databases)，例如 ```_ts```、```_self```、```_attachments```、```_rid``` 和 ```_etag```。 此外，Gremlin 引擎会添加与边缘有关的 ```inVPartition``` 和 ```outVPartition``` 属性。 默认情况下，这些属性可供遍历。 但是，可将特定属性或所有这些属性都包含在 Gremlin 遍历中。

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

此属性用于执行乐观并发控制。 如果应用程序需要将操作分为几个不同的遍历，则可以使用 eTag 属性避免并发写入时的数据丢失。

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>生存时间 (TTL)

如果集合已启用文档过期，并且文档上设置了 ```ttl``` 属性，则此属性将在 Gremlin 遍历中可用作常规的顶点或边缘属性。 启用生存时间属性曝光不需要 ```ProjectionStrategy```。

通过以下遍历创建的顶点将在 **123 秒**后自动删除。

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>后续步骤
* [Cosmos DB 乐观并发](faq.md#how-does-the-sql-api-provide-concurrency)
* Azure Cosmos DB 中的[生存时间 (TTL)](time-to-live.md)

<!-- Update_Description: update meta properties, wording update, update link -->