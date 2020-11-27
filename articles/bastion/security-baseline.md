---
title: Azure 堡垒的 azure 安全基线
description: Azure 堡垒安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 461d3800491dbe382beecbcdfe1f2a93bd6b2e5c
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301566"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Azure 堡垒的 azure 安全基线

此安全基线将 [Azure 安全基准版本 2.0](../security/benchmarks/overview.md) 中的指南应用于 azure 堡垒。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的 **安全控制措施** 进行分组，以及适用于 azure 堡垒的相关指南。 排除了不适用于 Azure 堡垒的 **控件**。

若要查看 Azure 堡垒如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure 堡垒安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：实现内部流量的安全性

**指南**：部署 Azure 堡垒资源时，必须创建或使用现有虚拟网络。 确保所有 Azure 虚拟网络都遵循与业务风险相匹配的企业分段原则。 任何可能会导致组织面临较高风险的系统都应在其自己的虚拟网络中隔离，并使用网络安全组 (NSG) 进行充分的保护。

需要打开以下端口才能使服务正常工作：

- 入口流量：
   - 来自公共 Internet 的入口流量： Azure Bastion 将创建一个公共 IP，需要在该公共 IP 上启用端口 443，用于入口流量。 不需要在 AzureBastionSubnet 上打开端口 3389/22。 

   - 来自 Azure Bastion 控制平面的入口流量： 对于控制平面连接，请启用从 GatewayManager 服务标记进行的端口 443 入站。 这将启用控制平面，即网关管理器能够与 Azure 堡垒通信。

- 出口流量：

   -  (Vm) 的目标虚拟机的出口流量： Azure 堡垒将通过专用 IP 访问目标 Vm。 NSG 需要允许端口 3389 和 22 的出口流量流向其他目标 VM 子网。

   - 流向 Azure 中其他公共终结点的出口流量： Azure Bastion 需要能够连接到 Azure 中的各种公共终结点，以便执行相应操作（例如，存储诊断日志和计量日志）。 因此，Azure Bastion 需要出站到 443，再到 AzureCloud 服务标记。

与网关管理器和 Azure 服务标记的连接受到保护 (由 Azure 证书) 锁定。 外部实体（包括这些资源的使用者）无法在这些终结点上进行通信。 

- [如何创建包含安全规则的网络安全组](../virtual-network/tutorial-filter-network-traffic.md)

- [可在此处了解有关堡垒 NSG 要求的详细信息](bastion-nsg.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ns-2-connect-private-networks-together"></a>NS-2：将专用网络连接在一起

**指南**： Azure 堡垒不公开可通过专用网络访问的任何终结点。 Azure 堡垒支持部署到对等互连网络，以集中你的堡垒部署并启用跨网络连接。

- [Azure 堡垒和虚拟网络对等互连](vnet-peering.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-management"></a>标识管理

有关详细信息，请参阅 [Azure 安全基准：标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：将 Azure Active Directory 标准化为标识和身份验证的中央系统

**指南**： azure 堡垒与 Azure Active Directory () Azure AD azure 的默认标识和访问管理服务集成。 用户可以使用 Azure AD 身份验证访问 Azure 门户来管理 Azure 堡垒服务， (创建、更新和删除堡垒资源) 。

使用 Azure 堡垒连接到虚拟机的方式取决于 SSH 密钥或用户名/密码，当前不支持使用 Azure AD 凭据。

除 SSH 密钥或用户名/密码外，使用 Azure 堡垒连接到虚拟机时，用户将需要以下角色分配：
- 目标虚拟机上的 "读取者" 角色
- 具有目标虚拟机专用 IP 的 NIC 上的读者角色
- Azure Bastion 资源上的读者角色

有关详细信息，请参阅以下资源：

- [使用 Azure 堡垒连接到 Linux 虚拟机](bastion-connect-vm-ssh.md)

- [使用 Azure Bastion 连接到 Windows 虚拟机](bastion-connect-vm-rdp.md)

- [Azure 内置角色](../role-based-access-control/built-in-roles.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：对应用程序访问使用 Azure AD 单一登录 (SSO)

**指南**：对虚拟机资源进行身份验证时，Azure 堡垒不支持用于身份验证的 SSO，只支持 SSH 或用户名/密码。 但是，Azure 堡垒使用 Azure Active Directory (Azure AD) 来提供总体服务的标识和访问管理。 用户可以通过 Azure AD Connect 进行身份验证，以便 Azure AD 访问和管理其 Azure 堡垒资源，并体验通过其自己的同步企业标识实现无缝单一登录。 

- [了解使用 Azure AD 的应用程序 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD 连接](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [使用 Azure 堡垒连接到 Linux 虚拟机](bastion-connect-vm-ssh.md)

- [使用 Azure Bastion 连接到 Windows 虚拟机](bastion-connect-vm-rdp.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对所有基于 Azure Active Directory 的访问使用强身份验证控制

**指南**： Azure 堡垒与 Azure AD 的 Azure Active Directory () 用于访问和管理服务。 为 Azure AD 租户配置 Azure 多重身份验证。 Azure AD 支持通过多重身份验证 (MFA) 和强无密码方法进行强身份验证控制。  
- 多重身份验证：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议来设置你的 MFA。 可以基于登录条件和风险因素，对所有用户、特选用户或单个用户强制执行 MFA。 

- 无密码身份验证：有三个无密码身份验证选项可用：Windows Hello for Business、Microsoft Authenticator 应用和本地身份验证方法（例如智能卡）。 

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory 的无密码身份验证选项简介](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视帐户异常并发出警报

**指南**：对 Azure 堡垒远程会话启用诊断日志，并使用这些日志来查看哪些用户连接到哪些工作负荷、在何处以及其他此类相关日志记录信息。 使用 Azure Monitor 在日志中检测到异常时接收通知，从而为某些已记录的堡垒会话创建警报。

- [你可以在此处找到有关如何启用诊断日志记录的详细信息](diagnostic-logs.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：基于条件限制 Azure 资源访问

**指南**：只能通过 Azure 门户访问 Azure 堡垒服务，可以使用 Azure Active Directory (Azure AD) 条件访问来限制对 Azure 门户的访问。 基于用户定义的条件，使用 Azure AD 条件访问进行更精细的访问控制，例如，要求从特定 IP 范围登录的用户使用 MFA。

客户还可以在加入域的虚拟机级别上使用不同的基于角色的访问控制策略，进一步限制对虚拟机的访问。

- [可在此处阅读有关 Azure AD 条件性访问的详细信息](../active-directory/conditional-access/overview.md)

- [Azure 条件访问概述](../active-directory/conditional-access/overview.md)

- [常见的条件访问策略](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用条件访问配置身份验证会话管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="privileged-access"></a>特权访问

有关详细信息，请参阅 [Azure 安全基准：特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

**指南**： azure 堡垒使用 azure 基于角色的访问控制 (azure RBAC) 通过限制授予访问特定虚拟机的访问权限的帐户，隔离对关键系统的访问。 请确保遵循最低权限原则，使用户只有执行特定任务所需的权限。

确保你还将访问权限限制为对业务关键访问具有管理访问权限的管理、标识和安全系统，如 Active Directory 域控制器 (DCs) 、安全工具和系统管理工具，以及安装在业务关键系统上的代理。 入侵这些管理和安全系统的攻击者可以立即将它们用作损害业务关键型资产的武器。

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。

- [使用 Azure 堡垒访问虚拟机所需的角色](bastion-faq.md#roles)

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [访问管理组](../governance/management-groups/overview.md#management-group-access)

- [Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

**指南**： Azure 堡垒使用 Azure Active Directory (Azure AD) 帐户和 Azure RBAC 来管理其资源。 定期查看用户帐户和访问分配，以确保帐户及其访问有效。 可使用 Azure AD 访问评审来审查组成员身份、对企业应用程序的访问权限和角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可以使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流，以便于审核过程。

此外，还可以将 Azure Privileged Identity Management 配置为在创建过多的管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。

- [在 Privileged Identity Management (PIM 中创建 Azure 资源角色的访问评审) ](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [删除对委派的访问权限](../lighthouse/how-to/remove-delegation.md)

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

**指南**： azure 堡垒与 Azure Active Directory 和 Azure RBAC 集成，以管理其资源。 为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化 

**指南**： azure 堡垒与 Azure Active Directory (Azure AD) 和 Azure RBAC 集成，以管理其资源。 使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。

- [什么是 Azure AD 访问评审](../active-directory/governance/access-reviews-overview.md)

- [什么是 Azure AD 权利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

**指南**：安全的隔离工作站对于敏感角色（如管理员、开发人员和关键服务操作员）的安全性至关重要。 根据你的要求，你可以使用高度安全的用户工作站在生产环境中执行 Azure 堡垒资源的管理管理任务。 使用 Azure Active Directory、Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 部署安全的托管用户工作站，用于执行管理任务。 可集中管理安全工作站，强制实施安全配置，包括强身份验证、软件和硬件基线，以及受限制的逻辑和网络访问。 

- [了解特权访问工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [部署特权访问工作站](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则） 

**指南**： azure 堡垒集成了 azure 基于角色的访问控制 (RBAC) 来管理其资源。 使用 Azure RBAC，可通过角色分配来管理 Azure 资源访问。 可以将这些内置角色分配给用户、组、服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的特权。 这是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期进行审查。 使用内置角色分配权限，并在需要时仅创建自定义角色。

使用 Azure 堡垒连接到虚拟机时，用户将需要以下角色分配：
- 目标虚拟机上的 "读取者" 角色
- 具有目标虚拟机专用 IP 的 NIC 上的读者角色
- Azure Bastion 资源上的读者角色

有关详细信息，请参阅以下资源：

- [使用 Azure 堡垒连接到 Linux 虚拟机](bastion-connect-vm-ssh.md)

- [使用 Azure Bastion 连接到 Windows 虚拟机](bastion-connect-vm-rdp.md)

- [Azure 内置角色](../role-based-access-control/built-in-roles.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="asset-management"></a>资产管理

有关详细信息，请参阅 [Azure 安全基准：资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

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

**指南**：将标记应用于 Azure 堡垒资源、资源组和订阅，以逻辑方式将它们组织到分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。 确保安全团队有权访问 Azure 上持续更新的资产清单。 他们可以使用 Azure 资源关系图来查询和发现订阅中的所有资源，包括 Azure 服务、应用程序和网络资源。

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

- [Azure 安全中心资产库存管理](../security-center/asset-inventory.md)

- [有关标记资产的详细信息，请参阅资源命名和标记决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略审核和限制用户可在你的环境中预配的服务，这包括能够允许或拒绝 Azure 堡垒资源的部署。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../governance/policy/samples/built-in-policies.md#general)

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

**指南**：删除已部署的 Azure 堡垒资源的访问权限，当不再需要这些资源来最大程度地减少攻击面时。 用户可以通过 Azure 门户、CLI 或 REST Api 来管理其 Azure 堡垒资源。 如果不再需要或将正在进行的远程会话确定为潜在的威胁，你还可以删除或强制断开正在进行的远程会话。

- [删除强制断开远程会话的连接](session-monitoring.md#view)

- [Azure 网络 CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

**指南**： Azure 堡垒与 Azure Active Directory (Azure AD) 用于标识和身份验证。 可以通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，来限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-threat-detection"></a>日志记录和威胁检测

有关详细信息，请参阅 [Azure 安全基准：日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

**指南**： Azure 堡垒与 Azure Active Directory (Azure AD) ，并通过 Azure 门户访问该服务。 默认情况下，对服务 (的管理操作（如创建、更新和删除) 通过 Azure 活动日志捕获。 用户还应启用 Azure 堡垒资源日志，例如 session BastionAuditLogs 来跟踪堡垒会话。

Azure AD 提供了以下用户日志，可以在报表中查看 Azure AD 报告或与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具进行集成，以实现更复杂的监视和分析用例： 
-   登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

-   审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

-   风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

-   已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可针对某些可疑活动发出警报，这些活动包括失败的身份验证尝试次数太多，以及帐户已在订阅中遭到弃用。 除了基本的安全卫生监视，Azure 安全中心的威胁防护模块还可从单个 Azure 计算资源（例如虚拟机、容器、应用服务）、数据资源（例如 SQL 数据库和存储）以及 Azure 服务层中收集信息更丰富的安全警报。 通过此功能可查看单个资源中的帐户异常情况。

- [Azure 堡垒资源日志](diagnostic-logs.md)

- [Azure AD 中的审核活动报告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [启用 Azure 标识保护](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 安全中心的威胁防护](/azure/security-center/threat-protection)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

**指南**：启用 Azure 堡垒资源日志，使用这些诊断日志来查看哪些用户连接到哪些工作负载、在何处以及其他此类相关日志记录信息。 用户可以将这些日志配置为发送到存储帐户，以便长期保留和审核。

启用和收集网络安全组 (NSG) 资源日志和网络安全组上的 NSG 流日志，这些组将应用于已部署 Azure 堡垒资源的虚拟网络。 然后，这些日志可用于分析网络安全，并支持事件调查、威胁搜寻和安全警报生成。 可将流日志发送到 Azure Monitor Log Analytics 工作区，然后使用流量分析提供见解。

- [启用和使用 Azure 堡垒日志](diagnostic-logs.md)

- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure 防火墙日志和指标](../firewall/logs-and-metrics.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [使用网络观察程序进行监视](../network-watcher/network-watcher-monitoring-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

**指南**：自动可用的活动日志包含 Azure 堡垒资源的所有写入操作 (PUT、POST、DELETE) ，但读取操作 (获取) 。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

- [启用 Azure 堡垒的 Azure 资源日志 ](diagnostic-logs.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

**指南**：集中记录存储和分析以启用关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

此外，启用数据并将数据集成到 Azure Sentinel 或第三方 SIEM。

许多组织选择使用 Azure Sentinel 作为 "热" 数据，这些数据经常使用，而 Azure 存储则用于不频繁使用的 "冷" 数据。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

**指南**：确保用于存储 Azure 堡垒日志的任何存储帐户或 Log Analytics 工作区都已根据组织的符合性法规设置了日志保持期。

在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。

- [如何配置 Log Analytics 工作区保留期](../azure-monitor/platform/manage-cost-storage.md)

- [在 Azure 存储帐户中存储资源日志](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [启用和使用 Azure 堡垒日志](diagnostic-logs.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：准备 - 更新 Azure 的事件响应流程

**指南**：确保你的组织具有安全事件的响应流程，已为 Azure 更新了这些流程，并会定期演练这些流程以确保准备就绪。

- [在企业环境中实现安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件响应参考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：准备 - 设置事件通知

**指南**：在 Azure 安全中心中设置安全事件联系人信息。 如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用此联系信息来与你取得联系。 还可以选择基于事件响应需求在不同的 Azure 服务中自定义事件警报和通知。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：检测和分析 - 根据高质量警报创建事件

**指南**：确保你有创建高质量警报的过程，并衡量警报的质量。 这样，你就可以从过去的事件中吸取经验，并为分析人员确定警报的优先级，这样他们就不会浪费时间来处理误报。

高质量的警报可以基于过去的事件经验、经验证的社区源以及旨在通过融合和关联各种信号源来生成和清理警报的工具。 

Azure 安全中心可跨多个 Azure 资产提供高质量的警报。 可以使用 ASC 数据连接器将警报流式传输到 Azure Sentinel。 借助 Azure Sentinel，可创建高级警报规则来自动生成事件以进行调查。 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：检测和分析 - 设置事件优先级

**指南**：根据警报严重性和资产敏感度，为分析人员提供上下文来确定应首要关注哪些事件。 

Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在查找或用于发出警报的分析中的置信度，以及导致警报的活动的恶意意图的置信度。

此外，使用标记来标记资源，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：遏制、根除和恢复 - 自动执行事件处理

**指南**：自动执行重复性手动任务，以加快响应速度并减轻分析人员的负担。 执行手动任务需要更长的时间，这会导致减慢每个事件的速度，并减少分析人员可以处理的事件数量。 手动任务还会使分析人员更加疲劳，这会增加可导致延迟的人为错误的风险，并降低分析人员专注于复杂任务的工作效率。 使用 Azure 安全中心和 Azure Sentinel 中的工作流自动化功能，可自动触发操作或运行 playbook，对传入的安全警报作出响应。 playbook 执行多项操作，如发送通知、禁用帐户和隔离有问题的网络。 

- [在安全中心配置工作流自动化](../security-center/workflow-automation.md)

- [在 Azure 安全中心设置自动威胁响应](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="posture-and-vulnerability-management"></a>状况和漏洞管理

有关详细信息，请参阅 [Azure 安全基准：态势和漏洞管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)。

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：为所有 Azure 服务建立安全配置 

**指南**：定义和实现 azure 堡垒与 azure 策略的标准安全配置。 使用 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure 堡垒的网络配置。 客户还可以利用 Azure 蓝图或 ARM 模板安全一致地部署堡垒资源，从而建立安全配置。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 ARM 模板](../azure-resource-manager/templates/overview.md)

- [Azure 蓝图概述](../governance/blueprints/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：为所有 Azure 服务维护安全配置

**指南**：定义和实现 azure 堡垒与 azure 策略的标准安全配置。 使用 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施堡垒资源的网络配置。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：执行定期攻击模拟

**指南**：根据需要，对 Azure 资源进行渗透测试和红队活动，确保修正所有关键的安全发现结果。

请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。

- [Azure 中的渗透测试](../security/fundamentals/pen-testing.md)

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="governance-and-strategy"></a>治理和策略

有关详细信息，请参阅 [Azure 安全基准：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。

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

- [Azure 安全基准 - 资产管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全基准 - 数据保护](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定义企业分段策略 

**指南**：建立企业范围的策略，将标识、网络、应用程序、订阅、管理组和其他控制措施结合使用以对资产的访问进行细分。

仔细权衡安全分离需求与为需要彼此通信并访问数据的系统启用日常操作的需求。

确保跨控制类型（包括网络安全、标识和访问模型、应用程序权限/访问模型，以及人机过程控制）一致地实现分段策略。

- [有关 Azure 中的分段策略的指南（视频）](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [有关 Azure 中的分段策略的指南（文档）](/security/compass/governance#enterprise-segmentation-strategy)

- [使网络分段与企业分段策略相匹配](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定义安全状况管理策略

**指南**：不断衡量并降低各个资产及其托管环境的风险。 确定高价值资产和暴露程度高的受攻击面（例如已发布的应用程序、网络入口和出口点、用户和管理员终结点等）的优先级。

- [Azure 安全基准 - 状况和漏洞管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：协调组织角色、职责和责任

**指南**：确保为安全组织中的角色和责任记录并传达清晰的策略。 优先考虑提供涉及安全决策的明确责任，对每个人进行共同职责模式培训，并为技术团队传授保护云的技术。

- [Azure 安全最佳做法 1 - 人员：针对云安全云之旅培训团队](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

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

- [Azure 安全基准 - 网络安全](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Azure 安全基准 - 标识管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全基准 - 特权访问](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 安全最佳做法 11 - 体系结构。单一的统一安全策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 标识管理安全概述](../security/fundamentals/identity-management-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定义日志记录和威胁响应策略

**指南**：建立日志记录和威胁响应策略，以在满足符合性要求的同时快速检测和修正威胁。 确定提供具有高质量警报和无缝体验的分析师的优先级，以便他们能够专注于威胁而不是集成和手动步骤。 

此策略应包括针对以下元素的记录在案的指南、策略和标准： 

-   安全运营 (SecOps) 组织的角色和职责 

-   符合 NIST 或其他行业框架要求的明确定义的事件响应流程 

-   日志捕获和保留，用于支持威胁检测、事件响应和合规性需求

-   使用 SIEM、原生 Azure 功能和其他源，集中查看和关联有关威胁的信息 

-   与客户、供应商和公开的利益相关方之间的通信和通知计划

-   使用 Azure 原生的和第三方的平台进行事件处理，例如日志记录和威胁检测、取证以及攻击补救和根除

-   处理事件和事件后活动的流程，例如经验教训和证据保留

有关详细信息，请参阅以下资源：

- [Azure 安全基准 - 日志记录和威胁检测](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全基准 - 事件响应](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全最佳做法 4 - 流程。更新云的事件响应流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 采用框架、日志记录和报告决策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企业规模、管理和监视](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
