---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
origin.date: 01/31/2020
ms.date: 02/17/2020
ms.author: v-tawe
ms.openlocfilehash: 239f11ad3ca2aff3d743d770e22e4ba6c6ce48aa
ms.sourcegitcommit: ada94ca4685855f58616e4bf1dd5ca757878dfdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430036"
---
在本快速入门中，你将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 将文本转换为合成语音。 在[文本转语音语言支持](../../../language-support.md#text-to-speech)下，文本转语音服务为合成语音提供了多种选项。 满足几个先决条件后，将合成语音呈现到默认扬声器只需四个步骤：
> [!div class="checklist"]
> * 通过语音主机和订阅密钥创建 `SpeechConfig` 对象。
> * 使用以上的 `SpeechConfig` 对象创建 `SpeechSynthesizer` 对象。
> * 使用 `SpeechSynthesizer` 对象来朗读文本。
> * 检查返回的 `SpeechSynthesisResult` 中是否有错误。
