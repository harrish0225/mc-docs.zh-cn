---
title: Azure CLI 脚本 - 创建 Azure Database for MySQL
description: 此示例 CLI 脚本创建 Azure Database for MySQL 服务器，并配置服务器级防火墙规则。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
origin.date: 02/28/2018
ms.date: 03/18/2019
ms.openlocfilehash: 656665368ccc9a7894b49facf6cd6b662c5dde56
ms.sourcegitcommit: c5646ca7d1b4b19c2cb9136ce8c887e7fcf3a990
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2019
ms.locfileid: "57990173"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>使用 Azure CLI 创建 MySQL 服务器并配置防火墙规则

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql-database-on-azure/)。

此示例 CLI 脚本创建 Azure Database for MySQL 服务器，并配置服务器级防火墙规则。 成功运行此脚本后，可以通过所有 Azure 服务和配置的 IP 地址访问 MySQL 服务器。 本文需要 Azure CLI 2.0 或更高版本。 通过运行 `az --version` 来查看版本。 请参阅[安装 Azure CLI]( /cli/install-azure-cli)，了解如何安装或升级 Azure CLI 的版本。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。

```cli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location chinaeast2

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location chinaeast2 \
--admin-user myadmin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresourcegroup \
--server mydemoserver \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 

```cli
#!/bin/bash
az group delete --name myresource
```

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mysql server create](/cli/mysql/server#az-mysql-server-create) | 创建用于托管数据库的 MySQL 服务器。 |
| [az mysql server firewall create](/cli/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) | 创建一个防火墙规则，以允许从输入的 IP 地址范围访问服务器及其下的所有数据库。 |
| [az group delete](/cli/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 阅读有关 Azure CLI 的更多信息：[Azure CLI 文档](/cli)。
- 请尝试其他脚本：[用于 MySQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
