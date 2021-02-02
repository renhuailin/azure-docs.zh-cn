---
title: 计划 Azure Active Directory 我的应用配置
description: 有效地在你的组织中使用我的应用的规划指南。
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 13fe9c52312ba6053a6093ec51d42f05a44627e0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258280"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>计划 Azure Active Directory 我的应用配置

> [!NOTE]
> 本文面向需要计划其组织的 "我的应用" 门户配置的 IT 专业人员。 有关如何使用我的应用和集合的最终用户的信息，请参阅 [从 "我的应用" 门户登录并启动应用](../user-help/my-apps-portal-end-user-access.md)。
>
> **有关最终用户的文档，请参阅 [从 "我的应用" 门户登录并启动应用](../user-help/my-apps-portal-end-user-access.md)**。

Azure Active Directory (Azure AD) 我的应用程序是一个基于 web 的门户，用于启动和管理应用程序。 "我的应用" 页为用户提供了一个开始工作的位置，并查找他们有权访问的所有应用程序。 用户访问我的应用程序 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 。

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>为什么要配置我的应用

默认情况下，"我的应用" 门户可供用户使用，并且不能关闭。 必须对其进行配置，使其尽可能获得最佳体验，而门户却始终有用。 

当同时满足以下两个条件时，将显示 Azure Active Directory 企业应用程序列表中的任何应用程序：

* 应用的 "可见性" 属性设置为 "true"。 

* 此应用将分配给任何用户或组。 它对于分配的用户是显示的。

配置门户可确保适当的人员能够轻松找到正确的应用。

 
### <a name="how-is-the-my-apps-portal-used"></a>我的应用门户如何使用？

用户访问 "我的应用" 门户以：

* 发现并访问其组织 Azure AD 连接的所有应用程序的应用程序。

   * 最好确保将应用配置为单一登录 (SSO) 以便为用户提供最佳体验。

* 请求访问配置为自助服务的新应用程序。

* 创建应用的个人集合。

* 为其他人分配对应用程序的组所有者或委派控制权限的访问权限，以用于向应用程序授予对应用程序 () 的访问权限。

管理员可以配置：

* [许可经验](../manage-apps/configure-user-consent.md)  包括服务条款。

* [自助服务应用程序发现和访问请求](../manage-apps/access-panel-manage-self-service-access.md)。

* [应用程序的集合](../manage-apps/access-panel-collections.md)。

* 向应用程序分配图标

* 应用程序的用户友好名称

* 我的应用上显示的公司品牌

 

## <a name="plan-consent-configuration"></a>计划同意配置

有两种类型的同意：用户同意和同意访问数据的应用。

![同意配置的屏幕截图](./media/my-apps-deployment-plan/my-apps-consent.png)

### <a name="user-consent-for-applications"></a>应用程序的用户同意 

用户或管理员必须同意任何应用程序的使用条款和隐私策略。 您必须决定用户或仅管理员是否可以同意应用程序。 **我们建议，如果你的业务规则允许，你可以使用管理员同意来控制租户中的应用程序**。

若要使用管理员许可，你必须是组织的全局管理员，并且应用程序必须是：

* 已在你的组织中注册。

* 已在另一 Azure AD 组织中注册，并且之前至少被一个用户同意。

如果你想要允许用户同意，你必须决定是要让他们同意任何应用，还是仅在特定情况下。

有关详细信息，请参阅 [在 Azure Active Directory 中配置最终用户同意应用程序的方式。](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>访问数据的应用的组所有者同意

确定 Azure AD 安全组或 M365 组的所有者是否能够同意应用程序访问其拥有的组的数据。 你可以禁止，允许所有组所有者，或仅允许一部分组所有者。

有关详细信息，请参阅 [配置组同意权限](../manage-apps/configure-user-consent-groups.md)。

然后，在 Azure 门户中配置 [用户和组的所有者同意设置](https://portal.azure.com/) 。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动向用户通知其体验将发生更改的方式和时间，以及如何在需要时获得支持。

尽管我的应用程序通常不会创建用户问题，但必须做好准备。 开始之前，请为支持人员创建指南和所有资源的列表。

#### <a name="communications-templates"></a>通信模板

Microsoft 为应用程序提供 [电子邮件和其他通信的可自定义模板](https://aka.ms/APTemplates) 。 你可以根据你的企业文化调整这些资产，以便在其他通信通道中使用。

 

## <a name="plan-your-sso-configuration"></a>规划 SSO 配置

最好在 "我的应用" 门户中为所有应用启用 SSO，使用户无需输入其凭据即可实现无缝体验。

Azure AD 支持多种 SSO 选项。 

* 若要了解详细信息，请参阅 [Azure AD 中的单一登录选项](sso-options.md)。

* 若要了解有关使用 Azure AD 作为应用的标识提供者的详细信息，请参阅 [应用程序管理中的快速入门系列](../manage-apps/view-applications-portal.md)。

### <a name="use-federated-sso-if-possible"></a>如果可能，请使用联合 SSO

若要使用 "我的应用" 页面获得最佳用户体验，请从可用于联合 SSO (OpenID Connect 或 SAML) 的云应用程序集成。 联合 SSO 使用户能够在应用程序启动表面上保持一致的一键式体验，并在配置控制中更可靠。

有关如何将软件作为服务配置 (SaaS) 应用程序的详细信息，请参阅 [SaaS SSO 部署计划]。/Desktop/plan-sso-deployment.md) 。

### <a name="considerations-for-special-sso-circumstances"></a>专用 SSO 环境的注意事项

> [!TIP]
> 若要获得更好的用户体验，请在应用程序支持时，将联合 SSO 与 Azure AD (OpenID Connect/SAML) 结合使用，而不是基于密码的 SSO 和 ADFS。

若要登录到基于密码的 SSO 应用程序，或 Azure AD 应用程序代理访问的应用程序，用户需要安装并使用 "我的应用" 安全登录扩展。 当用户首次启动基于密码的 SSO 或应用程序代理应用程序时，会提示用户安装该扩展。 

![的屏幕截图](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

有关扩展的详细信息，请参阅 [安装我的应用浏览器扩展](../user-help/my-apps-portal-end-user-access.md)。

如果必须集成这些应用程序，则应定义一种机制，以使用 [受支持的浏览器](../user-help/my-apps-portal-end-user-access.md)大规模部署扩展。 选项包括：

* [适用于 Chrome、Firefox、Microsoft Edge 或 IE 的用户驱动的下载和配置](../user-help/my-apps-portal-end-user-access.md)

* [Internet Explorer Configuration Manager](https://docs.microsoft.com/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

此扩展允许用户从其搜索栏启动任何应用、查找对最近使用的应用程序的访问权限，以及链接到 "我的应用程序" 页。

![我的应用扩展搜索屏幕截图](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>规划移动访问

对于使用基于密码的 SSO 的应用程序，或使用 [Microsoft Azure AD 应用程序代理](../manage-apps/application-proxy.md)进行访问的应用程序，你必须使用 Microsoft Edge mobile。 对于其他应用程序，可以使用任何移动浏览器。 

### <a name="linked-sso"></a>链接 SSO

可以使用链接的 SSO 选项添加应用程序。 你可以配置应用程序磁贴，该磁贴链接到现有 web 应用程序的 URL。 链接 SSO 使你可以在不将所有应用程序迁移到 Azure AD SSO 的情况下，开始将用户定向到 "我的应用" 门户。 你可以逐步迁移到 Azure AD SSO 配置的应用程序，而不会中断用户的体验。

## <a name="plan-the-user-experience"></a>规划用户体验

默认情况下，用户有权访问的所有应用程序以及为自助发现配置的所有应用程序都将显示在用户的 "我的应用" 面板中。 对于许多组织而言，这可能是一个非常大的列表，如果不进行整理，这会变得非常繁重

### <a name="plan-my-apps-collections"></a>规划我的应用集合

用户有权访问的每个 Azure AD 应用程序都将显示在 "所有应用" 集合中的 "我的应用" 上。 使用集合将相关应用程序分组，并将它们显示在单独的选项卡上，使其更易于查找。 例如，你可以使用集合为特定作业角色、任务、项目等创建应用程序的逻辑分组。 

最终用户还可以通过

* 创建自己的应用集合。

* [隐藏和重新排列应用程序集合](access-panel-collections.md)。

![自助服务配置屏幕截图](./media/my-apps-deployment-plan/collections.png)

有一个选项可以从 "我的应用" 门户中隐藏应用程序，同时仍允许来自其他位置（如 Microsoft 365 门户）的访问。 了解详细信息： [在 Azure Active Directory 中的用户体验中隐藏应用程序](hide-application-from-user-portal.md)。

> [!IMPORTANT]
> 只有可通过 "我的应用" 访问用户有权访问的950应用。 这包括用户或管理员隐藏的应用。 

### <a name="plan-self-service-group-management-membership"></a>规划自助服务组管理成员身份

可以让用户在 Azure AD 中创建和管理其自己的安全组或 Microsoft 365 组。 组的所有者可以批准或拒绝成员身份请求并委派对组成员身份的控制。 自助服务组管理功能不可用于启用邮件的安全组或通讯组列表。

若要规划自助服务组成员身份，请确定你是否允许组织中的所有用户创建和管理组，或者只是部分用户。 如果你允许某个用户的子集，则需要设置要添加这些人员的组。 

有关启用这些方案的详细信息，请参阅 [在 Azure Active Directory 中设置自助服务组管理](../enterprise-users/groups-self-service-management.md) 。

### <a name="plan-self-service-application-access"></a>规划自助应用程序访问

可以让用户通过 "我的应用" 面板发现并请求访问应用程序。 为此，必须首先 

* 启用自助服务组管理

* 为 SSO 启用应用

* 创建用于应用程序访问的组

![我的应用程序自助服务配置的屏幕截图](./media/my-apps-deployment-plan/my-apps-self-service.png)

当用户请求访问权限时，他们请求访问基础组，组所有者可以委派管理组成员身份和应用程序访问权限的权限。 审批工作流可用于明确批准访问应用程序。 如果有挂起的应用程序访问请求，作为审批者的用户将在 "我的应用" 门户中收到通知。

## <a name="plan-reporting-and-auditing"></a>规划报告和审核

Azure AD 提供 [提供技术和业务见解的报表] .。。/reports-monitoring/overview-reports.md) 。 与您的业务和技术应用程序所有者合作，以承担这些报告的所有权并定期使用这些报告。 下表提供了一些典型报表方案的示例。

| 示例| 管理风险| 提高工作效率| 管理和符合性 |
| - | - | - | -|
| 报表类型| 应用程序权限和使用情况| 帐户设置活动| 查看谁在访问应用程序 |
| 可能的操作| 审核访问;废除权限| 修正任何预配错误| “撤销访问权限” |


Azure AD 将大多数审核数据保持30天。 可以通过 Azure 管理门户或 API 使用这些数据下载到分析系统。

#### <a name="auditing"></a>审核

用于应用程序访问的审核日志在30天内可用。 如果你的组织需要更长的保留期，请将日志导出到安全信息事件和管理 (SIEM) 工具，例如 Splunk 或 ArcSight。

对于审核、报告和灾难恢复备份，记录所需的下载频率、目标系统以及负责管理每个备份的人员。 您可能不需要单独的审核和报告备份。 灾难恢复备份应为单独的实体。

## <a name="validate-your-deployment"></a>验证你的部署

确保已对 "我的应用程序" 部署进行全面测试，并且已准备好回滚计划。

对公司拥有的设备和个人设备执行以下测试。 这些测试用例还应反映你的业务用例。 下面是基于典型技术方案的一些情况。 添加特定于您需求的其他内容。

#### <a name="application-sso-access-test-case-examples"></a>应用程序 SSO 访问测试用例示例：


| 业务案例| 预期结果 |
| - | - |
| 用户登录到 "我的应用" 门户| 用户可以登录并查看其应用程序 |
| 用户启动联合 SSO 应用程序| 用户自动登录到应用程序 |
| 用户首次启动密码 SSO 应用程序| 用户需要安装 "我的应用" 扩展 |
| 用户在后续时间启动密码 SSO 应用程序| 用户自动登录到应用程序 |
| 用户从 Microsoft 365 门户启动应用| 用户自动登录到应用程序 |
| 用户从 Managed Browser 启动应用| 用户自动登录到应用程序 |


#### <a name="application-self-service-capabilities-test-case-examples"></a>应用程序自助服务功能测试用例示例


| 业务案例| 预期结果 |
| - | - |
| 用户可以管理应用程序的成员身份| 用户可以添加/删除有权访问该应用程序的成员 |
| 用户可以编辑应用程序| 用户可以为密码 SSO 应用程序编辑应用程序的描述和凭据 |


### <a name="rollback-steps"></a>回滚步骤

必须规划好当部署无法按计划进行时要怎样做。 如果在部署过程中 SSO 配置失败，你必须了解如何 [排查 sso 问题](../hybrid/tshoot-connect-sso.md) 并降低对用户的影响。 在极端情况下，可能需要 [回滚 SSO](plan-sso-deployment.md)。

## <a name="manage-your-implementation"></a>管理实现

使用最小特权角色来完成 Azure Active Directory 中所需的任务。 [查看可用的不同角色](../roles/permissions-reference.md) ，并选择正确的角色以解决此应用程序的每个角色的需求。 某些角色可能需要在部署完成后暂时应用并删除。

| 角色| 角色| Azure AD 角色 |
| - | - | - |
| 支持管理员| 第1层支持| 无 |
| 标识管理员| 在问题影响时进行配置和调试 Azure AD| 全局管理员 |
| 应用程序管理员| 应用程序中的用户证明，具有权限的用户配置| 无 |
| 基础结构管理员| 证书滚动更新所有者| 全局管理员 |
| 业务所有者/利益干系人| 应用程序中的用户证明，具有权限的用户配置| 无 |


你可以使用 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 来管理你的角色，以便为具有目录权限的用户提供其他审核、控制和访问评审。

## <a name="next-steps"></a>后续步骤

[规划 Azure AD 多重身份验证的部署](../authentication/howto-mfa-getstarted.md)

[计划应用程序代理部署](application-proxy-deployment-plan.md)

 
