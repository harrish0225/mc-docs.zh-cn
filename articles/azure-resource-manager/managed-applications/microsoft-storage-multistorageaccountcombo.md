---
title: MultiStorageAccountCombo UI 元素
description: 介绍了 Azure 门户的 Microsoft.Storage.MultiStorageAccountCombo UI 元素。
author: rockboyfor
ms.topic: conceptual
origin.date: 06/28/2018
ms.date: 01/20/2020
ms.author: v-yeche
ms.openlocfilehash: 4eec0becdc8a66fe08356a508abe70fca2eabc0e
ms.sourcegitcommit: 8de025ca11b62e06ba3762b5d15cc577e0c0f15d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170782"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI 元素

一组用于创建多个存储帐户的控件，名称以某个公用前缀开头。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>架构

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>备注

- 会将 `defaultValue.prefix` 的值与一个或多个整数相连接来生成存储帐户名称的序列。 例如，如果 `defaultValue.prefix` 为 **sa** 并且 `count` 为 **2**，则会生成存储帐户名称 **sa1** 和 **sa2**。 将自动验证生成的存储帐户名称的唯一性。
- 存储帐户名称将基于 `count` 按字典顺序生成。 例如，如果 `count` 为 10，则存储帐户名称以两位整数（01、02、03）结尾。
- `defaultValue.prefix` 的默认值为 **null**，`defaultValue.type` 的默认值为 **Premium_LRS**。
- 将隐藏未在 `constraints.allowedTypes` 中指定的任何类型，会显示未在 `constraints.excludedTypes` 中指定的任何类型。 `constraints.allowedTypes` 和 `constraints.excludedTypes` 都是可选的，并且不能同时使用。
- 除了生成存储帐户名称之外，`count` 还用于为元素设置合适的乘数。 它支持静态值（例如 **2**），也支持来自其他元素的动态值（例如 `[steps('step1').storageAccountCount]`）。 默认值是 **1**秒。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。

<!-- Update_Description: new article about microsoft storage multistorageaccountcombo -->
<!--NEW.date: 01/20/2020-->