---
title: 什么是访问评审？ - Azure Active Directory | Microsoft Docs
description: 使用 Azure Active Directory 访问评审可以控制组成员身份和应用程序访问权限，从而满足组织的监管、风险管理和符合性计划。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/25/2020
ms.author: v-junlch
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e8f103e07b8e45b7cf3bee5b88927a22986a1f
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79291009"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD 访问评审是什么？

Azure Active Directory (Azure AD) 访问评审可以使组织有效地管理组成员身份、对企业应用程序的访问权限，以及角色分配。 可以定期评审用户的访问权限，确保相应人员持续拥有访问权限。

## <a name="why-are-access-reviews-important"></a>访问评审为何重要？

Azure AD 支持在组织内进行内部协作和与外部组织的用户（例如合作伙伴）进行协作。 用户可以加入组、邀请来宾、连接到云应用以及通过工作或个人设备远程工作。 自助服务的便捷性使人们需要更好的访问管理功能。

- 新员工加入时，如何确保他们获得有助于提高工作效率的相应访问权限？
- 当员工在团队间调动或离开公司时，如何确保删除旧的访问权限，尤其是在涉及来宾时？
- 访问权限过多可能影响审计结果，导致利益受损，因为此类情况表示对访问权限缺乏控制。
- 需要主动与资源所有者联系，确保他们定期评审有权访问其资源的用户。

## <a name="when-to-use-access-reviews"></a>何时使用访问评审？

- **特权角色用户过多：** 建议检查多少用户具有管理访问权限，其中有多少用户是全局管理员，以及检查是否存在向其分配管理任务后未将其删除的受邀来宾和合作伙伴。 可以重新验证 [Azure AD 角色](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)（例如全局管理员）或 [Azure 资源角色](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)（例如 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 体验中的用户访问权限管理员）中的角色分配用户。
- **自动化不可行：** 可针对安全组或 Office 365 组中的动态成员身份创建规则，但如果人力资源数据不在 Azure AD 中或者如果用户在离开组后仍需访问权限来培训其接任者该怎么办？ 对于此类情况，可以对该组创建评审，确保仍需访问权限的用户能够继续获得访问权限。
- **将组用于新用途：** 如果要将组同步到 Azure AD，或计划为所有销售团队组成员启用 Salesforce 应用程序，则要求组所有者在将组用于其他风险内容前评审组成员资格会非常有用。
- **业务关键数据访问权限：** 对于特定资源，可能出于审核目的要求 IT 以外的人员定期注销并提供需要访问权限的正当理由。
- **要维护策略的例外列表：** 在理想情况下，所有用户都会遵循访问策略来保护对组织资源的访问。 但是，有时，某些业务案例要求例外处理。 IT 管理员可以管理此任务、避免忽视策略例外情况，为审核员提供定期评审这些例外情况的证明。
- **要求组所有者确认他们在组中是否仍需要来宾：** 员工访问可能会通过一些本地 IAM 自动执行，但受邀来宾除外。 如果组为来宾授予了业务敏感内容的访问权限，则由组所有者负责确认来宾是否仍有对访问权限的合法业务需求。
- **定期重复评审：** 可以设置按设定频率（例如每周、每月、每季度或每年）定期对用户进行访问评审，审阅者将在每次评审开始前收到通知。 审阅者可以借助友好界面和智能建议的帮助，批准或拒绝访问权限。

## <a name="where-do-you-create-reviews"></a>在哪里创建评审？

可在 Azure AD 访问评审、Azure AD 企业应用（预览版）或 Azure AD PIM 中创建访问评审，具体取决于要评审的内容。

| 用户访问权限 | 审阅者身份 | 评审创建位置 | 审阅者体验 |
| --- | --- | --- | --- |
| 安全组成员</br>Office 组成员 | 指定的审阅者</br>组所有者</br>自我评审 | Azure AD 访问评审</br>Azure AD 组 | 访问面板 |
| 分配联网应用 | 指定的审阅者</br>自我评审 | Azure AD 访问评审</br>Azure AD 企业应用（预览版） | 访问面板 |
| Azure AD 角色 | 指定的审阅者</br>自我评审 | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 门户 |
| Azure 资源角色 | 指定的审阅者</br>自我评审 | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 门户 |


## <a name="create-access-reviews"></a>创建访问评审

若要创建访问评审，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.cn)来管理访问评审并以全局管理员或用户管理员身份登录。

1. 搜索并选择“Azure Active Directory”  。

      ![在 Azure 门户中搜索 Azure Active Directory](./media/access-reviews-overview/search-azure-active-directory.png)

1. 选择“标识监管”  。

1. 在“开始使用”页上，单击“创建访问评审”  按钮。

   ![访问评审启动页](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 



## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>必须拥有多少个许可证？

确保你的目录具有的 Azure AD Premium P2 许可证至少与将执行以下任务的员工一样多：

- 指定为审阅者的成员和来宾用户
- 执行自我评审的成员和来宾用户
- 执行访问评审的组所有者
- 执行访问评审的应用程序所有者

对于以下任务，不需要  使用 Azure AD Premium P2 许可证：

- 具有“全局管理员”或“用户管理员”角色的用户不需要任何许可证，这些用户可设置访问评审、配置设置或应用基于评审做出的决策。

有关许可证的详细信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>示例许可证方案

下面是一些示例许可证方案，可帮助你确定必须拥有的许可证数量。

| 方案 | 计算 | 许可证数量 |
| --- | --- | --- |
| 管理员创建组 A 的访问评审，该组包含 75 个用户和 1 个组所有者，并将该组所有者指定为审阅者。 | 作为审阅者的组所有者需要 1 个许可证 | 1 |
| 管理员创建组 B 的访问评审，该组包含 500 个用户和 3 个组所有者，并将这 3 个组所有者指定为审阅者。 | 作为审阅者的各个组所有者共需 3 个许可证 | 3 |
| 管理员创建具有 500 个用户的组 B 的访问评审。 将其设为自我评审。 | 各个用户进行自我评审共需 500 个许可证 | 500 |
| 管理员创建具有 50 个成员用户和 25 个来宾用户的组 C 的访问评审。 将其设为自我评审。 | 各个用户进行自我评审共需 50 个许可证。<br/>（按所需的 1:5 比例包含来宾用户） | 50 |
| 管理员创建具有 6 个成员用户和 108 个来宾用户的组 D 的访问评审。 将其设为自我评审。 | 充当自我审阅者的各个用户共需 6 个许可证 + 按所要求的 1:5 比例涵盖所有 108 位来宾用户需要 16 个额外的许可证。 6 个许可证，涵盖 6\*5=30 个来宾用户。 对于余下的 (108-6\*5)=78 位来宾用户，需要 78/5=16 个额外的许可证。 因此，总共需要 6+16=22 个许可证。 | 22 |

## <a name="next-steps"></a>后续步骤

- [创建组或应用程序的访问评审](create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [评审组或应用程序的访问权限](perform-access-review.md)
- [完成组或应用程序的访问评审](complete-access-review.md)

<!-- Update_Description: wording update -->