---
ms.assetid: 
title: "Azure Key Vault 软删除 | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: v-junlch
manager: mbaldwin
ms.date: 05/10/2017
wacn.date: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 08618ee31568db24eba7a7d9a5fc3b079cf34577
ms.openlocfilehash: 278e56ab12f360d4fbc9a8c7deb9b85577eb1d18
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="azure-key-vault-soft-delete-feature-overview"></a>Azure Key Vault 软删除功能概述

>[!NOTE]
>在此预览版 Azure Key Vault 中，只有“软删除”功能处于预览版阶段。 作为一个整体，Azure Key Vault 是一项完整的生产服务。

针对此预览版的 Azure Key Vault，我们介绍了 Key Vault 和 Key Vault 对象的可恢复删除（称为“软删除”）。 本文将具体探讨以下方案：

- 针对 Key Vault 的可恢复删除提供的支持
- 针对 Key Vault 对象（例如密钥、机密和证书）的可恢复删除 提供的支持

## <a name="supporting-interfaces"></a>支持接口

软删除功能最初通过 REST、.NET/C# 和 PowerShell 接口提供。 有关详细信息，请参阅参考文献 [Key Vault 参考](./index.md)。

## <a name="scenarios"></a>方案

Azure Key Vault 是由 Azure Resource Manager 管理的跟踪资源。 Azure Resource Manager 还指定了定义明确的删除行为，要求成功的删除操作必须使该资源不再可供访问。 软删除功能解决了已删除对象的恢复问题，无论是意外删除还是有意删除。

1. 在典型情景中，用户可能无意中删除了 Key Vault 或 Key Vault 对象；如果 Key Vault 或 Key Vault 对象在预设的某个时间段内可恢复，则用户可以撤消删除并恢复其数据。

2. 在另一种情景中，恶意用户可能会试图删除 Key Vault 或 Key Vault 对象（例如保管库内的密钥），导致业务中断。 作为一项安全措施，可将 Key Vault 或 Key Vault 对象的删除与基础数据的实际删除区分开来，例如，将数据删除权限仅授予另一个受信任角色。 这实际上要求对可能会导致数据立即丢失的操作进行仲裁。

### <a name="soft-delete-behavior"></a>软删除行为

在此预览版中，针对 Key Vault 或 Key Vault 对象的删除操作是软删除，实际上会将资源保留给定的保留期，同时从表面上看对象已删除。 该服务进一步提供了用于恢复已删除对象的机制，实质上是撤消删除。 

软删除是一种可选的 Key Vault 行为，在此版本中**默认未启用**。 有关为 Key Vault 启用软删除的详细信息，请参阅你选择的接口对应的 [Key Vault 参考](./index.md)中的具体指南。

### <a name="key-vault-recovery"></a>Key Vault 恢复

删除 Key Vault 后，服务将在订阅下创建代理资源，并添加用于恢复的足够元数据。 代理资源是一个存储对象，位于与已删除 Key Vault 相同的位置。 

### <a name="key-vault-object-recovery"></a>Key Vault 对象恢复

删除 Key Vault 对象（例如密钥）时，服务会将该对象置于已删除状态，从而使其不可供任何检索操作访问。 在此状态下，只能列出、恢复或强制/永久删除该 Key Vault 对象。 

同时，Key Vault 将计划在预设的保留间隔后删除与已删除 Key Vault 或 Key Vault 对象对应的基础数据。 在保留间隔内，还会保留与该保管库相对应的 DNS 记录。

### <a name="soft-delete-retention-period"></a>软删除保留期

软删除的资源将保留设定的一段时间（90 天）。 

>[!NOTE]
> 2017 年 5 月 10 日的预览版无法配置此软删除保留期。 

在软删除保留间隔内，以下情况属实：

- 可以列出订阅中处于软删除状态的所有 Key Vault 和 Key Vault 对象，并可访问与这些对象有关的删除和恢复信息。
    - 只有具有特殊权限的用户才能列出已删除的保管库。 我们建议用户创建一个具有这些特殊权限的自定义角色来处理已删除的保管库。
- 无法在同一位置创建具有相同名称的 Key Vault；相应地，在创建 Key Vault 对象时，如果 Key Vault 中包含具有相同名称且处于已删除状态的对象，则无法在其中创建该对象。 
- 只有特权用户可以还原 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出恢复命令。
    - 有权在资源组下创建 key vault 的用户（自定义角色的成员）可以还原该保管库。
- 只有特权用户可以强制删除 Key Vault 或 Key Vault 对象，方法是对相应的代理资源发出删除命令。

除非恢复 Key Vault 或 Key Vault 对象，否则在保留间隔结束时，服务将清除已软删除的 Key Vault 或 Key Vault 对象及其内容。 资源删除不可重新计划。

### <a name="permissioned-purge"></a>授权清除

可通过对代理资源执行 POST 操作永久删除、清除 Key Vault，但此操作需要特殊权限。 通常，只有订阅所有者才能清除 Key Vault。 POST 操作将触发对该保管库的立即删除，且此删除不可恢复。 

但是，Azure 订阅已被标记为“不可删除”时例外。 在这种情况下，只有服务可以执行实际删除，并且将作为计划的进程执行此操作。 




