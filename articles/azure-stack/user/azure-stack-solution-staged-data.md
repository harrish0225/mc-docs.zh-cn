---
title: 将临时数据分析解决方案部署到 Azure Stack | Microsoft Docs
description: 了解如何将临时数据分析解决方案部署到 Azure Stack
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
origin.date: 06/20/2019
ms.date: 07/29/2019
ms.author: v-jay
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 15a5f34174c5ca88aa893e574380e3ace0095bca
ms.sourcegitcommit: 4d34571d65d908124039b734ddc51091122fa2bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68513520"
---
# <a name="tutorial-deploy-a-staged-data-analytics-solution-to-azure-stack"></a>教程：将临时数据分析解决方案部署到 Azure Stack

本文介绍如何部署一个解决方案用于收集在收集点上需要分析的数据，以快速做出决策。 这种数据收集通常是在没有 Internet 访问的情况下进行的。 建立连接后，可能需要对数据进行资源密集型分析以获取更多的见解。

在本教程中，需创建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建原始数据存储 Blob。
> - 创建新的 Azure Stack 函数，将纯净数据从 Azure Stack 移到 Azure。
> - 创建 Blob 存储触发的函数。
> - 创建包含 Blob 和队列的 Azure Stack 存储帐户。
> - 创建队列触发的函数。
> - 测试队列触发的函数。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 

## <a name="architecture-for-staged-data-analytics"></a>临时数据分析的体系结构

![临时数据分析](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>临时数据分析的先决条件

  - Azure 订阅。
  - 一个 Azure Active Directory (AAD) 服务主体，该主体有权访问 Azure 和 Azure Stack 上的租户订阅。 若 Azure Stack 使用 Azure 订阅以外的 AAD 租户，则你需要创建两个服务主体。 若要了解如何为 Azure Stack 创建服务主体，请参阅[创建为应用程序提供 Azure Stack 资源访问权限的服务主体](/azure-stack/user/azure-stack-create-service-principals)。
      - **记下每个服务主体的应用程序 ID、客户端机密、Azure AD 租户 ID 和租户名称 (xxxxx.partner.onmschina.cn)。**
  - 需要提供数据集合以用于数据分析。 我们提供了示例数据。
  - 在本地计算机上安装[用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/)。

## <a name="get-the-docker-image"></a>获取 Docker 映像

适用于每个部署的 Docker 映像消除了不同版 Azure PowerShell 之间出现的依赖项问题。
1.  确保用于 Windows 的 Docker 使用 Windows 容器。
2.  在提升的命令提示符下运行以下命令，获取包含部署脚本的 Docker 容器。

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>部署解决方案

1.  成功拉取容器映像以后，即可启动映像。

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  容器启动以后，系统会在容器中为你提供提升的 PowerShell 终端。 将目录切换到部署脚本的位置。

      ```powershell  
      cd .\SDDemo\
      ```

3.  运行此部署。 根据需要提供凭据和资源名称。 HA 是指将要在其中部署 HA 群集的 Azure Stack，而 DR 是指将要在其中部署 DR 群集的 Azure Stack。

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.partner.onmschina.cn" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  如果出现提示，请输入用于 Azure 部署与 Application Insights 的区域。

2.  键入“Y”以允许安装 NuGet 提供程序，此时会开始安装 API 配置文件“2018-03-01-hybrid”模块，并允许对 Azure 和 Azure Stack 进行部署。

3.  部署资源后，测试是否已生成 Azure Stack 和 Azure 的数据。

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  若要查看正在处理的数据，可以转到部署到 Azure 或 Azure Stack 的 Web 应用程序。

### <a name="azure-web-app"></a>Azure Web 应用
 
![临时数据分析解决方案](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Azure Stack Web 应用
 
![Azure Stack 的临时数据分析解决方案](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>后续步骤

  - 若要详细了解混合云应用程序，请参阅[混合云解决方案](/azure-stack/user/#step-by-step-tutorials)。

  - 使用自己的数据，或修改 [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) 上此示例的代码。