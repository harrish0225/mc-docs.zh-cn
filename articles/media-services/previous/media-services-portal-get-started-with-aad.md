---
title: 通过 Azure 门户开始使用 Azure AD 身份验证 | Microsoft Docs
description: 了解如何使用 Azure 门户访问 Azure Active Directory (Azure AD) 身份验证设置，以便使用 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/29/2018
ms.date: 04/06/2020
ms.author: v-jay
ms.openlocfilehash: 7ba7571ed333e5cadf4a4ef01bbc6ec0c41a937b
ms.sourcegitcommit: fe9ed98aaee287a21648f866bb77cb6888f75b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625688"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>通过 Azure 门户开始使用 Azure AD 身份验证

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](/media-services/latest/)。 另请参阅[从 v2 到 v3 的迁移指南](../latest/migrate-from-v2-to-v3.md)

了解如何使用 Azure 门户访问 Azure Active Directory (Azure AD) 身份验证设置，以便使用 Azure 媒体服务 API。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，请从 [Azure 试用版](https://www.azure.cn/pricing/1rmb-trial/)入手。 
- 一个媒体服务帐户。 有关详细信息，请参阅[利用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。

将 Azure AD 身份验证与 Azure 媒体服务结合使用时，可以选择下列两个身份验证选项：

- **服务主体身份验证**。 对服务进行身份验证。 常常使用这种身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用：Web 应用、函数应用、逻辑应用、API 或微服务。
- **用户身份验证**。 验证使用应用程序与媒体服务资源进行交互的用户。 交互式应用程序应首先提示用户输入凭据。 例如，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。 

## <a name="access-the-media-services-api"></a>访问媒体服务 API

可以通过此页选择需要用来连接到 API 的身份验证方法。 此页还提供连接到 API 所需的值。

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择媒体服务帐户。
2. 选择与媒体服务 API 的连接方式。
3. 在“连接到媒体服务 API”下，选择要连接到的媒体服务 API 版本。 

## <a name="service-principal-authentication--recommended"></a>服务主体身份验证（推荐）

使用 Azure Active Directory (Azure AD) 应用和机密对服务进行身份验证。 建议对调用媒体服务 API 的所有中间层服务执行此操作。 例如，Web 应用、Functions、逻辑应用、API 和微服务。 这是推荐使用的身份验证方法。

### <a name="manage-your-azure-ad-app-and-secret"></a>管理 Azure AD 应用和机密

“管理 AAD 应用和机密”  部分允许你选择或创建新的 Azure AD 应用并生成机密。 出于安全方面的原因，机密无法在边栏选项卡关闭后显示。 应用程序使用应用程序 ID 和机密进行身份验证，以获取媒体服务的有效令牌。

务必拥有足够的权限，以便向 Azure AD 租户注册应用程序，并将应用程序分配给 Azure 订阅中的角色。 有关详细信息，请参阅[所需权限](/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

**连接到媒体服务 API** 提供用于连接服务主体应用程序的值。 可以获取文本值或复制 JSON 或 XML 块。

## <a name="user-authentication"></a>用户身份验证

此选项可用于对某个使用应用与媒体服务资源交互的 Azure Active Directory 员工或成员进行身份验证。 交互式应用程序应先提示用户输入用户凭据。 此身份验证方法只应该用于管理型应用程序。

### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

从“连接到媒体服务 API”  部分复制用于连接用户应用程序的凭据。 可以获取文本值或复制 JSON 或 XML 块。

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-portal-upload-files.md)。
