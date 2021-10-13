---
title: 在 PIM 中执行对 Azure 资源和 Azure AD 角色的访问评审 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中对 Azure 资源和 Azure AD 角色执行访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a2d1b9e15f795da4931ffbff88f5222aad69c2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669580"
---
# <a name="perform-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>在 PIM 中执行对 Azure 资源和 Azure AD 角色的访问评审

Privileged Identity Management (PIM) 简化了企业对以特权身份访问 Azure Active Directory (AD) 中的资源和其他 Microsoft 联机服务（如 Microsoft 365 或 Microsoft Intune）的行为进行管理的方式。 按照本文中的步骤对角色执行访问评审。

如果已被分配到某个管理角色，组织中的特权角色管理员可能要求定期确认仍然需要使用该角色来完成工作。 可以通过所接收电子邮件中的链接或 [Azure 门户](https://portal.azure.com)执行此操作。

若为特权角色管理员或全局管理员，并有意了解访问评审，请参阅[如何开始访问评审](pim-create-azure-ad-roles-and-resource-roles-review.md)，了解详细信息。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限

可以基于用户是否仍然需要访问角色来批准或拒绝访问。 如果你希望他们继续充当该角色，请选择“批准”，如果他们不再需要此访问权限，请选择“拒绝”。 在评审结束并且管理员应用结果之前，用户的分配状态不会更改。 某些被拒绝的用户无法应用结果的常见情况可能包括：

- 查看已同步本地 Windows AD 组的成员：如果组是从本地 Windows AD 同步的，则该组无法在 Azure AD 中进行管理，因此无法更改成员身份。
- 查看分配有嵌套组的角色：对于通过嵌套组具有成员身份的用户，访问评审不会删除其嵌套组的成员身份，因此将保留对所评审角色的访问权限。
- 找不到用户或其他错误：这些也可能导致应用结果不受支持。

请遵循以下步骤来查找并完成访问权限审查：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 选择“Azure Active Directory”，然后打开“Privileged Identity Management”。
1. 选择“审阅访问权限”。 如果有任何挂起的访问评审，它们会显示在访问评审页中。

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png" alt-text="Privileged Identity Management 应用程序的屏幕截图，其中为 Azure AD 角色选择了“评审访问权限”边栏选项卡。" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png":::

1. 选择想要完成的审查。
1. 选择“批准”或“拒绝”。  在“提供理由”框中，根据需要输入你的决定的业务理由。

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png" alt-text="Privileged Identity Management 应用程序的屏幕截图，其中为 Azure AD 角色选择了“访问评审”。" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png":::

## <a name="next-steps"></a>后续步骤

- [在 PIM 中创建对 Azure 资源和 Azure AD 角色的访问评审](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [在 PIM 中完成对 Azure 资源和 Azure AD 角色的访问评审](pim-complete-azure-ad-roles-and-resource-roles-review.md)
