---
title: 限制 - Azure Database for PostgreSQL（单一服务器）
description: 本文介绍了 Azure Database for PostgreSQL（单一服务器）中的限制，例如连接数和存储引擎选项。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 01/28/2020
ms.date: 02/10/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1cf64db233655f95553fe9318e27b2194c665bc6
ms.sourcegitcommit: 925c2a0f6c9193c67046b0e67628d15eec5205c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77068368"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的限制
下列各部分介绍数据库服务中的容量和功能限制。 如果想了解资源（计算、内存、存储）层，请参阅[定价层](concepts-pricing-tiers.md)一文。


## <a name="maximum-connections"></a>最大连接数
每个定价层的最大连接数和 vCore 数如下所示。 Azure 系统需要使用五个连接来监视 Azure Database for PostgreSQL 服务器。 

|**定价层**| **vCore(s)**| 最大连接数  | **最大用户连接数** |
|---|---|---|---|
|基本| 1| 55 | 50|
|基本| 2| 105 | 100|
|常规用途| 2| 150| 145|
|常规用途| 4| 250| 245|
|常规用途| 8| 480| 475|
|常规用途| 16| 950| 945|
|常规用途| 32| 1500| 1495|
|常规用途| 64| 1900| 1895|
|内存优化| 2| 300| 295|
|内存优化| 4| 500| 495|
|内存优化| 8| 960| 955|
|内存优化| 16| 1900| 1895|
|内存优化| 32| 1987| 1982|

当连接数超出限制时，可能会收到以下错误：
> 严重：很抱歉，客户端数过多

> [!IMPORTANT]
> 为了获得最佳体验，我们建议你使用 pgBouncer 之类的连接池来有效地管理连接。

PostgreSQL 连接，即使空闲，也可以占用大约 10MB 的内存。 而且，创建新连接需要时间。 大多数应用程序都请求许多短期连接，这加剧了这种情况。 其结果是可用于实际工作负荷的资源减少，从而导致性能下降。 减少空闲连接并重用现有连接的连接池会有助于避免这种情况。

## <a name="functional-limitations"></a>功能限制
### <a name="scale-operations"></a>缩放操作
- 目前不支持动态缩放到“基本”定价层或从该层动态缩放。
- 目前不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。 如果要升级到下一个主版本，请进行[转储并将其还原](./howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

> 请注意，在 PostgreSQL 版本 10 之前，[PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)将_主版本_升级视为第一个_或_第二个数字的增加（例如，9.5 到 9.6 视为_主_版本升级）。
> 从版本 10 开始，只有第一个数字的更改才视为主版本升级（例如，10.0 到 10.1 是_次要_版本升级，10 到 11 是_主_版本升级）。

### <a name="vnet-service-endpoints"></a>VNet 服务终结点
- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

### <a name="restoring-a-server"></a>还原服务器
- 使用 PITR 功能时，将使用与新服务器所基于的服务器相同的定价层配置创建新服务器。
- 还原期间创建的新服务器没有原始服务器上存在的防火墙规则。 需要为此新服务器单独设置防火墙规则。
- 不支持还原已删除的服务器。

### <a name="utf-8-characters-on-windows"></a>Windows 上的 UTF-8 字符
- 在某些情况下，Windows 上的开源 PostgreSQL 不完全支持 UTF-8 字符，这会影响 Azure Database for PostgreSQL。 有关详细信息，请参阅 [postgresql-archive 中的 Bug #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) 上的话题。

## <a name="next-steps"></a>后续步骤
- 了解[每个定价层中有哪些可用资源](concepts-pricing-tiers.md)
- 了解[支持的 PostgreSQL 数据库版本](concepts-supported-versions.md)
- 查看[如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器](howto-restore-server-portal.md)
