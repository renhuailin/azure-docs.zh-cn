---
title: 部署计划 - Azure Active Directory | Microsoft Docs
description: 有关如何部署多个 Azure Active Directory 功能的指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d2a64ec05fb3f6d353374684c14733dda9b302d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443043"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署计划
是否在查找有关如何部署 Azure Active Directory (Azure AD) 功能的指南？ Azure AD 部署计划详述了成功部署常用 Azure AD 功能所需的业务价值、规划注意事项和操作过程。

在计划的任意页面，使用浏览器的“打印为 PDF”功能创建最新脱机版文档。


## <a name="deploy-authentication"></a>部署身份验证

| 功能 | 说明|
| -| -|
| [Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md)| Azure AD 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 Azure AD MFA 可使用管理员批准的身份验证方法帮助保护对数据和应用程序的访问，同时满足对简单登录过程的需求。 观看此视频，了解[如何在租户中配置和强制实施多重身份验证](https://www.youtube.com/watch?v=qNndxl7gqVM)|
| [条件访问](../conditional-access/plan-conditional-access.md)| 使用条件访问时，可以根据条件实施自动化的访问控制决策，决定谁能够访问云应用。 |
| [自助式密码重置](../authentication/howto-sspr-deployment.md)| 自助密码重置允许用户根据需要重置其密码，不需要管理员干预。 |
| [无密码](../authentication/howto-authentication-passwordless-deployment.md) | 在组织中使用 Microsoft Authenticator 应用或 FIDO2 安全密钥实施无密码身份验证 |

## <a name="deploy-application-and-device-management"></a>部署应用程序和设备管理

| 功能 | 说明|
| -| - |
| [单一登录](../manage-apps/plan-sso-deployment.md)| 进行单一登录时，用户只需登录一次，就能访问开展业务所需的全部应用和资源。 登录后，用户可以逐个访问 Microsoft Office、SalesForce、Box 和内部应用程序，而无需再次输入凭据。 |
| [我的应用](../manage-apps/my-apps-deployment-plan.md)| 为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 通过自助服务功能提高他们的工作效率，例如请求访问应用和组，或代表其他人管理对资源的访问。 |
| [设备](../devices/plan-device-deployment.md) | 本文可帮助你评估以下操作方法：将设备与 Azure AD 集成，选择实施计划，以及提供指向受支持的设备管理工具的关键链接。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合方案  

| 功能 | 说明|
| -| -|
| [AD FS 到云用户身份验证](../hybrid/migrate-from-federation-to-cloud-authentication.md)| 了解如何通过身份验证或密码哈希同步将用户身份验证从联合身份验证迁移到云身份验证。
| [Azure AD 应用程序代理](../app-proxy/application-proxy-deployment-plan.md) |现今的员工想要随时随地都能在任何设备上高效工作。 他们需要访问云中的 SaaS 应用和本地的公司应用。 Azure AD 应用程序代理支持这种强大的访问，而无需昂贵和复杂的虚拟专用网络 (VPN) 或外围子网 (DMZ)。 |
| [无缝 SSO](../hybrid/how-to-connect-sso-quick-start.md)| Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使连接到企业网络的企业设备上的用户自动登录。 启用此功能后，用户无需键入其密码即可登录到 Azure AD；通常情况下，无需键入其用户名。 此功能可让授权用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。 |

## <a name="deploy-user-provisioning"></a>部署用户预配

| 功能 | 说明|
| -| -|
| [用户预配](../app-provisioning/plan-auto-user-provisioning.md)| 可以通过 Azure AD 自动创建、维护和删除云 (SaaS) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 |
| [Cloud HR 用户预配](../app-provisioning/plan-cloud-hr-provision.md)| 预配到 Active Directory 的 Cloud HR 用户为正在进行的标识管理奠定了基础，并提高了依赖权威标识数据的业务流程的质量。 使用 Cloud HR 产品的这一功能（例如 Workday 或 Successfactors），可以通过配置将 Joiner-Mover-Leaver 流程（例如新雇佣、终止、转移）映射到 IT 预配操作（例如创建、启用、禁用等）的规则，无缝地管理员工和临时工的标识生命周期 |

## <a name="deploy-governance-and-reporting"></a>部署管理和报告

| 功能 | 说明|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) 可帮助跨 Azure AD、Azure 资源和其他 Microsoft 联机服务管理特权管理角色。 PIM 提供实时访问、请求审批工作流和完全集成的访问评审等解决方案，让用户可以实时识别、发现并防止特权角色的恶意活动。 |
| [报告和监视](../reports-monitoring/plan-monitoring-and-reporting.md)| 你的 Azure Active Directory (Azure AD) 报告和监视解决方案取决于你在法律、安全和运营等方面的要求以及你现有的环境和过程。 本文介绍各种不同的设计选项并指导你掌握正确的部署策略。 |
| [访问评审](../governance/deploy-access-reviews.md) | 访问评审是管理策略的重要组成部分，使你可以了解和管理有访问权限的人员以及他们的访问内容。 本文有助于规划和部署访问评审，以实现所需的安全和协作。 |

## <a name="include-the-right-stakeholders"></a>让合适的利益干系人参与

在开始规划新功能部署时，必须在组织中包含关键的利益干系人。 建议确定并记录满足以下各项角色的人员，并与他们共同来确定其在项目中的参与情况。  

角色可能包括 

|角色 |说明 |
|-|-|
|最终用户|代表用户群，该功能将为其实施。 经常预览试点计划中的更改。
|IT 支持经理|IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的信息。  
|标识架构师或 Azure 全局管理员|标识管理团队代表，负责定义如何根据组织中的核心标识管理基础结构实施此更改。|
|应用程序业务所有者 |受影响的应用程序的总体业务所有者，可能负责管理访问权限。还可能提供有关用户体验的信息，以及从最终用户的角度来考虑的此更改的有效性。
|安全所有者|安全团队的代表，可注销确保该计划符合组织的安全要求。|
|合规性管理器|组织中负责确保符合公司、行业或政府要求的人员。|

**职责级别包括：**

- **负责** 实施项目计划和结果 

- **批准** 项目计划和结果 

- 对项目计划和结果的 **贡献者** 

- **获知** 项目计划和结果

## <a name="best-practices-for-a-pilot"></a>关于试点的最佳做法
通过试点，你可以在面向所有人启动一项功能之前，先在一个小组中进行测试。 确保在测试过程中，组织内的每个用例都经过全面测试。 最好先针对一组特定的试点用户测试，然后再将部署整体推广到你的组织。

在最初阶段，以 IT、可用性和其他可以测试和提供反馈的合适用户为目标试点。 使用此反馈进一步改进你发送给用户的信息和指示，并深入了解你的支持人员可能发现的问题类型。 

应通过扩大目标群体的范围，将功能推广扩大到更广的用户群体。 通过[动态组成员资格](../enterprise-users/groups-dynamic-membership.md)或手动将用户添加到目标用户群这两种方式均可实现上述目标。