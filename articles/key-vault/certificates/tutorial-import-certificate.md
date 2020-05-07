---
title: 教程 - 使用 Azure 门户在 Key Vault 中导入证书 | Microsoft Docs
description: 一个介绍如何在 Azure Key Vault 中导入证书的教程
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
origin.date: 04/03/2020
ms.date: 04/20/2020
ms.author: v-tawe
ms.openlocfilehash: 17991c5b45329577423ff484885f7617b29074c4
ms.sourcegitcommit: 89ca2993f5978cd6dd67195db7c4bdd51a677371
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82588871"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>教程：在 Azure Key Vault 中导入证书

Azure Key Vault 是一项云服务，它为机密提供了安全的存储。 可以安全地存储密钥、密码、证书和其他机密。 可以通过 Azure 门户创建和管理 Azure Key Vault。 在本教程中，我们将创建一个密钥保管库并使用它来导入证书。 有关 Key Vault 的详细信息，请参阅[概述](../general/overview.md)。

本教程介绍如何：

> [!div class="checklist"]
> * 创建密钥保管库。
> * 使用门户在 Key Vault 中导入证书。
> * 使用 CLI 在 Key Vault 中导入证书。

在开始之前，请阅读 [Key Vault 的基本概念](../general/basic-concepts.md)。 

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.cn 登录到 Azure 门户。

## <a name="create-a-vault"></a>创建保管库

1. 在 Azure 门户菜单或“主页”中，选择“创建资源”   。
2. 在“搜索”框中输入“Key Vault”  。
3. 从结果列表中选择“Key Vault”  。
4. 在“Key Vault”部分，选择“创建”  。
5. 在“创建密钥保管库”部分，提供以下信息： 
    - **名称**：必须提供唯一的名称。 在本快速入门中，我们使用 Example-Vault  。 
    - **订阅**：选择订阅。
    - 在“资源组”  下选择“新建”  ，然后输入资源组名称。
    - 在“位置”下拉菜单中选择一个位置。 
    - 让其他选项保留默认值。
6. 提供上述信息后，选择“创建”  。

请记下下面列出的两个属性：

* **保管库名称**：在示例中，此项为 Example-Vault  。 将在其他步骤中使用此名称。
* **保管库 URI**：在本示例中，此项为 https://example-vault.vault.azure.cn/ 。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户有权对这个新保管库执行操作。

![Key Vault 创建完成后的输出](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>将证书导入到 Key Vault

若要将证书导入到保管库，需要将 PEM 或 PFX 证书文件存储在磁盘上。 在本例中，我们将导入文件名为 ExampleCertificate  的证书。

> [!IMPORTANT]
> 在 Azure Key Vault 中，支持的证书格式为 PFX 和 PEM。 
> - .pem 文件格式包含一个或多个 X509 证书文件。
> - .pfx 文件格式是一种存档文件格式，用于将多个加密对象存储在单个文件中，这些加密对象是：颁发给你的域的服务器证书、一个匹配的私钥，还可能包括一个中间 CA。  

1. 在 Key Vault 属性页中，选择“证书”  。
2. 单击“生成/导入”  。
3. 在“创建证书”  屏幕上，选择以下值：
    - **证书创建方法**：导入。
    - **证书名称**：ExampleCertificate。
    - **上传证书文件**：从磁盘选择证书文件
    - 让其他值保留默认设置。 单击**创建**。

![证书属性](../media/certificates/tutorial-import-cert/cert-import.png)

收到证书已成功创建的消息后，可以在列表中单击该证书。 然后，你可以看到它的一些属性。 

![证书属性](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>使用 Azure CLI 导入证书

将证书导入到指定的密钥保管库。 在将包含私钥的现有有效证书导入到 Azure Key Vault 中时，要导入的文件可以采用 PFX 格式或 PEM 格式。 如果证书采用 PEM 格式，则 PEM 文件必须包含密钥和 x509 证书。 此操作需要证书/导入权限。

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```
请在[此处](https://docs.azure.cn/cli/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)详细了解这些参数

## <a name="clean-up-resources"></a>清理资源

其他 Key Vault 快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组，可以将其删除，这将删除 Key Vault 和相关的资源。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”  。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除”   。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 Key Vault 并在其中导入了一个证书。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 参阅[使用 REST API 导入证书](https://docs.microsoft.com/rest/api/keyvault/importcertificate/importcertificate)的示例
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)