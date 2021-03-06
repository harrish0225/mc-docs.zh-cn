---
title: 排查常见问题
description: 了解如何排查部署、运行或管理 Azure 容器实例时的常见问题
ms.topic: article
origin.date: 09/25/2019
ms.date: 01/15/2020
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 360a5f79924f97fab8cd72f38fec87c2b55b47d5
ms.sourcegitcommit: 2b4507745b98b45f1ce3f3d30f397521148ef35a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78213743"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>排查 Azure 容器实例中的常见问题

本文展示了如何排查管理容器或向 Azure 容器实例部署容器时出现的常见问题。 另请参阅[常见问题解答](container-instances-faq.md)。

如果需要更多支持，请参阅 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)中可用的  “帮助 + 支持”选项。

<!--CORRECT ON (https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)-->

## <a name="issues-during-container-group-deployment"></a>容器组部署过程中的问题
### <a name="naming-conventions"></a>命名约定

定义容器规格时，某些参数需要遵循命名限制。 下表包含容器组属性的特定要求。

<!--Not Available on  [Naming conventions][azure-name-restrictions]-->

| 作用域 | Length | 大小写 | 有效的字符 | 建议的模式 | 示例 |
| --- | --- | --- | --- | --- | --- |
| 容器组名称 | 1-64 |不区分大小写 |第一个或最后一个字符不能为字母数字和连字符 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 容器名称 | 1-64 |不区分大小写 |第一个或最后一个字符不能为字母数字和连字符 |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| 容器端口 | 介于 1 和 65535 之间 |Integer |一个介于 1 和 65535 之间的整数 |`<port-number>` |`443` |
| DNS 名称标签 | 5-63 |不区分大小写 |第一个或最后一个字符不能为字母数字和连字符 |`<name>` |`frontend-site1` |
| 环境变量 | 1-63 |不区分大小写 |第一个或最后一个字符不能为字母数字和下划线 (_) |`<name>` |`MY_VARIABLE` |
| 卷名 | 5-63 |不区分大小写 |第一个或最后一个字符不能为小写字母、数字和连字符。 不能包含两个连续的连字符。 |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>不受支持的映像的操作系统版本

如果指定了 Azure 容器实例不支持的映像，则将返回 `OsVersionNotSupported` 错误。 该错误类似于以下内容，其中 `{0}` 是你尝试部署的映像的名称：

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

<!--Not Avaialble on This error is most often encountered when deploying Windows images that are based on Semi-Annual Channel release 1709 or 1803, which are not supported. For supported Windows images in Azure Container Instances, see [Frequently asked questions](container-instances-faq.md#what-windows-base-os-images-are-supported)-->

### <a name="unable-to-pull-image"></a>无法请求映像

如果 Azure 容器实例最初无法请求映像，则会重试一段时间。 如果映像请求操作继续失败，ACI 最终会使部署失败，可能会显示 `Failed to pull image` 错误。

若要解决此问题，请删除容器实例，然后重试部署。 请确保映像存在于注册表中，并且你已正确键入映像名称。

如果无法请求映像，[az container show][az-container-show] 的输出会显示如下事件：

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>资源不可用错误

由于 Azure 中的区域资源负载不同，尝试部署容器实例时可能会收到以下错误：

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

此错误指示由于尝试部署的区域中负载较重，无法在此时为容器分配指定的资源。 使用以下一个或多个缓解步骤来帮助解决此问题。

* 验证容器部署设置是否位于 [Azure 容器实例的区域可用性](container-instances-region-availability.md)中定义的参数内
* 为容器指定较低的 CPU 和内存设置
* 部署到其他 Azure 区域
* 稍后部署

## <a name="issues-during-container-group-runtime"></a>容器组运行过程中的问题
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>容器不断退出并重启（没有长时间运行的进程）

容器组的[重启策略](container-instances-restart-policy.md)默认为 **Always**，因此容器组中的容器在运行完成后始终会重启。 如果打算运行基于任务的容器，则可能需要将此策略更改为 **OnFailure** 或 **Never**。 如果指定了“失败时”  ，但仍不断重启，则可能容器中执行的应用程序或脚本存在问题。

在没有长时间运行的进程的情况下运行容器组时，可能会看到重复退出并重启 Ubuntu 或 Alpine 等映像。 通过 [EXEC](container-instances-exec.md) 连接将无法正常工作，因为容器没有使其保持活动的进程。 若要解决此问题，请在容器组部署中包含如下所示的启动命令，以使容器保持运行。

```azurecli
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```


<!--Not Available on ## Deploying a Windows container-->

容器实例 API 和 Azure 门户包含 `restartCount` 属性。 若要检查容器的重启次数，可在 Azure CLI 中使用 [az container show][az-container-show] 命令。 在以下示例输出中（为简洁起见已将其截断），可以在输出末尾看到 `restartCount` 属性。

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.cn/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.cn/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux 分发的大多数容器映像会设置一个 shell（如 bash）作为默认命令。 由于 Shell 本身不是长时间运行的服务，因此如果这些容器配置了“始终”重启策略，会立即退出并不断重启  。

### <a name="container-takes-a-long-time-to-start"></a>容器启动时间过长

影响 Azure 容器实例中的容器启动时间的三个主要因素是：

* [映像大小](#image-size)
* [映像位置](#image-location)
* [缓存的映像](#cached-images)

<!--Not Available on Windows images have [additional considerations](#cached-images)-->

#### <a name="image-size"></a>映像大小

如果容器启动时间过长，但最终成功启动，请先查看容器映像大小。 由于 Azure 容器实例按需请求容器映像，因此显示的启动时间与映像大小直接相关。

可在 Docker CLI 中使用 `docker images` 命令查看容器映像大小：

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

保持容器较小的关键是，确保最终映像不包含任何运行时不需要的内容。 执行此操作的一种方法是使用[多阶段生成][docker-multi-stage-builds]。 多阶段生成可轻松确保最终映像仅包含应用程序所需的项目，而不包含任何生成时需要的额外内容。

#### <a name="image-location"></a>映像位置

若要减小映像请求对容器启动时间的影响，另一种方法是在希望部署容器实例的同一区域的 [Azure 容器注册表](/container-registry/)中托管容器映像。 这会缩短容器映像需要经过的网络路径，显著缩短下载时间。

#### <a name="cached-images"></a>缓存的映像

Azure 容器实例使用缓存机制来帮助加快映像容器的启动时间。 常用的 Linux 映像（例如 `ubuntu:1604` 和 `alpine:3.6`）会缓存。 若要获取缓存的映像和标记的最新列表，请使用[列出缓存的映像][list-cached-images] API。

<!--Not Available on  built on common [Windows base images](container-instances-faq.md#what-windows-base-os-images-are-supported)-->
<!--Not Available on  including `nanoserver:1809`, `servercore:ltsc2019`, and `servercore:1809`-->
<!--Not Available on > Use of Windows Server 2019-based images in Azure Container Instances is in preview.-->
<!--Not Available on #### Windows containers slow network readiness-->

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>无法连接到基础 Docker API 或运行特权容器

Azure 容器实例不公开对托管容器组的底层基础结构的直接访问。 这包括访问运行在容器主机上的 Docker API 和运行特权容器。 如果需要 Docker 交互，请查看 [REST 参考文档](https://aka.ms/aci/rest)以了解 ACI API 支持的内容。 如果缺少某些内容，请在 [ACI 反馈论坛](https://aka.ms/aci/feedback)上提交请求。

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>容器组 IP 地址可能会由于端口不匹配而无法访问

Azure 容器实例尚不支持具有常规 docker 配置的端口映射。 如果你发现容器组的 IP 地址在你认为应该可以访问的情况下无法访问，请确保已使用 `ports` 属性将容器映像配置为侦听在容器组中公开的相同端口。

如果要确认 Azure 容器实例可以在容器映像中配置的端口上侦听，请测试公开了该端口的 `aci-helloworld` 映像的部署。 另外，请运行 `aci-helloworld` 应用，使其在该端口上侦听。 `aci-helloworld` 接受一个可选的环境变量 `PORT` 来替代它用于侦听的默认端口 80。 例如，若要测试端口 9000，请在创建容器组时设置该[环境变量](container-instances-environment-variables.md)：

1. 设置容器组来公开端口 9000，并将端口号传递为环境变量的值。 此示例已针对 Bash shell 格式化。 若要使用其他 shell（例如 PowerShell 或命令提示符），需要相应地调整变量赋值。
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. 在 `az container create` 的命令输出中找到该容器组的 IP 地址。 查找 **ip** 的值。 
1. 成功预配容器后，在浏览器中浏览到容器应用的 IP 地址和端口，例如：`192.0.2.0:9000`。 

    应该会看到 Web 应用显示的 "Welcome to Azure Container Instances!" 消息。
1. 完成容器的操作后，使用 `az container delete` 命令将其删除：

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>后续步骤

了解如何[检索容器日志和事件](container-instances-get-logs.md)来帮助调试你的容器。

<!-- LINKS - External -->

<!--Not Available on [azure-name-restrictions]: /cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources-->

[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->

[az-container-show]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-show
[list-cached-images]: https://docs.microsoft.com/rest/api/container-instances/listcachedimages

<!-- Update_Description: new article about container instances troubleshooting -->
<!--NEW.date: 01/15/2020-->