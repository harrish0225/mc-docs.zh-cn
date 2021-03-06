---
title: include 文件
description: include 文件
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
origin.date: 10/06/2019
ms.date: 03/16/2020
ms.author: v-tawe
ms.custom: include file
ms.openlocfilehash: 1483be677fd9bdfd0bc549f933ee4ce5c9ea8c49
ms.sourcegitcommit: 1d3d8dfdaf6281f06640cbee7124a1e8bf102c50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2020
ms.locfileid: "80272820"
---
## <a name="create-a-resource-group"></a>创建资源组

如果已有资源组，可以跳至[创建 Key Vault](#create-a-key-vault)。 

资源组是在其中部署和管理 Azure 资源的逻辑容器。 

使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) Azure CLI 命令、[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell 命令或从 [Azure 门户](https://portal.azure.cn)创建资源组。

### <a name="azure-cli"></a>Azure CLI

```azurecli
az group create --name "myResourceGroup" --location chinaeast
```
### <a name="azure-powershell"></a>Azure PowerShell
```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "ChinaEast"
```

## <a name="create-a-key-vault"></a>创建密钥保管库

如果已有 Key Vault，可以跳至[设置 Key Vault 高级访问策略](#set-key-vault-advanced-access-policies)。 

使用 [az keyvault create](https://docs.azure.cn/cli/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI 命令、[New-AzKeyvault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell 命令、[Azure 门户](https://portal.azure.cn)或[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)创建 Key Vault。

>[!WARNING]
> 为确保加密机密不会跨过区域边界，Azure 磁盘加密需要将 Key Vault 和 VM 共置在同一区域中。 在要加密的 VM 所在的同一区域中创建并使用 Key Vault。 

每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI 创建 Key Vault 时，请添加“--enabled-for-disk-encryption”标志。

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "chinaeast" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 Azure PowerShell 创建 Key Vault 时，请添加“-EnabledForDiskEncryption”标志。

```powershell
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "chinaeast" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager 模板

还可以使用[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)创建 Key Vault。

1. 在 Azure 快速入门模板中，单击“部署到 Azure”。 
2. 选择订阅、资源组、资源组位置、Key Vault 名称、对象 ID、法律条款和协议，然后单击“购买”。  


##  <a name="set-key-vault-advanced-access-policies"></a>设置 Key Vault 高级访问策略

Azure 平台需要访问 Key Vault 中的加密密钥或机密，才能使这些密钥和机密可供 VM 用来启动和解密卷。 

如果在创建时没有为磁盘加密、部署或模板部署启用 Key Vault（如上一步所示），则必须更新其高级访问策略。  

### <a name="azure-cli"></a>Azure CLI

使用 [az keyvault update](https://docs.azure.cn/cli/keyvault?view=azure-cli-latest#az-keyvault-update) 为 Key Vault 启用磁盘加密。 

- **为磁盘加密启用 Key Vault：** 需要使用 Enabled-for-disk-encryption。 

    ```azurecli
    az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
    ```  

- **根据需要为部署启用 Key Vault：** 在资源创建操作中引用此 Key Vault（例如，创建虚拟机）时，使 Microsoft.Compute 资源提供程序能够从此 Key Vault 中检索机密。

    ```azurecli
    az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
    ``` 

- **根据需要为模板部署启用 Key Vault：** 允许资源管理器从保管库中检索机密。
    ```azurecli  
    az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
    ```

### <a name="azure-powershell"></a>Azure PowerShell
使用 Key Vault PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 为 Key Vault 启用磁盘加密。

- **为磁盘加密启用 Key Vault：** 若要启用 Azure 磁盘加密，需要使用 EnabledForDiskEncryption。

    ```powershell 
    Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
    ```

- **根据需要为部署启用 Key Vault：** 在资源创建操作中引用此 Key Vault（例如，创建虚拟机）时，使 Microsoft.Compute 资源提供程序能够从此 Key Vault 中检索机密。

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
    ```

- **根据需要为模板部署启用 Key Vault：** 在模板部署中引用此 Key Vault 时，使 Azure 资源管理器能够从此 Key Vault 中获取机密。

    ```powershell             
    Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
    ```

### <a name="azure-portal"></a>Azure 门户

1. 选择 Key Vault，转到“访问策略”，然后选择“单击此处可显示高级访问策略”。  
2. 选中标有“启用对 Azure 磁盘加密的访问以进行卷加密”的框。 
3. 根据需要选择“启用对 Azure 虚拟机的访问以进行部署”和/或“启用对 Azure 资源管理器的访问以进行模板部署”。   
4. 单击“保存”  。

    ![Azure Key Vault 高级访问策略](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>设置密钥加密密钥 (KEK)

若要使用密钥加密密钥 (KEK) 来为加密密钥提供附加的安全层，请将 KEK 添加到 Key Vault。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。

可以使用 Azure CLI [az keyvault key create](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 命令、Azure PowerShell [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 或 [Azure 门户](https://portal.azure.cn/)生成新 KEK。 必须生成 RSA 密钥类型；Azure 磁盘加密尚不支持使用椭圆曲线密钥。

<!--Not Available on You can instead import a KEK from your on-premises key management HSM. For more information, see [Key Vault Documentation](/key-vault/key-vault-hsm-protected-keys)-->
<!--Not Available on [Key Vault Documentation](/key-vault/key-vault-hsm-protected-keys)-->

必须对 Key Vault KEK URL 进行版本控制。 Azure 会强制实施这项版本控制限制。 有关有效的机密和 KEK URL，请参阅以下示例：

* 有效机密 URL 的示例： *https://contosovault.vault.azure.cn/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 有效 KEK URL 的示例： *https://contosovault.vault.azure.cn/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure 磁盘加密不支持将端口号指定为 Key Vault 机密和 KEK URL 的一部分。 有关不支持和支持的 Key Vault URL 的示例，请参阅以下示例：

* 可接受的 Key Vault URL： *https://contosovault.vault.azure.cn/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 无法接受的 Key Vault URL： *https://contosovault.vault.azure.cn:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI [az keyvault key create](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 命令生成新 KEK 并将其存储在密钥保管库中。

```azurecli
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

<!--MOONCAKE: SHALL WE CHANGE --kty parameters to RSA-->

可以改用 Azure CLI [az keyvault key import](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 命令导入私钥：

在这两种情况下，都会向 Azure CLI [az vm encryption enable](https://docs.azure.cn/cli/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key 参数提供 KEK 的名称。 

```azurecli
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

### <a name="azure-powershell"></a>Azure PowerShell 

使用 Azure PowerShell [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) cmdlet 生成新 KEK 并将其存储在 Key Vault 中。

```powershell
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "Software"
```
<!--MOONCAKE: SHALL WE CHANGE Destination parameters with Software-->

可以改用 Azure PowerShell [az keyvault key import](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 命令导入私钥。

在这两种情况下，都会向 Azure PowerShell [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId 和 -KeyEncryptionKeyUrl 参数提供 KEK Key Vault 的 ID 和 KEK 的 URL。 请注意，此示例假定使用同一密钥保管库保存磁盘加密密钥和 KEK。

```powershell
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
```

