---
title: Azure HPC 缓存的 azure 安全基线
description: Azure HPC 缓存安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dc695cc36113430cb1820d978ed41f5250cad33e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974759"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azure HPC 缓存的 azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用到 Microsoft Azure HPC 缓存。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的 **安全控制措施** 进行分组，以及适用于 Azure HPC 缓存的相关指南。 排除了不适用于 Azure HPC 缓存的 **控件**。

若要查看 Azure HPC 缓存如何完全映射到 Azure 安全基准，请参阅 [完整的 AZURE Hpc 缓存安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指南**：部署 Azure HPC 缓存资源时，必须创建或使用现有虚拟网络。 

确保所有 Azure 虚拟网络都遵循与业务风险相匹配的企业分段原则。 任何可能会给组织带来更高风险的系统都应隔离在其自己的虚拟网络中，并通过网络安全组 (NSG) 和/或 Azure 防火墙进行充分保护。

应设置两个与网络相关的先决条件，然后才能使用缓存： 

- Azure HPC 缓存实例的专用子网

- DNS 支持，以便缓存可以访问存储和其他资源

Azure HPC 缓存需要具有以下特性的专用子网： 

- 子网必须具有至少64的可用 IP 地址。

- 子网不能托管任何其他 Vm，甚至是适用于客户端计算机的相关服务。

- 如果使用多个 Azure HPC 缓存实例，则每个实例都需要其自己的子网。

最佳做法是为每个缓存创建一个新的子网。 创建缓存时，可以创建新的虚拟网络和子网。

若要将 HPC 缓存用于本地 NAS 存储，必须确保本地网络中的某些端口允许来自 Azure HPC 缓存的子网的无限制流量。 

- [如何配置 NFS 存储访问端口](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [如何创建包含安全规则的网络安全组](../virtual-network/tutorial-filter-network-traffic.md)

 

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

**指南**：使用 azure ExpressRoute 或 azure 虚拟专用网络 (VPN) 在归置环境中的 Azure 数据中心和本地基础结构之间创建专用连接。 ExpressRoute 连接不通过公共 internet，它们提供比典型 internet 连接更高的可靠性、更快的速度和更低的延迟。 对于点到站点 VPN 和站点到站点 VPN，可使用这些 VPN 选项的任意组合以及 Azure ExpressRoute 将本地设备或网络连接到虚拟网络。 

若要将 Azure 中的两个或多个虚拟网络连接在一起，请使用虚拟网络对等互连。 对等互连虚拟网络之间的网络流量是专用的，且保留在 Azure 主干网络上。

- [什么是 ExpressRoute 连接模型](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 概述](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [虚拟网络对等](../virtual-network/virtual-network-peering-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立对 Azure 服务的专用网络访问

**指南**：使用 Azure 虚拟网络服务终结点提供对 HPC 缓存的安全访问。 服务终结点是通过 Azure 主干网络优化的路由，无需跨 internet。 

HPC 缓存不支持使用 Azure 专用链接将其管理终结点固定到专用网络。 

除了 Azure 服务提供的身份验证和流量安全性外，专用访问还是其他深层防御措施。

- [了解虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [了解如何装载 Azure HPC 缓存](hpc-cache-mount.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务不受外部网络攻击

**指南**：针对外部网络的攻击保护 HPC 缓存资源，包括分布式拒绝服务 (DDoS) 攻击、特定于应用程序的攻击，以及未经请求的恶意 internet 流量。 

Azure 包括此保护的本机功能： 

- 使用 Azure 防火墙保护应用程序和服务免受来自 Internet 和其他外部位置的潜在恶意流量的侵害。 
- 通过在 Azure 虚拟网络上启用 DDoS 标准保护，保护资产免受 DDoS 攻击。 
- 使用 Azure 安全中心来检测与网络资源相关的错误配置风险。

Azure HPC 缓存并不用于运行 web 应用程序，并且不需要配置任何其他设置，也不需要部署任何额外的网络服务来保护其免受针对 web 应用程序的外部网络攻击。

- [Azure 防火墙文档](/azure/firewall/) 

- [使用 Azure 门户管理 Azure DDoS 防护标准](/azure/virtual-network/manage-ddos-protection) 

- [Azure 安全中心建议](../security-center/recommendations-reference.md#recs-network)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

**指南**：结合使用 Azure 防火墙和基于威胁智能的筛选，可以发出警报，并/或阻止来自已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。 

需要进行负载检查时，可以使用负载检查功能在 Azure Marketplace 中部署第三方入侵检测/入侵阻止系统 (ID/IPS) 解决方案。 或者，你可以选择使用基于主机的 ID/IP 或基于主机的终结点检测和响应 (EDR) 解决方案与或而不是基于网络的 ID/IP 结合使用。

注意：如果你对 IDS/IP 使用有法规或其他要求，请确保始终对其进行优化，以便为你的 SIEM 解决方案提供高质量的警报。

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace 第三方 ID 功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR 功能](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

**指南**：不适用;此建议适用于可部署到 Azure 虚拟网络中的产品/服务，或者能够定义允许的 IP 范围分组以实现有效的管理。 HPC 缓存目前不支持服务标记。 

最佳做法是为每个缓存创建一个新的子网。 创建缓存时，可以创建新的虚拟网络和子网。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全域名服务 (DNS)

**指南**：按照 dns 安全的最佳做法来防范常见攻击，例如无关联 DNS、dns amplifications 攻击、dns 中毒和欺骗等。

Azure HPC 缓存需要 DNS 访问缓存专用虚拟网络外部的资源。 如果你的工作流包含 Azure 之外的资源，则除了使用 Azure DNS 之外，还需要设置和保护你自己的 DNS 服务器。

- 若要访问 Azure Blob 存储终结点、基于 Azure 的客户端计算机或其他 Azure 资源，请使用 Azure DNS。
- 若要访问本地存储，或从 Azure 外部的客户端连接到缓存，需要创建可解析这些主机名的自定义 DNS 服务器。
- 如果工作流同时包含内部和外部资源，请将自定义 DNS 服务器设置为将 Azure 特定的解析请求转发到 Azure DNS 服务器。 

将 Azure DNS 用作权威 DNS 服务时，请确保使用 Azure RBAC 和资源锁防止 DNS 区域和记录发生意外或恶意修改。

如果要配置自己的 DNS 服务器，请确保遵循以下安全指导原则：

- [安全域名系统 (DNS) 部署指南](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [阻止无关联的 DNS 项并避免子域接管](../security/fundamentals/subdomain-takeover.md) 

- [详细了解 HPC 缓存的 DNS 访问要求](hpc-cache-prerequisites.md#dns-access)

- [Azure DNS 概述](../dns/dns-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-management"></a>标识管理

*有关详细信息，请参阅 [Azure 安全基准：标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)。*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：标准化 Azure Active Directory 作为中心标识和身份验证系统

**指南**： Azure HPC 缓存未与内部操作的 Azure Active Directory 集成。 但是，可以使用 Azure AD 来对 Azure 门户或 CLI 中的用户进行身份验证，以便创建、查看和管理 HPC 缓存部署和相关组件。

Azure Active Directory (Azure AD) 是 Azure 中的默认标识和访问管理服务。 应将 Azure AD 标准化，以便在中管理组织的标识和访问管理：

- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 应用程序。

- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，有助于根据 Microsoft 的最佳实践建议评估标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。

注意： Azure AD 支持允许没有 Microsoft 帐户的用户使用其外部标识登录到其应用程序和资源的外部标识。

- [Azure Active Directory 中的租赁](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [为应用程序使用外部标识提供程序](/azure/active-directory/b2b/identity-providers) 

- [Azure Active Directory 中的标识安全分数是什么](../active-directory/fundamentals/identity-secure-score.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全自动地管理应用程序标识

**指南**： HPC 缓存将 Azure 托管的标识用于非人工帐户，如服务或自动化。 建议使用 Azure 的托管标识功能，而不是创建功能更强大的人工帐户来访问或执行资源。 

HPC 缓存可以通过预定义的访问授权规则对支持 Azure AD 身份验证的 Azure 服务/资源进行本机身份验证。 这样就无需在源代码或配置文件中使用硬编码的凭据。

- [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md) 

- [支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 用于应用程序访问

**指南**：对于内部操作，Azure HPC 缓存不与 Azure AD 集成。 但是，可以使用 Azure AD 来对 Azure 门户或 CLI 中的用户进行身份验证，以便创建、查看和管理 HPC 缓存部署和相关组件。

Azure Active Directory 提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 这包括企业标识（例如员工）以及外部标识（如合作伙伴、供应商和供应商）。 这会启用 (SSO) 的单一登录，以管理和保护对本地和云中的组织数据和资源的访问。 将所有用户、应用程序和设备连接到 Azure AD，以便进行无缝、安全的访问和更好的可见性和控制。

- [了解 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对基于 Azure Active Directory 的所有访问使用强身份验证控制

**指南**：尽管 Azure HPC 缓存与内部操作的 Azure AD 不集成，但 Azure AD 可用于在 AZURE 门户或 CLI 中对用户进行身份验证，以便创建、查看和管理 HPC 缓存部署和相关组件。  

Azure AD 通过多重身份验证 (MFA) 和强无密码方法支持强身份验证控制。

- 多重身份验证：启用 Azure AD MFA，并遵循 Azure 安全中心的标识和访问管理建议，了解 MFA 设置中的一些最佳实践。 可以基于登录条件和风险因素，对所有用户、特选用户或单个用户强制执行 MFA。
- 无密码 authentication –三个无密码 authentication 选项可用： Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。

对于管理员和特权用户，请确保使用强身份验证方法的最高级别。 向其他用户推出适当的强身份验证策略。

Azure HPC 缓存还支持旧的基于密码的身份验证，适用于连接到缓存的客户端系统。 这种类型的连接包括仅限云的帐户 (直接在 Azure) 中创建的用户帐户，这些帐户具有基准密码策略或混合帐户 (来自本地 Active Directory) 的用户帐户将遵循本地密码策略。 

使用基于密码的身份验证时，Azure AD 提供了密码保护功能，以防止用户设置容易猜出的密码。 Microsoft 提供了基于遥测进行更新的禁止密码的全局列表，客户可以根据其需求增加列表 (例如，使用品牌、文化引用等) 。 此密码保护可用于纯云帐户和混合帐户。

注意：仅基于密码凭据的身份验证容易遭受常见的攻击方法攻击。 为了提高安全性，请使用强身份验证，例如 MFA 和强密码策略。 如果使用具有默认密码的第三方应用程序和 marketplace 服务，则在首次设置服务时，请设置新的安全密码。 

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory 的无密码 authentication 选项简介](../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD 默认密码策略](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密码保护消除错误密码](../active-directory/authentication/concept-password-ban-bad.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指南**： Azure HPC 缓存可使用 Azure Active Directory 从 Azure 门户进行用户管理。  Azure Active Directory 提供以下数据源：

- 登录 - 在登录报告中，可了解托管应用程序的使用情况和用户登录活动。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可以与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

Azure 安全中心还可以针对某些可疑活动（例如，过多的身份验证尝试失败，以及在订阅中不推荐使用的帐户）发出警报。

Azure 高级威胁防护 (ATP) 是一种安全解决方案，它可以使用 Active Directory 信号来识别、检测和调查高级威胁、泄露身份和恶意有问必答操作。

- [Azure Active Directory 中的审核活动报表](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

 

- [Azure 安全中心的威胁情报保护模块中的警报](../security-center/alerts-reference.md) 

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据公开

**指南**：不适用;HPC 缓存不允许客户将任何持久化数据部署到正在运行的环境。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="privileged-access"></a>特权访问

*有关详细信息，请参阅 [Azure 安全基准：特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**： HPC 缓存使用 Azure RBAC 来隔离对关键关键系统的访问，方法是限制授予对其所在订阅和管理组的特权访问权限的帐户。

围绕专用管理帐户的使用创建标准操作程序。 若要创建缓存，HPC 缓存要求用户在订阅中具有足够的权限来创建 Nic。 如果使用 Blob 存储，则需要使用 RBAC 角色存储帐户参与者和存储 Blob 数据参与者来访问存储。 

确保你还限制对对关键业务资产具有管理访问权限的管理、标识和安全系统的访问权限，例如 Active Directory 域控制器 (DCs) 、安全工具和系统管理工具，以及安装在业务关键系统上的代理。 泄露这些管理和安全系统的攻击者可以立即 weaponize 它们，以损害业务关键资产。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

- [Azure HPC 缓存 RBAC 权限](hpc-cache-prerequisites.md#permissions)

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [访问管理组](../governance/management-groups/overview.md#management-group-access)

- [Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**：定期查看用户帐户和访问分配，以确保帐户及其访问级别有效。 

Azure HPC 缓存可使用 Azure Active Directory (Azure AD) 帐户来通过 Azure 门户和相关接口管理用户访问。 Azure AD 提供了访问评审，可帮助你查看组成员身份、对企业应用程序的访问权限以及角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可以使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流，以便于审核过程。

此外，Azure Privileged Identity Management 还可配置为在创建过多的管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。

注意：某些 Azure 服务支持不通过 Azure AD 管理的本地用户和角色。 需要单独管理这些用户。

使用 NFS 存储目标时，需要与网络管理员和防火墙管理员合作来验证访问设置，并确保 Azure HPC 缓存能够与 NFS 存储系统进行通信。

- [有关 HPC 缓存权限的列表，请阅读 Azure HPC 缓存必备组件](hpc-cache-prerequisites.md) 

- [在 Privileged Identity Management (PIM 中创建 Azure 资源角色的访问评审) ](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指南**： HPC 缓存可使用 Azure Active Directory 通过 Azure 门户管理资源访问权限。 为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指南**： HPC 缓存可使用 Azure Active Directory 通过 Azure 门户管理对缓存资源的访问。 

使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。 

- [什么是 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md) 

- [什么是 Azure AD 权限管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**： HPC 缓存与 Azure 基于角色的访问控制集成， (RBAC) 管理其资源。 使用 azure RBAC，可通过角色分配来管理 Azure 资源访问权限。 可以将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 

通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的权限。 这补充了 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法，应定期查看。

请使用内置角色来分配权限，仅在必要时创建自定义角色。

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1：发现、分类和标记敏感数据 

**指南**： HPC 缓存管理敏感数据，但无法发现、分类和标记敏感数据。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指南**：通过使用 Azure 基于角色的访问控制来限制对敏感数据的访问控制 (azure RBAC) 、基于网络的访问控制和 azure 服务中的特定控件 (例如，SQL 中的加密和) 的其他数据库。

为了确保一致的访问控制，所有类型的访问控制都应符合企业分段策略。 企业分段策略还应根据敏感的或业务关键型的数据和系统的位置来确定。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据始终安全，Microsoft 实施了一些默认的数据保护控制机制和功能。

- [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：监视未经授权的敏感数据传输

**指南**： HPC 缓存会传输敏感数据，但不支持监视敏感数据的未授权传输。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

**指南**： HPC 缓存在传输时支持 TLS 1.2 或更高版本的数据加密。

虽然这对于专用网络上的流量来说是可选的，但对于外部和公共网络上的流量来说，这是至关重要的。 对于 HTTP 流量，请确保连接到 Azure 资源的任何客户端能够协商 TLS v1.2 或更高版本。 对于远程管理，请使用 SSH（适用于 Linux）或 RDP/TLS（适用于 Windows），而不是使用未加密的协议。 应当禁用已过时的 SSL、TLS 和 SSH 版本和协议，以及弱密码。

默认情况下，Azure 为在 Azure 数据中心之间传输的数据提供加密。

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [有关 TLS 安全性的信息](/security/engineering/solving-tls1-problem) 

- [传输中的 Azure 数据的双重加密](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：加密静态敏感数据

**指南**：若要补充访问控制，应防止静态数据受到 "带外" 的攻击 (例如，使用加密访问基础存储) 。 这有助于确保攻击者无法轻松读取或修改数据。

默认情况下，Azure 提供静态数据加密。 对于高度敏感的数据，你可以选择在所有可用的 Azure 资源上进行额外的静态加密。 默认情况下，azure 会管理你的加密密钥，但 Azure 提供选项来管理你自己的密钥， (某些 Azure 服务) 客户管理的密钥。

默认情况下，在 Azure 中存储的所有数据（包括在缓存磁盘上）都使用 Microsoft 托管密钥进行静态加密。 如果要管理用于对数据进行加密的密钥，则只需要自定义 Azure HPC 缓存设置。

如果需要在计算资源上确保合规性，则实施第三方工具（如基于主机的自动数据丢失防护解决方案），以便对数据强制实施访问控制，即使数据从系统复制也是如此。

- [如何将客户托管的加密密钥用于 Azure HPC 缓存](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-create?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [如何配置客户管理的加密密钥](/azure/storage/common/storage-encryption-keys-portal) 

- [加密模型和密钥管理表](../security/fundamentals/encryption-atrest.md)

 

- [Azure 中的静态数据双重加密](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="asset-management"></a>资产管理

*有关详细信息，请参阅 [Azure 安全基准：资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 azure 租户和订阅中向安全团队授予安全读者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指南**： Azure HPC 缓存支持使用标记。 将标记应用于 Azure 资源、资源组和订阅，以逻辑方式将它们组织到分类。 每个标记均由名称和值对组成。 

例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。 可以在创建缓存时添加标记，也可以在部署缓存后添加标记。 

使用 Azure 虚拟机库存自动收集有关虚拟机上的软件的信息。 可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获取安装日期和其他信息的访问权限，请启用来宾级别诊断，并将 Windows 事件日志引入 Log Analytics 工作区。
HPC 缓存不允许在其资源上运行应用程序或软件安装。 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md) 

- [Azure 安全中心资产清单管理](../security-center/asset-inventory.md) 

- [资源命名和标记决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json) 

- [如何启用 Azure 虚拟机库存](../automation/automation-tutorial-installed-software.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指南**： HPC 缓存支持 Azure 资源管理器部署。 请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types) 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指南**：不适用。 不能使用 Azure HPC 缓存来确保生命周期管理过程中资产的安全性。 客户负责维护资产的属性和网络配置，这些资产被视为很大影响。 

建议客户创建一个流程来捕获属性和网络配置更改，测量更改影响，并创建相应的补救任务。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

*有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指南**：使用 Azure 安全中心内置威胁检测功能，并为 HPC 缓存资源启用 azure Defender (正式的 Azure 高级威胁防护) 。 Azure Defender for HPC 缓存提供额外的安全智能层，用于检测访问或利用缓存资源的异常和潜在有害尝试。

将所有日志从 HPC 缓存转发到你的 SIEM，可用于设置自定义威胁检测。 确保正在监视不同类型的 Azure 资产，以发现潜在的威胁和异常情况。 专注于获得高质量的警报，以减少对分析人员进行排序的误报。 警报可能源自日志数据、代理或其他数据。

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection) 

- [Azure 安全中心安全警报参考指南](../security-center/alerts-reference.md) 

- [创建自定义分析规则以检测威胁](../sentinel/tutorial-detect-threats-custom.md) 

- [利用 Azure Sentinel 进行网络威胁智能](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**： Azure AD 提供以下用户日志，可以在报告中查看 Azure AD 报告或与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具集成，以实现更复杂的监视和分析用例：
- 登录– "登录" 报表提供有关托管应用程序和用户登录活动的使用情况的信息。

- 审核日志-通过日志提供由 Azure AD 中各种功能完成的所有更改的跟踪。 审核日志的示例包括对 Azure AD 中的任何资源所做的更改，如添加或删除用户、应用、组、角色和策略。

- 风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可以针对某些可疑活动发出警报，如失败的身份验证尝试次数过多，或订阅中的帐户已弃用。 除了基本的安全卫生监视，Azure 安全中心的威胁防护模块还可以从单个 Azure 计算资源收集更详细的安全警报， (虚拟机、容器、应用服务) 、数据资源 (SQL 数据库和存储) 以及 Azure 服务层。 此功能可让你查看单个资源内的帐户异常情况。

- [Azure Active Directory 中的审核活动报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [启用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指南**：除了常用的数据包捕获工具外，还可以使用 VPN 网关和其数据包捕获功能来记录虚拟网络之间的网络数据包。

在部署了 Azure HPC 缓存资源的网络上部署网络安全组。 为流量审核启用网络安全组流日志。

流日志将保留在存储帐户中。 启用流量分析解决方案来处理这些流日志并将其发送到 Log Analytics 工作区。 流量分析提供有关 Azure 网络流量的更深入见解。 流量分析可帮助你直观显示网络活动，确定热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

缓存需要 DNS 来访问其虚拟网络外部的资源。 根据所使用的资源，可能需要设置自定义 DNS 服务器并配置该服务器与 Azure DNS 服务器之间的转发。 

根据组织的需求，从 Azure 市场实现 DNS 日志记录解决方案的第三方解决方案。

- [为 VPN 网关配置数据包捕获](../vpn-gateway/packet-capture.md) 

- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md) 

- [了解 Azure 安全中心提供的网络安全性](../security-center/security-center-network-recommendations.md) 

- [了解有关 DNS 先决条件的详细信息](hpc-cache-prerequisites.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指南**： Azure HPC 缓存资源自动创建活动日志。 这些日志包含 (PUT、POST、DELETE) 的所有写入操作，但不包括读取操作 (GET) 。 活动日志可用于在故障排除时查找错误，或监视组织中的用户如何修改资源。

还可以使用 Azure 安全中心和 Azure 策略为 HPC 缓存启用 Azure 资源日志，并使用和记录数据收集。 这些日志对于日后调查安全事件和执行鉴证演练可能至关重要。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md) 

- [了解 Azure 安全中心数据收集](../security-center/security-center-enable-data-collection.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指南**：集中记录存储和分析以启用关联。 对于每个日志源，请确保已分配一个数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

此外，启用数据并将数据集成到 Azure Sentinel 或第三方 SIEM。

许多组织选择使用 Azure Sentinel 作为 "热" 数据，这些数据经常使用，而 Azure 存储则用于不频繁使用的 "冷" 数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备–更新 Azure 的事件响应过程

**指南**：确保你的组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保准备情况。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2：准备–设置事件通知 

**指南**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析-基于高质量警报创建事件

**指南**：确保你有创建高质量警报的过程，并衡量警报的质量。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

高质量的警报可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具。 

Azure 安全中心可跨多个 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 Azure Sentinel 使你可以创建高级警报规则，以便自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析-调查事件

**指南**：确保分析师可以在调查潜在事件时查询和使用不同的数据源，从而生成所发生问题的完整视图。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。  还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。  

用于调查的数据源包括已从作用域内服务和正在运行的系统中收集的集中式日志记录源，但还可以包括以下内容：

- 网络数据-使用网络安全组的流日志、Azure 网络观察程序和 Azure Monitor 来捕获网络流日志和其他分析信息。 

- 正在运行的系统的快照： 

    - 使用 Azure 虚拟机的快照功能创建正在运行的系统磁盘的快照。 

    - 使用操作系统的本机内存转储功能来创建正在运行的系统内存的快照。

    - 使用 Azure 服务的快照功能或软件自带的功能来创建正在运行的系统的快照。

Azure Sentinel 跨几乎任何日志源和事例管理门户提供大量的数据分析，以管理事件的整个生命周期。 调查过程中的智能信息可与事件相关联，以便进行跟踪和报告。 

- [Windows 计算机的磁盘快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 计算机的磁盘快照](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支持诊断信息和内存转储收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [用 Azure Sentinel 调查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR 5：检测和分析–优先级事件

**指南**：根据警报严重性和资产敏感度向分析师提供要重点关注的事件的上下文。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在查找或用于发出警报的从分析上中的置信度，以及导致警报的活动的恶意意图的置信度。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复-自动化事件处理

**指南**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能来自动触发操作或运行操作手册来响应传入安全警报。 操作手册执行一些操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>状况和漏洞管理

*有关详细信息，请参阅 [Azure 安全基准：状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：为计算资源建立安全配置

**指南**：使用 Azure 安全中心和 azure 策略在所有计算资源（包括 vm、容器和其他）上建立安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md) 

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指南**：根据需要，对 Azure 资源进行渗透测试或 red 团队活动，并确保对所有关键安全发现的修正。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="backup-and-recovery"></a>备份和恢复

*有关详细信息，请参阅 [Azure 安全基准：备份和恢复](/azure/security/benchmarks/security-controls-v2-backup-recovery)。*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1：确保定期执行自动备份

**指南**：由于 Azure HPC 缓存是一种缓存解决方案，而不是存储系统，因此请重点确保定期备份其存储目标中的数据。 遵循 Azure Blob 容器的标准过程，并备份任何本地存储目标。 

若要最大程度地减少发生地区性中断时的中断，可以采取措施来确保跨区域数据访问。  

每个 Azure HPC 缓存实例在特定订阅和一个区域中运行。 这意味着，如果区域出现完全中断，则可能会中断缓存工作流。 为了最大限度地减少这种中断，组织应使用可从多个区域访问的后端存储。 此存储可以是具有适当 DNS 支持的本地 NAS 系统，也可以是驻留在与缓存不同的区域中的 Azure Blob 存储。

当你的工作流在主要区域中继续时，数据将保存在区域外的长期存储中。 如果缓存区域变得不可用，则可以在次要区域中创建重复的 Azure HPC 缓存实例，连接到相同的存储，并从新缓存恢复工作。

- [阅读有关区域故障转移的详细信息](hpc-region-recovery.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="br-2-encrypt-backup-data"></a>BR-2：加密备份数据

**指南**：确保你的备份受到保护。 这应包括对备份进行加密，以防止丧失机密性。

对于使用 Azure 备份的本地备份，将使用所提供的密码提供静态加密。 对于常规 Azure 服务备份，将使用 Azure 平台管理的密钥自动对备份数据进行加密。 你可以选择使用客户管理的密钥对备份进行加密。 在这种情况下，请确保密钥保管库中的此客户托管的密钥也在备份作用域中。

即使你为缓存磁盘添加了客户密钥，Azure HPC 缓存还受托管磁盘上的 VM 主机加密保护，这些磁盘保存缓存数据。 为双加密添加客户托管的密钥可为具有高安全性需求的客户提供额外的安全级别。 有关详细信息，请参阅 Azure 磁盘存储的服务器端加密。

在 Azure 备份、Azure Key Vault 或其他资源中使用基于角色的访问控制，以保护备份和客户管理的密钥。 此外，可启用高级安全功能，要求在更改或删除备份之前进行 MFA。

- [VM 主机加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Azure 磁盘存储的服务器端加密](../virtual-machines/disk-encryption.md)

- [Azure 备份中的安全功能概述](../backup/security-overview.md) 

- [使用客户托管密钥加密备份数据](../backup/encryption-at-rest-with-cmk.md)  

- [如何在 Azure 中备份 Key Vault 密钥](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：验证所有备份，包括客户管理的密钥

**指南**：定期确保你可以还原已备份的客户管理的密钥。

- [如何在 Azure 中还原 Key Vault 密钥](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：减少密钥丢失风险

**指南**：确保已准备好措施来防止密钥丢失和从密钥丢失中恢复。 在 Azure Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。

- [如何在 Key Vault 中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="governance-and-strategy"></a>治理和策略

*有关详细信息，请参阅 [Azure 安全基准：管理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定义资产管理和数据保护策略 

**指南**：确保为系统和数据的持续监视和保护记录并传达清晰的策略。 确定业务关键数据和系统的发现、评估、保护和监视优先级。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   与业务风险相符的数据分类标准

-   安全组织对风险和资产清单的洞察力 

-   安全组织对 Azure 服务使用的审批 

-   资产在其生命周期中的安全性

-   与组织数据分类相符的必需访问控制策略

-   使用 Azure 本机和第三方数据保护功能

-   传输中数据用例和静态数据用例的数据加密要求

-   合适的加密标准

有关详细信息，请参阅以下资源：
- [Azure 安全体系结构建议 - 存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全基准 - 资产管理](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure 安全基准 - 数据保护](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指南**：建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合来细分对资产的访问。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指南**：持续衡量并降低你的个人资产和托管的环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指南**：确保为安全组织中的角色和责任记录并传达清晰的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1-人员：教育团队开展云安全旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定义网络安全策略

**指南**：建立 Azure 网络安全方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的网络管理和安全职责

-   符合企业分段策略的虚拟网络分段模型

-   各种威胁和攻击场景中的补救策略

-   Internet 边缘及入口和出口策略

-   混合云和本地互连策略

-   最新的网络安全项目（例如网络关系图、参考网络体系结构）

有关详细信息，请参阅以下资源：
- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全基准 - 网络安全](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指南**：建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的一部分。  

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   集中化的标识和身份验证系统及其与其他内部和外部标识系统的互连

-   各种用例和条件中的强身份验证方法

-   保护权限高的用户

-   异常用户活动监视和处理  

-   用户标识和访问评审及协调流程

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 标识管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 安全基准 - 特权访问](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指南**：建立日志记录和威胁响应策略，以快速检测和修正威胁，同时满足合规性要求。 确定提供具有高质量警报和无缝体验的分析师的优先级，以便他们能够专注于威胁而不是集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   安全运营 (SecOps) 组织的角色和职责 

-   符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

-   日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

-   使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

-   与客户、供应商和公开的利益相关方之间的通信和通知计划

-   使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

-   处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 日志记录和威胁检测](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
