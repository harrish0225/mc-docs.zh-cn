---
title: 序号预生成实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的序号预构建实体的信息。
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
origin.date: 10/14/2019
ms.date: 10/31/2019
ms.author: v-lingwu
ms.openlocfilehash: 6b40dcf601116ff3fe562c72cd9dd840a25a52cb
ms.sourcegitcommit: d202f6fe068455461c8756b50e52acd4caf2d095
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78154552"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的序号预生成实体
序号是一个集合内的对象的数字表示形式：`first`、`second`、`third`。 此实体已定型，因此不需要将包含序号的陈述示例添加到应用程序意向中。 [许多语言区域](luis-reference-prebuilt-entities.md)都支持序号实体。 

## <a name="types-of-ordinal"></a>序号类型
序号托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub 存储库中

## <a name="resolution-for-prebuilt-ordinal-entity"></a>预构建序号实体的解析

查询返回以下实体对象：

`Order the second option`

#### <a name="v3-response"></a>[V3 响应](#tab/V3)

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/V3-verbose)
以下 JSON 的 `verbose` 参数设置为 `true`：

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 响应](#tab/V2)

以下示例展示了 **builtin.ordinal** 实体的解析。

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解[序号 V2](luis-reference-prebuilt-ordinal-v2.md)、[电话号码](luis-reference-prebuilt-phonenumber.md)和[温度](luis-reference-prebuilt-temperature.md)实体。 
