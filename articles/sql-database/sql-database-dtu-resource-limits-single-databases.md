---
title: DTU 资源限制单一数据库
description: 本页介绍 Azure SQL 数据库中单一数据库的一些常见 DTU 资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: ''
origin.date: 03/20/2019
ms.date: 02/17/2020
ms.openlocfilehash: 3d7811c9547c5311bbed965514d0b51caceb5b9b
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79293531"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>使用 DTU 购买模型的单一数据库的资源限制

本文提供了针对使用 DTU 购买模型的 Azure SQL 数据库的单一数据库的详细资源限制。

有关弹性池的 DTU 购买模型资源限制，请参阅 [DTU 资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。 有关 vCore 资源限制，请参阅 [vCore 资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和 [vCore 资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)。 有关不同购买模型的更多信息，请参阅[购买模型和服务层级](sql-database-purchase-models.md)。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>单一数据库：存储大小和计算大小

下表显示了可用于每个服务层级和计算大小的单一数据库的资源。 可通过 [Azure 门户](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 或 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) 为单一数据库设置服务层级、计算大小和存储量。

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放单一数据库](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>“基本”服务层级

| **计算大小** | **基本** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 包含的存储 (GB) | 2 |
| 最大存储选择 (GB) | 2 |
| 最大内存中 OLTP 存储 (GB) |不适用 |
| 最大并发工作线程数（请求数） | 30 |
| 最大并发会话数 | 300 |
|||

> [!IMPORTANT]
> “基本”服务层级提供的 vCore (CPU) 不到一个。  对于 CPU 密集型工作负荷，建议使用 S3 或更高的服务层级。 
>
>有关数据存储的“基本”服务层级放置在标准页 Blob 上。 标准页 Blob 使用基于硬盘驱动器 (HDD) 的存储介质，最适合用于对性能变化不太敏感的开发、测试和其他不频繁访问的工作负荷。
>

### <a name="standard-service-tier"></a>“标准”服务层级

| **计算大小** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU | 10 个 | 20 个 | 50 | 100 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |
| 最大并发工作线程数（请求数）| 60 | 90 | 120 | 200 |
| 最大并发会话数 |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> 标准 S0、S1 和 S2 层级提供的 vCore (CPU) 不到一个。  对于 CPU 密集型工作负荷，建议使用 S3 或更高的服务层级。 
>
>有关数据存储的标准 S0 和 S1 服务层级放置在标准页 Blob 上。 标准页 Blob 使用基于硬盘驱动器 (HDD) 的存储介质，最适合用于对性能变化不太敏感的开发、测试和其他不频繁访问的工作负荷。
>

### <a name="standard-service-tier-continued"></a>“标准”服务层级（续）

| **计算大小** | **S4** | **S6** |  S7 |  S9 |  S12 |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |不适用 |
| 最大并发工作线程数（请求数）| 400 | 800 | 1600 | 3200 |6000 |
| 最大并发会话数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>“高级”服务层级

| **计算大小** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 包含的存储 (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| 最大存储选择 (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| 最大内存中 OLTP 存储 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 最大并发工作线程数（请求数）| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 从 1024 GB 到 4096 GB，以 256 GB 为增量

> [!NOTE]
> 有关 `tempdb` 限制，请参阅 [tempdb 限制](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)。

## <a name="next-steps"></a>后续步骤

- 有关单一数据库的 vCore 资源限制，请参阅[使用 vCore 购买模型的单一数据库的资源限制](sql-database-vcore-resource-limits-single-databases.md)
- 有关弹性池的 vCore 资源限制，请参阅[使用 vCore 购买模型的弹性池的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用 DTU 购买模型的弹性池的资源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 有关托管实例的资源限制，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关数据库服务器上的资源限制的信息，请参阅 [SQL 数据库服务器资源限制概述](sql-database-resource-limits-database-server.md)了解有关服务器级别和订阅级别限制的信息。