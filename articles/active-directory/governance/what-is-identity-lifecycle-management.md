---
title: 使用 Azure Active Directory 进行的标识生命周期管理是什么？ | Microsoft Docs
description: 提供标识生命周期管理的概述。
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
ms.openlocfilehash: c6eade29bea91a9ea4bf50835a050356991cfcf0
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428139"
---
# <a name="what-is-identity-lifecycle-management"></a>标识生命周期管理是什么？

Identity Governance 可帮助组织在以下需求之间实现平衡：工作效率 - 用户可以多快地访问所需的资源（例如在刚入职时）？ 安全性 - 用户的访问权限会不断发生怎样的变化（例如，由于该用户的雇佣状态发生变化）？

标识生命周期管理是 Identity Governance 的基石，大规模的有效监管需要将应用程序的标识生命周期管理基础结构现代化。 标识生命周期管理的目的是对整个数字标识生命周期进程进行自动化和管理。 

![云预配](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>什么是数字标识？

数字标识是关于由一个或多个计算资源使用的实体的信息 - 例如操作系统或应用程序。 这些实体可以代表人员、组织、应用程序或设备。  标识通常是通过与其相关联的特性（如名称、标识符以及用于访问管理的角色等属性）进行描述的。  这些特性可帮助系统做出决策，例如决定谁有权访问哪些内容或者谁可以使用哪个系统。  

## <a name="managing-the-lifecycle-of-digital-identities"></a>管理数字标识的生命周期

管理数字标识是一项复杂的任务，尤其是当它将相关的实际对象（例如某位组织的员工以及该员工与组织的关系）与数字表示形式相关联时。    在小型组织中，保存需要标识的人员的数字表示形式可能是一个手动过程 - 当某人被雇佣或合同工达到时，IT 专家会在目录中为他们创建帐户，并为其分配他们所需的访问权限。  但是，在中型和大型组织中，自动化可让组织更有效地进行缩放并保持标识的准确性。

在组织中建立标识生命周期管理的典型过程遵循以下步骤：

1. 确定是否已经存在组织将其视为权威的记录系统：数据源。  例如，组织可能具有 HR 系统 Workday，并且该系统在提供当前的员工列表及其某些属性（如员工姓名或部门）方面具有权威性。  或者诸如 Exchange Online 之类的电子邮件系统可能在提供员工电子邮件地址方面具有权威性。

2. 将这些系统记录与应用程序使用的一个或多个目录和数据库连接起来，并解决目录和记录系统之间的任何不一致问题。 例如，目录可能包含不再需要的过时数据，例如前员工的帐户。 

3. 确定在没有记录系统的情况下，可以使用哪些过程来提供权威信息。  例如，如果只有访问者的数字标识，但组织没有可提供给访问者的数据库，则可能需要找到另一种方法来确定何时不再需要访问者的数字标识。

4. 将从记录系统或其他进程进行的更改配置为复制到每个需要更新的目录或数据库。

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>用于表示员工和具有组织关系的其他人员的标识生命周期管理

为员工或具有组织关系的其他人员（如合同工或学生）计划标识生命周期管理时，许多组织会对“加入、移动和离开”过程进行建模。  其中包括：
    
   - 加入 - 当个人需要访问权限时，这些应用程序需要标识，如果此人还没有标识，则可能需要创建一个新的数字标识
   - 移动 - 当个人在边界之间移动时，需要向其数字标识添加额外的访问授权或从中删除访问授权
   - 离开 - 当个人不需要访问权限时，可能需要删除访问权限，随后，除了审计或取证目的之外，应用程序可能不再需要标识

例如，如果一个新员工加入了你的组织，并且该员工从未加入过你的组织，则该员工需要一个新的数字标识，以 Azure AD 中的用户帐户表示。  创建此帐户属于“加入”过程，如果有记录系统（例如可以表示新员工何时入职的 Workday），则会自动执行此操作。  之后，如果组织中的某员工有职位变动，比如从销售部转到市场营销部，这就是“移动”过程。  在此过程中，需要删除他们在销售组织中的访问权限，他们已不再需要这些权限，然后授予他们当前所需的市场营销组织的权限。

## <a name="identity-lifecycle-management-for-guests"></a>来宾的标识生命周期管理

对于来宾和其他用户，也需要执行类似的过程。  Azure AD 权利管理利用 Azure AD 企业对企业 (B2B) 提供与组织外部的需要访问组织资源的人员进行协作所需的生命周期控制。 使用 Azure AD B2B 解决方案时，外部用户会向其主目录进行身份验证，但在你的目录中会有一个表示形式。 可以通过你的目录中的该表示形式为用户分配资源访问权限。  权利管理使组织外部的人员能够请求访问权限，并根据需要为他们创建数字标识。 当用户失去访问权限时，会自动删除这些数字标识。  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>Azure AD 如何自动化标识生命周期管理？

Azure AD 当前提供以下功能：

* 可使用 [HR 驱动的预配](what-is-hr-driven-provisioning.md)在 Azure AD 和 Active Directory 中自动创建和更新代表员工的用户
* 可使用[目录间预配](what-is-inter-directory-provisioning.md)在 Azure AD 中自动创建和维护 Active Directory 中已存在的用户
* 可使用[动态组](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups)基于用户属性将用户自动分配到组，并且可使用[权利管理](entitlement-management-scenarios.md)和 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 根据请求将用户分配到组、Teams、Azure AD 角色、Azure 资源角色和 SharePoint Online 站点
* 可使用[应用预配](what-is-app-provisioning.md)将用户的更新自动发送给更多应用程序

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [在 Azure AD 权利管理中管理外部用户的访问权限](./entitlement-management-external-users.md)
- [HR 驱动的预配是什么？](what-is-hr-driven-provisioning.md)
- [应用预配是什么？](what-is-app-provisioning.md)
- [目录间预配是什么？](what-is-inter-directory-provisioning.md)