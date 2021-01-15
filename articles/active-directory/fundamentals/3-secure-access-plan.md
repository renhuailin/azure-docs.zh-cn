---
title: 为外部访问创建安全计划 Azure Active Directory
description: 规划对组织资源的外部访问的安全性。
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
ms.openlocfilehash: 40738f8fcb14c48ccfe3bc7869e5176c4ab63165
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222324"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. 创建外部访问安全计划 

现在，你已 [确定外部访问所需的安全状态安全状况](1-secure-access-posture.md) 并 [发现当前的协作状态](2-secure-access-current-state.md)，你可以创建外部用户安全和监管计划。 

此计划应记录以下内容：

* 应进行分组以进行访问的应用程序和其他资源。

* 适用于外部用户的适当登录条件。 其中可能包括设备状态、登录位置、客户端应用程序要求和用户风险。

* 有关何时查看和删除访问权限的业务策略。 

* 要进行分组和处理的用户填充。

记录这些方面后，可以使用 Microsoft 或任何其他标识提供者 (IdP) 中的标识和访问管理策略来实现此计划。

## <a name="document-resources-to-be-grouped-for-access"></a>记录要进行分组以进行访问的资源

有多种方法可对资源进行访问。 

* Microsoft 团队在一个位置对文件、对话线程和其他资源进行分组。 应为 Microsoft 团队制定外部访问策略。 请参阅 [安全访问团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)。

* 通过授权管理包，您可以创建可向其授予访问权限的单个应用程序和其他资源包。 

* 条件性访问策略最多可应用于具有相同访问要求的250个应用程序。

但是，你将管理访问权限，你必须记录哪些应用程序应该组合在一起。 注意事项应包括：

* **风险配置文件**。 如果错误的参与者获取了对应用程序的访问权限，你的业务有什么风险？ 请考虑将每个应用程序编码为高、中或低风险。 对高风险应用程序进行分组时要格外小心。 

   * 记录永远也不应与外部用户共享的应用程序。

* **符合性框架**。 如果任何符合性框架必须符合应用程序，该怎么办？ 访问和审查要求有哪些？

* **特定作业角色或部门的应用程序**。 是否应该对应用程序进行分组，因为特定作业角色或部门中的所有用户都需要访问权限？

* **专注于协作的应用程序**。 外部用户应能够访问哪些关注协作的应用程序？ 可能需要访问 Microsoft 团队和 SharePoint。 对于 Office 365 内的工作效率应用程序（如 Word 和 Excel），外部用户是否会引入自己的许可证，或者你是否需要对其进行授权并提供访问权限？

对于要使其可供外部用户访问的每个应用程序和资源分组，请记录以下内容：

* 组的描述性名称，例如 *High_Risk_External_Access_Finance*。 

* 组中所有应用程序和资源的完整列表。

* 应用程序和资源所有者和联系信息。

* 访问由其控制还是被委派给业务所有者。

* 用于访问的任何先决条件，例如完成背景检查或培训。

* 访问资源的任何符合性要求。

* 任何其他挑战，例如需要对特定资源进行多重身份验证。

* 查看访问的频率、作者和记录位置。

这种类型的监管计划还可以以及还应为内部访问完成。

## <a name="document-sign-in-conditions-for-external-users"></a>外部用户的文档登录条件。

作为你的计划的一部分，你必须确定外部用户访问资源时的登录要求。 登录要求通常基于资源的风险配置文件，以及用户登录时的风险评估。

登录条件是在 [Azure AD 条件访问](../conditional-access/overview.md) 中配置的，由条件和结果组成。 例如，何时需要多重身份验证

**基于资源风险的登录条件。**

| 应用程序风险配置文件| 考虑这些用于触发多重身份验证的策略 |
| - |-|
| 低风险| 需要对特定应用程序集进行 MFA |
| Med-v 风险| 存在其他风险时要求 MFA |
| 高风险| 要求对外部用户进行 MFA 始终 |


现在，你可以 [在租户中对 B2B 用户强制实施多重身份验证](https://docs.microsoft.com/azure/active-directory/external-identities/b2b-tutorial-require-mfa)。 

**基于用户和设备的登录条件**。

| 用户或登录风险| 考虑这些策略 |
| - | - |
| 设备| 要求符合的设备 |
| 移动应用| 需要已批准的应用 |
| 标识保护显示高风险| 要求用户更改密码 |
| 网络位置| 需要从特定 IP 地址范围登录到高度机密项目 |

现在，若要使用设备状态作为策略的输入，设备必须已注册或已加入到你的租户。 

可以使用[基于身份保护风险的策略](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)。 但是，必须在用户的主租户中缓解问题。

对于 [网络位置](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location)，你可以将访问权限限制为你拥有的任何 IP 地址范围。 如果你只希望外部合作伙伴在你的组织中的站点上访问应用程序，则可以使用此。

[了解有关条件性访问策略的详细信息](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。

## <a name="document-access-review-policies"></a>文档访问评审策略

当你需要查看对资源的访问权限时，以及当你需要为外部用户删除帐户访问权限时，请记录你的业务策略。 这些决策的输入可能包括：

* 任何符合性框架中详细说明的要求。

* 内部业务策略和流程

* 用户行为

尽管你的策略将根据你的需求进行高度自定义，但请考虑以下事项：

* 授权 **管理访问评审**。 使用权限管理中的功能来

   * [自动使访问包过期](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-lifecycle-policy)，并因此外部用户访问所包含的资源。

   * 为访问评审设置 [所需的审阅频率](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-reviews-create) 。

   * 如果你正在使用 [连接的组织](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-organization) 来对单个合作伙伴的所有用户进行分组，请按业务所有者和合作伙伴代表计划定期审查。

* **Microsoft 365 组**。 为外部用户受邀 Microsoft 365 组设置 [组过期策略](https://docs.microsoft.com/microsoft-365/solutions/microsoft-365-groups-expiration-policy?view=o365-worldwide) 。 

* **其他选项**。 如果外部用户在授权管理访问包或 Microsoft 365 组外具有访问权限，则设置业务流程，以查看应将帐户设为非活动或删除的时间。 例如：

   * 删除任何未登录到90天的帐户的登录功能。

   * 评估访问需求，并在每个项目的结束时执行操作。

 

## <a name="determine-your-access-control-methods"></a>确定访问控制方法

现在，你已了解要控制其访问权限的方式，如何将这些资产划分为常见访问权限以及所需的登录和访问审核策略，你可以决定如何实现计划。 

某些功能（例如， [权利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)）仅适用于 Azure AD Premium 2 (P2) 许可证。 Microsoft 365 E5 和 Office 365 E5 许可证包括 Azure AD P2 许可证。 

Microsoft 365、Office 365 和 Azure AD 的其他组合也能实现某些用于管理外部用户的功能。 有关详细信息，请参阅 [信息保护](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) 。

> [!NOTE]
> 许可证按用户进行。 因此，你可以让特定用户（包括管理员和企业所有者委托访问控制）位于 Azure AD P2 或 Microsoft 365 E5 级别，而无需为所有用户启用这些许可证。 前50000个外部用户是免费的。 如果没有为其他内部用户启用 P2 许可证，则他们将无法使用权限管理功能，如访问包。 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>使用 Azure AD P2 和 Microsoft/Office 365 E5 控制访问权限
Azure AD P2 和 Microsoft 365 E5 具有全套安全和管理工具。

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>预配、登录、查看访问和取消预配。 粗体条目为首选方法

| Feature| 预配外部用户| 强制登录请求。| 评审访问权限| 取消设置访问权限 |
| - | - | - | - | - |
| Azure AD B2B 协作| 通过电子邮件、OTP、自助服务邀请| | **每个合作伙伴的定期审查**| 删除帐户<br>限制登录 |
| 权利管理| **通过分配或自助服务访问添加用户**| | 访问评审|**访问包的过期时间或从中删除**|
| Office 365 组| | | 查看组成员身份| 组过期或删除<br> 删除窗体组 |
| Azure AD 安全组| | **条件性访问策略** (根据需要将外部用户添加到安全组) | |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>对资源的访问权限。 粗体条目为首选方法

|Feature | 应用 & 资源访问| SharePoint & OneDrive 访问权限| 团队访问| 电子邮件 & 文档安全性 |
| - |-|-|-|-|
| 权利管理| **通过分配或自助服务访问添加用户**| **访问包**| **访问包**|  |
| Office 365 组| | 访问站点 ()  (和关联的内容) 组中包含| ) 组中包含的对团队 (和关联内容的访问权限|  |
| 敏感度标签| | **手动并自动分类和限制访问**| **手动并自动分类和限制访问**| **手动并自动分类和限制访问** |
| Azure AD 安全组| **访问包中未包含访问的条件访问策略**| | |  |


### <a name="entitlement-management"></a>权利管理 

使用授权[管理访问包](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-create)可以对组和团队、应用程序和 SharePoint 站点进行预配和取消预配。 你可以定义允许哪些连接的组织访问、是否允许自助服务请求，以及在任何授予访问权限的)  (需要哪些批准工作流。 若要确保访问不会长时间保持不变，你可以为每个访问包定义过期策略和访问评审。 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>使用 Azure AD P1 和 Microsoft/Office 365 E3 控制访问权限
可以通过 Azure AD P1 和 Microsoft 365 E3 实现强健的管理

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>预配、登录、查看访问和取消预配


|Feature | 预配外部用户| 强制登录要求| 评审访问权限| 取消设置访问权限 |
| - |-|-|-|-|
| Azure AD B2B 协作| **通过电子邮件、OTP、自助服务邀请**| 直接 B2B 联合| **每个合作伙伴的定期审查**| 删除帐户<br>限制登录 |
| Microsoft 或 Office 365 组| | | | 组过期或删除。<br>从组中删除。 |
| 安全组| | **将外部用户添加到安全组 (组织、团队、项目等 )**| |  |
| 条件访问策略| | **外部用户的登录条件访问策略**| |  |


 ### <a name="access-to-resources"></a>对资源的访问权限。

|Feature | 应用 & 资源访问| SharePoint & OneDrive 访问权限| 团队访问| 电子邮件 & 文档安全性 |
| - |-|-|-|-|
| Microsoft 或 Office 365 组| | **) 组 (和关联的内容中包含对站点 (的访问)**|**Microsoft 365 组 (和关联的内容中包含的团队的访问权限)**|  |
| 敏感度标签| | 手动分类和限制访问| 手动分类和限制访问。| 手动分类以限制和加密 |
| 条件访问策略| 访问控制的条件性访问策略| | |  |
| 其他方法| | 使用安全组限制 SharePoint 站点访问的粒度。<br>禁止直接共享。| **限制团队内的外部邀请**|  |


### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. 在此处[创建调控计划](3-secure-access-plan.md) (。 ) 

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)