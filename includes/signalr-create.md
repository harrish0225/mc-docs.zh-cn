---
title: include 文件
description: include 文件
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
origin.date: 04/17/2018
ms.date: 02/17/2019
ms.author: v-tawe
ms.custom: include file
ms.openlocfilehash: 680ceec5324161cf69c0c3a4e6fa11f2f50e1872
ms.sourcegitcommit: 0b07f1d36ac02da055874630d6edc31cb0a15269
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112290"
---
1. 若要创建 Azure SignalR 服务资源，请先登录到 [Azure 门户](https://portal.azure.cn)。 在页面的左上角，选择“+ 创建资源”  。 在“搜索市场”文本框中，输入“SignalR 服务”。  

2. 在结果中选择“SignalR 服务”，然后选择“创建”。  

3. 在新的“SignalR”  设置页上，为新的 SignalR 资源添加以下设置：

    | 名称 | 建议的值 | 说明 |
    | ---- | ----------------- | ----------- |
    | 资源名称 | *testsignalr* | 输入用于 SignalR 资源的唯一资源名称。 该名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母和连字符 (`-`) 字符。 该名称的开头或末尾不能是连字符字符，并且连续的连字符字符无效。|
    | 订阅 | 选择自己的订阅 |  选择要用来测试 SignalR 的 Azure 订阅。 如果帐户只有一个订阅，则会自动选择该订阅并且不显示“订阅”下拉菜单  。|
    | 资源组 | 创建一个名为 *SignalRTestResources* 的资源组| 为 SignalR 资源选择或创建资源组。 此组可用于组织多个资源，删除该资源组可以同时删除这些资源。 有关详细信息，请参阅 [Using resource groups to manage your Azure resources](../articles/azure-resource-manager/management/overview.md)（使用资源组管理 Azure 资源）。 |
    | 位置 | *中国东部* | 使用“位置”指定在其中托管 SignalR 资源的地理位置  。 为获得最佳性能，我们建议在应用程序的其他组件所在的同一区域创建资源。 |
    | 定价层 | *免费* | 目前可以使用“免费”和“标准”选项。   |
    | 固定到仪表板 | ✔ | 选中此框可将资源固定到仪表板，以方便查找。 |

4. 选择“创建”  。 部署可能需要几分钟时间才能完成。

5. 部署完成后，在“设置”下选择“密钥”。   复制主密钥的连接字符串。 稍后要使用此字符串将应用配置为使用 Azure SignalR 服务资源。

    该连接字符串采用以下格式：
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
