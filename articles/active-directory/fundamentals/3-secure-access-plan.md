---
title: 为外部访问 Azure Active Directory 创建一份安全性计划
description: 规划从外部访问组织资源的安全性。
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
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554014"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3.为外部访问创建安全性计划 

现在你已[确定了外部访问所需的安全态势](1-secure-access-posture.md)，并且[发现了当前的协作状态](2-secure-access-current-state.md)，你可以创建外部用户安全性和治理计划。 

此计划应记录以下内容：

* 应分组访问的应用程序和其他资源。

* 外部用户的适当登录条件。 其中可能包括设备状态、登录位置、客户端应用程序要求和用户风险。

* 有关何时查看和删除访问权限的业务策略。 

* 要进行分组以获得相似待遇的用户群体。

记录这些方面后，可以使用 Microsoft 或任何其他标识提供者 (IdP) 提供的标识和访问管理策略来实现此计划。

## <a name="document-resources-to-be-grouped-for-access"></a>记录要分组访问的资源

将资源分组以进行访问有多种方法。 

* Microsoft Teams 将文件、对话线程和其他资源分为一组。 应为 Microsoft Teams 制定外部访问策略。 请参阅[保护对 Teams、OneDrive 和 SharePoint 的访问](9-secure-access-teams-sharepoint.md)。

* 通过权利管理访问包，可以将要授予访问权限的应用程序和其他资源放入单个包中。 

* 条件访问策略可应用于多达 250 个具有相同访问要求的应用程序。

无论你将如何管理访问，都必须记录应将哪些应用程序分为一组。 注意事项应包括：

* **风险状况**。 如果坏人获得应用程序的访问权限，你的业务将面临什么风险？ 请考虑将每个应用程序编码为高、中或低风险。 将高风险应用程序与低风险应用程序分为一组时，请谨慎操作。 

   * 记录不应与外部用户共享的应用程序。

* **合规性框架**。 如果任何合规性框架必须符合应用程序要求，该怎么办？ 有哪些访问和审查要求？

* **用于特定作业角色或部门的应用程序**。 有没有因为特定作业角色或部门的所有用户都需要访问权限，而应分组的应用程序？

* **专注于协作的应用程序**。 外部用户应该能够访问哪些专注于协作的应用程序？ Microsoft Teams 和 SharePoint 可能需要可供访问。 对于 Office 365 中的生产力应用程序（如 Word 和 Excel），是外部用户自带许可证，还是需要授予他们许可并提供访问权限？

在每次对想要允许外部用户访问的应用程序和资源进行分组时，请记录以下内容：

* 组的描述性名称，例如 *High_Risk_External_Access_Finance*。 

* 组中所有应用程序和资源的完整列表。

* 应用程序和资源所有者和联系信息。

* 访问权限由 IT 控制还是委派给企业主。

* 访问的任何先决条件，例如完成背景检查或训练。

* 访问资源的任何合规性要求。

* 任何其他挑战，例如要求对特定资源进行多重身份验证。

* 审查访问的频率、审查者以及访问情况的记录位置。

对于内部访问，也可以并且也应该完成这种类型的治理计划。

## <a name="document-sign-in-conditions-for-external-users"></a>记录外部用户的登录条件。

在计划中，必须确定外部用户访问资源时的登录要求。 登录要求通常基于资源的风险状况以及用户登录的风险评估。

登录条件在 [Azure AD 条件访问](../conditional-access/overview.md)中配置，包含条件和结果。 例如，何时请求多重身份验证

**基于资源风险的登录条件。**

| 应用程序风险状况| 请考虑这些用于触发多重身份验证的策略 |
| - |-|
| 低风险| 要求对特定应用程序集执行多重身份验证 |
| 中风险| 要求当存在其他风险时执行多重身份验证 |
| 高风险| 要求对外部用户始终执行多重身份验证 |


现在，你可以[对租户中的 B2B 用户强制实施多重身份验证](../external-identities/b2b-tutorial-require-mfa.md)。 

**基于用户和设备的登录条件**。

| 用户或登录风险| 请考虑以下策略 |
| - | - |
| 设备| 要求符合的设备 |
| 移动应用| 要求已批准的应用 |
| 标识保护显示高风险| 要求用户更改密码 |
| 网络位置| 要求从特定 IP 地址范围登录高度机密项目 |

现在，若要将设备状态用作策略的输入，必须将设备注册或加入到你的租户。 

可以使用[标识保护的基于风险的策略](../conditional-access/howto-conditional-access-policy-risk.md)。 但是，必须在用户的主租户中缓解问题。

对于[网络位置](../conditional-access/howto-conditional-access-policy-location.md)，可以对你拥有的任何 IP 地址范围进行访问权限限制。 如果只希望外部合作伙伴在你组织的现场访问应用程序，则可以使用此功能。

[详细了解条件访问策略](../conditional-access/overview.md)。

## <a name="document-access-review-policies"></a>记录访问评审策略

请记录在需要查看对资源的访问权限时，以及在需要删除外部用户的帐户访问权限时适用的业务策略。 这些决策的输入可能包括：

* 在任何合规性框架中详细说明的要求。

* 内部业务策略和流程

* 用户行为

尽管你的策略将根据你的需求进行高度自定义，但还请考虑以下事项：

* **权利管理访问评审**。 使用权利管理中的功能可以

   * [自动使访问包过期](../governance/entitlement-management-access-package-lifecycle-policy.md)，从而使外部用户访问所包含的资源。

   * 设置访问评审的[必需评审频率](../governance/entitlement-management-access-reviews-create.md)。

   * 如果使用[连接的组织](../governance/entitlement-management-organization.md)对来自单个合作伙伴的所有用户进行分组，请安排与企业主和合作伙伴代表进行定期评审。

* **Microsoft 365 组**。 为外部用户被邀请到的 Microsoft 365 组设置[组过期策略](/microsoft-365/solutions/microsoft-365-groups-expiration-policy)。 

* **其他选项**。 如果外部用户具有权利管理访问包或 Microsoft 365 组之外的访问权限，请设置业务流程以查看应何时使帐户处于非活动状态或删除帐户。 例如：

   * 删除 90 天内未登录的任何帐户的登录功能。

   * 在每个项目结束时与外部用户一起评估访问需求并采取措施。

 

## <a name="determine-your-access-control-methods"></a>确定访问控制方法

你已了解哪些内容要控制访问权限、如何将这些资产分组以进行常见访问，以及要求的登录和访问评审策略，现在可以决定如何实现计划。 

某些功能（例如[权利管理](../governance/entitlement-management-overview.md)）仅适用于 Azure AD Premium 2 (P2) 许可证。 Microsoft 365 E5 和 Office 365 E5 许可证包括 Azure AD P2 许可证。 

Microsoft 365、Office 365 和 Azure AD 的其他组合也能实现一些用于管理外部用户的功能。 有关详细信息，请参阅[信息保护](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance)。

> [!NOTE]
> 许可证按用户分配。 因此，你可以在 Azure AD P2 或 Microsoft 365 E5 级别向特定用户（包括管理员和企业主）委托访问控制，而无需为所有用户启用这些许可证。 前 50,000 个外部用户免费。 如果没有为其他内部用户启用 P2 许可证，则他们将无法使用权利管理功能（如访问包）。 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>使用 Azure AD P2 和 Microsoft/Office 365 E5 控制访问权限
Azure AD P2 和 Microsoft 365 E5 具有全套安全性和治理工具。

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>预配、登录、评审访问和取消预配。 粗体项为首选方法

| 功能| 预配外部用户| 强制执行登录要求。| 评审访问权限| 取消预配访问权限 |
| - | - | - | - | - |
| Azure AD B2B 协作| 通过电子邮件、OTP、自助服务邀请| | **按合作伙伴定期评审**| 删除帐户<br>限制登录 |
| 权利管理| **通过分配或自助服务访问添加用户**| | 访问评审|**访问包过期或从访问包中删除**|
| Office 365 组| | | 评审组成员身份| 组过期或删除组<br> 从组中删除 |
| Azure AD 安全组| | **条件访问策略**（根据需要将外部用户添加到安全组）| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>访问资源。 粗体项为首选方法

|功能 | 应用和资源访问| SharePoint 和 OneDrive 访问| Teams 访问| 电子邮件和文档的安全性 |
| - |-|-|-|-|
| 权利管理| **通过分配或自助服务访问添加用户**| **访问包**| **访问包**|  |
| Office 365 组| | 对组中包含的站点（以及相关内容）的访问权限| 对组中包含的团队（以及相关内容）的访问权限|  |
| 敏感度标签| | **手动和自动对访问进行分类和限制**| **手动和自动对访问进行分类和限制**| **手动和自动对访问进行分类和限制** |
| Azure AD 安全组| **访问包中未包含用于访问的条件访问策略**| | |  |


### <a name="entitlement-management"></a>权利管理 

[权利管理访问包](../governance/entitlement-management-access-package-create.md)支持预配和取消预配对组和团队、应用程序，以及 SharePoint 站点的访问权限。 你可以定义允许哪些连接的组织访问、是否允许自助服务请求，以及授予访问权限所需的审批工作流（如果有）。 为确保访问权限存在的时间超过所需时间，你可以为每个访问包定义过期策略和访问评审。 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>使用 Azure AD P1 和 Microsoft/Office 365 E3 控制访问权限
可以通过 Azure AD P1 和 Microsoft 365 E3 实现可靠的治理

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>预配、登录、评审访问和取消预配


|功能 | 预配外部用户| 强制执行登录要求| 评审访问权限| 取消预配访问权限 |
| - |-|-|-|-|
| Azure AD B2B 协作| **通过电子邮件、OTP、自助服务邀请**| 直接 B2B 联合| **按合作伙伴定期评审**| 删除帐户<br>限制登录 |
| Microsoft 或 Office 365 组| | | | 组过期或删除组。<br>从组中删除。 |
| 安全组| | **将外部用户添加到安全组（组织、团队、项目等）**| |  |
| 条件访问策略| | **登录用于外部用户的条件访问策略**| |  |


 ### <a name="access-to-resources"></a>访问资源。

|功能 | 应用和资源访问| SharePoint 和 OneDrive 访问| Teams 访问| 电子邮件和文档的安全性 |
| - |-|-|-|-|
| Microsoft 或 Office 365 组| | **对组中包含的站点（以及相关内容）的访问权限**|**对 Microsoft 365 组中包含的团队（以及相关内容）的访问权限**|  |
| 敏感度标签| | 手动分类和限制访问| 手动分类和限制访问。| 手动分类以限制和加密 |
| 条件访问策略| 用于访问控住的条件访问策略| | |  |
| 其他方法| | 使用安全组精确限制 SharePoint 站点访问。<br>禁止直接共享。| **限制从团队内发出外部邀请**|  |


### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [了解当前状态](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)（本文）。

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)