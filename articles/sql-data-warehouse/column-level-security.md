---
title: 什么是 SQL 数据仓库的列级安全性？
description: 借助列级别安全性，客户可以根据用户的执行上下文或组成员身份，控制对数据库表列的访问，从而简化应用程序中的安全性设计和编码，以及对列访问实施限制。
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
origin.date: 04/02/2019
ms.date: 03/02/2020
ms.author: v-jay
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 37e8d0d1424d3a83df4ef9d12a9a4e3bac1ad17f
ms.sourcegitcommit: 892137d117bcaf9d88aec0eb7ca756fe39613344
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78154395"
---
# <a name="column-level-security"></a>列级别安全性

借助列级别安全性，客户可以根据用户的执行上下文或组成员身份，控制对表列的访问。

SQL 数据仓库中还提供了[行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017)。 

列级别安全性简化了应用程序中的安全性设计和编程，使你能够限制列访问权限以保护敏感数据。 例如，确保具体用户只能访问表中与其部门相关的特定列。 访问限制逻辑位于数据库层中，而不是在另一个应用层中远离数据。 每当有任何一层的数据访问请求，数据库就会应用访问限制。 此限制会减少整个安全系统的外围应用，从而提高安全措施的可靠性。 此外，使用列级别安全性还无需引入用于筛选掉列的视图以限制用户访问。

可以使用 [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL 语句实现列级别安全性。 借助此机制，SQL 和 Azure Active Directory (AAD) 身份验证同时受支持。

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>语法

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>示例
下面的示例展示了如何限制 `TestUser` 访问 `Membership` 表的 `SSN` 列：

创建 `Membership` 表，其中包含用于存储社会安全号码的“SSN”列：

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

允许 `TestUser` 访问所有列，包含敏感数据的“SSN”列除外：

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

如果包含“SSN”列，以 `TestUser` 身份执行的查询便会失败：

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>用例

下面是目前的一些列级别安全性用例示例：

- 金融服务公司只允许客户经理访问客户的社会安全号码 (SSN)、电话号码和其他个人身份信息 (PII)。
- 医疗保健提供商只允许医生和护士访问敏感病史档案，不允许计费部门成员查看此类数据。
