---
title: 防范 Microsoft 365 遭受本地攻击
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
ms.openlocfilehash: 456f635e3cd7be91064a15b87c0da627678fdc45
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732973"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>防范 Microsoft 365 遭受本地攻击

许多客户将其企业专用网络连接到 Microsoft 365，使其用户、设备和应用程序能够从中受益。 不过，这些专用网络可能会遭到攻击者以多种已广为记载的方式入侵。 许多组织将 Microsoft 365 看作一个令人忧虑的系统，因此，防范已遭入侵的本地基础结构对其造成影响至关重要。

本文介绍如何配置系统以防范 Microsoft 365 云环境遭受本地入侵。 本文内容侧重于： 

- Azure Active Directory (Azure AD) 租户配置设置。
- 如何将 Azure AD 租户安全连接到本地系统。
- 需要采取哪些折衷方案才能以防范云系统遭受本地入侵的方式运行系统。

我们强烈建议实施本指导来保护 Microsoft 365 云环境。
> [!NOTE]
> 本文最初以博客文章的形式发布。 之所以将其转移到当前位置，是因为这样可以长期对其进行更新和维护。
>
> 若要创建本文的脱机版本，请使用浏览器的“打印到 PDF”功能。 请不时回来查看更新内容。

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>遭到入侵的本地环境中的主要威胁向量


Microsoft 365 云环境受益于功能丰富的监视和安全基础结构。 Microsoft 365 使用机器学习和人工智能审视全球流量。 它可以快速检测攻击，并允许你近乎实时地重新进行配置。 

在将本地基础结构连接到 Microsoft 365 的混合部署中，许多组织向本地组件委托信任，以做出关键的身份验证和目录对象状态管理决策。
不幸的是，如果本地环境遭到入侵，这些信任关系就成了攻击者入侵 Microsoft 365 环境的契机。

两个主要威胁向量是联合身份验证信任关系和帐户同步。  这两个向量都可以向攻击者授予对云的管理访问权限。

* **联合身份验证信任关系**（例如 SAML 身份验证）用于通过本地标识基础结构向 Microsoft 365 进行身份验证。 如果 SAML 令牌签名证书已泄露，则拥有该证书的任何人都可以利用联合身份验证来仿冒你云中的任何用户。 我们建议在向 Microsoft 365 进行身份验证时尽可能地禁用联合身份验证信任关系。

* **帐户同步** 可用于修改在 Microsoft 365 中拥有管理特权的特权用户（包括其凭据）或组。 对于同步的对象，我们建议你确保以直接的方式或通过将其包含在受信任角色或组中，来避免其持有的特权超越 Microsoft 365 中用户的特权。 确保这些对象在受信任的云角色或组中没有直接或嵌套的分配。

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>防范 Microsoft 365 遭受本地入侵


为了解决前面所述的威胁向量，我们建议遵守下图中所示的原则：

![用于保护 Microsoft 365 的参考体系结构。](media/protect-m365/protect-m365-principles.png)

1. **完全隔离 Microsoft 365 管理员帐户。** 这些帐户应该：

    * 在 Azure AD 中受到掌控。

     * 使用多重身份验证进行身份验证。

     *  受 Azure AD 条件访问的保护。

     *  仅使用 Azure 托管的工作站进行访问。

    这些管理员帐户在使用上受到限制。 在 Microsoft 365 中，不应有任何本地帐户拥有管理特权。 

    有关详细信息，请参阅 [Microsoft 365 管理员角色概述](/microsoft-365/admin/add-users/about-admin-roles)。 另请参阅 [Azure AD 中 Microsoft 365 的角色](../roles/m365-workload-docs.md)。

1. **从 Microsoft 365 管理设备。** 使用 Azure AD 加入和基于云的移动设备管理 (MDM) 来消除对本地设备管理基础结构的依赖关系。 这些依赖关系可能会侵害设备和安全控制。

1. **确保没有任何本地帐户对 Microsoft 365 拥有提升的特权。**
    某些帐户会访问需要 NTLM、LDAP 或 Kerberos 身份验证的本地应用程序。 这些帐户必须位于组织的本地标识基础结构中。 确保这些帐户（包括服务帐户）未包含在特权云角色或组中。 另外，确保对这些帐户所做的更改不会影响云环境的完整性。 特权本地软件不得影响 Microsoft 365 特权帐户或角色。

1. **使用 Azure AD 云身份验证** 消除对本地凭据的依赖关系。 始终使用强身份验证，例如 Windows Hello、FIDO、Microsoft Authenticator 或 Azure AD 多重身份验证。

## <a name="specific-security-recommendations"></a>具体安全建议


以下部分提供了有关如何实施上述原则的具体指导。

### <a name="isolate-privileged-identities"></a>隔离特权标识


在 Azure AD 中，具有特权角色的用户（例如管理员）是用于构建和管理剩余环境部分的信任的根。 实施以下做法可以最大程度地减轻入侵所造成的影响。

* 为 Azure AD 和 Microsoft 365 特权角色使用仅限云的帐户。

* 部署[特权访问设备](/security/compass/privileged-access-devices#device-roles-and-profiles)，供使用特权访问来管理 Microsoft 365 和 Azure AD。

*  部署 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM)，以用于即时 (JIT) 访问具有特权角色的所有用户帐户。 要求通过强身份验证来激活角色。

* 提供允许[使用必要的最低特权来完成所需任务](../roles/delegate-by-task.md)的管理角色。

* 若要启用同时包括委托和多个角色的丰富角色分配体验，请考虑使用 Azure AD 安全组或 Microsoft 365 组。 这些组统称为“云组”。 另外，[启用基于角色的访问控制](../roles/groups-assign-role.md)。 可以使用[管理单位](../roles/administrative-units.md)将角色范围限制为组织的一部分。

* 部署[紧急访问帐户](../roles/security-emergency-access.md)。 不要使用本地密码保管库来存储凭据。

有关详细信息，请参阅[保护特权访问](/security/compass/overview)。 另请参阅 [Azure AD 中面向管理员的安全访问做法](../roles/security-planning.md)。

### <a name="use-cloud-authentication"></a>使用云身份验证 

凭据是主要的攻击向量。 实施以下做法，使凭据更安全：

* [部署无密码身份验证](../authentication/howto-authentication-passwordless-deployment.md)。 尽可能通过部署无密码凭据来减少密码的使用。 这些凭据在云中以原生方式进行管理和验证。 从以下身份验证方法中选择：

   * [Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Microsoft Authenticator 应用](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 安全密钥](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [部署多重身份验证](../authentication/howto-mfa-getstarted.md)。 [使用 Azure AD 多重身份验证预配多个强凭据](../fundamentals/resilience-in-credentials.md)。 这样，在访问云资源时，除了需要提供一个可以操控的本地密码以外，还需要提供在 Azure AD 中管理的凭据。 有关详细信息，请参阅[使用 Azure AD 创建可复原的访问控制管理策略](./resilience-overview.md)。

### <a name="limitations-and-tradeoffs"></a>限制和弊端

* 混合帐户密码管理需要密码保护代理和密码写回代理等混合组件。 如果本地基础结构遭到入侵，攻击者可以控制这些代理所在的计算机。 这种漏洞不会使云基础结构受到侵害。 但是，云帐户无法防范这些组件遭到本地入侵。

*  从 Active Directory 同步的本地帐户在 Azure AD 中标记为永不过期。 本地 Active Directory 密码设置通常会减轻此项设置的作用。 但是，如果本地的 Active Directory 实例遭到入侵且禁用了同步，则必须设置 [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) 选项来强制实施密码更改。

## <a name="provision-user-access-from-the-cloud"></a>预配从云进行用户访问

“预配”是指在应用程序或标识提供者中创建用户帐户和组。

![预配体系结构示意图。](media/protect-m365/protect-m365-provision.png)

我们建议采用以下预配方法：

* **从云 HR 应用到 Azure AD 的预配**：这种预配可使本地入侵受到隔离，且不会中断从云 HR 应用到 Azure AD 的“加入者-转移者-离开者”(joiner-mover-leaver) 循环。

* **云应用程序**：尽可能部署 [Azure AD 应用预配](../app-provisioning/user-provisioning.md)，而不是本地预配解决方案。 在出现本地违规时，此方法可以防范某些软件即服务 (SaaS) 应用受到恶意黑客配置文件的影响。 

* **外部标识**：使用 [Azure AD B2B 协作](../external-identities/what-is-b2b.md)。
    此方法可以减少在与合作伙伴、客户和供应商展开外部协作时对本地帐户的依赖。 认真评估与其他标识提供者的任何直接联合。 我们建议通过以下方式限制 B2B 来宾帐户：

   *  将来宾访问权限限制为浏览目录中的组和其他属性。 使用外部协作设置来限制来宾读取他们不属于的组的能力。 

    *   阻止对 Azure 门户进行访问。 可以指定极少量的必要例外项。  创建涵盖所有来宾和外部用户的条件访问策略。 然后[实施某个策略来阻止访问](../../role-based-access-control/conditional-access-azure-management.md)。 

* **离线林**：使用 [Azure AD 云预配](../cloud-sync/what-is-cloud-sync.md)。 此方法可让你连接到离线林，而无需建立可能扩大本地违规所造成影响的跨林连接或信任。 
 
### <a name="limitations-and-tradeoffs"></a>限制和弊端

用于预配混合帐户时，来自云 HR 系统的 Azure AD 依赖于使用本地同步来完成从 Active Directory 到 Azure AD 的数据流。 如果同步中断，Azure AD 中不会提供新员工记录。

## <a name="use-cloud-groups-for-collaboration-and-access"></a>使用云组进行协作和访问

使用云组可将协作和访问与本地基础结构分离。

* **协作**：将 Microsoft 365 组和 Microsoft Teams 展开新型协作。 取消配置本地通讯组列表，[将通讯组列表升级为 Outlook 中的 Microsoft 365 组](/office365/admin/manage/upgrade-distribution-lists)。

* **访问**：使用 Azure AD 安全组或 Microsoft 365 组来授权访问 Azure AD 中的应用程序。
* **Office 365 许可**：使用基于组的许可，通过仅限云的组预配到 Office 365。 此方法可将组成员身份控制与本地基础结构分离。

应将用于访问的组的所有者视为特权标识，以避免在发生本地入侵时接管成员身份。
接管包括对本地组成员身份的直接操控，或者对可能影响 Microsoft 365 中动态组成员身份的本地属性的操控。

## <a name="manage-devices-from-the-cloud"></a>从云中管理设备


使用 Azure AD 功能来安全管理设备。

-   **使用 Windows 10 工作站**：使用 MDM 策略 [部署已加入 Azure AD](../devices/azureadjoin-plan.md) 的设备。 启用 [Windows Autopilot](/mem/autopilot/windows-autopilot) 以实现全自动化预配体验。

    -   弃用运行 Windows 8.1 和更低版本的计算机。

    -   不要将服务器 OS 计算机部署为工作站。

    -   使用 [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) 作为所有设备管理工作负载的授权来源。

-   [部署特权访问设备](/security/compass/privileged-access-devices#device-roles-and-profiles)：使用特权访问来管理 Microsoft 365 和 Azure AD 作为[保护特权访问](/security/compass/overview)的完整方法的一部分。

## <a name="workloads-applications-and-resources"></a>工作负载、应用程序和资源 

-   **本地单一登录 (SSO) 系统** 

    弃用任何本地联合身份验证和 Web 访问管理基础结构。 将应用程序配置为使用 Azure AD。  

-   **支持新式身份验证协议的 SaaS 和业务线 (LOB) 应用程序** 

    [使用 Azure AD 实现 SSO](../manage-apps/what-is-single-sign-on.md)。 配置为使用 Azure AD 进行身份验证的应用越多，遭受本地入侵的风险就越小。


* **旧式应用程序** 

   * 可对不支持新式身份验证的旧式应用程序启用身份验证、授权和远程访问。 使用 [Azure AD 应用程序代理](../app-proxy/application-proxy.md)。 还可以使用[安全混合访问合作伙伴集成](../manage-apps/secure-hybrid-access.md)通过网络或应用程序交付控制器解决方案来启用这些功能。   

   * 选择支持新式身份验证的 VPN 供应商。 将其身份验证与 Azure AD 相集成。 发生本地入侵时，可以使用 Azure AD 通过禁用 VPN 来禁用或阻止访问。

*  **应用程序和工作负载服务器**

   * 需要服务器的应用程序或资源可迁移到 Azure 基础结构即服务 (IaaS)。 使用 [Azure AD 域服务](../../active-directory-domain-services/overview.md) (Azure AD DS) 分离对本地 Active Directory 实例的信任和依赖关系。 若要实现这种分离，请确保用于 Azure AD DS 的虚拟网络未连接到企业网络。

   * 遵循有关[凭据分层](/security/compass/privileged-access-access-model#ADATM_BM)的指导。 应用程序服务器通常被视为第 1 层资产。

## <a name="conditional-access-policies"></a>条件访问策略

使用 Azure AD 条件访问来解释信号，并使用这些信号做出身份验证决策。 有关详细信息，请参阅[条件访问部署计划](../conditional-access/plan-conditional-access.md)。

* 尽可能地使用条件访问来[阻止旧式身份验证协议](../conditional-access/howto-conditional-access-policy-block-legacy.md)。 此外，使用应用程序特定的配置在应用程序级别禁用旧式身份验证协议。

   有关详细信息，请参阅[旧式身份验证协议](../fundamentals/auth-sync-overview.md)。 或者查看有关 [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) 和 [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant) 的具体详细信息。

* 实施建议的[标识和设备访问配置](/microsoft-365/security/office-365-security/identity-access-policies)。

* 如果使用的 Azure AD 版本不包含条件访问，请确保使用 [Azure AD 安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)。

   有关 Azure AD 功能许可的详细信息，请参阅 [Azure AD 定价指南](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

## <a name="monitor"></a>监视 

将环境配置为防范 Microsoft 365 遭受本地入侵后，[主动监视](../reports-monitoring/overview-monitoring.md)环境。
### <a name="scenarios-to-monitor"></a>要监视的方案

除了特定于你组织的任何场景外，还要监视以下关键场景。 例如，应该主动监视对业务关键型应用程序和资源的访问。

* **可疑活动** 

    监视所有 [Azure AD 风险事件](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation)以识别可疑活动。 [Azure AD 标识保护](../identity-protection/overview-identity-protection.md)与 Azure 安全中心原生集成。

    定义网络[命名位置](../conditional-access/location-condition.md)，以免基于位置的信号产生干扰性的检测结果。 
*  **用户和实体行为分析 (UEBA) 警报** 

    使用 UEBA 获取有关异常情况检测的见解。
    * Microsoft Cloud App Security (MCAS) [在云中提供 UEBA](/cloud-app-security/tutorial-ueba)。

    * 可以[从 Azure 高级威胁防护 (ATP) 集成本地 UEBA](/defender-for-identity/install-step2)。 MCAS 从 Azure AD 标识保护读取信号。 

* **紧急访问帐户活动** 

    监视使用[紧急访问帐户](../roles/security-emergency-access.md)的任何访问活动。 创建警报以便于调查。 此项监视必须包括： 

   * 登录。 

   * 凭据管理。 

   * 对组成员身份进行的任何更新。 

   * 应用程序分配。 
* **特权角色活动**

    配置并审查 [Azure AD Privileged Identity Management (PIM) 生成的安全警报](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)。
    每当直接分配用户时就生成警报，以此监视 PIM 外部的特权角色直接分配。

* **Azure AD 租户范围的配置**

    对租户范围的配置所做的任何更改都应在系统中生成警报。 这些更改包括但不限于：

  *  已更新自定义域。  

  * 在 Azure AD B2B 中对允许列表和阻止列表做出的更改。

  * Azure AD B2B 通过直接联合或社交登录对允许的标识提供者（SAML 标识提供者）做出的更改。  

  * 条件访问或风险策略的更改。 

* **应用程序对象和服务主体对象**
    
   * 可能需要条件访问策略的新应用程序或服务主体。 

   * 添加到服务主体的凭据。
   * 应用程序同意活动。 

* **自定义角色**
   * 对自定义角色定义做出更新。 

   * 新建的自定义角色。 

### <a name="log-management"></a>日志管理

定义日志存储和保留策略、设计与实施方案，促使工具集保持一致。 例如，可将安全信息和事件管理 (SIEM) 系统视为 Azure Sentinel、常用查询以及调查和取证 playbook。

* **Azure AD 日志**：始终如一地遵循诊断、日志保留和 SIEM 引入等设置的最佳做法，引入生成的日志和信号。 

    日志策略必须包括以下 Azure AD 日志：
   * 登录活动 

   * 审核日志 

   * 风险事件 

    Azure AD 提供登录活动日志和审核日志的 [Azure Monitor 集成](../reports-monitoring/concept-activity-logs-azure-monitor.md)。 可以通过 [Microsoft Graph API](/graph/api/resources/identityriskevent) 引入风险事件。 可[将 Azure AD 日志流式传输到 Azure Monitor 日志](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

* **混合基础结构 OS 安全日志**：由于对外围应用造成的影响，应该存档所有混合标识基础结构 OS 日志，并将其作为第 0 层系统认真监视。 包括以下元素： 

   *  Azure AD Connect。 必须部署 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) 来监视标识同步。

   *  应用程序代理 


   * 密码写回代理 

   * 密码保护网关计算机  

   * 装有 Azure AD 多重身份验证 RADIUS 扩展的网络策略服务器 (NPS) 

## <a name="next-steps"></a>后续步骤
* [使用 Azure AD 在标识和访问管理中融入复原能力](resilience-overview.md)

* [安全的资源外部访问](secure-external-access-resources.md) 
* [将所有应用与 Azure AD 集成](five-steps-to-full-application-integration-with-azure-ad.md)
