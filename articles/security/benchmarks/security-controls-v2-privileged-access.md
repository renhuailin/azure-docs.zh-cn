---
title: Azure 安全基准 V2 - 特权访问
description: Azure 安全基准 V2 特权访问
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a4f1c6e32bbc679a7ec946384903b2bda3887d05
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720573"
---
# <a name="security-control-v2-privileged-access"></a>安全控制 V2：特权访问

特权访问包含用于保护对 Azure 租户和资源的特权访问的控制措施。 这包括一系列用于避免管理模型、管理帐户和特权访问工作站面临有意和无意的风险的控制措施。

若要查看适用的内置 Azure 策略，请参阅 [Azure 安全基准管理法规符合性内置计划的详细信息：特权访问](../../governance/policy/samples/azure-security-benchmark#privileged-access)

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制具有较高权限的用户

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-1 | 4.3、4.8 | AC-2 |

限制具有较高权限的用户帐户的数量，并提升这些帐户的保护级别。 Azure AD 中最重要的内置角色是全局管理员和特权角色管理员，因为分配到这两种角色的用户可以委派管理员角色。 有了这些特权，用户可直接或间接读取和修改 Azure 环境中的每项资源：

- 全局管理员：具有此角色的用户有权访问 Azure AD 中的所有管理功能，以及使用 Azure AD 标识的服务。

- 特权角色管理员：具有此角色的用户可管理 Azure AD 和 Azure AD Privileged Identity Management (PIM) 中的角色分配。 此外，该角色可管理 PIM 和管理单元的各个方面。

注意：如果你使用的是分配了某些特权的自定义角色，则可能有其他关键角色需要进行管理。 此外，你可能还需要将类似的控制措施应用于关键业务资产的管理员帐户。

你可使用Azure AD Privileged Identity Management (PIM) 提供对 Azure 资源和 Azure AD 的实时 (JIT) 特权访问权限。 JIT 仅在用户需要执行特权任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还会生成安全警报。

- [Azure AD 中的管理角色权限](../../active-directory/roles/permissions-reference.md)

- [使用 Azure Privileged Identity Management 安全警报](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](../../active-directory/roles/security-planning.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全运营](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务型系统的管理访问权限

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-2 | 13.2、2.10 | AC-2、SC-3、SC-7 |

通过限制向哪些帐户授予对其所属的订阅和管理组的特权访问权限，隔离对业务关键型系统的访问。 确保还限制了对你的业务关键型资产具有管理访问权限的管理、标识和安全系统的访问，这些资产包括在业务关键型系统上安装了代理的 Active Directory 域控制器 (DC)、安全工具和系统管理工具。 入侵这些管理和安全系统的攻击者可以立即将它们用作损害业务关键型资产的武器。 

所有类型的访问控制都应符合企业分段策略，确保访问控制保持一致。 

确保分配单独的特权帐户，这些帐户应不同于电子邮件、浏览和高效办公任务所用的标准用户帐户。

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [访问管理组](../../governance/management-groups/overview.md#management-group-access)

- [Azure 订阅管理员](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期审查和协调用户访问权限

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-3 | 4.1、16.9、16.10 | AC-2 |

定期审查用户帐户和访问权限分配，确保帐户及其访问权限级别均有效。 可使用 Azure AD 访问评审来审查组成员身份、对企业应用程序的访问权限和角色分配。 Azure AD 报告提供日志来帮助发现过时的帐户。 你还可使用 Azure AD Privileged Identity Management 来创建便于审查的访问评审报表工作流。
此外，Azure Privileged Identity Management 还可配置为在创建过多的管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。 

注意：某些 Azure 服务支持不通过 Azure AD 管理的本地用户和角色。 你必须单独管理这些用户。

- [在 Privileged Identity Management (PIM) 中创建对 Azure 资源角色的访问评审](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [如何使用 Azure AD 标识和访问评审](../../active-directory/governance/access-reviews-overview.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-4 | 16 | AC-2、CP-2 |

为了防止意外退出 Azure AD 组织，请设置一个紧急访问帐户，以便在正常管理帐户无法使用时进行访问。 紧急访问帐户通常拥有较高的权限，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。
应确保妥善保管紧急访问帐户的凭据（例如密码、证书或智能卡），仅将其告诉只能在紧急情况下有权使用它们的个人。

- [在 Azure AD 中管理紧急访问帐户](../../active-directory/roles/security-emergency-access.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全运营 (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5：将权利管理自动化

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-5 | 16 | AC-2、AC-5、PM-10 |

使用 Azure AD 的权利管理功能可自动执行访问请求工作流，包括访问权限分配、审查和过期。 还支持两阶段或多阶段审批。
- [什么是 Azure AD 访问评审](../../active-directory/governance/access-reviews-overview.md) 

- [什么是 Azure AD 权利管理](../../active-directory/governance/entitlement-management-overview.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-6 | 4.6、11.6、12.12 | AC-2、SC-3、SC-7 |

受保护的隔离工作站对于敏感角色（如管理员、开发人员和关键服务操作员）的安全性至关重要。 使用高度安全的用户工作站和/或 Azure Bastion 执行管理任务。 使用 Azure Active Directory、Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 部署安全的托管用户工作站，用于执行管理任务。 可集中管理安全工作站，强制实施安全配置，包括强身份验证、软件和硬件基线，以及受限制的逻辑和网络访问。 

- [了解特权访问工作站](/security/compass/privileged-access-deployment)

- [部署特权访问工作站](../../active-directory/devices/howto-azure-managed-workstation)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全运营 (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循 Just Enough Administration（最小特权原则）

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-7 | 14.6 | AC-2、AC-3、SC-3 |

借助 Azure 基于角色的访问控制 (Azure RBAC)，可通过角色分配管理 Azure 资源访问权限。 你可将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可通过 Azure CLI、Azure PowerShell 和 Azure 门户等工具来清点或查询这些角色。 通过 Azure RBAC 分配给资源的权限应始终限制为角色所需的权限。 受限权限是对 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法的补充，应定期检查这些权限。

使用内置角色分配权限，并且仅在需要时创建自定义角色。

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)

- [如何配置 Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [如何使用 Azure AD 标识和访问评审](../../active-directory/governance/access-reviews-overview.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：选择 Microsoft 支持的批准流程 

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-8 | 16 | AC-2、AC 3、AC 4 |

在 Microsoft 需要访问客户数据的支持方案中，客户密码箱提供一项功能，使你可以显式查看和批准或拒绝每个客户数据访问请求。

- [了解客户密码箱](../fundamentals/customer-lockbox-overview.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)