---
title: 管理虚拟机规模集中的 VM | Azure
description: 使用 Azure PowerShell 在虚拟机规模集中管理虚拟机。
services: virtual-machine-scale-sets
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 09/27/2016
ms.date: 01/05/2017
ms.author: v-dazen
ms.openlocfilehash: bf53844a56dff0d0b14263d33ee9166b555dd2f4
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52657925"
---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>在虚拟机规模集中管理虚拟机
使用本文中的任务在虚拟机规模集中管理虚拟机。

在执行大多数涉及到在规模集中管理虚拟机的任务时，需要知道要管理的虚拟机的实例 ID。

有关安装最新版 Azure PowerShell、选择订阅和登录到帐户的信息，请参阅[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="display-information-about-a-scale-set"></a>显示有关规模集的信息
可获取有关规模集的常规信息，也称为实例视图。 或者可以获取更具体的信息，如规模集中资源的相关信息。

将带引号的值替换为资源组和规模集的名称，并运行该命令：

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

它会返回类似于下面的内容：

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : chinaeast
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.chinacloudapi.cn/vmss
        VhdContainers[1]                        : https://gstore.blob.core.chinacloudapi.cn/vmss
        VhdContainers[2]                        : https://mstore.blob.core.chinacloudapi.cn/vmss
        VhdContainers[3]                        : https://sstore.blob.core.chinacloudapi.cn/vmss
        VhdContainers[4]                        : https://ystore.blob.core.chinacloudapi.cn/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

将带引号的值替换为资源组和规模集的名称。 将 *#* 替换为要获取其相关信息的虚拟机的实例标识符，并运行该命令：

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

它会返回与此示例类似的内容：

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : chinaeast
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.chinacloudapi.cn/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : chinaeast
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>启动规模集中的虚拟机
将带引号的值替换为资源组和规模集的名称。 将 *#* 替换为要启动的虚拟机的标识符，并运行该命令：

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

在资源浏览器中，我们可以看到该实例的状态是 **正在运行**：

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

不使用 -InstanceId 参数即可启动规模集中的所有虚拟机。

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>停止规模集中的虚拟机
将带引号的值替换为资源组和规模集的名称。 将 *#* 替换为要停止的虚拟机的标识符，并运行该命令：

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

在资源浏览器中，我们可以看到该实例的状态是“已解除分配” ：

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

若要停止但不解除分配虚拟机，请使用 -StayProvisioned 参数。 不使用 -InstanceId 参数即可停止规模集中的所有虚拟机。

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>重启规模集中的虚拟机
将带引号的值替换为资源组和规模集的名称。 将 *#* 替换为要重启的虚拟机的标识符，并运行该命令：

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

不使用 -InstanceId 参数即可重启规模集中的所有虚拟机。

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>从规模集中删除虚拟机
将带引号的值替换为资源组和规模集的名称。 将 *#* 替换为要删除的虚拟机的标识符，并运行该命令：  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

不使用 -InstanceId 参数即可一次性删除整个虚拟机规模集。

## <a name="change-the-capacity-of-a-scale-set"></a>更改规模集的容量
通过更改规模集的容量，可添加或删除虚拟机。 获取要更改的规模集，按需设置容量，并使用新容量更新规模集。 在这些命令中，将带引号的值替换为资源组和规模集的名称。

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

如果要从规模集中删除虚拟机，则首先需要删除 ID 最高的虚拟机。
