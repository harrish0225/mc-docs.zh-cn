---
title: 使用 PowerShell 获取 Azure VM 的维护通知
description: 使用 PowerShell 查看在 Azure 中运行的虚拟机的维护通知并启动自助维护。
services: virtual-machines
documentationcenter: ''
author: rockboyfor
editor: ''
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
origin.date: 11/19/2019
ms.date: 02/17/2020
ms.author: v-yeche
ms.openlocfilehash: 3de634fb557e8ca3086578a68e54aecb8265d65b
ms.sourcegitcommit: ada94ca4685855f58616e4bf1dd5ca757878dfdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429964"
---
# <a name="handling-planned-maintenance-using-powershell"></a>使用 PowerShell 处理计划内维护

**本文适用于同时运行 Linux 和 Windows 的虚拟机。**

可以使用 Azure Powershell 查看何时安排 VM 进行[维护](maintenance-notifications.md)。 使用 `-status` 参数时可通过 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) cmdlet 获得计划内维护信息。

仅当有计划内维护时，才会返回维护信息。 如果未计划任何影响 VM 的维护，该 cmdlet 不返回任何维护信息。 

```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

在 MaintenanceRedeployStatus 下返回以下属性： 

| Value | 说明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指示此时是否可以在 VM 上启动维护 |
| PreMaintenanceWindowStartTime         | 可以在 VM 上启动维护的自助式维护时段的起点 |
| PreMaintenanceWindowEndTime           | 可以在 VM 上启动维护的自助式维护时段的终点 |
| MaintenanceWindowStartTime            | Azure 在 VM 上启动维护的计划内维护时段的起点 |
| MaintenanceWindowEndTime              | Azure 在 VM 上启动维护的计划内维护时段的终点 |
| LastOperationResultCode               | 上次尝试在 VM 上启动维护的结果 |

还可以通过使用 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) 并且不指定 VM 来获取资源组中所有 VM 的维护状态。

```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

以下 PowerShell 示例获取订阅 ID，并返回计划进行维护的 VM 列表。

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>使用 PowerShell 在 VM 上启动维护

如果 **IsCustomerInitiatedMaintenanceAllowed** 设置为 true，以下命令使用上一部分中函数的信息，在 VM 上启动维护。

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>经典部署

如果你仍在使用由经典部署模型部署的旧 VM，则可以使用 PowerShell 查询 VM，并启动维护。

若要获取 VM 的维护状态，请键入：

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

若要在经典 VM 上启动维护，请键入：

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>后续步骤

还可以使用 [Azure CLI](maintenance-notifications-cli.md) 或[门户](maintenance-notifications-portal.md)处理计划内维护。

<!-- Update_Description: new article about maintenance notifications powershell -->
<!--NEW.date: 02/17/2020-->