---
title: 适用于 Windows 虚拟桌面的 Azure 安全基线
description: Windows 虚拟桌面安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 393495dabb77a5d177c97d37313433bb00ce5a36
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726744"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>适用于 Windows 虚拟桌面的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用于 Windows 虚拟桌面。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容由 Azure 安全基准定义的 **安全控制** 和适用于 Windows 虚拟桌面的相关指南进行分组。 排除了不适用于 Windows 虚拟桌面的 **控件**。

Windows 虚拟桌面服务包括服务本身，Windows 10 企业版适用于多会话虚拟 sku 和 FSLogix。 有关 FSLogix 相关的安全建议，请参阅 [存储的安全基线](../storage/common/security-baseline.md)。 该服务具有在虚拟机上运行的代理，但由于虚拟机完全控制客户，因此请遵循 [有关计算的安全建议](../virtual-machines/windows/security-baseline.md)

若要查看 Windows 虚拟桌面完全映射到 Azure 安全基准的方式，请参阅 [完整的 Windows 虚拟桌面安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](../security/benchmarks/security-controls-v2-network-security.md)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指南**：部署要注册到 Windows 虚拟桌面的虚拟机时，必须创建或使用现有虚拟网络。 确保所有 Azure 虚拟网络都遵循与业务风险相匹配的企业分段原则。 可能会对组织造成较高风险的任何系统都应在其自己的虚拟网络中隔离，并使用网络安全组或 Azure 防火墙进行充分的保护。

使用 Azure 安全中心的自适应网络强化功能推荐网络安全组配置，这些配置使用外部网络流量规则对端口和源 Ip 进行限制。

考虑应用程序和企业分段策略的实际情况，根据网络安全组规则限制或允许内部资源之间的流量传递。 对于明确定义的特定应用程序（例如 3 层应用），可采用高度安全的“默认拒绝，允许例外”方法。 如果有多个应用程序和终结点彼此交互，那么这种方法的扩展性可能并不好。 如果在中心/辐射拓扑中的大量企业分段或辐射 (需要集中管理，则还可以使用 Azure 防火墙) 

对于与属于 Windows 虚拟桌面) 子网的虚拟机 (关联的网络安全组，必须允许传出流量发送到特定终结点。 

- [了解需要哪些 Url 才能访问 Windows 虚拟桌面](safe-url-list.md)

- [Azure 安全中心内的自适应网络强化](../security-center/security-center-adaptive-network-hardening.md) 

- [适用于 Windows 虚拟桌面的 Azure 防火墙 ](../firewall/protect-windows-virtual-desktop.md)

- [如何创建包含安全规则的网络安全组](../virtual-network/tutorial-filter-network-traffic.md)

 

- [如何部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

**指南**：使用 azure ExpressRoute 或 azure 虚拟专用网络在归置环境中的 azure 数据中心和本地基础结构之间创建专用连接。 ExpressRoute 连接不通过公共 internet，提供更高的可靠性、更快的速度和比典型 internet 连接更少的延迟。 

对于点到站点和站点到站点虚拟专用网络，可以使用虚拟专用网络选项和 Azure ExpressRoute 的任意组合将本地设备或网络连接到虚拟网络。

使用虚拟网络对等互连在 Azure 中将两个或多个虚拟网络连接在一起。 对等互连虚拟网络之间的网络流量是专用的，并保持在 Azure 主干网络中。

- [什么是 ExpressRoute 连接模型](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 概述](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [虚拟网络对等](../virtual-network/virtual-network-peering-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保护应用程序和服务不受外部网络攻击

**指南**：使用 Azure 防火墙来保护应用程序和服务免受来自 internet 和其他外部位置的潜在恶意流量的侵害。 保护 Windows 虚拟桌面资源免受来自外部网络的攻击，包括分布式拒绝服务攻击、特定于应用程序的攻击、未经请求的恶意 internet 流量。 通过在 Azure 虚拟网络上启用 DDoS 标准保护，保护资产免受分布式拒绝服务攻击。 使用 Azure 安全中心来检测与网络相关资源相关的错误配置风险。

Windows 虚拟桌面不打算运行 web 应用程序，并且不需要配置任何其他设置，也不需要部署任何额外的网络服务来保护其免受针对 web 应用程序的外部网络攻击。

- [Azure 防火墙文档](../firewall/index.yml)

- [使用 Azure 门户管理 Azure DDoS 防护标准](../ddos-protection/manage-ddos-protection.md) 

- [Azure 安全中心建议](../security-center/recommendations-reference.md#networking-recommendations)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

**指南**：结合使用 Azure 防火墙和基于威胁情报的筛选功能，可以在已知的恶意 IP 地址和域之间发出警报并有选择地阻止流量。 IP 地址和域源自 Microsoft 威胁智能源。 需要进行负载检查时，可以从 Azure Marketplace 部署第三方入侵检测或防护解决方案。 

如果对入侵检测或预防解决方案的使用有法规或其他要求，请确保始终对其进行调整，以便向安全信息和事件管理提供高质量的警报 (SIEM) 解决方案。

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure 市场包含第三方 IDS 功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR 功能](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：简化网络安全规则

**指南**：使用 Azure 虚拟网络服务标记来定义网络安全组或为 Windows 虚拟桌面资源配置的 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称 (例如： WindowsVirtualDesktop) ，可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

- [在此处了解 Windows 虚拟桌面服务标记所涵盖的内容 ](safe-url-list.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指南**： Windows 虚拟桌面使用 Azure Active Directory (Azure AD) 为默认的标识和访问管理服务。 你应该使 Azure AD 标准化，以便控制组织在以下资源中的标识和访问管理：

- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机（Linux 和 Windows）、Azure Key Vault、PaaS 和 SaaS 应用程序。

- 你的组织的资源，例如 Azure 上的应用程序，或公司网络资源。

在组织的云安全做法中，应优先处理 Azure AD 保护事宜。 Azure AD 提供标识安全分数，帮助你评估与 Microsoft 的最佳做法建议相关的标识安全状况。 使用评分来估计你的配置与最佳做法建议的匹配程度，并改善你的安全状况。

Azure AD 支持允许没有 Microsoft 帐户的用户使用其外部标识登录到其应用程序和资源的外部标识。

- [Azure AD 中的租户](../active-directory/develop/single-and-multi-tenant-apps.md)

- [使用应用程序的外部标识提供者](../active-directory/external-identities/identity-providers.md)

- [Azure AD 中的标识安全评分是什么](../active-directory/fundamentals/identity-secure-score.md)

- [运行 Windows 虚拟桌面所需的特定角色 ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指南**： Windows 虚拟桌面支持非人工帐户（如服务或自动化）的 Azure 托管标识。 建议使用 Azure 托管标识功能，而不是创建功能更强大的人工帐户来访问或执行资源。 

Windows 虚拟桌面建议使用 Azure Active Directory (Azure AD) 在资源级别创建具有受限权限的服务主体，以使用证书凭据配置服务主体，并回退到客户端密码。 在这两种情况下，都可以将 Azure Key Vault 与 Azure 托管标识结合使用，以便运行时环境 (例如，Azure Function) 可以从密钥保管库中检索凭据。

- [支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps) 

- [使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [使用 Azure Key Vault 进行安全主体注册](../key-vault/general/authentication.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

**指南**： Windows 虚拟桌面使用 Azure Active Directory (Azure AD) 提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 此内容包括企业标识（例如员工）以及外部标识（如合作伙伴和供应商）。 这样，单一登录 (SSO) 便可以管理和保护对本地和云中的组织数据和资源的访问。 将所有用户、应用程序和设备连接到 Azure AD 以获得更好的可见性和控制。

- [了解 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制 

**指南**： Windows 虚拟桌面使用 Azure Active Directory (Azure AD) ，通过多重身份验证和无密码方法支持强身份验证控制。

- 多重身份验证-启用 Azure AD 多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议，以获取多重身份验证设置中的一些最佳实践。 可对所有用户或选定用户强制执行多重身份验证，也可根据登录条件和风险因素在每个用户级别这样做。

- 无密码身份验证 - 提供三个无密码身份验证选项：Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。

Windows 虚拟桌面支持传统的基于密码的身份验证，例如仅限云的帐户 (直接在 Azure) 中创建的用户帐户，这些用户帐户具有基准密码策略或混合帐户 (从本地 Azure AD 遵循本地密码策略) 的用户帐户。 使用基于密码的身份验证时，Azure AD 提供密码保护功能，使用户能够设置容易猜测的密码。 Microsoft 提供了基于遥测进行更新的禁止密码的全局列表，客户可以根据其需要 (例如品牌、文化引用等) 来补充列表。 此密码保护可用于纯云帐户和混合帐户。

仅基于密码凭据的身份验证容易受到常见的攻击方法。 为了提高安全性，请使用强身份验证，例如多重身份验证和强密码策略。 对于可能具有默认密码的第三方应用程序和 marketplace 服务，应在服务初始设置时更改它们。

对于管理员和特权用户，请确保使用的是最高级别的强身份验证方法，然后将相应的强身份验证策略推出给其他用户。

- [Azure Active Directory 的无密码身份验证选项简介](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD 默认密码策略](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [使用 Azure Active Directory 密码保护消除错误密码](../active-directory/authentication/concept-password-ban-bad.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指南**： Windows 虚拟桌面与提供以下数据源的 Azure Active Directory (Azure AD) 集成：

- 登录-登录报表提供有关托管应用程序和用户登录活动的使用情况的信息。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

- 风险登录-有风险登录是指可能已由不是用户帐户合法所有者执行的登录尝试的指示符。

- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可以与 Azure Monitor、Azure Sentinel 或第三方安全信息和事件管理 (SIEM) 系统相集成。 Azure 安全中心还可针对某些可疑活动发出警报，例如，失败的身份验证尝试次数过多，使用了订阅中的已弃用帐户。 Azure 高级威胁防护 (ATP) 是一种安全解决方案，它可使用 Active Directory 信号来识别、检测和调查高级威胁、泄露的标识以及恶意的内部操作。

- [Azure AD 中的审核活动报告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 安全中心的威胁情报保护模块中的警报](../security-center/alerts-reference.md)

- [如何将 Azure 活动日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指南**：对于基于用户定义的条件的粒度访问控制，Windows 虚拟桌面支持 Azure Active Directory (Azure AD) 的条件性访问。 例如，可能需要来自某些 IP 范围的用户登录，才能使用多重身份验证进行访问。 

此外，精细身份验证会话管理策略还可用于不同的用例。

- [Azure 条件访问概述](../active-directory/conditional-access/overview.md) 

- [常见的条件访问策略](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [使用条件访问配置身份验证会话管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [可在此处找到 Windows 虚拟桌面特定的条件访问设置信息](set-up-mfa.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**： Windows 虚拟桌面使用基于 azure 角色的访问控制 (azure RBAC) 隔离对关键业务系统的访问。 确保你还将访问权限限制为对业务关键访问具有管理访问权限的管理、标识和安全系统，如 Active Directory 域控制器、安全工具以及安装在业务关键系统上的代理的系统管理工具。 泄露这些管理和安全系统的攻击者可能会立即 weaponize 它们，以损害业务关键资产。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [访问管理组](../governance/management-groups/overview.md#management-group-access) 

- [Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [管理 Windows 虚拟桌面所需的最少管理员权限](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**： Windows 虚拟桌面使用 Azure Active Directory (Azure AD) 帐户来管理其资源、定期查看用户帐户和访问分配，以确保帐户及其访问有效。

使用 Azure AD 访问评审来查看组成员身份、对企业应用程序的访问权限以及角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。

此外，Azure Privileged Identity Management 还可配置为在创建过多管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。

某些 Azure 服务支持不通过 Azure AD 进行管理的本地用户和角色。 你需要单独管理这些用户。

- [Windows 虚拟桌面的内置角色](rbac.md)

- [在 Privileged Identity Management (PIM) 中创建对 Azure 资源角色的访问评审](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指南**： Windows 虚拟桌面使用 Azure Active Directory (Azure AD) 来管理其资源。 为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](../active-directory/roles/security-emergency-access.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指南**： Windows 虚拟桌面与 Azure Active Directory (集成 Azure AD) 管理其资源。 使用 Azure AD 的权利管理功能来自动执行访问请求工作流，包括访问权限分配、审核和过期。 另外，还支持双重或多阶段审批。

- [什么是 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md) 

- [什么是 Azure AD 权利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指南**：安全和隔离工作站对于敏感角色（如、管理员、开发人员和关键服务操作员）的安全性至关重要。 使用高度安全的用户工作站和/或 Azure Bastion 执行管理任务。 

使用 Azure Active Directory (Azure AD) ，Microsoft Defender 高级威胁防护 (ATP) 或 Microsoft Intune 部署用于管理任务的安全管理用户工作站。 可以集中管理安全的工作站来强制实施受保护的配置，包括强身份验证、软件和硬件基准、受限的逻辑和网络访问。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [部署特权访问工作站](/security/compass/privileged-access-deployment)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**： Windows 虚拟桌面与 azure 基于角色的访问控制 (azure RBAC) 集成，以管理其资源。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可以将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 

使用 Azure RBAC 分配给资源的特权应始终限制为这些角色所需的权限。 这补充了 Privileged Identity Management (PIM) 的实时 (JIT) 方法（Azure Active Directory (Azure AD) ），应定期查看。

此外，使用内置角色来分配权限，并且仅在需要时才创建自定义角色。

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

- [Windows 虚拟桌面的内置角色](rbac.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：选择 Microsoft 支持的批准流程  

**指南**：在 Microsoft 需要访问客户数据的支持方案中，Windows 虚拟桌面支持客户密码箱提供用于查看和批准或拒绝客户数据访问请求的接口。

- [了解客户密码箱](../security/fundamentals/customer-lockbox-overview.md)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-controls-v2-data-protection.md)。

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1：对敏感数据进行发现、分类和标记

**指南**：对敏感数据进行发现、分类和标记，以便可以设计适当的控件。 这是为了确保组织的技术系统安全存储、处理和传输敏感信息。

使用 Azure 信息保护 (及其关联的扫描工具) 了解 Azure、本地、Office 365 和其他位置中的 Office 文档中的敏感信息。

使用 Azure SQL 信息保护有助于对 Azure SQL 数据库中存储的信息进行分类和标记。

- [使用 Azure 信息服务标记敏感信息](/azure/information-protection/what-is-information-protection) 

- [如何实现 Azure SQL 数据发现](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC)、基于网络的访问控制以及 Azure 服务中的特定控制（例如 SQL 和其他数据库中的加密）来限制访问，从而保护敏感数据。

为了确保一致的访问控制，所有类型的访问控制都应符合企业分段策略。 企业分段策略还应根据敏感的或业务关键型的数据和系统的位置来确定。

Microsoft 将所有客户内容视为敏感数据，并防范客户数据丢失和公开。 为了确保 Azure 中的客户数据始终安全，Microsoft 实施了一些默认的数据保护控制机制和功能。

- [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：监视未经授权的敏感数据传输

**指南**：监视是否有向企业可见和控制范围以外的位置进行未经授权的数据传输。 这通常涉及监视那些可能意味着未经授权的数据外泄的异常活动（大型或异常传输）。

高级威胁防护 (具有 Azure 存储和 Azure SQL ATP 的 ATP) 功能，可以在信息异常传输时发出警报，指示可能会未经授权传输敏感信息。

Azure 信息保护 (AIP) 提供的监视功能针对已分类并标记的信息。

使用数据丢失防护解决方案（例如基于主机的解决方案）来强制使用数据丢失和/或预防控制来防止数据渗透。

- [启用 Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [启用 Azure 存储 ATP](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](../security/benchmarks/security-controls-v2-asset-management.md)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任的构建方式，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

若要查看工作负荷和服务，可能需要其他权限。 

- [安全读取者角色概述](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指导**：将标记应用到 Azure 资源、资源组和订阅，以便有条理地将它们组织成分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

使用 Azure 虚拟机库存自动收集有关虚拟机上的软件的信息。 可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获取安装日期和其他信息的访问权限，请启用来宾级别诊断，并将 Windows 事件日志引入 Log Analytics 工作区。

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md) 

- [Azure 安全中心资产库存管理](../security-center/asset-inventory.md) 

- [资源命名和标记决策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [如何启用 Azure 虚拟机库存](../automation/automation-tutorial-installed-software.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指导**：请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general) 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指导**：不适用。 Windows 虚拟桌面不能用于确保生命周期管理过程中资产的安全性。 客户负责维护资产的属性和网络配置，这些资产被视为具有重大影响。 

建议客户创建一个流程来捕获属性和网络配置的更改，并根据情况测量更改影响和创建补救任务。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6：仅使用计算资源中经过批准的应用程序

**指南**：使用 Azure 虚拟机库存自动收集有关虚拟机上所有软件的信息。 可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获取安装日期和其他信息的访问权限，请启用来宾级别诊断，并将 Windows 事件日志引入 Log Analytics 工作区。

- [如何启用 Azure 虚拟机库存](../automation/automation-tutorial-installed-software.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](../security/benchmarks/security-controls-v2-logging-threat-detection.md)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指南**：使用 Azure 安全中心内置威胁检测功能，并为你的 Windows 虚拟桌面资源启用 azure Defender (正式的 Azure 高级威胁防护) 。 Azure Defender for Windows 虚拟桌面提供额外的安全智能层，用于检测访问或利用 Windows 虚拟桌面资源的异常和潜在有害尝试。

将 Windows 虚拟桌面中的所有日志转发到安全信息事件管理 (SIEM) 解决方案，该解决方案可用于设置自定义威胁检测。 确保正在监视不同类型的 Azure 资产，以发现潜在的威胁和异常情况。 专注于获取高质量警报以减少误报，便于分析人员进行分类整理。 警报可能源自日志数据、代理或其他数据。

- [Azure 安全中心的威胁防护](../security-center/azure-defender.md) 

- [Azure 安全中心安全警报参考指南](../security-center/alerts-reference.md)

- [创建自定义分析规则以检测威胁](../sentinel/tutorial-detect-threats-custom.md) 

- [利用 Azure Sentinel 进行网络威胁智能](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**： Azure Active Directory (Azure AD) 提供了以下用户日志，可在 Azure AD 报表中查看这些用户日志，或将其与 Azure Monitor、Azure Sentinel 或其他安全信息和事件管理 (，用于更复杂的监视和分析用例的 SIEM) 或监视工具：

- 登录–登录报表提供有关托管应用程序和用户登录活动的使用情况的信息。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

- 有风险的登录是指可能已由不是用户帐户合法所有者的用户执行的登录尝试的指示符的指示。

- 已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可以针对某些可疑活动发出警报，例如，订阅中的失败身份验证尝试次数过多，不推荐使用帐户。 除了基本的安全卫生监视，Azure 安全中心中的威胁防护模块还可以从单个 Azure 计算资源收集更详细的安全警报 (虚拟机、容器、应用服务) 、数据资源 (SQL DB 和存储) 以及 Azure 服务层。 此功能可让你查看单个资源内的帐户异常情况。

- [Azure Active Directory 中的“审核活动”报表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [启用 Azure 标识保护](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure 安全中心的威胁防护](../security-center/azure-defender.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指南**： Windows 虚拟桌面不会生成或处理域名服务 (DNS) 查询日志。 但注册到服务的资源可以生成流日志。

启用和收集网络安全组资源和流日志、Azure 防火墙日志和 Web 应用程序防火墙 (WAF) 日志进行安全分析，以支持事件调查、威胁搜寻和安全警报生成。 可将流日志发送到 Azure Monitor Log Analytics 工作区，然后使用流量分析提供见解。

- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure 防火墙日志和指标](../firewall/logs-and-metrics.md) 

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md) 

- [Azure Monitor 中的 Azure 网络监视解决方案](../azure-monitor/insights/azure-networking-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指南**：活动日志（自动启用）包含 Windows 虚拟桌面资源的所有写入操作 (PUT、POST、DELETE) ，但读取操作 (获取) 。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/essentials/platform-logs-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：集中记录存储和分析来实现关联。 对于每个日志源，请确保分配了数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

此外，启用数据并将数据集成到 Azure Sentinel 或第三方安全信息事件管理 (SIEM) 。 许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md) 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

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

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：为计算资源建立安全配置

**指南**：使用 Azure 安全中心和 azure 策略在所有计算资源（包括 vm、容器和其他）上建立安全配置。

你可以使用自定义的操作系统映像或 Azure 自动化状态配置来建立你的组织所需的操作系统的安全配置。

- [如何监视 Azure 安全中心建议](../security-center/security-center-recommendations.md) 

- [Azure 自动化 State Configuration 概述](../automation/automation-dsc-overview.md) 

- [Windows 虚拟桌面环境](environment-setup.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4：为计算资源维护安全配置

**指南**：使用 Azure 安全中心和 azure 策略定期评估和修正 Azure 计算资源（包括虚拟机、容器和其他资源）的配置风险。 此外，你可以使用 Azure 资源管理器模板、自定义操作系统映像或 Azure 自动化状态配置来维护组织所需的操作系统的安全配置。 结合 Azure Automation State Configuration，Microsoft 虚拟机模板可能有助于满足和维护安全要求。

Microsoft 发布的 Azure Marketplace 虚拟机映像由 Microsoft 进行管理和维护。

Azure 安全中心还可以扫描容器映像中的漏洞，并针对中心 Internet 安全 Docker 基准对容器中的 Docker 配置执行连续监视。 可以使用 Azure 安全中心的建议页面来查看建议和修正问题。

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/deploy-vulnerability-assessment-vm.md) 

- [如何从 ARM 模板创建 Azure 虚拟机](../virtual-machines/windows/ps-template.md) 

- [Azure 自动化 State Configuration 概述](../automation/automation-dsc-overview.md) 

- [安全中心的容器安全性](../security-center/container-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5：安全存储自定义操作系统和容器映像

**指南**： Windows 虚拟桌面允许客户管理操作系统映像。 请使用 Azure 基于角色的访问控制 (Azure RBAC) 来确保只有授权用户才能访问自定义映像。 使用 Azure 共享映像库你可以将映像共享到组织内的不同用户、服务主体或 Active Directory 组。 将容器映像存储在 Azure 容器注册表中，并使用 RBAC 来确保只有经过授权的用户才能进行访问。

- [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [如何配置 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [共享映像库概述](../virtual-machines/shared-image-galleries.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6：执行软件漏洞评估

**指南**：通过 Windows 虚拟桌面，可以部署自己的虚拟机，并将其注册到服务，并在环境中运行 SQL 数据库。

Windows 虚拟桌面可以使用第三方解决方案来执行网络设备和 web 应用程序的漏洞评估。 执行远程扫描时，不要使用单个永久管理帐户。 请考虑为扫描帐户实现 JIT 预配方法。 扫描帐户的凭据应受到保护、监视，并且仅用于漏洞扫描。

按要求，按一致的间隔导出扫描结果，并将结果与先前的扫描进行比较，以验证是否已修正了漏洞。

按照 Azure 安全中心提供的建议在 Azure 虚拟机上执行漏洞评估 (和 SQL server) 。 Azure 安全中心为虚拟机、容器映像和 SQL 数据库提供内置漏洞扫描程序。

根据需要，按一致的间隔导出扫描结果，并将结果与先前的扫描进行比较，以验证是否已修正了漏洞。 使用 Azure 安全中心建议的漏洞管理建议时，可以转到选定解决方案的门户查看历史扫描数据。

- [如何实现 Azure 安全中心漏洞评估建议](../security-center/deploy-vulnerability-assessment-vm.md) 

- [用于虚拟机的集成漏洞扫描程序](../security-center/deploy-vulnerability-assessment-vm.md) 
- [SQL 漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自动修正软件漏洞

**指南**： Windows 虚拟桌面不使用或需要任何第三方软件。 但是，通过 Windows 虚拟桌面，可以部署自己的虚拟机并将其注册到服务。

使用 Azure 自动化更新管理或第三方解决方案，以确保 Windows Server 虚拟机上安装了最新的安全更新。 对于 Windows 虚拟机，请确保已启用 Windows 更新，并将其设置为自动更新。

为 Configuration Manager 使用第三方软件或 System Center Updates Publisher 的第三方修补程序管理解决方案。

- [如何在 Azure 中为虚拟机配置更新管理](../automation/update-management/overview.md) 

- [管理 Azure VM 的更新和修补程序](../automation/update-management/manage-updates-for-vm.md)

- [为 Windows 虚拟桌面配置 Microsoft Endpoint Configuration Manager](configure-automatic-updates.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指南**： Windows 虚拟桌面不允许客户在其 Windows 虚拟桌面资源上执行自己的渗透测试。

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="endpoint-security"></a>终结点安全性

*有关详细信息，请参阅 [Azure 安全基线：终结点安全性](../security/benchmarks/security-controls-v2-endpoint-security.md)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用终结点检测和响应 (EDR)

**指南**： Windows 虚拟桌面不提供任何特定功能用于终结点检测和响应 (EDR) 进程。 但注册到服务的资源可以从终结点检测和响应功能中获益。 

为服务器和客户端启用终结点检测和响应功能，并将其与安全信息和事件管理集成 (SIEM) 解决方案和安全操作过程。

来自 Microsoft Defender 的高级威胁防护提供终结点检测和响应功能，作为企业终结点安全平台的一部分，以防止、检测、调查和响应高级威胁。

- [Microsoft Defender 高级威胁防护概述](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [适用于 Windows Server 的 Microsoft Defender ATP 服务](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [适用于非 Windows Server 的 Microsoft Defender ATP 服务](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [用于非持久性虚拟桌面基础结构的 Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2：使用集中管理的新式反恶意软件

**指南**：使用集中管理的新式终结点反恶意软件解决方案来保护 Windows 虚拟桌面资源，该解决方案支持实时和定期扫描。

Azure 安全中心可以自动识别针对虚拟机的多种常用反恶意软件解决方案的使用情况，并报告终结点保护运行状态和提出建议。

适用于 Azure 云服务的 Microsoft Antimalware 是适用于 Windows 虚拟机 (VM) 的默认反恶意软件。 此外，还可以使用 Azure 安全中心的威胁检测来检测已上传到 Azure 存储帐户的恶意软件。

- [如何为云服务和虚拟机配置 Microsoft Antimalware](../security/fundamentals/antimalware.md) 

- [支持的终结点保护解决方案](../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3：确保反恶意软件和签名已更新

**指南**：确保反恶意软件签名快速且一致地进行更新。

遵循 Azure 安全中心中的建议： "计算 &amp; 应用"，以确保所有虚拟机和/或容器都是最新的签名。

默认情况下，Microsoft Antimalware 将自动安装最新的签名和引擎更新。

- [如何为 Azure 云服务和虚拟机部署 Microsoft Antimalware](../security/fundamentals/antimalware.md) 

- [Azure 安全中心中的 Endpoint Protection 评估和建议](../security-center/security-center-endpoint-protection.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="backup-and-recovery"></a>备份和恢复

有关详细信息，请参阅 [Azure 安全基准：备份和恢复](../security/benchmarks/security-controls-v2-backup-recovery.md)。

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1：确保定期执行自动备份

**指南**：确保在发生意外事件后，备份系统和数据，以保持业务连续性。 这应该是针对恢复点目标 (RPO) 和恢复时间目标 (RTO) 的任何目标的指导。

启用 Azure 备份，配置备份源（例如 Azure VM、SQL Server、HANA 数据库或文件共享）以及所需的频率和保持期。

为了实现更高级别的冗余，可以启用异地冗余存储选项，将备份数据复制到次要区域，并使用跨区域还原进行恢复。

- [企业规模业务连续性和灾难恢复](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [如何启用 Azure 备份](../backup/index.yml) 

- [如何启用跨区域还原](../backup/backup-azure-arm-restore-vms.md#cross-region-restore) 

- [如何在 Windows 虚拟桌面中设置业务连续性和灾难恢复计划](disaster-recovery.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="br-2-encrypt-backup-data"></a>BR-2：加密备份数据

**指南**：确保备份受到防范攻击的威胁。 这应包括对备份进行加密，以防止丧失机密性。

对于常规 Azure 服务备份，将使用 Azure 平台管理的密钥自动对备份数据进行加密。 你可以选择使用客户管理的密钥对备份进行加密。 在这种情况下，请确保 Key Vault 中客户管理的密钥也在备份范围内。

在 Azure 备份、Azure Key Vault 或其他资源中使用基于角色的访问控制，以保护备份和客户管理的密钥。 此外，可以在更改或删除备份之前，启用高级安全功能以要求多重身份验证。

Azure 备份/azure/backup/security-overview 中的安全功能概述 

- [使用客户托管密钥加密备份数据](../backup/encryption-at-rest-with-cmk.md) 

- [如何在 Azure 中备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

- [可帮助保护混合备份免受攻击的安全功能](../backup/backup-azure-security-feature.md#prevent-attacks)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：验证所有备份，包括客户管理的密钥

**指南**：建议通过执行数据还原过程来确保备份正常工作，从而定期验证备份介质上的数据完整性。

- [如何从 Azure 虚拟机备份恢复文件](../backup/backup-azure-restore-files-from-vm.md)

- [安全实现](../backup/backup-azure-restore-files-from-vm.md#security-implementations)

**Azure 安全中心监视**：目前不可用

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

-   使用 Azure 原生的和第三方的数据保护功能

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

- [Azure 安全基准 - 标识管理](../automation/update-management/overview.md)

- [Azure 安全基准 - 特权访问](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指导**：建立日志记录和威胁响应策略，以快速检测和修正威胁，同时满足合规性要求。 优先为分析人员提供高质量警报和无缝体验，以便他们能够专注于威胁而不是集成和手动步骤。 

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
