---
title: 在 Azure SQL 数据仓库中使用动态 SQL | Microsoft Docs
description: 有关在开发解决方案时使用 Azure SQL 数据仓库中的动态 SQL 的技巧。
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 32ee71063a14fec27f00bc86f50aaa3b3c062037
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52654705"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL 数据仓库中的动态 SQL
有关在开发解决方案时使用 Azure SQL 数据仓库中的动态 SQL 的技巧。

## <a name="dynamic-sql-example"></a>动态 SQL 示例

为 SQL 数据仓库开发应用程序代码时，可能需要使用动态 SQL 来帮助提供灵活、通用且模块化的解决方案。 SQL 数据仓库目前不支持 Blob 数据类型。 不支持 blob 数据类型可能会限制字符串的大小，因为 blob 数据类型包括 varchar(max) 和 nvarchar(max) 类型。 如果已在应用程序代码中使用这些类型构建大型字符串，则需要将代码分解成块，并改用 EXEC 语句。

一个简单的示例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字符串较短，则可以像平时一样使用 [sp_executesql](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)。

> [!NOTE]
> 作为动态 SQL 执行的语句仍将遵循所有 TSQL 验证规则。
> 
> 

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。

