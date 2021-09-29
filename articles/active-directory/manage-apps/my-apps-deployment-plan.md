---
title: 计划 Azure Active Directory 我的应用配置
description: 在组织中有效使用“我的应用”的计划指南。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/02/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.openlocfilehash: 6d77f3d93ad3b69f6cc8842e8a320221e533c4c7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791472"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>计划 Azure Active Directory 我的应用配置

> [!NOTE]
> 本文面向需要计划其组织的“我的应用”门户配置的 IT 专业人员。
>
> 有关最终用户文档，请参阅[从“我的应用”门户登录和启动应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

Azure Active Directory (Azure AD) 我的应用是一个基于 Web 的门户，用于启动和管理应用。 “我的应用”页为用户提供了单一位置来开始工作并查找他们有权访问的所有应用程序。 用户可通过 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 访问“我的应用”。

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>为何需配置“我的应用”

“我的应用”门户在默认情况下可供用户使用，无法关闭。 需要对它进行配置，以便用户尽可能获得最佳体验，并且门户始终有用，这十分重要。

当同时满足以下两个条件时，Azure Active Directory 企业应用程序列表中的任何应用程序都会出现：

* 应用的可见性属性设置为 true。
* 应用被分配给任何用户或组。 它会向分配的用户进行显示。

配置该门户可确保正确的人员可以轻松找到正确的应用。

### <a name="how-is-the-my-apps-portal-used"></a>如何使用“我的应用”门户？

用户可访问“我的应用”门户，以便：

* 发现并访问其组织中他们有权访问的与 Azure AD 连接的所有应用程序。
   * 最好确保将应用配置为单一登录 (SSO) 以便为用户提供最佳体验。
* 请求访问为自助服务配置的新应用。
* 创建个人应用集合。
* 在分配有组所有者权限或是被委托了用于授予应用程序访问权限的组控制权时，管理对应用的访问。

管理员可以配置：

* [同意体验](../manage-apps/configure-user-consent.md)，包括服务条款。
* [自助服务应用程序发现和访问请求](../manage-apps/access-panel-manage-self-service-access.md)。
* [应用程序集合](../manage-apps/access-panel-collections.md)。
* 应用程序的图标分配
* 应用程序的用户友好名称
* “我的应用”上显示的公司品牌打造

## <a name="plan-consent-configuration"></a>计划同意配置

### <a name="user-consent-for-applications"></a>用户同意应用程序

用户或管理员必须授予应用程序权限，然后用户才能够登录应用程序，应用程序才能够访问组织的数据。 你可以配置是否允许用户同意，以及在哪些条件下允许。 **Microsoft 建议你只允许用户同意已验证的发布者提供的应用程序。**

有关详细信息，请参阅[配置最终用户同意应用程序的方式](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>组所有者同意应用访问数据

组和团队所有者可授权应用程序（如第三方供应商发布的应用程序）访问你的组织中与组关联的数据。 若要了解详细信息，请参阅 [Microsoft Teams 中的资源特定同意](/microsoftteams/resource-specific-consent)。

你可以配置是要允许还是禁用此功能。

有关详细信息，请参阅[配置组同意权限](../manage-apps/configure-user-consent-groups.md)。

### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 主动向用户告知其体验会如何变化、何时会变化以及在需要时如何获取支持。

虽然“我的应用”通常不会形成用户问题，但做好准备十分重要。 开始之前，请为支持人员创建指南和所有资源的列表。

#### <a name="communications-templates"></a>通信模板

Microsoft 为“我的应用”提供了[用于电子邮件和其他通信的可自定义模板](https://aka.ms/APTemplates)。 你可以根据企业文化调整这些资产，以便在其他通信渠道中使用。

## <a name="plan-your-sso-configuration"></a>计划 SSO 配置

最好在“我的应用”门户中为所有应用启用 SSO，以便用户可获得无缝体验，无需输入其凭据。

Azure AD 支持多种 SSO 选项。

* 若要了解详细信息，请参阅 [Azure AD 中的单一登录选项](sso-options.md)。
* 若要详细了解如何将 Azure AD 用作应用的标识提供者，请参阅[应用程序管理快速入门系列](../manage-apps/view-applications-portal.md)。

### <a name="use-federated-sso-if-possible"></a>在可能的情况下使用联合 SSO

若要获得“我的应用”页所提供的最佳用户体验，请首先集成可用于联合身份验证单一登录 (SSO)（如 OpenID Connect 或 SAML）的云应用程序。 通过联合身份验证 SSO，用户可在登录应用程序时获得一致的一键式体验，并在配置控制方面更可靠。

若要详细了解如何为应用程序配置单一登录，请参阅[计划单一登录部署](plan-sso-deployment.md)。

### <a name="considerations-for-special-sso-circumstances"></a>特殊 SSO 情况的注意事项

> [!TIP]
> 若要获得更好的用户体验，请在应用程序支持时将联合 SSO 与 Azure AD (OpenID Connect/SAML) 结合使用，而不是使用基于密码的 SSO 和 ADFS。

若要登录基于密码的 SSO 应用程序，或是 Azure AD 应用程序代理访问的应用程序，用户需要安装并使用“我的应用”安全登录扩展。 当用户首次启动基于密码的 SSO 或应用程序代理应用程序时，系统会提示用户安装该扩展。

![屏幕截图](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

有关该扩展的详细信息，请参阅[安装“我的应用”浏览器扩展](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

如果必须集成这些应用程序，则应定义一种机制，以使用[支持的浏览器](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)大规模部署扩展。 选项包括：

* [适用于 Chrome、Firefox、Microsoft Edge 或 IE 的用户驱动型下载和配置](../user-help/my-apps-portal-end-user-access.md)
* [Internet Explorer 的 Configuration Manager](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

该扩展使用户可以从其搜索栏启动任何应用、查找对最近使用的应用程序的访问以及链接到“我的应用”页。

![“我的应用”扩展搜索的屏幕截图](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>计划移动访问

对于使用基于密码的 SSO 的应用程序，或是使用 [Microsoft Azure AD 应用程序代理](../app-proxy/application-proxy.md)进行访问的应用程序，必须使用 Microsoft Edge 移动版。 对于其他应用程序，可以使用任何移动浏览器。

### <a name="linked-sso"></a>链接 SSO

可以使用链接 SSO 选项添加应用程序。 可以配置链接到现有 Web 应用程序 URL 的应用程序磁贴。 链接 SSO 使你可以在不将所有应用程序迁移到 Azure AD SSO 的情况下，开始将用户定向到“我的应用”门户。 可以逐步迁移到配置了 Azure AD SSO 的应用程序，而不会中断用户体验。

## <a name="plan-the-user-experience"></a>计划用户体验

默认情况下，用户有权访问的所有应用程序以及为自助服务发现配置的所有应用程序都会出现在用户的“我的应用”面板中。 对于许多组织而言，这可能是非常庞大的列表，如果不进行整理，可能会成为负担

### <a name="plan-my-apps-collections"></a>计划“我的应用”集合

用户有权访问的每个 Azure AD 应用程序都会出现在“我的应用”上的“应用”集合中。 可使用集合将相关应用程序组合在一起，将它们显示在单独的选项卡上，使其更易于找到。 例如，可以使用集合为特定作业角色、任务、项目等创建应用程序的逻辑分组。

最终用户还可以通过以下方式自定义其体验

* 创建自己的应用集合。
* [隐藏和重新排列应用集合](access-panel-collections.md)。

![自助服务配置的屏幕截图](./media/my-apps-deployment-plan/collections.png)

有一个选项可用于在“我的应用”门户中隐藏应用，同时仍允许从其他位置（如 Microsoft 365 门户）进行访问。 了解详细信息：[使应用程序不出现在用户在 Azure Active Directory 中的体验中](hide-application-from-user-portal.md)。

> [!IMPORTANT]
> 通过“我的应用”只能访问 950 个用户有权访问的应用。 这包括用户或管理员隐藏的应用。

### <a name="plan-self-service-group-management-membership"></a>计划自助服务组管理成员身份

可以在 Azure AD 中允许用户创建和管理他们自己的安全组或 Microsoft 365 组。 组的所有者可以批准或拒绝成员身份请求，并委托对组成员身份的控制。 自助服务组管理功能不可用于启用了邮件的安全组或通讯组列表。

若要计划自助服务组成员身份，请确定是允许组织中的所有用户创建和管理组，还是只允许部分用户。 如果允许部分用户，则需要设置在其中添加这些人员的组。

有关启用这些方案的详细信息，请参阅[在 Azure Active Directory 中设置自助服务组管理](../enterprise-users/groups-self-service-management.md)。

### <a name="plan-self-service-application-access"></a>计划自助服务应用程序访问

可以允许用户通过“我的应用”面板发现应用程序并请求访问应用程序。 为此，必须首先

* 启用自助服务组管理
* 为 SSO 启用应用
* 创建组以进行应用程序访问

![“我的应用”自助服务配置的屏幕截图](./media/my-apps-deployment-plan/my-apps-self-service.png)

当用户请求访问权限时，他们会请求访问基础组，组所有者可以委托权限来管理组成员身份，从而管理应用程序访问。 审批工作流可用于明确批准访问应用程序。 如果有挂起的应用程序访问请求，则作为审批者的用户会在“我的应用”门户中收到通知。

## <a name="plan-reporting-and-auditing"></a>计划报告和审核

Azure AD 提供[可提供技术和业务见解的报告]../reports-monitoring/overview-reports.md)。 与业务和技术应用程序所有者合作承担这些报告的所有权并定期使用它们。 下表提供了典型报告方案的一些示例。

| 示例| 管理风险| 提高工作效率| 管理和符合性 |
| - | - | - | -|
| 报表类型| 应用程序权限和使用情况| 帐户设置活动| 查看访问应用程序的人员 |
| 可能的操作| 审核访问权限；撤销权限| 修正任何预配错误| “撤销访问权限” |

Azure AD 将大部分审核数据保留 30 天。 数据通过 Azure 管理门户或 API 提供，可供你下载到分析系统中。

#### <a name="auditing"></a>审核

应用程序访问的审核日志在 30 天内可用。 如果组织需要更长的保留期，请将日志导出到安全信息事件和管理 (SIEM) 工具，例如 Splunk 或 ArcSight。

对于审核、报告和灾难恢复备份，记录所需的下载频率、目标系统以及负责管理每个备份的人员。 你可能不需要单独的审核和报告备份。 灾难恢复备份应是单独的实体。

## <a name="validate-your-deployment"></a>验证部署

确保对“我的应用”部署进行全面测试，并实施回滚计划。

对公司拥有的设备和个人设备执行以下测试。 这些测试用例还应反映业务用例。 下面是基于典型技术方案的一些案例。 可添加特定于需求的其他案例。

#### <a name="application-sso-access-test-case-examples"></a>应用程序 SSO 访问测试用例示例：

| 业务案例| 预期结果 |
| - | - |
| 用户登录“我的应用”门户| 用户可以登录并看到其应用程序 |
| 用户启动联合 SSO 应用程序| 用户自动登录应用程序 |
| 用户首次启动密码 SSO 应用程序| 用户需要安装“我的应用”扩展 |
| 用户后续启动密码 SSO 应用程序| 用户自动登录应用程序 |
| 用户从 Microsoft 365 门户启动应用| 用户自动登录应用程序 |
| 用户从 Managed Browser 启动应用| 用户自动登录应用程序 |

#### <a name="application-self-service-capabilities-test-case-examples"></a>应用程序自助服务功能测试用例示例

| 业务案例| 预期结果 |
| - | - |
| 用户可以管理应用程序的成员身份| 用户可以添加/删除有权访问应用的成员 |
| 用户可以编辑应用程序| 用户可以为密码 SSO 应用程序编辑应用程序描述和凭据 |

### <a name="rollback-steps"></a>回滚步骤

必须规划好当部署无法按计划进行时要怎样做。 如果 SSO 配置在部署过程中失败，则必须了解如何[排查 SSO 问题](../hybrid/tshoot-connect-sso.md)并降低对用户的影响。 在极端情况下，可能需要[回滚 SSO](plan-sso-deployment.md)。

## <a name="manage-your-implementation"></a>管理实施

使用最小特权角色在 Azure Active Directory 中完成所需任务。 [查看可用的不同角色](../roles/permissions-reference.md)，并且选择正确的角色 (role) 来满足你对此应用程序的每个角色 (persona) 的需求。 可能需要暂时应用某些角色，在部署完成后再将其删除。

| 角色| 角色| Azure AD 角色 |
| - | - | - |
| 支持管理员| 第 1 层支持| 无 |
| 标识管理员| 在问题影响 Azure AD 时进行配置和调试| 全局管理员 |
| 应用程序管理员| 在应用程序中进行用户证明，为用户配置权限| 无 |
| 基础结构管理员| 证书滚动更新所有者| 全局管理员 |
| 企业主/利益干系人| 在应用程序中进行用户证明，为用户配置权限| 无 |

可用使用 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 管理角色，以便为具有目录权限的用户提供其他的审核、控制和访问评审。

## <a name="next-steps"></a>后续步骤

[计划 Azure AD 多重身份验证部署](../authentication/howto-mfa-getstarted.md)

[计划应用程序代理部署](../app-proxy/application-proxy-deployment-plan.md)