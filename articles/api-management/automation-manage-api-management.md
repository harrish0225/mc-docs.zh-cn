---
title: 使用 Azure 自动化管理 Azure API 管理
description: 了解如何使用 Azure 自动化服务管理 Azure API 管理。
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/13/2018
ms.author: v-yiso
ms.date: 03/11/2019
ms.openlocfilehash: 6cdfa953242643c6d233d5d3b5f02a52f7d16727
ms.sourcegitcommit: 1224987f3ad1179177c72dfcbb0a30edf8871974
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57196618"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>使用 Azure 自动化管理 Azure API 管理
本指南介绍 Azure 自动化服务，以及如何使用它来简化 Azure API 管理的管理。

## <a name="what-is-azure-automation"></a>什么是 Azure 自动化？
[Azure 自动化](/automation/)是用于通过流程自动化简化云管理的一项 Azure 服务。 使用 Azure 自动化可以自动完成那些人工操作、重复、长时间运行且易出错的任务，从而改善组织的可靠性、效率和价值生成时间。

Azure 自动化提供了具有高可靠性和高可用性的工作流执行引擎，该引擎可以根据需求进行缩放。 在 Azure 自动化中，流程可以手动、通过第三方系统或按计划的间隔启动，使任务能够完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT 和开发运营人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Azure 自动化如何帮助管理 Azure API Management？
可使用[适用于 Azure API 管理 API 的 Windows PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.apimanagement) 在 Azure 自动化中管理 API 管理。 在 Azure 自动化中，可以编写 PowerShell 工作流脚本来使用 cmdlet 执行多种 API 管理任务。 还可以将 Azure 自动化中的这些 cmdlet 与其他 Azure 服务的 cmdlet 搭配使用，以自动完成跨 Azure 服务和第三方系统的复杂任务。

以下是利用 Powershell 使用 API 管理的一些示例：

* [适用于 API 管理的 Azure PowerShell 示例](/api-management/powershell-samples)

## <a name="next-steps"></a>后续步骤
在了解 Azure 自动化以及如何使用它来管理 Azure API Management 的基础知识后，请使用以下链接了解更多信息。

* 请参阅 Azure 自动化[入门教程](../automation/automation-first-runbook-graphical.md)。

