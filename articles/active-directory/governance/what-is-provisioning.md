---
title: 使用 Azure Active Directory 预配的是什么？ | Microsoft Docs
description: 概述标识预配和 ILM 场景。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: compliance
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca42c2b4a817e01bd6ef0e2875c7516a2103b880
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428152"
---
# <a name="what-is-provisioning"></a>什么是预配？

预配和取消预配是确保多个系统之间数字标识一致性的过程。  这些过程通常作为[标识生命周期管理](what-is-identity-lifecycle-management.md)的一部分加以利用。

**预配** 是在目标系统中基于特定条件创建标识的过程。  **取消预配** 是在不再满足条件时从目标系统中删除标识的过程。 **同步** 是使预配的对象保持最新状态的过程，目的是使源对象和目标对象相似。

例如，当某个新员工加入组织时，会在人力资源 (HR) 系统中输入该员工。  此时，从 HR 到 Azure Active Directory (Azure AD) 的预配可在 Azure AD 中创建相应的用户帐户 。 查询 Azure AD 的应用程序可以查看该新员工的帐户。  如果存在不使用 Azure AD 的应用程序，则从 Azure AD 预配到这些应用程序的数据库，确保用户能够访问用户需要访问的所有应用程序 。  这一过程使用户能够开始工作，且在第一天就有权访问所需的应用程序和系统。  同样，当用户属性（例如部门或雇用状态）在 HR 系统中更改时，这些更新将从 HR 系统同步到 Azure AD，以及其他应用程序和目标数据库，确保一致性。

Azure AD 目前提供三个自动预配领域。  它们具有以下特点：  

- 通过 [HR 驱动的预配](#hr-driven-provisioning)将记录从外部非目录权威系统预配到 Azure AD  
- 通过[应用预配](#app-provisioning)从 Azure AD 预配到应用程序  
- 通过[目录间预配](#inter-directory-provisioning)在 Azure AD 和 Active Directory 域服务之间预配 

![标识生命周期管理](media/what-is-provisioning/provisioning.png)

## <a name="hr-driven-provisioning"></a>HR 驱动的预配

![HR 预配](media/what-is-provisioning/cloud-2a.png)

从 HR 到 Azure AD 的预配涉及到创建对象，通常需创建表示每个员工的用户标识，但在某些情况下，需创建表示部门或其他结构的其他对象，具体取决于你的 HR 系统中的信息。  

最常见的场景是，当新员工加入公司时，将其输入到 HR 系统中。  发生这种情况时，它们会自动预配为 Azure AD 中的新用户，而无需对每个新员工进行管理干预。  通常情况下，从 HR 进行预配可能涵盖以下场景。

- **招聘新员工** - 将新员工添加到 HR 系统后，Active Directory、Azure AD、（可选）Azure AD 支持的其他应用程序的目录中会自动创建一个用户帐户，并将电子邮件地址写回到 HR 系统。
- **员工属性和个人资料更新** - 在 HR 系统中更新员工记录（例如其姓名、职称或上司）后，Active Directory、Azure AD、（可选）Azure AD 支持的其他应用程序中会自动更新相应员工的用户帐户。
- **员工离职** - 当员工在 HR 中为离职状态时，会自动阻止其用户帐户登录或将该账户从 Active Directory、Azure AD 和其他应用程序中删除。
- **员工重新雇用** - 当员工在云 HR 中的状态为重新雇用时，他们的旧帐户可以自动重新激活或重新预配（具体取决于你的首选项）。

使用 Azure AD 进行的 HR 驱动的预配可用于以下三种部署选项：

1. 适用于只有 Workday 或 SuccessFactors 订阅且不使用 Active Directory 的组织
1. 适用于只有 Workday 或 SuccessFactors 订阅且同时具有 Active Directory 和 Azure AD 的组织
1. 适用于具有多个 HR 系统或一个本地 HR 系统（如 SAP、Oracle eBusiness 或 PeopleSoft）的组织

有关详细信息，请参阅[什么是 HR 驱动的预配？](what-is-hr-driven-provisioning.md)

## <a name="app-provisioning"></a>应用预配

![应用预配](media/what-is-provisioning/cloud-3b.png)

在 Azure AD 中， **[应用预配](../app-provisioning/user-provisioning.md)** 一词是指在用户需要访问的应用程序中自动创建用户标识的副本，因为应用程序有自己的数据存储，不同于 Azure AD 或 Active Directory。 除了创建用户标识外，应用预配还包括在用户状态或角色发生更改时，维护和删除这些应用中的用户标识。 常见场景包括将 Azure AD 用户预配到 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../saas-apps/salesforce-provisioning-tutorial.md) 和 [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) 等应用程序，因为这些应用程序都有各自的用户存储库，不同于 Azure AD。

有关详细信息，请参阅[什么是应用预配？](what-is-app-provisioning.md)

## <a name="inter-directory-provisioning"></a>目录间预配

![目录间预配](media/what-is-provisioning/cloud-4a.png)

许多组织同时依赖于 Active Directory 和 Azure AD，并且可能会将应用程序连接到 Active Directory，例如本地文件服务器。

许多组织过去都已在本地部署了 HR 驱动的预配，他们可能已经在 Active Directory 中为其所有员工提供了用户标识。   目录间预配的最常见场景是将已在 Active Directory 中的用户预配到 Azure AD 中。  这一预配通常通过 Azure AD Connect 同步或 Azure AD Connect 云预配来实现。 

此外，组织可能还希望从 Azure AD 预配到本地系统。  例如，组织可能已将来宾置于 Azure AD 目录中，但这些来宾将需要通过应用代理访问基于 Windows 集成身份验证 (WIA) 的本地 Web 应用程序。  这就需要为 Azure AD 中的用户预配本地 AD 帐户。

有关详细信息，请参阅[什么是目录间预配？](what-is-inter-directory-provisioning.md)

 
## <a name="next-steps"></a>后续步骤 
- [标识生命周期管理是什么？](what-is-identity-lifecycle-management.md)
- [HR 驱动的预配是什么？](what-is-hr-driven-provisioning.md)
- [应用预配是什么？](what-is-app-provisioning.md)
- [目录间预配是什么？](what-is-inter-directory-provisioning.md)