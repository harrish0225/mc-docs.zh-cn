---
title: 部署托管应用时使用 Key Vault
description: 演示在部署托管应用程序时如何使用 Azure Key Vault 中的访问机密
author: rockboyfor
ms.topic: conceptual
origin.date: 01/30/2019
ms.date: 01/20/2020
ms.author: v-yeche
ms.openlocfilehash: 7ef8850448f47b0f461cb1cdc021740b26e5cd3b
ms.sourcegitcommit: 8de025ca11b62e06ba3762b5d15cc577e0c0f15d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170632"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>部署 Azure 托管应用程序时访问 Key Vault 机密

在部署过程中，需要将安全值（例如密码）作为参数传递时，可从 [Azure Key Vault](../../key-vault/key-vault-overview.md) 检索值。 若要在部署托管应用程序时访问 Key Vault，必须授予对**设备资源提供程序**服务主体的访问权限。 托管应用程序服务使用此标识来运行操作。 若要在部署过程中从密钥保管库成功检索某个值，服务主体必须能够访问密钥保管库。

本文介绍如何配置 Key Vault 以与托管应用程序一起使用。

## <a name="enable-template-deployment"></a>启用模板部署

1. 在门户中，选择 Key Vault。

1. 选择“访问策略”。    

    ![选择访问策略](./media/key-vault-access/select-access-policies.png)

1. 选择“单击以显示高级访问策略”  。

    ![显示高级访问策略](./media/key-vault-access/advanced.png)

1. 选择“启用对 Azure 资源管理器的访问以进行模板部署”  。 Then, select <bpt id="p1">**</bpt>Save<ept id="p1">**</ept>.

    ![启用模板部署](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>将服务添加为参与者

1. 选择“访问控制(IAM)”  。

    ![选择访问控制](./media/key-vault-access/access-control.png)

1. 选择“添加角色分配”  。

    ![选择“添加”](./media/key-vault-access/add-access-control.png)

1. 对角色选择“参与者”  。 搜索“设备资源提供程序”  ，然后从可用选项中选择它。

    ![搜索提供程序](./media/key-vault-access/search-provider.png)

1. 选择“保存”  。

## <a name="reference-key-vault-secret"></a>引用 Key Vault 机密

若要将 Key Vault 中的机密传递给托管应用程序中的模板，必须使用[链接模板](../templates/linked-templates.md)并在链接模板的参数中引用 Key Vault。 提供 Key Vault 的资源 ID 和机密名称。

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>后续步骤

已将 Key Vault 配置为在部署托管应用程序期间可访问。

* 有关从 Key Vault 传递值作为模板参数的信息，请参阅[使用 Azure Key Vault 在部署期间传递安全参数值](../templates/key-vault-parameter.md)。
* 有关托管应用程序示例，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。
* 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。

<!-- Update_Description: new article about key vault access -->
<!--NEW.date: 01/20/2020-->