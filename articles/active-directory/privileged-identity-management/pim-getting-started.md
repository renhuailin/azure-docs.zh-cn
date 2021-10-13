---
title: 开始使用 PIM - Azure Active Directory | Microsoft Docs
description: 了解如何启用并开始在 Azure 门户中使用 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 890f794846427b66081d97018e5590225142c45f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667281"
---
# <a name="start-using-privileged-identity-management"></a>开始使用 Privileged Identity Management

本文介绍如何启用 Privileged Identity Management (PIM) 并开始使用它。

使用 Privileged Identity Management (PIM) 可管理、控制和监视 Azure Active Directory (Azure AD) 组织内的访问。 使用 PIM，可以根据需要及时提供对 Azure 资源、Azure AD 资源和其他 Microsoft 联机服务（如 Microsoft 365 或 Microsoft Intune）的访问。

## <a name="prerequisites"></a>先决条件

若要使用 Privileged Identity Management，则必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5

有关详细信息，请参阅[使用 Privileged Identity Management 的许可要求](subscription-requirements.md)。

> [!Note]
> 具有 Premium P2 许可证的 Azure AD 组织中的特权角色用户转到 Azure AD 中的“角色和管理员”并选择一个角色（或甚至只是访问 Privileged Identity Management）时：
>
> - 我们会自动为组织启用 PIM
> - 他们现在可以分配“常规”的角色分配，也可以分配合格的角色分配
>
> PIM 启用后，不会对组织造成任何其他影响，无需担心。 它为你提供了其他分配选项，例如具有开始和结束时间的“有效”和“合格”选项。 通过 PIM，你还可以使用管理单元和自定义角色来定义角色分配的范围。 如果你是全局管理员或特权角色管理员，你可能会开始收到其他一些电子邮件，例如 PIM 每周摘要。 你还可能会在与角色分配有关的审核日志中看到 MS-PIM 服务主体。 这是预期的变化，对工作流没有影响。

## <a name="prepare-pim-for-azure-ad-roles"></a>准备将 PIM 用于 Azure AD 角色

建议执行以下任务，以准备使用 Privileged Identity Management 来管理 Azure AD 角色：

1. [配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)。
1. [指定符合条件的分配](pim-how-to-add-role-to-user.md)。
1. [允许符合条件的用户实时激活其 Azure AD 角色](pim-how-to-activate-role.md)。

## <a name="prepare-pim-for-azure-roles"></a>准备将 PIM 用于 Azure 角色

建议执行以下任务，以准备使用 Privileged Identity Management 来管理订阅的 Azure 角色：

1. [发现 Azure 资源](pim-resource-roles-discover-resources.md)
1. [配置 Azure 角色设置](pim-resource-roles-configure-role-settings.md)。
1. [指定符合条件的分配](pim-resource-roles-assign-roles.md)。
1. [允许符合条件的用户实时激活其 Azure 角色](pim-resource-roles-activate-your-roles.md)。

## <a name="navigate-to-your-tasks"></a>导航到任务

设置 Privileged Identity Management 后，即可熟悉其用法。

![Privileged Identity Management 中的导航窗口，其中显示“任务”和“管理”选项](./media/pim-getting-started/pim-quickstart-tasks.png)

| 任务 + 管理 | 说明 |
| --- | --- |
| **我的角色**  | 显示已向你分配的符合条件的活动角色列表。 可以在此处激活任何符合条件的已分配角色。 |
| **挂起的请求** | 显示要激活符合条件的角色分配的挂起的请求。 |
| **审批请求** | 按用户显示你的目录中指定由你进行审批的要激活符合条件的角色的请求列表。 |
| **审阅访问权限** | 列出指定要由你完成的活动访问审阅（无论你是审阅自己还是审阅其他人的访问权限）。 |
| **Azure AD 角色** | 为特权角色管理员显示用来管理 Azure AD 角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示访问此仪表板的用户的相关信息，而非整个组织的相关信息。 |
| **Azure 资源** | 为特权角色管理员显示用来管理 Azure 资源角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示访问此仪表板的用户的相关信息，而非整个组织的相关信息。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>将 PIM 磁贴添加到仪表板

为了更轻松地打开 Privileged Identity Management，请将 PIM 磁贴添加到 Azure 门户仪表板。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“所有服务”，并查找“Azure AD Privileged Identity Management”服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 选择 Privileged Identity Management 的“快速入门”。

1. 选择“将边栏选项卡固定到仪表板”，将 Privileged Identity Management“快速入门”页固定到仪表板 。

    ![用于将 Privileged Identity Management 页固定到仪表板的图钉图标](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 仪表板上，你将看到如下所示的一个磁贴：

    ![仪表板上的 Privileged Identity Management 快速入门磁贴](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中管理 Azure 资源访问](pim-resource-roles-discover-resources.md)
