---
title: Azure AD Connect 安装路线图。 | Microsoft Docs
description: 本文档概述可用于安装 Azure AD Connect 的安装选项和路径。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 09/18/2018
ms.date: 03/15/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1e6fcbec042ece172b08e9528ff3a124860d50d
ms.sourcegitcommit: 46a8da077726a15b5923e4e688fd92153ebe2bf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58186654"
---
# <a name="azure-ad-connect-installation-roadmap"></a>Azure AD Connect 安装路线图

## <a name="install-azure-ad-connect"></a>安装 Azure AD Connect

> [!IMPORTANT]
> Microsoft 不支持在正式记录的这些操作之外修改或操作 Azure AD Connect 同步。 其中的任何操作都可能会导致 Azure AD Connect 同步出现不一致或不受支持状态。因此，Microsoft 无法提供这种部署的技术支持。

可以在 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?LinkId=615771)找到 Azure AD Connect 的下载文件。

| 解决方案 | 方案 |
| --- | --- |
| 开始之前 - [硬件和先决条件](how-to-connect-install-prerequisites.md) |<li>开始安装 Azure AD Connect 之前所要完成的步骤。</li> |
| [快速设置](how-to-connect-install-express.md) |<li>如果只有一个林 AD，我们建议使用此选项。</li> <li>使用密码同步以同一密码进行用户登录。</li> |
| [自定义设置](how-to-connect-install-custom.md) |<li>有多个林时使用。 支持许多本地[拓扑](plan-connect-topologies.md)。</li> <li>自定义登录选项，例如用于联合身份验证的 ADFS，或使用第三方标识提供者。</li> <li>自定义同步功能，例如筛选。</li> |
| [从 DirSync 升级](how-to-dirsync-upgrade-get-started.md) |<li>在已有 DirSync 服务器运行的情况下使用。</li> |
| [从 Azure AD Sync 或 Azure AD Connect 升级](how-to-upgrade-previous-version.md) |<li>可以根据偏好选择多种不同的方法。</li> |

[安装后](how-to-connect-post-installation.md)，应该验证程序是否按预期工作，并将许可证分配给用户。

### <a name="next-steps-to-install-azure-ad-connect"></a>Azure AD Connect 安装后续步骤
|主题 |链接|  
| --- | --- |
|下载 Azure AD Connect | [下载 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|使用快速设置安装 | [Azure AD Connect 的快速安装](./how-to-connect-install-express.md)|
|使用自定义设置安装 | [Azure AD Connect 的自定义安装](./how-to-connect-install-custom.md)|
|从 DirSync 升级 | [从 Azure AD 同步工具 (DirSync) 升级](./how-to-dirsync-upgrade-get-started.md)|
|安装后 | [验证安装并分配许可证](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>了解有关安装 Azure AD Connect 的详细信息
还要预先了解 [操作](how-to-connect-sync-operations.md) 注意事项。 可能要部署一台待机服务器，以便在发生[灾难](how-to-connect-sync-staging-server.md#disaster-recovery)时轻松进行故障转移。 如果要频繁进行配置更改，应该计划部署一台[暂存模式](how-to-connect-sync-staging-server.md)服务器。

|主题 |链接|  
| --- | --- |
|支持的拓扑 | [Azure AD Connect 的拓扑](plan-connect-topologies.md)|
|设计概念 | [Azure AD Connect 设计概念](plan-connect-design-concepts.md)|
|用于安装的帐户 | [有关 Azure AD Connect 凭据和权限的详细信息](reference-connect-accounts-permissions.md)|
|操作规划 | [Azure AD Connect 同步：操作任务和注意事项](how-to-connect-sync-operations.md)|
|用户登录选项 | [Azure AD Connect 用户登录选项](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>配置同步功能
Azure AD Connect 随附了多个可以选择启用或已按默认启用的功能。 在某些方案和拓扑中，有些功能可能需要进行其他配置。

如果要限制同步到 Azure AD 的对象，可以使用[筛选](how-to-connect-sync-configure-filtering.md)。 默认同步所有用户、联系人、组和 Windows 10 计算机。 可以根据域、OU 或属性更改筛选设置。

[密码哈希同步](how-to-connect-password-hash-synchronization.md) 可将 Active Directory 中的密码哈希同步到 Azure AD。 最终用户可以在本地与云中使用相同的密码，且只需在一个位置管理此密码。 由于它使用本地 Active Directory，因此用户还可以使用自己的密码策略。

[防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md)功能默认处于打开状态，它可以保护云目录，避免同时进行多次删除。 默认情况下，每运行一次可以进行 500 次删除。 可以根据组织大小更改此设置。

使用快速设置安装时，将默认启用[自动升级](how-to-connect-install-automatic-upgrade.md)，确保 Azure AD Connect 始终保持最新版本。

### <a name="next-steps-to-configure-sync-features"></a>同步功能配置后续步骤
|主题 |链接|  
| --- | --- |
|配置筛选 | [Azure AD Connect 同步：配置筛选](how-to-connect-sync-configure-filtering.md)|
|密码哈希同步 | [密码哈希同步](how-to-connect-password-hash-synchronization.md)|
|防止意外删除 | [Azure AD Connect 同步：防止意外删除](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|自动升级 | [Azure AD Connect：自动升级](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>自定义 Azure AD Connect 同步
Azure AD Connect 同步随附一个适用于大部分客户和拓扑的默认配置。 但总存在默认配置不适用的情况，因此必须进行调整。 可以根据本部分和链接主题中所述进行更改。

如果以前没有用过同步拓扑，请先了解[技术概念](how-to-connect-sync-technical-concepts.md)中所述的基本概念和术语。 Azure AD Connect 是在 MIIS2003、ILM2007 和 FIM2010 基础上演进而来的。 即使有些功能相同，但改变的部分也有很多。

[默认配置](concept-azure-ad-connect-sync-default-configuration.md)假设配置中可能存在多个林。 在这些拓扑中，用户对象可能表示为另一个林中的联系人。 用户还可能具有另一个资源林中的链接邮箱。 [用户和联系人](concept-azure-ad-connect-sync-user-and-contacts.md)中介绍了默认配置的行为。

同步的配置模型称为[声明性预配](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)。 高级属性流程使用[函数](reference-connect-sync-functions-reference.md)来表示属性转换。 可以使用 Azure AD Connect 随附的工具来检查整个配置。 如果需要进行配置更改，请确保遵循[最佳做法](how-to-connect-sync-best-practices-changing-default-configuration.md)，以便可以更轻松地采用新版本。

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>自定义 Azure AD Connect 同步的后续步骤
|主题 |链接|  
| --- | --- |
|所有 Azure AD Connect 同步文章 | [Azure AD Connect 同步](how-to-connect-sync-whatis.md)|
|技术概念 | [Azure AD Connect 同步：技术概念](how-to-connect-sync-technical-concepts.md)|
|了解默认配置 | [Azure AD Connect 同步：了解默认配置](concept-azure-ad-connect-sync-default-configuration.md)|
|了解用户和联系人 | [Azure AD Connect 同步：了解用户和联系人](concept-azure-ad-connect-sync-user-and-contacts.md)|
|声明性预配 | [Azure AD Connect 同步：了解声明性预配表达式](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|更改默认配置 | [更改默认配置的最佳做法](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>配置联合身份验证功能

Azure AD Connect 提供多项功能，简化了使用 AD FS 通过 Azure AD 进行联合身份验证以及管理联合身份验证信任的过程。 Azure AD Connect 支持 Windows Server 2012R2 或更高版本上的 AD FS。

[更新 AD FS 场的 SSL 证书](how-to-connect-fed-ssl-update.md)，即使不使用 Azure AD Connect 来管理联合身份验证信任。

向场[添加 AD FS 服务器](how-to-connect-fed-management.md#addadfsserver)，以便根据需要扩展场。

[修复信任](how-to-connect-fed-management.md#repairthetrust)（针对 Azure AD），只需单击数下即可。

可将 ADFS 配置为支持[多个域](how-to-connect-install-multiple-domains.md)。 例如，在联合身份验证功能中可能需要使用多个顶级域。

如果 ADFS 服务器未配置为自动更新 Azure AD 中的证书，或者如果使用非 ADFS 解决方案，则在需要[更新证书](how-to-connect-fed-o365-certs.md)时会通知你。

### <a name="next-steps-to-configure-federation-features"></a>配置联合身份验证功能的后续步骤
|主题 |链接|  
| --- | --- |
|所有 AD FS 文章 | [Azure AD Connect 和联合身份验证](how-to-connect-fed-whatis.md)|
|配置带有子域的 ADFS | [与 Azure AD 联合的多域支持](how-to-connect-install-multiple-domains.md)|
|管理 AD FS 场 | [使用 Azure AD Connect 管理和自定义 AD FS](how-to-connect-fed-management.md)|
|手动更新联合身份验证证书 | [续订 Office 365 和 Azure AD 的联合身份验证证书](how-to-connect-fed-o365-certs.md)|


## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [密码哈希同步](how-to-connect-password-hash-synchronization.md)|
- [Azure AD Connect 和联合身份验证](how-to-connect-fed-whatis.md)
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)

<!-- Update_Description: link update -->