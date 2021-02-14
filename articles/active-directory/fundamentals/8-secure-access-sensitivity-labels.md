---
title: 在 Azure Active Directory 中使用敏感度标签控制对资源的外部访问。
description: 使用敏感度标签是针对外部访问的总体安全计划的一部分。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac3141823eb72b17754a771cd63264fe851949d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365355"
---
# <a name="control-access-with-sensitivity-labels"></a>通过敏感度标签控制访问 

[敏感度标签](/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide)帮助你控制对 Office 365 应用程序和容器（例如 Microsoft Teams、Microsoft 365 组和 SharePoint 站点）中的内容的访问。 它们可以保护你的内容，且不会妨碍用户的协作和生产能力。 使用敏感度标签，你可以在设备、应用和服务之间发送组织的内容，同时保护数据并满足合规性和安全性策略。 

使用敏感度标签，你可以：

* 对内容进行分类，而无需添加任何保护设置。 可以为内容分配一个分类（就像不干胶标签那样）。该分类会持久存在，在用户使用和共享内容时随内容一起移动。 可以使用此分类生成使用情况报表并查看敏感内容的活动数据。

* 强制执行保护设置，例如加密、水印和访问限制。 例如，用户可以为文档或电子邮件应用“机密”标签，该标签可以[加密内容](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)并添加一个“机密”水印。 此外，你还可以[将敏感度标签应用于容器](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)（例如 SharePoint 站点），并强制规定外部用户是否可以访问其包含的内容。

电子邮件和其他内容上的敏感度标签会随内容一起移动。 容器上的敏感度标签可以限制对容器的访问，但容器中的内容不会继承标签。 例如，用户可以从受保护的站点获取内容，下载该内容，然后不受限制地将其共享，除非该内容还有一个敏感度标签。

 >[!NOTE]
>若要应用敏感度标签，用户必须登录到其 Microsoft 工作或学校帐户。 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>创建和管理敏感度级别所需的权限

将创建敏感度标签的合规团队的成员需要具有对 Microsoft 365 合规中心、Microsoft 365 安全中心或安全与合规中心的权限。

默认情况下，你的租户的全局管理员可以访问这些管理中心，并可向合规官和其他人员授予访问权限，而无需为其提供租户管理员的所有权限。若要实现这个已委派的有限管理访问权限，请将用户添加到“合规性数据管理员”、“合规性管理员”或“安全管理员”角色组。

 

## <a name="determine-your-sensitivity-label-strategy"></a>确定敏感度标签策略

当你考虑治理对内容的外部访问时，请确定以下各项：

**对于所有内容和容器**

* 如何定义高、中、低业务影响（HBI、MBI、LBI）？ 考虑当特定类型的内容共享不当时对组织造成的影响。

   * 具有特定类型的固有[敏感内容](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)的内容，例如信用卡或护照号码

   * 由特定的组或人员（例如合规官、财务主管或执行官）创建的内容

   * 特定库或站点中的内容。 例如，承载着组织策略或私密财务数据的容器

   * 其他条件

* 哪些类别的内容（例如 HBI 内容）应限制外部用户访问？

   * 限制可能包括限制对容器的访问，以及对内容进行加密等操作。

* 对于 HBI 数据、站点或 Microsoft 365 组，应采用哪些默认值？

* 你将在何处使用敏感度标签[进行标记和监视](/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide)，而不是[强制执行加密](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)或[强制执行容器访问限制](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)？

**对于电子邮件和内容**

* 你要对内容[自动应用敏感度标签](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)还是手动应用？

   * 如果你选择手动应用，是否要[建议用户应用标签](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)？

**对于容器**

* 哪些条件将决定 M365 组、Teams 或 SharePoint 站点是否需要使用敏感度标签来限制访问？

* 你是希望只在以后对这些容器中的内容进行标记，还是希望[自动标记](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) SharePoint 和 OneDrive 中的现有文件？

有关如何使用敏感度标签的其他建议，请参阅这些[敏感度标签常见方案](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)。

### <a name="sensitivity-labels-on-email-and-content"></a>电子邮件和内容上的敏感度标签

当你为文档或电子邮件指定敏感度标签时，它就像一个应用于内容的标记，可自定义、是明文且持久存在。 

* “可自定义”是指，你可以创建适用于你的组织的标签，并决定应用它们时所发生的情况。

* “明文”是指它是项的元数据的一部分，可由应用程序和服务读取，以便它们可以应用自己的保护措施。

* “持久存在”是指，标签和任何关联的保护将随内容移动，并成为应用和强制实施策略的基础。

 

> [!NOTE]
> 每个内容项都可以有一个应用于它的敏感度标签。


### <a name="sensitivity-labels-on-containers"></a>容器上的敏感度标签

您可以对容器（如 [Microsoft 365 组](../enterprise-users/groups-assign-sensitivity-labels.md)、 [Microsoft 团队](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)和 [SharePoint 站点](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)）应用灵敏度标签。 将此敏感度标签应用于受支持的容器时，标签会自动将分类和保护设置应用于所连接的站点或组。 这些容器上的敏感度标签可以控制容器的以下方面：

* **隐私**。 你可以选择谁可以查看站点：特定用户、所有内部用户或任何人。

* 外部用户访问。 控制组所有者是否可以将来宾添加到组。

* 从非管理的设备访问。 决定非管理的设备是否可以访问内容，以及如何访问内容。

 

![编辑敏感度标签的屏幕截图](media/secure-external-access/8-edit-label.png)

 

当你将敏感度标签应用于 SharePoint 站点之类的容器时，它不是应用于那里的内容：容器上的敏感度标签控制对容器中内容的访问。 

* 如果想要将标签自动应用于容器中的内容，请参阅[自动将敏感度应用于内容](/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)。

* 如果你希望用户能够手动将标签应用于此内容，请确保你已[在 SharePoint 和 OneDrive 中为 Office 文件启用了敏感度标签](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)。

### <a name="plan-to-implement-sensitivity-labels"></a>计划实施敏感度标签

确定要如何使用敏感度标签以及要将其应用于哪些内容和站点后，请参阅以下文档来了解如何进行实施。

1. [开始使用敏感度标签](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [创建部署策略](/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [创建并发布敏感度标签](/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [使用敏感度标签限制对内容的访问以应用加密](/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [将敏感度标签用于团队、组和站点](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [在 SharePoint 和 OneDrive 中为 Office 文件启用敏感度标签](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问所需的安全状况](1-secure-access-posture.md)

2. [了解当前状况](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)（本文）

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)