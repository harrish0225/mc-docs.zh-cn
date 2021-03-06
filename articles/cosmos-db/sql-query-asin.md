---
title: Azure Cosmos DB 查询语言中的 ASIN
description: 了解 Azure Cosmos DB 中的 Arcsine (ASIN) SQL 系统函数如何返回其正弦是指定数值表达式的角度（以弧度为单位）
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 09/13/2019
ms.date: 02/10/2020
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 5d513aaec41cb347c676289c3506d0d55ae2b37c
ms.sourcegitcommit: 5c4141f30975f504afc85299e70dfa2abd92bea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77028694"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 返回角度（弧度），其正弦是指定的数值表达式。 也被称为反正弦。  

## <a name="syntax"></a>语法

```sql
ASIN(<numeric_expr>)  
```  

## <a name="arguments"></a>参数

*numeric_expr*  
  是一个数值表达式。  

## <a name="return-types"></a>返回类型

  返回一个数值表达式。  

## <a name="examples"></a>示例

  以下示例返回 -1 的 `ASIN`。 

```sql
SELECT ASIN(-1) AS asin  
```  

 下面是结果集。  

```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!-- Update_Description: update meta properties, wording update, update link -->