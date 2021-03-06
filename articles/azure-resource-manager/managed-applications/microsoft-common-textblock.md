---
title: TextBlock UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.TextBlock UI 元素。 用于向界面添加文本。
author: rockboyfor
ms.topic: conceptual
origin.date: 06/27/2018
ms.date: 01/20/2020
ms.author: v-yeche
ms.openlocfilehash: 8199fadee5b252fe8403c4c116352defa42fab5a
ms.sourcegitcommit: 8de025ca11b62e06ba3762b5d15cc577e0c0f15d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170665"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI 元素

可用于向门户界面添加文本的控件。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>架构

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>示例输出

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。

<!-- Update_Description: new article about microsoft common textblock -->
<!--NEW.date: 01/20/2020-->