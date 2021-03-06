---
title: 在 Azure IoT Central 中配置规则和操作 | Microsoft Docs
description: 本操作指南文章介绍构建人员如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: vavilla
ms.author: v-yiso
origin.date: 11/11/2019
ms.date: 12/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1d137a0836d217b65154af8700f036aa65ea04dc
ms.sourcegitcommit: 6ffa4d50cee80c7c0944e215ca917a248f2a4bcd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74883069"
---
# <a name="configure-rules-preview-features"></a>配置规则（预览版功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*本文适用于操作员、构建者和管理员。*

IoT Central 中的规则充当一种可自定义的响应手段，它们是基于已连接设备发生的、有效监视到的事件触发的。 以下部分介绍如何评估规则。

## <a name="select-target-devices"></a>选择目标设备

使用“目标设备”部分选择要对哪种类型的设备应用此规则。 使用筛选器可以进一步具体化要包含的设备。 筛选器使用设备模板中的属性来筛选设备集。 筛选器本身不会触发操作。 在以下屏幕截图中，目标设备的设备模板类型为“冰箱”。  该筛选器指出，规则只能包含“制造地所在州”属性为“华盛顿”的“冰箱”。   

![Conditions](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>使用多个条件

条件是规则的触发依据。 目前，在将多个条件添加到规则时，这些条件将以逻辑 AND 运算符联接到一起。 换言之，必须满足所有条件才会将规则评估为 true。  

在以下屏幕截图中，条件将检查是否温度大于 90 且湿度小于 10。 如果这两个语句均为 true，则规则将评估为 true 并触发操作。

![Conditions](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>使用聚合开窗

规则将聚合时间窗口作为翻转窗口评估。 在以下屏幕截图中，时间窗口为 5 分钟。 每隔五分钟根据过去五分钟的数据评估规则。 数据只会在其对应的窗口中评估一次。

![翻转窗口](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>在 IoT Edge 模块中使用规则

应用到 IoT Edge 模块的规则存在一种限制。 基于来自不同模块的遥测数据的规则不会评估为有效规则。 下面是一个示例。 该规则的第一个条件是模块 A 的温度遥测数据。该规则的第二个条件是模块 B 的湿度遥测数据。由于这两个条件来自不同的模块，因此这是一组无效的条件。 该规则无效，尝试保存该规则时会引发错误。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中配置规则后，可以：

> [!div class="nextstepaction"]
> [即时分析数据](howto-create-analytics.md)
