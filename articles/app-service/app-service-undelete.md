---
title: 还原已删除的应用
description: 了解如何在 Azure 应用服务中还原已删除的应用。 避免意外删除应用带来的麻烦。
author: btardif
ms.author: v-tawe
origin.date: 09/23/2019
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 065fbc811a0346b7da1050ce9132b138e0002bfd
ms.sourcegitcommit: 1e68aea05a8d979237d6377a3637bb7654097111
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77566651"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 还原已删除的应用服务应用

如果意外删除了 Azure 应用服务中的应用，可以使用 [Az PowerShell 模块](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令还原它。

> [!NOTE]
> 初始删除 30 天后，已删除的应用将从系统中清除。 应用一旦被清除，将无法恢复。
>

## <a name="re-register-app-service-resource-provider"></a>重新注册应用服务资源提供程序
有些客户可能会遇到这样的问题：检索已删除的应用的列表时失败。 若要解决此问题，请运行以下命令：

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>列出已删除的应用

若要获取已删除的应用集合，可以使用 `Get-AzDeletedWebApp`。

若要获取有关特定的已删除应用的详细信息，可以使用：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

详细信息包括:

- **DeletedSiteId**：应用的唯一标识符，适用于删除了多个同名应用的情况
- **SubscriptionID**：包含已删除的资源的订阅
- **位置**：原始应用的位置
- **ResourceGroupName**：原始资源组的名称
- **名称**：原始应用的名称。
- **Slot**：槽的名称。
- **Deletion Time**：删除应用的时间  

## <a name="restore-deleted-app"></a>还原已删除的应用

确定想要还原的应用后，可以使用 `Restore-AzDeletedWebApp` 来还原它。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

命令的输入为：

- **资源组**：要将应用还原到的目标资源组
- **名称**：应用的名称，应全局唯一。
- **TargetAppServicePlanName**：链接到该应用的应用服务计划

默认情况下，`Restore-AzDeletedWebApp` 会同时还原应用的配置和内容。 如果只想还原内容，请在此 cmdlet 中使用 `-RestoreContentOnly` 标志。

> [!NOTE]
> 如果应用托管在应用服务环境中，后来已将其删除，则仅当相应的应用服务环境仍然存在时，才能还原该应用。
>

可在以下文章中找到完整的 cmdlet 参考信息：[Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
