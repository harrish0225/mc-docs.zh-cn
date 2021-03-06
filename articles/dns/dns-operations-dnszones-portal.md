---
title: 在 Azure DNS 中管理 DNS 区域 - Azure 门户 | Microsoft Docs
description: 可以使用 Azure 门户管理 DNS 区域。 本文介绍如何在 Azure DNS 上更新、删除和创建 DNS 区域
services: dns
documentationcenter: na
author: WenJason
manager: digimobile
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 05/18/2017
ms.date: 02/17/2020
ms.author: v-jay
ms.openlocfilehash: a8ca95a89ac89e11c389b045f00e81995ade5c41
ms.sourcegitcommit: ada94ca4685855f58616e4bf1dd5ca757878dfdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77428750"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>如何在 Azure 门户中管理 DNS 区域

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure 经典 CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

本文介绍如何使用 Azure 门户管理 DNS 区域。 也可使用跨平台的 [Azure CLI](dns-operations-dnszones-cli.md) 或 Azure [PowerShell](dns-operations-dnszones.md) 管理 DNS 区域。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户
2. 在“中心”菜单上，导航至“创建资源”> 搜索“DNS 区域”  ，以打开“创建 DNS 区域”  边栏选项卡。

    ![DNS 区域](./media/dns-operations-dnszones-portal/openzone650.png)

4. 在“创建 DNS 区域”边栏选项卡上，输入以下值，并单击“创建”：  


   | **设置** | **值** | **详细信息** |
   |---|---|---|
   |**名称**|contoso.com|DNS 区域的名称|
   |**订阅**|[你的订阅]|选择要在其中创建 DNS 区域的订阅。|
   |**资源组**|**新建：** contosoDNSRG|创建资源组。 资源组名称在所选订阅中必须唯一。 若要详细了解资源组，请阅读 [Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 概述文章。|
   |**位置**|中国东部||

> [!NOTE]
> 资源组指的是资源组的位置，对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

## <a name="list-dns-zones"></a>列出 DNS 区域

在 Azure 门户中，导航到“更多服务”   > “网络”   > “DNS 区域”  。 每个 DNS 区域都是其自己的资源，可从该视图查看记录集的数目和名称服务器等信息。 列 NAME SERVERS  不在默认视图中。 要添加它，请单击“列”  ，选择“名称服务器”  ，然后单击“完成”  。

![列出 DNS 区域](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

在门户中导航到 DNS 区域。 在“DNS 区域”  边栏选项卡上，单击“删除区域”  。 然后系统会提示你确认是否需要删除 DNS 区域。 删除 DNS 区域还会删除该区域中包含的所有记录。

## <a name="next-steps"></a>后续步骤

访问[通过 Azure 门户开始使用 Azure DNS](dns-getstarted-portal.md)，可了解如何处理 DNS 区域和记录。