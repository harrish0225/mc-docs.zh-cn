---
title: 将本地网络连接到 Azure 虚拟网络：站点到站点 VPN：门户
description: 通过公共 Internet 创建从本地网络到 Azure 虚拟网络的 IPsec 连接的步骤。 这些步骤将帮助你使用门户创建跨界站点到站点 VPN 网关连接。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 03/03/2020
ms.date: 04/06/2020
ms.author: v-jay
ms.openlocfilehash: 4340705d44efa1413b8c6070ece091ad247efefb
ms.sourcegitcommit: 5fb45da006859215edc8211481f13174aa43dbeb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634607"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>在 Azure 门户中创建站点到站点连接

本文介绍如何使用 Azure 门户创建站点到站点 VPN 网关连接，以便从本地网络连接到 VNet。 本文中的步骤适用于 Resource Manager 部署模型。 也可使用不同的部署工具或部署模型创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure 门户（经典）](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

![站点到站点 VPN 网关跨界连接示意图](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>准备阶段

在开始配置之前，请验证是否符合以下条件：

* 确保有一台兼容的 VPN 设备和能够对其进行配置的人员。 有关兼容的 VPN 设备和设备配置的详细信息，请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。
* 确认 VPN 设备有一个面向外部的公共 IPv4 地址。
* 如果熟悉本地网络配置中的 IP 地址范围，则需咨询能够提供此类详细信息的人员。 创建此配置时，必须指定 IP 地址范围前缀，Azure 会将该前缀路由到本地位置。 本地网络的任何子网都不得与要连接到的虚拟网络子网重叠。 

### <a name="example-values"></a><a name="values"></a>示例值

本文中的示例使用以下值。 可使用这些值创建测试环境，或参考这些值以更好地理解本文中的示例。 有关通用 VPN 网关设置的详细信息，请参阅[关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md)。

* **虚拟网络名称：** VNet1
* **地址空间：** 10.1.0.0/16
* **订阅：** 要使用的订阅
* **资源组：** TestRG1
* **区域：** 中国北部
* **子网：** FrontEnd：10.1.0.0/24，BackEnd：10.1.1.0/24（可选，适用于本练习）
* **网关子网地址范围：** 10.1.255.0/27
* **虚拟网络网关名称：** VNet1GW
* **公共 IP 地址名称：** VNet1GWpip
* **VPN 类型：** 基于路由
* **连接类型：** 站点到站点 (IPsec)
* **网关类型：** VPN
* **本地网络网关名称：** Site1
* **连接名称：** VNet1toSite1
* **共享机密：** 本示例使用“abc123”。 但是，你可以使用与 VPN 硬件兼容的任何密钥。 重要的是连接两端的值要匹配。

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1.创建虚拟网络

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-the-vpn-gateway"></a><a name="VNetGateway"></a>2.创建 VPN 网关

在此步骤中为 VNet 创建虚拟网络网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选网关 SKU。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="example-settings"></a>示例设置

* **实例详细信息 > 区域：** 中国北部
* **虚拟网络 > 虚拟网络：** VNet1
* **实例详细信息 > 名称：** VNet1GW
* **实例详细信息 > 网关类型：** VPN
* **实例详细信息 > VPN 类型：** 基于路由
* **虚拟网络 > 网关子网地址范围：** 10.1.255.0/27
* **公共 IP 地址 > 公共 IP 地址名称：** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]


## <a name="3-create-the-local-network-gateway"></a><a name="LocalNetworkGateway"></a>3.创建本地网关

本地网络网关通常是指本地位置。 可以为站点提供一个名称供 Azure 引用，并指定本地 VPN 设备的 IP 地址，以便创建一个连接来连接到该设备。 此外还可指定 IP 地址前缀，以便通过 VPN 网关将其路由到 VPN 设备。 指定的地址前缀是位于本地网络的前缀。 如果之后本地网络发生了更改，或需要更改 VPN 设备的公共 IP 地址，可轻松更新这些值。

**示例值**

* **名称：** Site1
* **资源组：** TestRG1
* **位置：** 中国北部


[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="4-configure-your-vpn-device"></a><a name="VPNDevice"></a>4.配置 VPN 设备

通过站点到站点连接连接到本地网络需要 VPN 设备。 在此步骤中，请配置 VPN 设备。 配置 VPN 设备时，需要以下项：

- 共享密钥。 此共享密钥就是在创建站点到站点 VPN 连接时指定的共享密钥。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的密钥来使用。
- 虚拟网关的“公共 IP 地址”。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。 若要使用 Azure 门户查找 VPN 网关的公共 IP 地址，请导航到“虚拟网关”，然后单击网关的名称。 

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="5-create-the-vpn-connection"></a><a name="CreateConnection"></a>5.创建 VPN 连接

在虚拟网关和本地 VPN 设备之间创建站点到站点 VPN 连接。

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="6-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>6.验证 VPN 连接

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>连接到虚拟机

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>如何重置 VPN 网关

如果丢失一个或多个站点到站点隧道上的跨界 VPN 连接，重置 Azure VPN 网关可有效解决该情况。 在此情况下，本地 VPN 设备都在正常工作，但却无法与 Azure VPN 网关建立 IPsec 隧道。 有关步骤，请参阅[重置 VPN 网关](vpn-gateway-resetgw-classic.md)。

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>如何更改网关 SKU（重设网关大小）

有关更改网关 SKU 的步骤，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>如何将其他连接添加到 VPN 网关

可以添加其他连接，前提是连接之间不存在地址空间重叠。

1. 若要添加其他连接，请导航到 VPN 网关，然后单击“连接”  打开“连接”页。
2. 单击“+添加”  添加连接。 调整连接类型以反映“VNet 到 VNet”（如果连接到另一个 VPN 网关）或“站点到站点”。
3. 如果要使用“站点到站点”连接进行连接，并且尚未为要连接到的站点创建本地网络网关，则可以创建一个新的本地网络网关。
4. 指定要使用的共享密钥，然后单击“确定”  以创建连接。

## <a name="next-steps"></a>后续步骤

* 有关 BGP 的信息，请参阅 [BGP 概述](vpn-gateway-bgp-overview.md)和[如何配置 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
* 有关强制隧道的信息，请参阅[关于强制隧道](vpn-gateway-forced-tunneling-rm.md)。
* 有关高可用性主动-主动连接的信息，请参阅[高可用性跨界连接与 VNet 到 VNet 连接](vpn-gateway-highlyavailable.md)。
* 有关如何限制发往虚拟网络中资源的网络流量的信息，请参阅[网络安全](../virtual-network/security-overview.md)。
* 有关 Azure 如何在 Azure 资源、本地资源和 Internet 资源之间路由流量的信息，请参阅[虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)。
* 有关使用 Azure 资源管理器模板创建站点到站点 VPN 连接的信息，请参阅[创建站点到站点 VPN 连接](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/)。
* 有关使用 Azure 资源管理器模板创建 Vnet 到 Vnet VPN 连接的信息，请参阅[部署 HBase 异地复制](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)。
