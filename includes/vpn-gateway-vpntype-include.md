---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: b9fb13413bd3b48b4005d31de1c6423f7ae7d6ea
ms.sourcegitcommit: 0a5a7daaf864ef787197f2b8e62539786b6835b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2018
ms.locfileid: "53711690"
---
* **PolicyBased：** PolicyBased VPN 以前在经典部署模型中称为“静态路由网关”。 基于策略的 VPN 会根据使用本地网络和 Azure VNet 之间的地址前缀的各种组合配置的 IPsec 策略，加密数据包并引导其通过 IPsec 隧道。 通常会在 VPN 设备配置中将策略（或流量选择器）定义为访问列表。 基于策略的 VPN 类型的值为 *PolicyBased*。 使用 PolicyBased VPN 时，请记住下列限制：
  
  * PolicyBased VPN **仅** 可在基本网关 SKU 上使用。 此 VPN 类型与其他网关 SKU 不兼容。
  * 如果使用 PolicyBased VPN，可以只有 1 个隧道。
  * 只能将 PolicyBased VPN 用于 S2S 连接且只能用于特定配置。 大多数 VPN 网关配置需要 RouteBased VPN。
* **RouteBased**：RouteBased VPN 以前在经典部署模型中称为“动态路由网关”。 RouteBased VPN 使用 IP 转发或路由表中的“路由”将数据包引导到相应的隧道接口中。 然后，隧道接口会加密或解密出入隧道的数据包。 RouteBased VPN 的策略（或流量选择器）配置为任意到任意（或通配符）。 基于路由的 VPN 类型的值为 *RouteBased*。