---
title: "Azure CLI 脚本示例 - 从 GitHub 使用部署创建 Web 应用 | Azure"
description: "Azure CLI 脚本示例 - 从 GitHub 使用部署创建 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
wacn.date: 
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a114d832e9c5320e9a109c9020fcaa2f2fdd43a9
ms.openlocfilehash: bec7ba3072a13e34cef3a4486e0bc8f9be379fc8
ms.lasthandoff: 04/14/2017

---

# <a name="create-a-web-app-with-deployment-from-github"></a>从 GitHub 使用部署创建 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后从公共 GitHub 存储库部署 Web 应用代码（不进行连续部署）。 有关不进行连续部署的 GitHub 部署，请参阅[从 GitHub 使用连续部署创建 Web 应用](../app-service-continuous-deployment.md#overview)。

必要时，请使用 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的说明安装 Azure CLI，然后运行 `az login` 创建与 Azure 的连接。 此外，还需要指向含 web 应用代码的 GitHub 存储库的链接。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../../virtual-machines/virtual-machines-windows-cli-options.md)。

## <a name="create-app-sample"></a>创建应用示例

```azurecli
#!/bin/bash

gitrepo=<Replace with a public GitHub repo URL. e.g. https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git>
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location chinanorth --name myResourceGroup

# Create an App Service plan in `FREE` tier.
az appservice plan create --name $webappname --resource-group myResourceGroup --sku FREE

# Create a web app.
az appservice web create --name $webappname --resource-group myResourceGroup --plan $webappname

# Deploy code from a public GitHub repository. 
az appservice web source-control config --name $webappname --resource-group myResourceGroup \
--repo-url $gitrepo --branch master --manual-integration

# Browse to the web app.
az appservice web browse --name $webappname --resource-group myResourceGroup
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建应用服务计划。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | 创建 Azure Web 应用。 |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | 将 Azure Web 应用与 Git 或 Mercurial 存储库相关联。 |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | 在浏览器中打开 Azure Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。