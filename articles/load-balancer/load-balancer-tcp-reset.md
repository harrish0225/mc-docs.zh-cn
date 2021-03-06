---
title: Azure 中的负载均衡器空闲情况下的 TCP 重置
titleSuffix: Azure Load Balancer
description: 本文介绍具有空闲超时情况下发送双向 TCP RST 数据包功能的负载均衡器。
services: load-balancer
documentationcenter: na
author: WenJason
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 05/03/2019
ms.date: 04/06/2020
ms.author: v-jay
ms.openlocfilehash: 7d70c4b4b37801c8e976e1f2389841cb44803e69
ms.sourcegitcommit: fe9ed98aaee287a21648f866bb77cb6888f75b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625684"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>在空闲时发送 TCP 重置的负载均衡器

可以使用[标准负载均衡器](load-balancer-standard-overview.md)，通过为给定规则启用“空闲时执行 TCP 重置”，为方案创建可预测度更高的应用程序行为。 负载均衡器的默认行为是当达到流的空闲超时的情况下，以静默方式删除流。  启用此功能将导致负载均衡器在空闲超时情况下发送双向 TCP 重置（TCP RST 包）。  这将通知应用程序终结点，连接已超时且不再可用。  终结点可以视需要立即建立新连接。

![负载均衡器 TCP 重置](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
你可以更改此默认行为，并启用根据入站 NAT 规则、负载均衡规则和[出站规则](/load-balancer/load-balancer-outbound-rules-overview)在空闲超时情况下发送 TCP 重置。  根据规则启用时，负载均衡器将在所有匹配流的空闲超时情况下向客户端和服务器终结点发送双向 TCP 重置（TCP RST 数据包）。

接收 TCP RST 数据包的终结点会立即关闭相应的套接字。 这会立即通知终结点该连接已释放，并且同一 TCP 连接上的任何后续通信都将失败。  应用程序可以在套接字关闭时清除连接，并根据需要重新建立连接，而无需等待 TCP 连接最终超时。

对于许多方案，这样可以减少发送 TCP（或应用层）保持连接以刷新流空闲超时的需要。 

如果空闲持续时间超过配置所允许的持续时间，或者你的应用程序显示启用了 TCP 重置的不良行为，则可能仍需要使用 TCP 保持连接（或应用层保持连接）来监视 TCP 连接的活跃性。  此外，当路径中某处的连接已经过代理时，保持连接（特别是应用层保持连接）也仍然有用。  

请仔细检查整个端到端方案，确定能否从启用 TCP 重置、调整空闲超时中受益，以及是否需要执行其他步骤来确保所需的应用程序行为。

## <a name="enabling-tcp-reset-on-idle-timeout"></a>启用空闲超时情况下的 TCP 重置

使用 API 版本 2018-07-01，可以在每个规则的基础上启用在空闲超时情况下发送双向 TCP 重置：

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>区域可用性

在所有区域中可用。

## <a name="limitations"></a>限制

- 只有在 TCP 连接的状态为“已建立”时才会发送 TCP RST。

## <a name="next-steps"></a>后续步骤

- 了解[标准负载均衡器](load-balancer-standard-overview.md)。
- 了解[出站规则](load-balancer-outbound-rules-overview.md)。
- [配置在空闲超时时 TCP RST](load-balancer-tcp-idle-timeout.md)
