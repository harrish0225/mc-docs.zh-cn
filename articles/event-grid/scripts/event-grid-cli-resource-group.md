---
title: Azure CLI 脚本示例 - 订阅资源组 | Microsoft Docs
description: 本文提供了一个示例 Azure CLI 脚本，演示如何订阅资源组的 Azure 事件网格事件。
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 01/23/2020
ms.date: 3/16/2020
ms.author: v-lingwu
ms.openlocfilehash: 9753ec8bb57f56d87a4cff9bad3663a80a0d6084
ms.sourcegitcommit: 7995ca87e9e10388948f714f94c61d66880f3bb3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2020
ms.locfileid: "79452483"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>使用 Azure CLI 订阅资源组的事件

此脚本创建资源组事件的事件网格订阅。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

预览示例脚本需要事件网格扩展。 若要安装，请运行 `az extension add --name eventgrid`。

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

<!--[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]-->

## <a name="sample-script---preview-extension"></a>示例脚本 - 预览扩展

<!--[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]-->

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az eventgrid event-subscription create](/cli/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | 创建事件网格订阅。 |
| [az eventgrid event-subscription create](/cli/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - 扩展版本 | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关查询订阅的信息，请参阅[查询事件网格订阅](../query-event-subscriptions.md)。
* 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。
