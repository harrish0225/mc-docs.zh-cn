---
title: Azure Key Vault 的 Azure 事件网格事件架构
description: 介绍针对 Azure 事件网格中的 Azure Key Vault 事件提供的属性和架构
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
origin.date: 10/25/2019
ms.date: 01/20/2020
ms.author: v-yiso
ms.openlocfilehash: 5c62b29b3359c0df0ebd042d0c6b06fc120c2a74
ms.sourcegitcommit: 925c2a0f6c9193c67046b0e67628d15eec5205c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77068322"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault 的 Azure 事件网格事件架构（预览版）

本文提供了 [Azure Key Vault](/key-vault/)（当前为预览版）中事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="available-event-types"></a>可用事件类型

Azure Key Vault 帐户生成以下事件类型：

| 事件全名 | 事件显示名称 | 说明 |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | 创建的证书新版本 | 创建新证书或新证书版本时触发。 |
| Microsoft.KeyVault.CertificateNearExpiry | 证书即将过期 | 当前版本的证书即将过期时触发。 （默认值为到期日期前的 30 天。） |
| Microsoft.KeyVault.CertificateExpired | 证书已过期 | 证书过期时触发。 |
| Microsoft.KeyVault.KeyNewVersionCreated | 创建的密钥新版本 | 创建新密钥或新密钥版本时触发。 |
| Microsoft.KeyVault.KeyNearExpiry | 密钥即将过期 | 当前版本的密钥即将过期时触发。 （默认值为到期日期前的 30 天。） |
| Microsoft.KeyVault.KeyExpired | 密钥已过期 | 密钥过期时触发。 |
| Microsoft.KeyVault.SecretNewVersionCreated | 创建的机密新版本 | 创建新机密或新机密版本时触发。 |
| Microsoft.KeyVault.SecretNearExpiry | 机密即将过期 | 当前版本的机密即将过期时触发。 （默认值为到期日期前的 30 天。） |
| Microsoft.KeyVault.SecretExpired | 机密已过期 | 机密过期时触发。 |

## <a name="event-examples"></a>事件示例

以下示例显示 **Microsoft.KeyVault.SecretNewVersionCreated** 的架构：

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| ---------- | ----------- |---|
| id | string | 触发了此事件的对象的 ID |
| vaultName | string | 触发了此事件的对象的密钥保管库名称 |
| objectType | string | 触发了此事件的对象的类型 |
| objectName | string | 触发了此事件的对象的名称 |
| 版本 | string | 触发了此事件的对象的版本 |
| nbf | number | 触发了此事件的对象的 not-before 日期（自 1970-01-01T00:00:00Z 以来的秒数） |
| exp | number | 触发了此事件的对象的到期日期（自 1970-01-01T00:00:00Z 以来的秒数） |


## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)。
* 有关如何创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 若要获取有关 Key Vault 和 Azure 自动化的其他指南，请参阅：
    - [什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md)
    - [Azure 自动化概述](/automation/)
