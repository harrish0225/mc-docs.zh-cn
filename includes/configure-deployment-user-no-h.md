---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
origin.date: 06/14/2019
ms.date: 08/28/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: a2e8ead77aefb269373a9cc6a82b592264201fb1
ms.sourcegitcommit: 13642a99cc524a416b40635f48676bbf5cdcdf3d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104224"
---
可以使用“部署用户”  将 FTP 和本地 Git 部署到 Azure Web 应用。 配置部署用户之后，可对所有 Azure 部署使用此用户。 帐户级部署用户名和密码不同于 Azure 订阅凭据。 

若要配置部署用户，请在 Azure Cloud Shell 中运行 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 命令。 将 \<username> 和 \<password> 替换为部署用户名和密码。 

- 用户名必须在 Azure 中唯一，并且对于本地 Git 推送，不能包含“@”符号。 
- 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 输出会将该密码显示为 `null`。 如果收到 `'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 `'Bad Request'. Details: 400` 错误，请使用更强的密码。 

请记录你要用于部署 Web 应用的用户名和密码。
