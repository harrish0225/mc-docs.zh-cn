---
title: Azure CLI 脚本 - 还原 Azure Database for MariaDB 服务器
description: 此示例 Azure CLI 脚本演示如何将 Azure Database for MariaDB 服务器及其数据库还原到前一个时间点。
author: WenJason
ms.author: v-jay
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
origin.date: 11/28/2018
ms.date: 05/27/2019
ms.openlocfilehash: ad92ff52cde7e3b4c6204e8d3b61e2966615a634
ms.sourcegitcommit: 193f49f19c361ac6f49c59045c34da5797ed60ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68732373"
---
# <a name="restore-an-azure-database-for-mariadb-server-using-azure-cli"></a>使用 Azure CLI 还原 Azure Database for MariaDB 服务器
此示例 CLI 脚本可将单个 Azure Database for MariaDB 服务器还原到前一个时间点。

本文需要 Azure CLI 2.0 或更高版本。 通过运行 `az --version` 来查看版本。 请参阅[安装 Azure CLI]( /cli/install-azure-cli)，了解如何安装或升级 Azure CLI 的版本。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。 将 `az monitor` 命令中使用的订阅 ID 替换为自己的订阅 ID。

```azurecli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location chinaeast2

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location chinaeast2 \
--admin-user myadmin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2 \

# Restore a server from backup to a new server
az mariadb server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2018-02-11T13:10:00Z" \
--source-server mydemoserver
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 

```azurecli
#!/bin/bash
az group delete --name myresourcegroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mariadb server create](/cli/mariadb/server#az-mariadb-server-create) | 创建用于托管数据库的 MariaDB 服务器。 |
| [az mariadb server restore](/cli/mariadb/server#az-mariadb-server-restore) | 从备份还原服务器。 |
| [az group delete](/cli/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 阅读有关 Azure CLI 的更多信息：[Azure CLI 文档](/cli/)。
- 请尝试其他脚本：[Azure Database for MariaDB 的 Azure CLI 示例](../sample-scripts-azure-cli.md)
