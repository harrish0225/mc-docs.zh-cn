---
title: SSL 连接 - Azure Database for MySQL
description: 有关配置 Azure Database for MySQL 和关联应用程序以正确使用 SSL 连接的信息
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: conceptual
origin.date: 03/10/2020
ms.date: 03/30/2020
ms.openlocfilehash: a53655f10b57e743b9eef3118909a362fcf1b6bc
ms.sourcegitcommit: 303a16c7117b6f3495ef0493b4ae8ccb67d7dbba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80342344"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的 SSL 连接

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql-database-on-azure/)。

Azure Database for MySQL 支持使用安全套接字层 (SSL) 将数据库服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

## <a name="ssl-default-settings"></a>SSL 默认设置

默认情况下，应将数据库服务配置为需要 SSL 连接才可连接到 MySQL。  建议尽量不要禁用 SSL 选项。

通过 Azure 门户和 CLI 预配新的 Azure Database for MySQL 服务器时，默认情况下会强制实施 SSL 连接。 

Azure 门户中显示了各种编程语言的连接字符串。 这些连接字符串包含连接到数据库所需的 SSL 参数。 在 Azure 门户中，选择服务器。 在“设置”标题下，选择“连接字符串”   。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

若要了解如何在开发应用程序期间启用或禁用 SSL 连接，请参阅[如何配置 SSL](howto-configure-ssl.md)。

## <a name="tls-connectivity-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的 TLS 连接

对于使用传输层安全性 (TLS) 连接到数据库服务器的客户端，Azure Database for MySQL 支持加密。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现安全的网络连接，使你能够满足合规性要求。

### <a name="tls-settings"></a>TLS 设置

对于连接到 Azure Database for MySQL 的客户端，客户现在能够强制为其使用 TLS 版本。 若要使用 TLS 选项，请使用**最低 TLS 版本**选项设置。 此选项设置允许以下值：

|  最低 TLS 设置             | 支持的 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled（默认值） | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2 及更高版本 |
| TLS1_1                           | TLS 1.1、TLS 1.2 及更高版本          |
| TLS1_2                           | TLS 版本 1.2 及更高版本           |


例如，将此最低 TLS 设置版本设置为 TLS 1.0 意味着服务器将允许使用 TLS 1.0、1.1 和 1.2 + 的客户端进行连接。 或者，将此选项设置为 1.2 意味着仅允许使用 TLS 1.2 的客户端进行连接，并且将拒绝 TLS 1.0 和 TLS 1.1 的所有连接。

> [!Note] 
> Azure Database for MySQL 默认情况下为所有新服务器禁用 TLS。
>
> 目前，Azure Database for MySQL 支持的 TLS 版本为 TLS 1.0、1.1 和 1.2。

若要了解如何为 Azure Database for MySQL 设置 TLS 设置，请参阅 [如何配置 TLS 设置](howto-configure-ssl.md)。

## <a name="next-steps"></a>后续步骤

[Azure Database for MySQL 的连接库](concepts-connection-libraries.md)
