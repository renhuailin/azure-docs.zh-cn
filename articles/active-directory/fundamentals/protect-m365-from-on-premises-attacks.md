---
title: 保护 Microsoft 365 免受本地攻击
description: 有关如何确保本地攻击不会影响 Microsoft 365 的指导。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e2e87196f9d4d38743847ee68983216b8790e0b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257264"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>保护 Microsoft 365 免受本地攻击

许多客户将其专用企业网络连接到 Microsoft 365，以便为其用户、设备和应用程序带来好处。 不过，这些专用网络可能会受到许多有记录的方式的损害。 由于 Microsoft 365 是为许多组织提供一种紧张系统，因此保护该系统免受了本地基础结构的损害至关重要。

本文介绍如何配置系统以保护 Microsoft 365 云环境免受本地泄露。 主要关注： 

- Azure Active Directory (Azure AD) 租户配置设置。
- Azure AD 租户如何安全连接到本地系统。
- 对你的系统进行操作所需的折衷方式是保护你的云系统免受本地攻击。

我们强烈建议你实现此指导以保护 Microsoft 365 云环境。
> [!NOTE]
> 本文最初发布为博客文章。 它已移动到其当前位置以实现寿命和维护。
>
> 若要创建本文的脱机版本，请使用浏览器的打印到 PDF 功能。 经常回来查看以获取更新。

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>泄露的本地环境中的主要威胁向量


Microsoft 365 云环境从广泛的监视和安全基础结构中获益。 使用机器学习和人类情报，Microsoft 365 跨全球流量查看。 它可以快速检测攻击，并使你能够进行几乎实时的重新配置。 

在将本地基础结构连接到 Microsoft 365 的混合部署中，许多组织将信任委托给本地组件，以便进行关键身份验证和目录对象状态管理决策。
遗憾的是，如果本地环境受到威胁，这些信任关系成为攻击者损害 Microsoft 365 环境的机会。

这两个主要威胁向量是 *联合信任关系* 和 *帐户同步。* 这两个矢量可以向攻击者授予对你的云的管理访问权限。

* **联合信任关系**（如 SAML 身份验证）用于通过本地标识基础结构对 Microsoft 365 进行身份验证。 如果 SAML 令牌签名证书已泄露，则联合身份验证允许拥有该证书的任何用户模拟云中的任何用户。 *建议你禁用联合身份验证信任关系，以便在可能的情况 Microsoft 365 进行身份验证。*

* **帐户同步** 可用于修改特权用户 (包括其凭据) 或在 Microsoft 365 中具有管理权限的组。 *建议你确保同步的对象不会在 Microsoft 365 中的用户（* 直接或通过包含在受信任的角色或组中）中包含任何权限。 确保这些对象在受信任的云角色或组中没有直接或嵌套的赋值。

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>保护 Microsoft 365 免受本地攻击


为了满足前面概述的威胁媒介的需要，我们建议遵循下图所示的原则：

![用于保护 Microsoft 365 的参考体系结构。](media/protect-m365/protect-m365-principles.png)

1. **完全隔离 Microsoft 365 管理员帐户。** 它们应为：

    * Azure AD 中的 Mastered。

     * 使用多重身份验证进行身份验证。

     *  受 Azure AD 条件访问保护。

     *  仅使用 Azure 托管的工作站进行访问。

    这些管理员帐户是受限使用的帐户。 *在 Microsoft 365 中，本地帐户不应具有管理权限。* 

    有关详细信息，请参阅 [Microsoft 365 管理员角色概述](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)。 另请参阅 [Azure AD 中 Microsoft 365 的角色](../roles/m365-workload-docs.md)。

1. **从 Microsoft 365 管理设备。** 使用 Azure AD 联接和基于云的移动设备管理 (MDM) ，以消除本地设备管理基础结构的依赖关系。 这些依赖项可能会危及设备和安全控制。

1. **确保没有本地帐户拥有 Microsoft 365 的提升权限。**
    某些帐户访问需要 NTLM、LDAP 或 Kerberos 身份验证的本地应用程序。 这些帐户必须位于组织的本地标识基础结构中。 确保这些帐户（包括服务帐户）不包含在特权云角色或组中。 另外，请确保对这些帐户所做的更改不会影响云环境的完整性。 特权本地软件不得影响 Microsoft 365 特权帐户或角色。

1. **使用 Azure AD 云身份验证** 消除本地凭据的依赖项。 始终使用强身份验证，例如 Windows Hello、FIDO、Microsoft Authenticator 或 Azure AD 多重身份验证。

## <a name="specific-security-recommendations"></a>特定安全建议


以下各节提供了有关如何实现前面所述原则的具体指导。

### <a name="isolate-privileged-identities"></a>隔离特权标识


在 Azure AD 中，具有特权角色的用户（如管理员）是用于构建和管理环境其余部分的信任的根。 实现以下做法，以最大程度地降低安全漏洞的影响。

* 对 Azure AD 和 Microsoft 365 特权角色使用仅限云的帐户。

* 部署 [特权访问设备](/security/compass/privileged-access-devices#device-roles-and-profiles) 以进行特权访问以管理 Microsoft 365 和 Azure AD。

*  将 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) 用于实时 (JIT) 访问具有特权角色的所有人为帐户。 需要强身份验证才能激活角色。

* 提供管理角色，允许 [执行所需任务所需的最小特权](../roles/delegate-by-task.md)。

* 若要启用包含委托和多个角色的丰富角色分配体验，请考虑使用 Azure AD 安全组或 Microsoft 365 组。 这些组统称为 *云组*。 同时 [启用基于角色的访问控制](../roles/groups-assign-role.md)。 你可以使用 [管理单元](../roles/administrative-units.md) 将角色的作用域限制为组织的某个部分。

* 部署 [紧急访问帐户](../roles/security-emergency-access.md)。 不要 *使用本地* 密码保管库来存储凭据。

有关详细信息，请参阅 [保护特权访问](/security/compass/overview)。 另请参阅 [Azure AD 中管理员的安全访问做法](../roles/security-planning.md)。

### <a name="use-cloud-authentication"></a>使用云身份验证 

凭据是一个主要攻击向量。 实现以下做法，使凭据更安全：

* [部署无密码 authentication](../authentication/howto-authentication-passwordless-deployment.md)。 部署无密码凭据，尽可能减少密码的使用。 这些凭据在云中以本机方式进行管理和验证。 请从以下身份验证方法中进行选择：

   * [Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Microsoft Authenticator 应用](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 安全密钥](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [部署多重身份验证](../authentication/howto-mfa-getstarted.md)。 [使用多重身份验证来预配多个强凭据 Azure AD](../fundamentals/resilience-in-credentials.md)。 这样一来，对云资源的访问权限将需要在 Azure AD 中管理的凭据以及可操作的本地密码。 有关详细信息，请参阅 [使用 Azure AD 创建弹性访问控制管理策略](./resilience-overview.md)。

### <a name="limitations-and-tradeoffs"></a>限制和折衷

* 混合帐户密码管理需要混合组件，例如密码保护代理和密码写回代理。 如果本地基础结构受到侵害，攻击者可以控制这些代理所在的计算机。 此漏洞不会损害你的云基础结构。 但你的云帐户不会保护这些组件免受本地泄露。

*  从 Active Directory 同步的本地帐户被标记为在 Azure AD 之前永不过期。 此设置通常由本地 Active Directory 密码设置来缓解。 但是，如果 Active Directory 的本地实例受到威胁且禁用了同步，则必须将 [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) 选项设置为强制更改密码。

## <a name="provision-user-access-from-the-cloud"></a>预配云的用户访问权限

*设置* 指的是在应用程序或标识提供程序中创建用户帐户和组。

![预配体系结构示意图。](media/protect-m365/protect-m365-provision.png)

建议采用以下预配方法：

* **从 CLOUD hr Apps 预配到 Azure AD**：此预配允许隔离本地泄露，而不会中断从云 HR 应用到 Azure AD 的 leaver 循环。

* **云应用程序**：尽可能部署 [Azure AD 应用预配](../app-provisioning/user-provisioning.md) ，而不是本地预配解决方案。 此方法可保护你的一些软件即服务 (SaaS) 应用受到本地违规的恶意黑客配置文件的影响。 

* **外部标识**：使用 [Azure AD B2B 协作](../external-identities/what-is-b2b.md)。
    此方法减少了对与合作伙伴、客户和供应商的外部协作的本地帐户的依赖。 仔细评估任何与其他标识提供者的直接联合。 建议通过以下方式限制 B2B 来宾帐户：

   *  限制来宾访问目录中的浏览组和其他属性。 使用外部协作设置限制来宾读取不是其成员的组的能力。 

    *   阻止对 Azure 门户的访问。 您可以做出极少的必要例外。  创建包括所有来宾和外部用户的条件性访问策略。 然后 [实现策略来阻止访问](../../role-based-access-control/conditional-access-azure-management.md)。 

* **断开连接的林**：使用 [Azure AD 云设置](../cloud-provisioning/what-is-cloud-provisioning.md)。 此方法使你能够连接到断开连接的林，无需建立跨林连接或信任，从而扩大了本地漏洞的影响。 
 
### <a name="limitations-and-tradeoffs"></a>限制和折衷

当用于预配混合帐户时，Azure AD-HR-HR 系统依赖于本地同步来完成从 Active Directory 到 Azure AD 的数据流。 如果同步中断，将无法在 Azure AD 中使用新的员工记录。

## <a name="use-cloud-groups-for-collaboration-and-access"></a>使用云组进行协作和访问

利用云组，你可以从本地基础结构中分离协作和访问权限。

* **协作**：将 Microsoft 365 组和 Microsoft 团队用于新式协作。 取消本地通讯组列表，并 [将分发列表升级到 Outlook 中的 Microsoft 365 组](/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide)。

* **访问**：使用 Azure AD 安全组或 Microsoft 365 组来授予 Azure AD 中的应用程序的访问权限。
* **Office 365 许可**：通过使用仅限云的组，使用基于组的许可来预配到 Office 365。 此方法将对组成员身份的控制从本地基础结构中分离出来。

用于访问的组的所有者应被视为特权标识，以避免在本地安全方面接管成员身份。
接管操作包括对组成员身份的直接操作，或对可能影响 Microsoft 365 中动态组成员身份的本地属性进行操作。

## <a name="manage-devices-from-the-cloud"></a>从云中管理设备


使用 Azure AD 功能安全管理设备。

-   **使用 Windows 10 工作站**： [部署 AZURE AD 已加入](../devices/azureadjoin-plan.md) MDM 策略的设备。 为 [Windows Autopilot](/mem/autopilot/windows-autopilot) 实现完全自动化的预配体验。

    -   运行 Windows 8.1 和更早版本的弃用计算机。

    -   不要将服务器 OS 计算机部署为工作站。

    -   使用 [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) 作为所有设备管理工作负荷的授权来源。

-   [**部署特权访问设备**](/security/compass/privileged-access-devices#device-roles-and-profiles)：使用特权访问管理 Microsoft 365 和 Azure AD。

## <a name="workloads-applications-and-resources"></a>工作负载、应用程序和资源 

-   **本地单一登录 (SSO) 系统** 

    弃用任何本地联合身份验证和 web 访问管理基础结构。 将应用程序配置为使用 Azure AD。  

-   **SaaS 和业务线 (LOB) 支持新式身份验证协议的应用程序** 

    [使用 Azure AD 作为 SSO](../manage-apps/what-is-single-sign-on.md)。 配置为使用 Azure AD 进行身份验证的应用越多，本地泄露的风险就越小。


* **旧版应用程序** 

   * 可以对不支持新式身份验证的旧版应用程序启用身份验证、授权和远程访问。 使用 [Azure AD 应用程序代理](../manage-apps/application-proxy.md)。 你还可以通过网络或应用程序传送控制器解决方案使用 [安全混合访问伙伴集成](../manage-apps/secure-hybrid-access.md)来启用它们。   

   * 选择支持新式身份验证的 VPN 供应商。 将其身份验证与 Azure AD 集成。 在本地泄露中，可以使用 Azure AD 通过禁用 VPN 来禁用或阻止访问。

*  **应用程序和工作负荷服务器**

   * 需要服务器的应用程序或资源可以迁移到 Azure 基础结构即服务 (IaaS) 。 使用 (Azure AD DS) [Azure AD 域服务](../../active-directory-domain-services/overview.md) 将信任和依赖关系与 Active Directory 的本地实例分离。 若要实现这种分离，请确保用于 Azure AD DS 的虚拟网络未连接到公司网络。

   * 遵循 [凭据分层](/security/compass/privileged-access-access-model#ADATM_BM)指导。 应用程序服务器通常被视为第1层资产。

## <a name="conditional-access-policies"></a>条件访问策略

使用 Azure AD 的条件访问来解释信号，并使用它们来做出身份验证决策。 有关详细信息，请参阅 [条件性访问部署计划](../conditional-access/plan-conditional-access.md)。

* 尽可能使用条件性访问来 [阻止旧的身份验证协议](../conditional-access/howto-conditional-access-policy-block-legacy.md) 。 此外，使用特定于应用程序的配置在应用程序级别禁用旧的身份验证协议。

   有关详细信息，请参阅 [旧身份验证协议](../fundamentals/auth-sync-overview.md)。 或者查看 [Exchange online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) 和 [SharePoint online](/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps)的特定详细信息。

* 实施推荐的 [标识和设备访问配置](/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)。

* 如果你使用的是不包含条件性访问的 Azure AD 版本，请确保使用 [Azure AD 安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)。

   有关 Azure AD 功能许可的详细信息，请参阅 [Azure AD 定价指南](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="monitor"></a>监视 

将环境配置为保护 Microsoft 365 免受本地泄露时， [主动监视](../reports-monitoring/overview-monitoring.md) 环境。
### <a name="scenarios-to-monitor"></a>要监视的方案

除了特定于组织的任何方案外，还可以监视以下关键方案。 例如，你应该主动监视对关键业务应用程序和资源的访问权限。

* **可疑活动** 

    监视可疑活动的所有 [Azure AD 风险事件](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) 。 [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) 与 Azure 安全中心本机集成。

    定义网络 [命名位置](../reports-monitoring/quickstart-configure-named-locations.md) ，以避免在基于位置的信号上检测到干扰。 
*  **用户和实体行为分析 (UEBA) 警报** 

    使用 UEBA 获取有关异常情况检测的见解。
    * Microsoft Cloud App Security (MCAS) [在云中提供 UEBA](/cloud-app-security/tutorial-ueba)。

    * 可以将 [本地 UEBA 从 Azure 高级威胁防护 (ATP) 集成 ](/defender-for-identity/install-step2)。 MCAS 读取 Azure AD Identity Protection 的信号。 

* **紧急访问帐户活动** 

    监视使用 [紧急访问帐户](../roles/security-emergency-access.md)的任何访问权限。 创建调查警报。 此监视必须包括： 

   * 登录。 

   * 凭据管理。 

   * 对组成员身份的任何更新。 

   * 应用程序分配。 
* **特权角色活动**

    配置和查看 [Azure AD Privileged Identity Management (PIM) 生成的安全警报 ](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)。
    通过在用户直接分配时生成警报，监视在 PIM 外直接分配特权角色。

* **Azure AD 租户范围的配置**

    对租户范围的配置所做的任何更改都应在系统中生成警报。 这些更改包括但不限于：

  *  更新的自定义域。  

  * Azure AD B2B 对 allowlists 和阻止列表的更改。

  * Azure AD B2B 通过直接联盟或社交登录)  (SAML 标识提供者更改为允许的标识提供者。  

  * 条件性访问或风险策略更改。 

* **应用程序对象和服务主体对象**
    
   * 可能需要条件性访问策略的新应用程序或服务主体。 

   * 添加到服务主体的凭据。
   * 应用程序同意活动。 

* **自定义角色**
   * 更新自定义角色定义。 

   * 新创建的自定义角色。 

### <a name="log-management"></a>日志管理

定义日志存储和保留策略、设计和实现，以促进一致的工具集。 例如，可以将安全信息和事件管理视为 (SIEM) 系统，例如 Azure Sentinel、常见查询和调查和取证行动手册。

* **Azure AD 日志**：通过遵循诊断、日志保留和 SIEM 引入等设置的最佳做法，引入生成的日志和信号。 

    日志策略必须包括以下 Azure AD 日志：
   * 登录活动 

   * 审核日志 

   * 风险事件 

    Azure AD 为登录活动日志和审核日志提供 [Azure Monitor 集成](../reports-monitoring/concept-activity-logs-azure-monitor.md) 。 风险事件可以通过 [MICROSOFT GRAPH API](/graph/api/resources/identityriskevent)来引入。 可以 [将 Azure AD 日志流式传输到 Azure Monitor 日志](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

* **混合基础结构操作系统安全日志**：应将所有混合标识基础结构操作系统日志作为第0层系统进行存档并进行认真监视，因为这是因为外围应用的影响。 包括以下元素： 

   *  Azure AD Connect。 必须部署[Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md)来监视标识同步。

   *  应用程序代理 


   * 密码写回代理 

   * 密码保护网关计算机  

   * 网络策略服务器 (具有 Azure AD 多重身份验证 RADIUS 扩展的 NPSs)  

## <a name="next-steps"></a>后续步骤
* [使用 Azure AD 在标识和访问管理中构建复原能力](resilience-overview.md)

* [安全的资源外部访问](secure-external-access-resources.md) 
* [将所有应用与 Azure AD 集成](five-steps-to-full-application-integration-with-azure-ad.md)