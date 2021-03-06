---
title: 在容器组中启用 SSL
description: 为 Azure 容器实例中运行的容器组创建 SSL 或 TLS 终结点
ms.topic: article
origin.date: 04/03/2019
ms.date: 01/15/2020
ms.author: v-yeche
ms.openlocfilehash: db14ca87dbf1df8d213115cfb65efad5a3c13456
ms.sourcegitcommit: 2b4507745b98b45f1ce3f3d30f397521148ef35a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78213758"
---
<!--Verified successfully-->
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>在容器组中启用 SSL 终结点

本文介绍如何使用一个应用程序容器以及一个运行 SSL 提供程序的挎斗容器创建[容器组](container-instances-container-groups.md)。 使用单独的 SSL 终结点设置容器组，可为应用程序启用 SSL 连接，而无需更改应用程序代码。

设置包含两个容器的容器组：
* 一个运行简单 Web 应用且使用公共 Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 映像的应用程序容器。 

    <!--CORRECT ON Microsoft [aci-helloworld]-->
    
* 一个运行公共 [Nginx](https://hub.docker.com/_/nginx) 映像且配置为使用 SSL 的挎斗容器。 

在此示例中，容器组仅使用其公共 IP 地址公开 Nginx 的端口 443。 Nginx 将 HTTPS 请求路由到伴侣 Web 应用，后者在内部侦听端口 80。 可以改编侦听其他端口的容器应用示例。

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

可以使用本地安装的 Azure CLI 来完成本文。 如果想要在本地使用它，建议使用 2.0.55 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/cli/install-azure-cli?view=azure-cli-latest)。

<!--Not Available on Azure Cloud Shell or-->

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

若要将 Nginx 设置为 SSL 提供程序，需要一个 SSL 证书。 本文介绍如何创建和设置自签名的 SSL 证书。 对于生产方案，应从证书颁发机构获取证书。

若要创建自签名的 SSL 证书，请使用 Azure 本地 Shell 以及许多 Linux 分发版中提供的 [OpenSSL](https://www.openssl.org/) 工具，或使用操作系统中的类似客户端工具。

首先在本地工作目录中创建证书请求（.csr 文件）：

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

按提示添加标识信息。 对于“公用名”，请输入与证书关联的主机名。 当系统提示输入密码时，请直接按 Enter 而不要键入任何内容，以跳过添加密码的步骤。

运行以下命令，基于证书请求创建自签名证书（.crt 文件）。 例如：

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

现在，该目录中应会出现三个文件：证书请求 (`ssl.csr`)、私钥 (`ssl.key`) 和自签名证书 (`ssl.crt`)。 在后续步骤中将使用 `ssl.key` 和 `ssl.crt`。

## <a name="configure-nginx-to-use-ssl"></a>将 Nginx 配置为使用 SSL

### <a name="create-nginx-configuration-file"></a>创建 Nginx 配置文件

在本部分，你将为 Nginx 创建配置文件以使用 SSL。 首先将以下文本复制到名为 `nginx.conf` 的新文件中。 在 Azure 本地 Shell 中，可以使用 Visual Studio Code 在工作目录中创建该文件：

```console
code nginx.conf
```

在 `location` 中，请务必将 `proxy_pass` 设置为应用的正确端口。 本示例为 `aci-helloworld` 容器设置了端口 80。

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;

        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80

            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>对机密和配置文件进行 Base64 编码

对 Nginx 配置文件、SSL 证书和 SSL 密钥进行 Base64 编码。 在下一部分，你将在用于部署容器组的 YAML 文件中输入编码的内容。

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>部署容器组

现在，通过在 [YAML 文件](container-instances-multi-container-yaml.md)中指定容器配置来部署容器组。

### <a name="create-yaml-file"></a>创建 YAML 文件

将以下 YAML 复制到名为 `deploy-aci.yaml` 的新文件中。 在 Azure 本地 Shell 中，可以使用 Visual Studio Code 在工作目录中创建该文件：

```console
code deploy-aci.yaml
```

输入 base64 编码文件的内容（在 `secret` 下指示）。 例如，对每个 base64 编码的文件运行 `cat` 以查看其内容。 在部署期间，这些文件将添加到容器组中的[机密卷](container-instances-volume-secret.md)。 在本示例中，机密卷已装载到 Nginx 容器。

```YAML
api-version: 2018-10-01
location: chinanorth2
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>部署容器组

使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) 命令创建资源组：

```azurecli
az group create --name myResourceGroup --location chinaeast2
```

使用 [az container create](https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create) 命令部署容器组并传递 YAML 文件作为参数。

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>查看部署状态

若要查看部署状态，请运行以下 [az container show](https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-show) 命令：

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

如果部署成功，输出将如下所示：

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     chinanorth2
```

## <a name="verify-ssl-connection"></a>验证 SSL 连接

若要查看正在运行的应用程序，请在浏览器中转到它的 IP 地址。 例如，本示例中显示的 IP 地址是 `52.157.22.76`。 由于 Nginx 服务器配置的原因，必须使用 `https://<IP-ADDRESS>` 查看正在运行的应用程序。 尝试连接 `http://<IP-ADDRESS>` 失败。

![浏览器屏幕截图，显示应用程序在 Azure 容器实例中运行](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 由于本示例使用自签名证书而不是证书颁发机构提供的证书，因此，在通过 HTTPS 连接站点时，浏览器会显示安全警告。 这是预期的行为。
>

## <a name="next-steps"></a>后续步骤

本文介绍了如何设置 Nginx 容器，以便与容器组中运行的 Web 应用建立 SSL 连接。 对于侦听端口 80 以外的端口的应用，可以改编此示例。 还可以更新 Nginx 配置文件来自动重定向端口 80 (HTTP) 上的服务器连接，以使用 HTTPS。

尽管本文在挎斗中使用 Nginx，但你可以使用另一个 SSL 提供程序，例如 [Caddy](https://caddyserver.com/)。

<!--Not Available on [Azure virtual network](container-instances-vnet.md)-->

<!-- Update_Description: new article about container instances container group ssl -->
<!--NEW.date: 01/15/2020-->