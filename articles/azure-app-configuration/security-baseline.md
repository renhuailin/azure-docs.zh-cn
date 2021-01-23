---
title: Azure 应用配置的 Azure 安全基线
description: Azure 应用配置安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4af00b2e0e5445ecc904f603d813d843a9c54b93
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735010"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure 应用配置的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用到 Azure 应用配置。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容由 Azure 安全基准定义的 **安全控制** 和适用于 Azure 应用配置的相关指南进行分组。 排除了不适用于 Azure 应用配置的 **控件**。

若要查看 Azure 应用配置如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure 应用配置安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-controls-v2-network-security.md)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指南**： Azure 应用配置不会直接将任何资源部署到虚拟网络。 由于服务未部署到虚拟网络中，因此无法利用某些网络功能来保护服务的内部流量，例如：网络安全组、路由表或其他网络设备，如 Azure 防火墙。 但是，应用配置允许你使用专用终结点安全地连接到虚拟网络中的 Azure 应用配置。

使用 Azure Sentinel 发现旧的不安全协议，如 SSL/TLSv1、SMBv1、LM/NTLMv1、wDigest、未签名的 LDAP 绑定和 Kerberos 中的弱密码。

- [为 Azure 应用配置使用专用终结点](concept-private-endpoint.md)

- [Azure Sentinel 不安全协议工作簿](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

**指南**： Azure 应用配置支持使用专用终结点通过专用链接安全发送数据。 使用 Azure ExpressRoute 或 Azure 虚拟专用网络 (VPN) 在归置环境中的 Azure 数据中心和本地基础结构之间创建专用连接。 ExpressRoute 连接并不通过公共 Internet，与典型的 Internet 连接相比，它们的可靠性更高、速度更快且延迟时间更短。 对于点到站点 VPN 和站点到站点 VPN，可使用这些 VPN 选项的任意组合以及 Azure ExpressRoute 将本地设备或网络连接到虚拟网络。

若要将 Azure 中的两个或更多虚拟网络连接在一起，请使用虚拟网络对等互连。 对等互连虚拟网络之间的网络流量是专用的，且保留在 Azure 主干网络上。

- [什么是 ExpressRoute 连接模型](../expressroute/expressroute-connectivity-models.md)

- [Azure VPN 概述](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [虚拟网络对等](../virtual-network/virtual-network-peering-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立对 Azure 服务的专用网络访问

**指南**：使用 Azure 专用链接可以在不通过 internet 连接的情况下从虚拟网络启用对 Azure 应用配置的专用访问。

除了 Azure 服务提供的身份验证和流量安全性外，专用访问还提供额外的深层防御措施。

- [了解 Azure 专用链接](../private-link/private-link-overview.md)

- [如何在 Azure 应用配置中设置专用链接](concept-private-endpoint.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务不受外部网络攻击

**指南**：通过虚拟网络访问配置值时，针对外部网络的攻击保护你的资源，包括分布式拒绝服务 (DDoS) 攻击、特定于应用程序的攻击，以及未经请求和潜在恶意的 internet 流量。 使用 Azure 防火墙保护应用程序和服务免受来自 Internet 和其他外部位置的潜在恶意流量的侵害。 通过在 Azure 虚拟网络上启用 DDoS 标准保护，保护资产免受 DDoS 攻击。 使用 Azure 安全中心来检测与网络相关资源相关的错误配置风险。

Azure 应用配置不用于运行 web 应用程序，它提供了这些 web 应用程序的配置。 不需要配置任何其他设置，也不需要部署任何额外的网络服务来保护其免受针对 web 应用程序的外部网络攻击。

- [Azure 防火墙文档](../firewall/index.yml)

- [使用 Azure 门户管理 Azure DDoS 防护标准](../ddos-protection/manage-ddos-protection.md)

- [Azure 安全中心建议](../security-center/recommendations-reference.md#recs-networking)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

**指南**：结合使用 Azure 防火墙和基于威胁智能的筛选，可以发出警报，并/或阻止来自已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。 需要进行负载检查时，可以使用负载检查功能从 Azure Marketplace 部署第三方 ID/IPS 解决方案。 或者，你可以选择使用基于主机的 ID/IP 或基于主机的终结点检测和响应 (EDR) 解决方案与或而不是基于网络的 ID/IP 结合使用。

注意：如果你对 IDS/IP 使用有法规或其他要求，请确保始终对其进行优化，以便为你的 SIEM 解决方案提供高质量的警报。

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace 包含第三方 ID 功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR 功能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

**指南**：使用 Azure 虚拟网络服务标记定义为应用配置资源配置的网络安全组或 Azure 防火墙上的网络访问控制。 在应用程序网络中创建出站流量的安全规则时，可以使用服务标记 "AppConfiguration" 代替特定的 IP 地址。 通过在规则的相应“源”或“目标”字段中指定服务标记名称，可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指南**： Azure 应用配置与 Azure 默认标识和访问管理服务 Azure Active Directory (Azure AD) 集成。 你应该使 Azure AD 标准化，以便控制组织在以下资源中的标识和访问管理：
- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。
- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，帮助你评估与 Microsoft 的最佳做法建议相关的标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。

Azure 提供下列 Azure 内置角色，用于使用 Azure AD 和 OAuth 授予对应用程序配置数据的访问权限：

- 应用程序配置数据所有者：使用此角色授予对应用程序配置数据的读取/写入/删除访问权限。 这不会授予对应用程序配置资源的访问权限。

- 应用程序配置数据读取者：使用此角色授予对应用程序配置数据的读取访问权限。 这不会授予对应用程序配置资源的访问权限。

- 参与者：使用此角色管理应用程序配置资源。 虽然可以使用访问密钥访问应用程序配置数据，但此角色不会使用 Azure AD 授予对数据的直接访问权限。

- 读者：使用此角色授予对应用程序配置资源的读取访问权限。 这不会授予对资源的访问密钥的访问权限，也不会授予对存储在应用程序配置中的数据的访问权限。

有关详细信息，请参阅以下资源：

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Active Directory 中的标识安全分数是什么](../active-directory/fundamentals/identity-secure-score.md)

- [使用 Azure AD 授予对 Azure 应用配置的访问权限](concept-enable-rbac.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指南**：使用 azure 托管标识从非人工帐户（如其他 Azure 服务）访问 Azure 应用配置。 建议使用 Azure 托管标识功能（而不是创建功能更强大的人工帐户来访问或执行资源），以限制管理其他凭据的需求。 还可以向 Azure 应用配置分配托管标识本身，以对支持 Azure AD 身份验证的其他 Azure 服务/资源进行本机身份验证。 这可用于在检索机密时启用从应用配置到 Azure Key Vault 的轻松访问。 使用托管标识时，该标识由 Azure 平台托管，不需要你预配或轮换任何机密。

Azure 应用配置支持应用程序被授予两种类型的标识：
- 配置资源绑定了一个系统分配的标识。 如果删除配置资源，则标识将一并删除。 一个配置资源只能有一个系统分配的标识。
- 用户分配的标识是可以分配给配置资源的独立 Azure 资源。 一个配置资源可以有多个用户分配的标识。

如果无法利用托管标识，请在 Azure 应用配置资源级别创建具有受限权限的服务主体。 使用证书凭据配置这些服务主体，并仅回退到客户端机密。 在这两种情况下，都可以将 Azure Key Vault 与 Azure 托管标识结合使用，以便运行时环境（例如 Azure 函数）可以从密钥保管库中检索凭据。

- [如何将托管标识用于 Azure 应用程序配置](overview-managed-identity.md)

- [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)

- [支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [使用托管标识来访问应用程序配置](howto-integrate-azure-managed-service-identity.md)

- [Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps) 

- [使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [使用 Azure Key Vault 进行安全主体注册](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

**指南**： Azure 应用配置使用 Azure Active Directory (Azure AD) 提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 此内容包括企业标识（例如员工）以及外部标识（如合作伙伴和供应商）。 Azure AD 通过使用任何同步的企业 Active Directory 标识，启用单一登录 (SSO) 通过 Azure 门户来管理应用配置服务。 将所有用户、应用程序和设备连接到 Azure AD，实现无缝的安全访问和更好的可见性和控制。

- [了解 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指南**： Azure 应用配置使用 Azure Active Directory，通过多重身份验证 (MFA) 和强无密码方法支持强身份验证控制。
- 多重身份验证 - 启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议，以获得 MFA 设置中的一些最佳做法。 可基于登录条件和风险因素，对所有用户、精选用户或在每用户级别强制执行 MFA。
- 无密码身份验证 - 提供三个无密码身份验证选项：Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。

对于管理员和特权用户，请确保使用的是最高级别的强身份验证方法，然后将相应的强身份验证策略推出给其他用户。

注意：可以对访问和管理应用配置的用户帐户强制实施 MFA，而不是在编程服务帐户上执行。 尽可能使用无密码 authentication （如托管标识），并对任何用户帐户强制执行 MFA。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory 的无密码身份验证选项简介](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指南**： Azure 应用配置与 Azure Active Directory 集成，提供以下数据源：

-   登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

-   审核日志 - 对于通过 Azure AD 中的各种功能所做的所有更改，可以通过日志为其提供可跟踪性。 所记录的更改审核日志的示例包括添加或删除用户、应用、组、角色和策略。

-   风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

-   已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

Azure 安全中心还可针对某些可疑活动（例如失败的身份验证尝试次数太多，以及帐户已在订阅中遭到弃用）发出警报。 

Azure 高级威胁防护 (ATP) 是一种安全解决方案，可使用本地 Active Directory 信号来识别、检测和调查高级威胁、遭到入侵的标识和恶意的内部操作。

- [Azure AD 中的审核活动报告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [如何确定标记为存在风险活动的 Azure AD 用户](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

- [Azure 安全中心的威胁情报保护模块中的警报](../security-center/alerts-reference.md)

- [如何将 Azure 活动日志集成到 Azure Monitor 中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [连接 Azure AD Identity Protection 的数据](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure 高级威胁防护](/azure-advanced-threat-protection/what-is-atp)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指南**： Azure 应用配置支持 Azure Active Directory () Azure AD 基于用户定义的条件进行更精细的访问控制的条件性访问，如来自某些 IP 范围的用户登录需要使用 MFA 进行登录。 精细身份验证会话管理策略还可用于不同的用例。 这些条件访问策略仅适用于对 Azure AD 进行身份验证的用户帐户访问和管理应用配置服务，但不会应用于服务主体或连接到配置资源的连接字符串。

- [Azure 条件访问概述](../active-directory/conditional-access/overview.md)

- [常见的条件访问策略](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用条件访问配置身份验证会话管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据透露

**指南**： Azure 应用配置允许客户存储可能包含标识或机密的配置。 建议实施凭据扫描器来识别配置中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

将 Azure 应用配置服务与 Azure Key Vault 一起使用。 在 Key Vault 中存储任何凭据，并通过在应用配置资源中创建 Key Vault 引用来链接到这些凭据。 当应用配置创建这些引用时，它将存储 Key Vault 值的 Uri，而不是值本身。 应用程序可以连接到应用配置，以从 Key Vault 检索任何凭据。

对于 GitHub，你可以使用原生的机密扫描功能来识别代码中的凭据或其他形式的机密。

- [在 ASP.NET Core 应用程序中使用 Key Vault 引用的教程](use-key-vault-references-dotnet-core.md)

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 机密扫描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

**指南**：限制高特权帐户或角色的数量并在提升的级别保护这些帐户，因为具有此权限的用户可以直接或间接地读取和修改 Azure 环境中的每个资源。

你可使用Azure AD Privileged Identity Management (PIM) 提供对 Azure 资源和 Azure AD 的实时 (JIT) 特权访问权限。 JIT 仅在用户需要执行特权任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还会生成安全警报。

访问密钥具有很高的特权，应定期进行轮替，作为最佳安全方案。 访问密钥包含连接字符串，其中包含凭据信息，并被视为机密信息。 这些机密需要存储在 Azure Key Vault 中，你的代码必须向 Key Vault 进行身份验证才能检索这些机密。 访问密钥可授予对应用程序的读写或只读访问权限。 确保颁发了正确类型的访问密钥，以防止未经授权的访问。 若要提高安全性，请使用 Azure AD 中的托管标识功能。 这只要求应用程序具有访问配置值的配置终结点 URL。

- [应用配置最佳实践](howto-best-practices.md#app-configuration-bootstrap)

- [使用托管标识来访问应用程序配置](howto-integrate-azure-managed-service-identity.md)
- [Azure AD 中的管理角色权限](../active-directory/roles/permissions-reference.md)

- [使用 Azure Privileged Identity Management 安全警报](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](../active-directory/roles/security-planning.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**： Azure 应用配置使用 Azure RBAC 通过限制授予了特权访问权限的帐户来隔离对关键系统的访问。 资源级别的应用配置支持 Azure RBAC。 若要安全地将此信息存储在其自己的应用配置资源中，请将此信息存储在其自身的 在资源中，也可以通过只读访问帐户或密钥，以及标记和标记来使用粒度访问。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

- [访问管理组](../governance/management-groups/overview.md#management-group-access)

- [Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [使用 Azure AD 与应用配置集成 RBAC](concept-enable-rbac.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**： Azure 应用配置使用 Azure Active Directory (Azure AD) 帐户来管理其资源、定期查看用户帐户和访问分配，以确保帐户及其访问有效。 

Azure 提供下列 Azure 内置角色，用于使用 Azure AD 和 OAuth 授予对应用程序配置数据的访问权限：

- 应用程序配置数据所有者：使用此角色授予对应用程序配置数据的读取/写入/删除访问权限。 这不会授予对应用程序配置资源的访问权限。

- 应用程序配置数据读取者：使用此角色授予对应用程序配置数据的读取访问权限。 这不会授予对应用配置资源的访问权限

你可以使用 Azure AD 访问评审来查看组成员身份、对企业应用程序的访问权限和角色分配，如上述应用配置角色。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流以便于执行评审。

注意：建议在可能的情况下，从其他服务或应用程序对应用配置进行身份验证。 使用时，你将需要管理配置有权访问应用配置的任何服务主体或连接字符串。

- [在 Privileged Identity Management (PIM) 中创建对 Azure 资源角色的访问评审](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

- [使用 Azure AD 授予对 Azure 应用配置的访问权限](concept-enable-rbac.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指南**： Azure 应用配置与 Azure Active Directory 集成，以管理其资源。 为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](../active-directory/roles/security-emergency-access.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指南**： Azure 应用配置与 Azure Active Directory 集成，以管理其资源。 使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md)

- [什么是 Azure AD 权利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指导**：安全的独立工作站对于确保敏感角色（如管理员、开发人员和关键服务操作员）的安全至关重要。 对于与应用程序配置相关的管理任务，使用高度安全的用户工作站和/或 Azure 堡垒。 使用 Azure Active Directory、Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 部署安全的托管用户工作站，用于执行管理任务。 可通过集中管理安全的工作站来强制实施安全配置，包括强身份验证、软件和硬件基线、受限的逻辑和网络访问。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [部署特权访问工作站](/security/compass/privileged-access-deployment)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**： Azure 应用配置与 Azure 基于角色的访问控制集成 (RBAC) 管理其资源。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可以将这些角色分配给用户、组服务主体和托管标识。 Azure 应用配置有预定义的内置角色，可以通过 Azure CLI、Azure PowerShell 或 Azure 门户等工具来清点或查询这些角色。 通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的特权。 这是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期进行审查。

Azure 提供下列 Azure 内置角色，用于使用 Azure AD 和 OAuth 授予对应用程序配置数据的访问权限：
- 应用程序配置数据所有者：使用此角色授予对应用程序配置数据的读取/写入/删除访问权限。 这不会授予对应用程序配置资源的访问权限。
- 应用程序配置数据读取者：使用此角色授予对应用程序配置数据的读取访问权限。 这不会授予对应用程序配置资源的访问权限。

请使用内置角色来分配权限，仅在必要时创建自定义角色。 

应用配置支持在一个应用配置资源中存储多个应用程序的配置。 若要限制应用程序之间的信息访问，请为每个应用程序创建一个应用配置资源，并相应地设置 Azure RBAC。

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

- [使用 Azure AD 授予对 Azure 应用配置的访问权限](concept-enable-rbac.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：选择 Microsoft 支持的批准流程  

**指南**：对 Microsoft 可能需要访问应用配置数据的支持方案实施组织审批流程。 客户密码箱当前不适用于应用配置支持方案。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-controls-v2-data-protection.md)。

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1：发现、分类和标记敏感数据

**指导**：对敏感数据进行发现、分类和标记，以便设计合适的控件，确保组织的技术系统能够安全地存储、处理和传输敏感信息。 应用配置资源支持以标记形式对敏感信息进行标记，而不是包含在其中的配置值。 当应用程序具有对配置存储区的读取或读/写访问权限后，它将对该存储区中的任何配置具有完全访问权限。

- [使用 Azure 信息服务标记敏感信息](/azure/information-protection/what-is-information-protection)

- [在 Azure 中标记数据分类](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指南**：对于由 microsoft 管理的底层平台，microsoft 将所有客户内容视为敏感数据，并防范客户数据丢失和公开。 为了确保 Azure 中的客户数据始终安全，Microsoft 实施了一些默认的数据保护控制机制和功能。 确保定期将访问密钥轮换到应用配置资源。 连接字符串中的凭据信息可以存储在 Azure Key Vault 中，你的代码必须通过身份验证才能 Key Vault 检索它们。 访问密钥可授予对应用程序的读写或只读访问权限。 确保颁发了正确类型的访问密钥，以防止未经授权的访问。 若要提高安全性，请使用 Azure AD 中的托管标识功能。 这只要求应用程序具有访问配置值的配置终结点 URL。

使用 azure RBAC)  (基于角色的访问控制来限制访问权限：

- 将敏感数据分隔到其自己的应用配置资源中，并相应地分配 RBAC 策略，以便仅启用授权的访问 

- 使用基于网络的访问控制

- Azure 服务中的特定控件 (例如 SQL 中的加密和其他数据库) 并确保一致的访问控制，所有类型的访问控制都应该与企业分段策略一致。

- 企业分段策略还应根据敏感的或业务关键型的数据和系统的位置来确定。

有关详细信息，请参阅以下资源：

- [使用 Azure Active Directory 授予对 Azure 应用程序配置的访问权限](concept-enable-rbac.md)

- [应用配置数据加密](faq.md#does-app-configuration-encrypt-my-data)

- [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

**指南**：若要补充访问控制，应使用加密对传输中的数据进行防止 "带外" 攻击。 这有助于确保攻击者无法轻松读取或修改数据。

Azure 应用配置对所有 HTTP 请求使用 TLS 加密。 Azure 基础结构为 Azure 数据中心之间的所有请求提供了额外的网络级别的加密层。 确保连接到应用配置资源的任何客户端都可以协商 TLS 1.2 或更高版本的 HTTP 流量。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：加密静态敏感数据

**指南**：若要补充访问控制，应防止静态数据受到 "带外" 的攻击， (例如使用加密访问基础存储) 。 这有助于确保攻击者无法轻松读取或修改数据。

默认情况下，Azure 提供静态数据加密。 对于高度敏感的数据，你可以选择在所有可用的 Azure 资源上进行额外的静态加密。 默认情况下，azure 会管理你的加密密钥，但 Azure 提供选项来管理你自己的密钥 (客户管理的密钥) 用于 Azure 应用配置。

- [使用客户托管密钥在 Azure 应用配置中加密数据](concept-customer-managed-keys.md)

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [加密模型和密钥管理表](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Azure 中的静态数据双重加密](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](../security/benchmarks/security-controls-v2-asset-management.md)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指南**：确保安全团队有权访问 Azure 上不断更新的资产清单，如 Azure 应用配置。 安全团队通常需要此清单，以评估其组织遭遇新兴风险的可能性，并根据它不断提高安全性。 创建一个 Azure Active Directory 组，使其包含组织的授权安全团队，并向他们分配对所有 Azure 应用配置资源的读取访问权限，可以通过订阅中的单个高级角色分配简化。

Azure 安全中心清单功能和 Azure 资源图可查询和发现订阅中的所有资源，包括 Azure 服务、应用程序和网络资源。

将标记应用到 Azure 资源、资源组和订阅，以便有条理地将它们组织成分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

- [Azure 安全中心资产库存管理](../security-center/asset-inventory.md)

- [有关标记资产的详细信息，请参阅资源命名和标记决策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指南**： Azure 应用配置使用 azure 策略支持基于 azure 资源管理器的部署和配置强制。 请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general)

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指南**：建立或更新安全策略，用于应对资产生命周期管理过程的可能具有重大影响的修改。 这些修改包括但不限于对以下内容的更改：标识提供者和访问权限、数据敏感度、网络配置，以及管理特权分配。

如果不再需要 Azure 资源，请将其删除。 确保管理员定期轮换访问密钥，以确保只有经过身份验证的用户才能访问其配置资源。

- [旋转用于应用程序配置的加密密钥](concept-customer-managed-keys.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](../security/benchmarks/security-controls-v2-logging-threat-detection.md)。

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**：应用配置与 Azure Active Directory (Azure AD) 集成。 这提供了以下用户日志，可以在 Azure AD reporting 中查看或与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具进行集成，以实现更复杂的监视和分析用例：
- 登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。
- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。
- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。
- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。 除了基本的安全卫生监视，Azure 安全中心的威胁防护模块还可以收集 Azure 服务层的更深入安全警报。 可通过此功能查看各个资源内的帐户异常情况。

获取对应用配置配置资源的访问权限的另一种方法是使用访问密钥。 需要定期轮替这些要求，以确保未授权的代理获得配置资源的访问权限。 可以在门户中的 "访问密钥" 下直接进行轮换。

- [允许 Azure 应用配置使用托管标识访问其他 Azure AD 受保护的资源](overview-managed-identity.md)

- [将托管标识用于 Azure 应用配置](howto-integrate-azure-managed-service-identity.md)

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md)

- [启用 Azure 标识保护](../active-directory/identity-protection/overview-identity-protection.md)

- [使用 Azure AD 授权对 Azure 应用配置的访问](concept-enable-rbac.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指南**： Azure 应用配置不会直接将任何资源部署到虚拟网络。 但是，应用配置允许你使用专用终结点安全地连接到虚拟网络中的 Azure 应用配置。 Azure 应用配置也不会生成或处理需要启用的 DNS 查询日志。

在配置的应用配置专用终结点上启用日志记录，以捕获：
- 专用终结点处理的数据（传入/传出）
- 专用链接服务处理的数据（传入/传出）
- NAT 端口可用性

有关详细信息，请参阅以下资源：

- [Azure 专用链接监视](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指南**：自动可用的活动日志包含对应用配置资源 (PUT、POST、DELETE) 的所有写入操作，但读取操作 (获取) 。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。 对于应用配置，活动日志仅在控制平面上可用，并由 Azure 资源管理器 (ARM) 出现。 目前不支持面向客户的应用配置日志记录。 还不能配置 Azure 资源日志。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：集中记录存储和分析来实现关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

另外，请启用 Azure Sentinel 或第三方 SIEM 并将数据载入其中。 许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指南**：确保用于存储应用配置日志的任何存储帐户或 Log Analytics 工作区的日志保留期都已根据组织的符合性规定进行了设置。 将 Azure 存储、Data Lake 或 Log Analytics 工作区帐户用于长期存储和存档存储。

在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。

- [如何配置 Log Analytics 工作区保留期](../azure-monitor/platform/manage-cost-storage.md)

- [在 Azure 存储帐户中存储资源日志](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-controls-v2-incident-response.md)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指导**：确保组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保就绪性。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件

**指导**：确保具有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具构建高质量警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指南**：确保分析人员在调查潜在事件时可查询和使用不同的数据源，以全面了解所发生的情况。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。  还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据 - 使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建正在运行的系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自带的功能来创建正在运行的系统的快照。

Azure Sentinel 提供几乎针对任何日志源的广泛数据分析，并提供一个事例管理门户来管理事件的整个生命周期。 调查过程中的情报信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 调查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)。

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：为所有 Azure 服务建立安全配置 

**指南**： Azure 应用配置支持 Azure 安全中心提供的以下特定于服务的策略，用于审核和强制执行 azure 资源的配置。 这可以在 Azure 安全中心或 Azure 策略计划中进行配置。
- 应用配置应使用客户管理的密钥：通过允许管理加密密钥，客户管理的密钥提供增强的数据保护。 这通常是满足合规性要求所必需的。
- 应用配置应使用专用链接：专用终结点连接允许虚拟网络上的客户端通过专用链接安全访问 Azure 应用配置。

可以在单个蓝图定义中使用 Azure 蓝图自动部署和配置服务和应用程序环境，包括 Azure 资源管理器模板、Azure RBAC 控件和策略。

- [有关应用配置策略的详细信息](../governance/policy/samples/built-in-policies.md#app-configuration)

- [在 Azure 安全中心内使用安全策略](../security-center/tutorial-security-policy.md)

- [企业级登陆区域中 Guardrails 实现的插图](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 蓝图](../governance/blueprints/overview.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：为所有 Azure 服务维护安全配置

**指南**：使用 Azure 安全中心监视配置基线，并强制使用 Azure 策略。 适用于应用配置的 Azure 策略包括： 
- 应用配置应使用客户管理的密钥：通过允许管理加密密钥，客户管理的密钥提供增强的数据保护。 这通常是满足合规性要求所必需的。
- 应用配置应使用专用链接：专用终结点连接允许虚拟网络上的客户端通过专用链接安全访问 Azure 应用配置。

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md) 

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="backup-and-recovery"></a>备份和恢复

有关详细信息，请参阅 [Azure 安全基准：备份和恢复](../security/benchmarks/security-controls-v2-backup-recovery.md)。

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：减少密钥丢失风险

**指导**：确保你有适当的措施来防止和恢复丢失的密钥。 在 Azure Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。

- [如何在 Key Vault 中启用软删除和清除保护](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](../security/benchmarks/security-controls-v2-governance-strategy.md)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略 

**指导**：确保为系统和数据的持续监视和保护记录并传达明确的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   与业务风险相符的数据分类标准

-   安全组织对风险和资产清单的洞察力 

-   安全组织对 Azure 服务使用的审批 

-   资产在其生命周期中的安全性

-   与组织数据分类相符的必需访问控制策略

-   使用 Azure 原生和第三方数据保护功能

-   传输中数据用例和静态数据用例的数据加密要求

-   合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure 安全基准 - 资产管理](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure 安全基准 - 数据保护](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指导**：建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合来细分对资产的访问。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指导**：持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指导**：确保为安全组织中的角色和责任记录并传达明确的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全历程培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

**指导**：建立 Azure 网络安全方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的网络管理和安全职责

-   符合企业分段策略的虚拟网络分段模型

-   各种威胁和攻击场景中的补救策略

-   Internet 边缘及入口和出口策略

-   混合云和本地互连策略

-   最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指导**：建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

-   各种用例和条件中的强身份验证方法

-   保护权限高的用户

-   异常用户活动监视和处理  

-   用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 标识管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure 安全基准 - 特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指南**：建立日志记录和威胁响应策略，以在满足符合性要求的同时快速检测和修正威胁。 优先为分析师提供高质量警报和无缝体验，以便他们能够专注于威胁，而不是执行集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   安全运营 (SecOps) 组织的角色和职责 

-   符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

-   日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

-   使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

-   与客户、供应商和公开的利益相关方之间的通信和通知计划

-   使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

-   处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 日志记录和威胁检测](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure 安全基准 - 事件响应](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)