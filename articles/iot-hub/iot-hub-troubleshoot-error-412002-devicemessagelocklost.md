---
title: 排查 Azure IoT 中心错误 412002 DeviceMessageLockLost
description: 了解如何修复错误 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
origin.date: 01/30/2020
ms.date: 02/17/2020
ms.author: v-yiso
ms.openlocfilehash: b0c4a3bc9275c4350c0eef876dde94fcec58bb14
ms.sourcegitcommit: 925c2a0f6c9193c67046b0e67628d15eec5205c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77068537"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

本文介绍 **412002 DeviceMessageLockLost** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

尝试发送云到设备的消息时，请求失败，并出现错误 **412002 DeviceMessageLockLost**。

## <a name="cause"></a>原因

当设备通过某种方式（例如，使用 [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)）从队列接收云到设备的消息时，IoT 中心会将该消息锁定，锁定超时持续时间为一分钟。 如果在锁定超时过期后设备尝试完成消息，则 IoT 中心会引发此异常。

## <a name="solution"></a>解决方案

如果 IoT 中心在一分钟的锁定超时期限内没有收到通知，则会将该消息设置回“已排队”  状态。 设备可以再次尝试接收消息。 若要防止将来发生错误，请在接收消息的一分钟内实现设备端逻辑来完成该消息。 无法更改这个一分钟的超时时间。