---
title: 使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 列出 Azure RBAC 中的角色定义 | Microsoft Docs
description: 了解如何使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 列出 Azure RBAC 中的内置角色和自定义角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2020
ms.author: v-junlch
ms.reviewer: bagovind
ms.openlocfilehash: f8c6618523d86120e76ba03376b312944480664f
ms.sourcegitcommit: 64584c0bf31b4204058ae2b4641356b904ccdd58
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80581723"
---
# <a name="list-role-definitions-in-azure-rbac"></a>列出 Azure RBAC 中的角色定义

角色定义是可执行特权的集合，例如读取、写入和删除。 它通常直接称为“角色”。 [Azure 基于角色的访问控制 (RBAC) ](overview.md) 具有超过 120 种[内置角色](built-in-roles.md)，也可以创建自己的自定义角色。 本文介绍如何列出可用于授予 Azure 资源访问权限的内置角色和自定义角色。

若要查看 Azure Active Directory 的管理员角色列表，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

## <a name="azure-portal"></a>Azure 门户

### <a name="list-all-roles"></a>列出所有角色

按照以下步骤在 Azure 门户中列出所有角色。

1. 在 Azure 门户中单击“所有服务”，然后选择任意范围  。 例如，可以选择“管理组”、“订阅”、“资源组”或某个资源    。

1. 单击特定的资源。

1. 单击“访问控制(IAM)”  。

1. 单击“角色”选项卡以查看包含所有内置角色和自定义角色的列表  。

   可以看到在当前范围分配到每个角色的用户和组的数目。

   ![角色列表](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>列出所有角色

若要列出 Azure PowerShell 中的所有角色，请使用 [Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>列出角色定义

若要列出特定角色的详细信息，请使用 [Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>以 JSON 格式列出角色定义

若要以 JSON 格式列出角色，请使用 [Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>列出角色定义的特权

若要列出特定角色的特权，请使用 [Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>列出所有角色

若要列出 Azure CLI 中的所有角色，请使用 [az role definition list](/cli/role/definition#az-role-definition-list)。

```azurecli
az role definition list
```

以下示例列出了所有可用的角色定义的名称和说明：

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

下面的示例列出了所有内置角色。

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-a-role-definition"></a>列出角色定义

若要列出角色的详细信息，请使用 [az role definition list](/cli/role/definition#az-role-definition-list)。

```azurecli
az role definition list --name <role_name>
```

下面的示例列出了“参与者”  角色定义：

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>列出角色定义的特权

以下示例仅列出“参与者”角色的“actions”和“notActions”    。

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

以下示例仅列出了“虚拟机参与者”角色的“actions”  。

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>列出角色定义

若要列出角色定义，请使用[角色定义 - 列出](https://docs.microsoft.com/rest/api/authorization/roledefinitions/list) REST API。 若要优化结果，请指定一个范围和可选的筛选器。

1. 从下面的请求开始：

    ```http
    GET https://management.chinacloudapi.cn/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. 在 URI 中，将“{scope}”  替换为要列出角色定义的范围。

    > [!div class="mx-tableFixed"]
    > | 作用域 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 资源 |

    在前面的示例中，microsoft.web 是引用应用服务实例的资源提供程序。 同样，可以使用任何其他资源提供程序并指定范围。 有关详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)和支持的 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)。  
     
1. 将 *{filter}* 替换为筛选角色定义列表时要应用的条件。

    > [!div class="mx-tableFixed"]
    > | 筛选器 | 说明 |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | 列出指定范围和任何子范围的角色定义。 |
    > | `$filter=type+eq+'{type}'` | 列出指定类型的角色定义。 角色的类型可以是 `CustomRole` 或 `BuiltInRole`。 |

### <a name="list-a-role-definition"></a>列出角色定义

若要列出特定角色的详细信息，请使用[角色定义 - 获取](https://docs.microsoft.com/rest/api/authorization/roledefinitions/get)或[角色定义 - 按 ID 获取](https://docs.microsoft.com/rest/api/authorization/roledefinitions/getbyid) REST API。

1. 从下面的请求开始：

    ```http
    GET https://management.chinacloudapi.cn/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    对于目录级别的角色定义，可以使用以下请求：

    ```http
    GET https://management.chinacloudapi.cn/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 中，将“{scope}”  替换为要列出角色定义的范围。

    > [!div class="mx-tableFixed"]
    > | 作用域 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 资源 |
     
1. 将“{roleDefinitionId}”  替换为角色定义标识符。

## <a name="next-steps"></a>后续步骤

- [Azure 资源的内置角色](built-in-roles.md)
- [Azure 资源的自定义角色](custom-roles.md)
- [使用 Azure RBAC 和 Azure 门户列出角色分配](role-assignments-list-portal.md)
- [使用 Azure RBAC 和 Azure 门户添加或删除角色分配](role-assignments-portal.md)

