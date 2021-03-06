---
title: 什么是云服务模型和包 | Microsoft Docs
description: 描述 Azure 中的云服务模型（.csdef、.cscfg）和包 (.cspkg)
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 02/19/2020
ms.author: v-junlch
ms.openlocfilehash: 3c514d0f154c6e39068770feee57483150cedcc0
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79291387"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>什么是云服务模型，如何将其打包？
云服务由以下三个组件创建：服务定义 (.csdef)、服务配置 (.cscfg) 和服务包 (.cspkg)    。 ServiceDefinition.csdef 和 ServiceConfig.cscfg 文件都基于 XML，同时介绍云服务的结构及其配置方式；统称为模型   。 **ServicePackage.cspkg** 是基于 **ServiceDefinition.csdef** 和其他文件生成的 zip 文件，它包含所有必需的基于二进制的依赖项。 Azure 可从 ServicePackage.cspkg 和 ServiceConfig.cscfg 两者创建云服务   。

云服务在 Azure 中开始运行后，可以通 **ServiceConfig.cscfg** 文件重新进行配置，但不能更改定义。

## <a name="what-would-you-like-to-know-more-about"></a>想了解哪方面的详细信息？
* 我想了解有关 [ServiceDefinition.csdef](#csdef) 和 [ServiceConfig.cscfg](#cscfg) 文件的详细信息。
* 我知道了，请提供有关可配置内容的 [示例](#next-steps) 。
* 我想要创建 [ServicePackage.cspkg](#cspkg)。
* 我正在使用 Visual Studio，我想要...
  * [创建云服务][vs_create]
  * [重新配置现有云服务][vs_reconfigure]
  * [部署云服务项目][vs_deploy]
  * [通过远程桌面连接到云服务实例][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef** 文件指定 Azure 用于配置云服务的设置。 [Azure Service Definition Schema (.csdef File)](https://docs.microsoft.com/previous-versions/azure/reference/ee758711(v=azure.100))（Azure 服务定义架构（.csdef 文件））为服务定义文件提供允许的格式。 以下示例显示了可为 Web 角色和辅助角色定义的设置：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

可以参考 [服务定义架构](https://docs.microsoft.com/previous-versions/azure/reference/ee758711(v=azure.100)) 更好地了解此处使用的 XML 架构，而以下是部分元素的快速说明：

**Sites**  
包含 IIS7 中承载的网站或 Web 应用程序的定义。

**InputEndpoints**  
包含用于联系云服务的终结点的定义。

**InternalEndpoints**  
包含角色实例用于相互通信的终结点的定义。

**ConfigurationSettings**  
包含特定角色功能的设置定义。

**Certificates**  
包含角色所需证书的定义。 前面的代码示例显示了用于 Azure Connect 的配置的证书。

**LocalResources**  
包含本地存储资源的定义。 本地存储资源是角色实例运行于的虚拟机的文件系统中的保留目录。

**Imports**  
包含已导入模块的定义。 前面的代码示例显示了远程桌面连接和 Azure Connect 的模块。

**Startup**  
包含在角色启动时运行的任务。 任务在 .cmd 文件或可执行文件中定义。

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
云服务设置配置由 **ServiceConfiguration.cscfg** 文件中的值确定。 指定要为此文件中每个角色部署的实例数。 在服务定义文件中定义的配置设置值会添加到服务配置文件。 与云服务相关联的所有管理证书的指纹也会添加到该文件。 [Azure Service Configuration Schema (.cscfg File)](https://docs.microsoft.com/previous-versions/azure/reference/ee758710(v=azure.100))（Azure 服务配置架构（.cscfg 文件））为服务配置文件提供允许的格式。

服务配置文件不与应用程序一起打包，而是作为单独的文件上传到 Azure 并用于配置云服务。 无需重新部署云服务即可上传新的服务配置文件。 云服务正在运行时可以更改云服务的配置值。 以下示例显示了可为 Web 角色和辅助角色定义的配置设置：

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

可以参考 [服务配置架构](https://docs.microsoft.com/previous-versions/azure/reference/ee758710(v=azure.100)) 以更好了解此处使用的 XML 架构，而以下是元素的快速说明：

**实例**  
为角色配置运行角色实例数。 若要防止云服务在升级期间可能变得不可用，建议部署面向 web 角色的多个实例。 部署多个实例即表示遵守 [Azure 计算服务级别协议 (SLA)](https://www.azure.cn/support/legal/sla/) 中的准则，此协议可以保证在为一个服务部署了两个或多个角色实例时，面向 Internet 的角色拥有 99.95% 的外部连接。

**ConfigurationSettings**  
为角色配置运行实例的设置。 `<Setting>` 元素的名称必须与服务定义文件中的设置定义匹配。

**Certificates**  
配置服务使用的证书。 前面的代码示例演示如何定义 RemoteAccess 模块的证书。 *thumbprint* 属性的值必须设置为要使用的证书的指纹。

<p/>

> [!NOTE]
> 通过使用文本编辑器，可以将证书的指纹添加到配置文件中。 或者，可以在 Visual Studio 中角色的“属性”  页的“证书”  选项卡上添加值。
> 
> 

## <a name="defining-ports-for-role-instances"></a>定义角色实例的端口
Azure 仅允许 Web 角色有一个入口点。 即所有通信都通过一个 IP 地址完成。 可以通过配置主机头使请求指向正确的位置来配置网站共享一个端口。 此外，可将应用程序配置为侦听 IP 地址上的已知端口。

以下示例显示了具有网站和 Web 应用程序的 Web 角色的配置。 该网站配置为端口 80 上的默认入口位置，Web 应用程序配置为接收来自名为“mail.mysite.chinacloudapp.cn”的备用主机标头的请求。

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.chinacloudapp.cn" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>更改角色的配置
当云服务在 Azure 中运行时，可以更新其配置而无需使服务处于脱机状态。 要更改配置信息，可以上传新的配置文件或就地编辑配置文件，并将其应用于正在运行的服务。 可对服务配置进行以下更改：

* **更改配置设置的值**  
  当配置设置改变时，角色实例可以选择在实例处于联机状态时应用此更改，或选择正常回收实例并在实例处于脱机状态时应用此更改。
* **更改角色实例的服务拓扑**  
  拓扑更改不会影响正在运行的实例，但正在删除实例的情况除外。 所有剩余的实例通常不需要回收；但可以选择回收角色实例以响应拓扑更改。
* **更改证书指纹**  
  仅可在角色实例处于脱机状态时更新一个证书。 如果在角色实例处于联机状态时添加、删除或更改了某个证书，则 Azure 会使实例脱机以更新证书，并在更改完成后使其重新联机。

### <a name="handling-configuration-changes-with-service-runtime-events"></a>使用服务运行时事件处理配置更改
[Azure 运行时库](https://docs.microsoft.com/previous-versions/azure/reference/mt419365(v=azure.100))包括 [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) 命名空间，它提供类用于与来自角色的 Azure 环境进行交互。 [RoleEnvironment](https://docs.microsoft.com/previous-versions/azure/reference/ee773173(v=azure.100)) 类定义在配置更改前后引发的以下事件：

* [Changing](https://docs.microsoft.com/previous-versions/azure/reference/ee758134(v=azure.100)) 事件   
  此事件发生在配置更改应用于某个角色的指定实例之前，使你有机会记下角色实例（如有必要）。
* [Changed](https://docs.microsoft.com/previous-versions/azure/reference/ee758129(v=azure.100)) 事件   
  发生在配置更改已应用于某个角色的指定实例之后。

> [!NOTE]
> 由于证书更改始终使角色实例处于脱机状态，因此不会引发 RoleEnvironment.Changing 或 RoleEnvironment.Changed 事件。
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> 可部署的最大包大小为 600MB

要将应用程序部署为 Azure 中的云服务，必须首先以适当的格式打包该应用程序。 可以使用 CSPack 命令行工具（与 [Azure SDK](/downloads/) 一起安装）创建包文件，作为 Visual Studio 的替代  。

**CSPack** 使用服务定义文件和服务配置文件的内容来定义包的内容。 CSPack 生成可以使用 [Azure 门户](cloud-services-how-to-create-deploy-portal.md#create-and-deploy) 上传到 Azure 的应用程序包文件 (.cspkg)  。 默认情况下，该应用程序包名为 `[ServiceDefinitionFileName].cspkg`，但可以通过使用 CSPack 的 `/out` 选项指定不同的名称  。

CSPack 位于   
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe（在 Windows 中）可通过运行随 SDK 一起安装的“Azure 命令提示符”快捷方式使用  。  
> 
> 运行 CSPack.exe 程序本身来查看有关所有可能的开关和命令的文档。
> 
> 

<p />

> [!TIP]
> 在“Azure 计算模拟器”  中本地运行云服务时，使用 **/copyonly** 选项。 此选项将应用程序的二进制文件复制到目录布局，以便可以在计算模拟器中运行它们。
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>打包云服务的示例命令
以下示例创建包含 Web 角色信息的应用程序包。 该命令指定待使用的服务定义文件、可以找到二进制文件的目录以及包文件名称。

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

如果应用程序包含 Web 角色和辅助角色，则使用以下命令：

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

其中变量如下所示定义：

| 变量 | Value |
| --- | --- |
| \[DirectoryName\] |包含 Azure 项目 .csdef 文件的根项目目录下的子目录。 |
| \[ServiceDefinition\] |服务定义文件的名称。 默认情况下，此文件名为 ServiceDefinition.csdef。 |
| \[OutputFileName\] |生成的包文件的名称。 通常，此值设为该应用程序的名称。 如果未指定任何文件名称，则应用程序包将创建为 \[ApplicationName\].cspkg。 |
| \[RoleName\] |在服务定义文件中定义的角色的名称。 |
| \[RoleBinariesDirectory] |该角色二进制文件的位置。 |
| \[VirtualPath\] |在服务定义的站点部分中定义的每个虚拟路径的物理目录。 |
| \[PhysicalPath\] |在服务定义的站点节点中定义的每个虚拟路径的内容的物理目录。 |
| \[RoleAssemblyName\] |角色的二进制文件的名称。 |

## <a name="next-steps"></a>后续步骤
我正在创建云服务包，并且我想要...

* [为云服务实例设置远程桌面][remotedesktop]
* [部署云服务项目][deploy]

我正在使用 Visual Studio，我想要...

* [创建新的云服务][vs_create]
* [重新配置现有云服务][vs_reconfigure]
* [部署云服务项目][vs_deploy]
* [为云服务实例设置远程桌面][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md




