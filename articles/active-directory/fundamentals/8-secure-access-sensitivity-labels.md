---
title: 使用敏感度标签控制对 Azure Active Directory 中资源的外部访问。
description: 使用敏感度标签作为外部访问的总体安全计划的一部分。
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
ms.openlocfilehash: ef987305fadb83f9ed8b3f080cfb5a8d52fc36df
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743867"
---
# <a name="control-access-with-sensitivity-labels"></a>使用敏感度标签控制访问权限 

[敏感性标签](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide) 可帮助你控制对 Office 365 应用程序中内容的访问，以及 Microsoft 团队、Microsoft 365 组和 SharePoint 站点等容器中的访问权限。 他们可以保护您的内容，而不会妨碍用户的协作和生产能力。 通过敏感度标签，可在设备、应用和服务之间发送组织的内容，同时保护数据并满足合规性和安全策略。 

对于敏感度标签，可以：

* 对 **内容进行分类，无需添加任何保护设置**。 可以将分类分配到内容 (例如不干胶标签) ，它会保留并随内容一起使用和共享。 可以使用此分类生成使用情况报表并查看敏感内容的活动数据。

* **强制执行保护设置，例如加密、水印和访问限制**。 例如，用户可以对文档或电子邮件应用机密标签，并且该标签可以 [加密内容](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) 并添加 "机密" 水印。 此外，你还可以 [将敏感度标签应用到容器](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) （如 SharePoint 站点），并强制外部用户访问其包含的内容。

电子邮件和其他内容上的敏感度标签内容。 容器上的敏感度标签可以限制对容器的访问，但容器中的内容不会继承标签。 例如，用户可以从受保护的站点获取内容，下载它，然后将其共享而不受限制，除非该内容还具有敏感度标签。

 >[!NOTE]
>若要应用敏感度标签，用户必须登录到 Microsoft 工作或学校帐户。 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>创建和管理敏感度级别所需的权限

将创建敏感度标签的符合性团队的成员需要 Microsoft 365 符合性中心、Microsoft 365 安全中心或安全 & 符合性中心的权限。

默认情况下，你的租户的全局管理员可以访问这些管理中心，并可向其授予符合性监察官和其他人的访问权限，而无需为他们提供租户管理员的所有权限。对于此委派的有限管理员访问权限，请将用户添加到符合性数据管理员、合规性管理员或安全管理员角色组。

 

## <a name="determine-your-sensitivity-label-strategy"></a>确定敏感度标签策略

当你考虑管理对内容的外部访问权限时，请确定以下各项：

**适用于所有内容和容器**

* 如何定义高、中或低业务影响 (HBI，MBI，LBI) ？ 如果特定类型的内容共享不当，请考虑对组织造成的影响。

   * 内容具有特定类型的本质上 [敏感的内容](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)，如信用卡或护照号码

   * 由特定组或人员创建的内容 (例如，合规性专员、金融官员或高级管理人员) 

   * 特定库或站点中的内容。 例如，承载组织策略或私有财务数据的容器

   * 其他条件

*  (的内容的类别（例如 HBI 内容) 应限制外部用户访问？

   * 限制可能包括限制对容器的访问，以及对内容进行加密等操作。

* 对于 HBI 数据、站点或 Microsoft 365 组，应采用哪些默认值？

* 您将使用敏感性标签来 [标记和监视](https://docs.microsoft.com/microsoft-365/compliance/label-analytics?view=o365-worldwide)，而不是 [强制执行加密](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) 或 [强制实施容器访问限制](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)？

**电子邮件和内容**

* 是要 [自动将敏感度标签应用](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) 到内容还是手动应用？

   * 如果你选择手动执行此操作，是否 [建议用户应用标签](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)？

**对于容器**

* 哪些条件将确定 M365 组、团队或 SharePoint 站点是否需要使用敏感度标签来限制访问权限？

* 你是希望仅在这些容器中对内容进行标记，还是希望在 SharePoint 和 OneDrive 中 [自动标记](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) 现有文件？

有关如何使用敏感度标签的其他建议，请参阅 [敏感标签的常见方案](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide) 。

### <a name="sensitivity-labels-on-email-and-content"></a>电子邮件和内容上的敏感度标签

将敏感度标签分配到文档或电子邮件时，该标签就像是应用于可自定义、明文和持久性内容的标记。 

* **自定义** 意味着你可以创建适用于你的组织的标签，并确定应用时所发生的情况。

* **明文** 表示它是项的元数据的一部分，可由应用程序和服务读取，以便他们能够应用自己的保护措施。

* **Persistent** 表示标签和任何相关的防护会随内容漫游，并成为应用和强制实施策略的基础。

 

> [!NOTE]
> 每个内容项均可应用单个敏感度标签。


### <a name="sensitivity-labels-on-containers"></a>容器上的敏感度标签

您可以对容器（如 [Microsoft 365 组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels)、 [Microsoft 团队](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)和 [SharePoint 站点](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)）应用灵敏度标签。 将此敏感度标签应用于支持的容器时，标签会自动将分类和保护设置应用到连接的站点或组。 这些容器上的灵敏度标签可以控制容器的以下方面：

* **隐私**。 你可以选择可以查看站点的人员：特定用户、所有内部用户或任何人。

* **外部用户访问**。 控制组所有者是否可以将来宾添加到组。

* **从非管理的设备访问**。 确定非托管设备是否可以访问内容。

 

![编辑敏感度标签的屏幕截图](media/secure-external-access/8-edit-label.png)

 

将敏感度标签应用到容器（如 SharePoint 站点）时，它不会应用于其中的内容：容器上的敏感度标签控制对容器中内容的访问。 

* 如果要将标签自动应用到容器中的内容，请参阅对 [内容自动应用敏感度](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)。

* 如果希望用户能够手动将标签应用到此内容，请确保已 [在 SharePoint 和 OneDrive 中为 Office 文件启用了敏感度标签](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)。

### <a name="plan-to-implement-sensitivity-labels"></a>规划实现敏感度标签

确定要如何使用敏感性标签以及要将其应用到哪些内容和网站后，请参阅以下文档，以帮助您执行实现。

1. [敏感度标签入门](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [创建部署策略](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [创建和发布敏感度标签](https://docs.microsoft.com/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [使用敏感度标签限制对内容的访问，以应用加密](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [结合使用敏感度标签与团队、组和站点](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [在 SharePoint 和 OneDrive 中启用 Office 文件的敏感度标签](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问所需的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感性标签进行安全访问](8-secure-access-sensitivity-labels.md) (你在此处。 ) 

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
