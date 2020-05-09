---
title: 临时表
description: 提供了有关在 Synapse SQL 池中使用临时表的基本指导，并重点介绍了会话级临时表的原则。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
origin.date: 04/01/2019
ms.date: 05/11/2020
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 8a2ec1d3b8a06071c79cb77dd3a184d46761e635
ms.sourcegitcommit: f8d6fa25642171d406a1a6ad6e72159810187933
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82198738"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Synapse SQL 池中的临时表
本文包含使用临时表的基本指导，并重点介绍会话级别临时表的原则。 

可以根据本文中的信息将代码模块化，同时提高代码的可重用性和易维护性。

## <a name="what-are-temporary-tables"></a>什么是临时表？
临时表在处理数据时非常有用，尤其是在具有暂时性中间结果的转换期间。 在 SQL 池中，临时表存在于会话级别。  

临时表仅显示给创建它们的会话，并会在该会话注销时自动删除。  

临时表可以提高性能，因为其结果将写入到本地而不是远程存储。

临时表在处理数据时非常有用，尤其是在具有暂时性中间结果的转换期间。 对于 SQL Analytics，临时表存在于会话级别。  它们仅显示给创建它们的会话。 因此，当该会话注销时，它们会被自动删除。 

## <a name="temporary-tables-in-sql-pool"></a>SQL 池中的临时表

在 SQL 池资源中，临时表可以提高性能，因为其结果将写入到本地而不是远程存储。

### <a name="create-a-temporary-table"></a>创建临时表

只需为表名添加 `#` 前缀，即可创建临时表。  例如：

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

此外可以使用 `CTAS` 通过完全相同的方法来创建临时表：

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` 是一个强大的命令并具有附加优势，可有效利用事务日志空间。 
> 
> 

## <a name="dropping-temporary-tables"></a>删除临时表
创建新会话时，应不存在任何临时表。  

如果要调用使用相同名称创建临时表的同一存储过程来确保 `CREATE TABLE` 语句成功执行，可以使用带 `DROP` 的简单预存在检查，如下面的示例所示：

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

为了实现编码一致性，好的做法是对表和临时表都使用此模式。  在代码中完成对临时表的操作后，也可使用 `DROP TABLE` 来删除临时表。  

在存储过程开发中，通常将 drop 命令捆绑在过程末尾，以确保清除这些对象。

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>模块化代码
由于可以在用户会话中的任何位置查看临时表，因此可以利用此功能将应用程序代码模块化。  

例如，以下存储过程生成 DDL，按统计信息名称更新数据库中的所有统计信息：

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

在此阶段发生的唯一操作是创建存储过程，该存储过程使用 DDL 语句生成临时表 #stats_ddl。  

此存储过程会删除现有的 #stats_ddl，确保在会话中运行多次也不会失败。  

但是，由于存储过程的末尾没有 `DROP TABLE`，当存储过程完成后，它将保留创建的表，以便能够在存储过程之外进行读取。  

与其他 SQL Server 数据库不同，在 SQL 池中，可以脱离创建临时表的过程来使用该临时表。  可以在会话中的任何位置  使用 SQL 池临时表。 此功能可提高代码的模块化程度与易管理性，如以下示例所示：

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>临时表的限制
SQL 池在实现临时表时确实会施加一些限制。  目前，仅支持会话范围的临时表。  不支持全局临时表。  

此外，不能基于临时表创建视图。  只能使用哈希分布或轮循机制分布来创建临时表。  不支持复制的临时表分布。 

## <a name="next-steps"></a>后续步骤

若要详细了解如何开发表，请参阅[使用 SQL Analytics 资源设计表](sql-data-warehouse-tables-overview.md)一文。
