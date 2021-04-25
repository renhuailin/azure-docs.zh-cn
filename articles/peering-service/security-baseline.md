---
title: 适用于 Microsoft Azure 对等互连服务的 Azure 安全基线
description: Microsoft Azure 对等互连服务安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315192"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>适用于 Microsoft Azure 对等互连服务的 Azure 安全基线

此安全基线将 [Azure 安全基准 2.0 版](../security/benchmarks/overview.md)中的指南应用到 Microsoft Azure 对等互连服务。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 本文内容分为 Azure 安全基准定义的“安全控制”和适用于 Microsoft Azure 对等互连服务的相关指南。

> [!NOTE]
> 其中已排除不适用于 Microsoft Azure 对等互连服务或由 Microsoft 负责的“控制措施”。 若要了解 Microsoft Azure 对等互连服务如何完全映射到 Azure 安全基准，请参阅 **[完整的 Microsoft Azure 对等互连服务安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)** 。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵检测/入侵防护系统 (IDS/IPS)

**指南**：对等互连服务不提供本机入侵检测或防护服务。 客户应遵循安全最佳做法，例如，根据业务要求，使用 Azure 防火墙中基于威胁情报的筛选功能，警告或阻止已知恶意 IP 地址和域的出入流量。 这些 IP 地址和域来自 Microsoft 威胁情报源。 

需要执行有效负载检查时，可以从 Azure 市场部署第三方入侵检测或防护系统 (IDS/IPS)。  
另外，还可以使用基于主机的终结点检测和响应 (EDR) 解决方案和（或是取代）基于网络的入侵检测或防护系统。  

如果有使用入侵检测或入侵防护系统的法规要求，请确保始终对其进行优化以使用或提供高质量警报。 

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure 市场包含第三方 IDS 功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR 功能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**责任**：客户

Azure 安全中心监视：无

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为中央标识和身份验证系统

**指南**：维护对 Microsoft Azure 对等互连服务资源的控制平面（例如，Azure 门户）具有管理访问权限的用户帐户清单。

可以在 Azure 门户中为订阅使用“标识和访问控制 (IAM)”窗格来配置 Azure 基于角色的访问控制 (Azure RBAC)。 角色将应用到 Azure Active Directory (Azure AD) 中的用户、组、服务主体和托管标识。

- [使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自动地管理应用程序标识

**指南**：对等互连服务不支持托管标识。 对于不支持托管标识的服务，请使用 Azure Active Directory (Azure AD) 在资源级别创建权限受限的服务主体。 

- [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)

- [支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)

- [使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 进行应用程序访问

**指南**：Microsoft Azure 对等互连服务使用 Azure Active Directory (Azure AD) 来提供 Azure 资源的标识和访问管理。 此内容包括企业标识（例如员工）以及外部标识（如合作伙伴和供应商）。 

可通过单一登录管理本地和云中的组织数据和资源，并对其进行安全访问。 将所有用户、应用程序和设备连接到 Azure AD，实现无缝的安全访问和更好的可见性和控制。

- [了解 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指南**：利用 Azure Active Directory (Azure AD) 启用多重身份验证，并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

**指南**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 生成日志和警报。 此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指南**：对于基于用户定义条件的更精细的访问控制，例如，从特定 IP 范围登录的用户需要使用多重身份验证进行登录，则可以使用 Azure Active Directory (Azure AD) 的条件访问来实现。 还可以通过 Azure AD 条件访问策略对不同的用例使用精细的身份验证会话管理。 

- [Azure 条件访问概述](../active-directory/conditional-access/overview.md)

- [常见条件访问策略](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用条件访问配置身份验证会话管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据透露

**指南**：在 Azure DevOps 中实施凭据扫描器来识别代码中的凭据。 凭据扫描程序还会建议你将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

对于 GitHub，你可以使用原生的机密扫描功能来识别代码中的凭据或其他形式的机密。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 机密扫描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**责任**：客户

Azure 安全中心监视：无

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

**指南**：限制具有较高权限的用户帐户的数量，并提升这些帐户的保护级别。 

Azure Active Directory (Azure AD) 中最关键的内置角色是全局管理员和特权角色管理员，因为分配到这两个角色的用户可以委托管理员角色。 

有了这些特权，用户可直接或间接读取和修改 Azure 环境中的每项资源：

- 全局管理员/公司管理员：具有此角色的用户可访问 Azure AD 中的所有管理功能，还可访问使用 Azure AD 标识的服务。

- 特权角色管理员：具有此角色的用户可管理 Azure AD 和 Azure AD Privileged Identity Management (PIM) 中的角色分配。 此外，该角色可管理 PIM 和管理单元的各个方面。

如果你使用的是已分配某些特权的自定义角色，则可能需要对其他关键角色进行管理。 请将类似的控制措施应用到关键业务资产的管理员帐户。  

请使用 Azure AD Privileged Identity Management (PIM) 提供对 Azure 资源和 Azure AD 的实时 (JIT) 特权访问权限。 JIT 仅在用户需要执行特权任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还会生成安全警报。

- [Azure AD 中的管理角色权限](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [使用 Azure Privileged Identity Management 安全警报](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**：对于已授予所属订阅和管理组的特权访问权限的帐户，通过限制来隔离对关键系统的访问。 

限制对业务关键型资产（例如，已在业务关键系统上安装代理的 Active Directory 域控制器 (DC)、安全工具和系统管理工具）具有管理访问权限的管理、标识和安全系统的访问。 入侵这些管理和安全系统的攻击者可以立即将它们用作损害业务关键型资产的武器。 

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。 

确保分配单独的特权帐户，这些帐户应不同于电子邮件、浏览和高效办公任务所用的标准用户帐户。

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [访问管理组](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指导**：Azure Active Directory (AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

- [查找 Azure AD 活动报告](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指南**：设置用于访问的紧急帐户，当常规管理帐户不可用时，可防止遭到意外锁定而无法访问 Azure Active Directory (Azure AD) 组织。 紧急访问帐户可能包含较高级别的权限，不得分配给特定个人。 将紧急访问帐户限制为只能在紧急或“救急”场景下使用。

确保妥善保管紧急访问帐户的凭据（例如，密码、证书或智能卡），只能将其告诉有权在紧急情况下使用此帐户的个人。

- [在 Azure AD 中管理紧急访问帐户](/azure/active-directory/users-groups-roles/directory-emergency-access)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指南**：使用 Azure Active Directory (Azure AD) 的权利管理功能可自动访问请求工作流，包括访问分配、评审和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure Active Directory (Azure AD) 的访问评审](../active-directory/governance/access-reviews-overview.md)

- [什么是 Azure Active Directory (Azure AD) 的权利管理](../active-directory/governance/entitlement-management-overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指南**：使用已从 Azure Active Directory (Azure AD) 启用多重身份验证的特权访问工作站 (PAW) 来登录和配置 Azure Sentinel 的相关资源。

- [特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [规划基于云的 Azure AD 多重身份验证部署](../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**：利用 Azure 基于角色的访问控制 (Azure RBAC)，你可通过角色分配来管理 Azure 资源的访问权限。 你可将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可通过 Azure CLI、Azure PowerShell 和 Azure 门户等工具来清点或查询这些角色。

通过 Azure RBAC 分配给资源的权限应始终限制为角色所需的权限。 受限权限是对 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期检查这些权限。

请使用内置角色来分配权限，仅在必要时创建自定义角色。

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

**指南**：确保在 Azure 租户和订阅中向安全团队授予了安全读取者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任划分方式的不同，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

若要了解工作负载和服务，可能需要更多权限。 

- [安全读取者角色概述](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Azure 管理组概述](../governance/management-groups/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

**指南**：确保安全团队有权访问 Azure 上持续更新的资产清单。 安全团队通常需要此清单，以评估其组织遭遇新兴风险的可能性，并根据它不断提高安全性。 

Azure 安全中心清单功能和 Azure Resource Graph 可以查询和发现订阅中的所有资源，包括 Azure 服务、应用程序和网络资源。  

请使用 Azure 中的标记以及其他元数据（名称、说明和类别）以合乎逻辑的方式组织资产。  

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

- [Azure 安全中心资产库存管理](../security-center/asset-inventory.md)

- [有关标记资产的详细信息，请参阅资源命名和标记决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**责任**：客户

Azure 安全中心监视：无

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指南**：Microsoft Azure 对等互连服务支持使用“Microsoft.Peering/peerings”命名空间中的 Azure 策略来执行基于 Azure 资源管理器的部署和配置实施。 请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

**指南**：确保正在监视不同类型的 Azure 资产，以发现潜在的威胁和异常情况。 专注于获取高质量警报以减少误报，便于分析人员进行分类整理。 警报可能源自日志数据、代理或其他数据。

使用 Azure 安全中心内置的威胁检测功能，该功能基于监视 Azure 服务遥测和分析服务日志。 数据是使用 Log Analytics 代理收集的，该代理从系统中读取各种与安全相关的配置和事件日志，然后将数据复制到工作区进行分析。 

此外，还可以使用 Azure Sentinel 构建分析规则，从而在环境中搜寻符合特定条件的威胁。 这些规则会在符合条件时生成事件，以便调查每个事件。 Azure Sentinel 还可以导入第三方威胁情报，从而增强威胁检测功能。 

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

- [Azure 安全中心安全警报参考指南](../security-center/alerts-reference.md)

- [创建自定义分析规则以检测威胁](../sentinel/tutorial-detect-threats-custom.md)

- [Azure Sentinel 网络威胁情报](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**：Azure Active Directory (Azure AD) 会提供可通过 Azure AD 报告查看或与 Azure Monitor 集成的用户日志。 这些日志还可与 Azure Sentinel 或其他安全信息和事件管理 (SIEM) 解决方案或监视工具集成，以实现更复杂的监视和分析用例：

- 登录 – 登录报告提供有关托管应用程序使用情况和用户登录活动的信息。

- 审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如，添加或删除用户、应用、组、角色和策略）所做的更改。

- 风险登录 - 风险登录指示可能有非合法用户帐户所有者执行了登录尝试。

- 标记为存在风险的用户 - 风险用户指示可能已泄露的用户帐户。

Azure 安全中心还可以针对某些可疑活动（例如，对订阅中已弃用的帐户进行登录尝试且失败的次数太多）发出警报。 除了基本的安全监视机制，安全中心的威胁防护模块还可以从各 Azure 计算资源（例如，虚拟机、容器、应用服务）、数据资源（例如，SQL 数据库和存储）以及 Azure 服务层收集信息更丰富的安全警报。 通过此功能可查看单个资源中的帐户异常情况。

- [Azure AD 中的审核活动报告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [启用 Azure 标识保护](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指南**：配置连接遥测以通过 Microsoft Azure 对等互连服务提供连接位置与

Microsoft 网络之间的连接性的见解。

- [配置连接遥测](measure-connection-telemetry.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指导**：启用 Azure 活动日志，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户，以便存档 

活动日志提供有关在控制平面级别对 Azure ExpressRoute 资源执行的操作的见解。 使用 Azure 活动日志数据，你可以确定在控制平面级别对 Azure 资源执行任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

- [Azure 活动日志](/azure/azure-monitor/platform/activity-log)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指导**：集中记录存储和分析来实现关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

另外，请启用 Azure Sentinel 或第三方 SIEM 并将数据载入其中。

许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规定，为与 Azure 资源关联的 Log Analytics 工作区设置日志保留期。

- [如何设置日志保留参数](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**责任**：客户

Azure 安全中心监视：无

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指导**：确保组织具有响应安全事件的流程，已为 Azure 更新这些流程，并定期运用这些流程来确保就绪性。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指导**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 基于高质量警报创建事件 

**指导**：确保具有创建高质量警报和衡量警报质量的流程。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。 

可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具构建高质量警报。 

Azure 安全中心可跨许多 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

使用导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 手动导出或持续导出警报和建议。

- [如何配置导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：检测和分析 - 调查事件

**指南**：确保分析人员在调查潜在事件时可以查询和使用不同的数据源，以便全面了解发生的情况。 应收集各种各样的日志，以跟踪整个终止链中潜在攻击者的活动，避免出现盲点。  还应确保收集见解和经验，以供其他分析人员使用和用作将来的历史参考资料。  

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

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 确定事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的确信程度，以及对导致警报的活动背后存在恶意意图的确信程度。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**责任**：客户

Azure 安全中心监视：无

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：包含、根除和恢复 - 自动执行事件处理

**指导**：自动执行手动重复性任务来加快响应时间并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

**责任**：客户

Azure 安全中心监视：无

## <a name="posture-and-vulnerability-management"></a>安全状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：安全状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：为所有 Azure 服务建立安全配置 

**指导**：为基础结构和 DevOps 团队定义安全护栏，使其可以轻松且安全地配置其使用的 Azure 服务。 

在 Azure 安全基准检验中，通过服务基线开始 Azure 服务的安全配置，并根据组织的需要对其进行自定义。 

使用 Azure 安全中心配置 Azure Policy，以审核和强制实施 Azure 资源的配置。 

你可以使用 Azure 蓝图，在单个蓝图定义中自动部署和配置服务和应用程序环境，包括 Azure 资源管理器模板、Azure RBAC 控制措施和策略。

- [企业级缩放登陆区域中护栏实现的插图](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [在 Azure 安全中心内使用安全策略](../security-center/tutorial-security-policy.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 蓝图](../governance/blueprints/overview.md)

**责任**：客户

Azure 安全中心监视：无

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：为所有 Azure 服务维护安全配置

**指南**：不适用；此建议适用于计算资源。

**责任**：客户

Azure 安全中心监视：无

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指导**：根据需要，对 Azure 资源进行渗透测试或红队活动，并确保修正所有关键安全发现。
请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**责任**：共享

Azure 安全中心监视：无

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。

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
- [Azure 安全体系结构建议 - 存储、数据和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全基础知识 - Azure 数据安全、加密和存储](../security/fundamentals/encryption-overview.md)

- [云采用框架 - Azure 数据安全和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全基准 - 资产管理](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure 安全基准 - 数据保护](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指导**：建立企业范围的策略，以便使用标识、网络、应用程序、订阅、管理组和其他控件的组合来细分对资产的访问。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指导**：持续衡量并缓解你的个人资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指导**：确保为安全组织中的角色和责任记录并传达明确的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全历程培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全最佳做法 2 - 人员：针对云安全技术培训团队](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全最佳做法 3 - 流程：针对云安全决策分配责任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**责任**：客户

Azure 安全中心监视：无

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

- [Azure 安全基准 - 网络安全](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 网络安全概述](../security/fundamentals/network-overview.md)

- [企业网络体系结构策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**责任**：客户

Azure 安全中心监视：无

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定义标识和特权访问策略

**指导**：建立 Azure 标识和特权访问方法，作为组织整体安全访问控制策略的一部分。  

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

**责任**：客户

Azure 安全中心监视：无

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

- [Azure 安全基准 - 日志记录和威胁检测](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**责任**：客户

Azure 安全中心监视：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
