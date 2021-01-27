---
title: Azure 安全控制 - 安全配置
description: Azure 安全控制安全配置
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f945699256b4de9bd87ee4ab7500268302e93089
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880163"
---
# <a name="security-control-secure-configuration"></a>安全控制：安全配置

建立、实现并主动管理（跟踪、报告、更正）Azure 资源的安全配置，防止攻击者利用易受攻击的服务和设置。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.1 | 5.1 | 客户 |

使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的配置。 你还可以使用内置的 Azure Policy 定义。

此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.2 | 5.1 | 客户 |

使用 Azure 安全中心建议来维护所有计算资源上的安全配置。  此外，你可以使用自定义操作系统映像或 Azure 自动化 State Configuration来建立组织所需的操作系统的安全配置。

- [如何监视 Azure 安全中心建议](../../security-center/security-center-recommendations.md)

- [安全建议 - 参考指南](../../security-center/recommendations-reference.md)

- [Azure 自动化 State Configuration 概述](../../automation/automation-dsc-overview.md)

- [在 Azure 中上传 VHD 并使用它创建新 Windows VM](../../virtual-machines/windows/upload-generalized-managed.md)

- [使用 Azure CLI 从自定义磁盘创建 Linux VM](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.3 | 5.2 | 客户 |

使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。  此外，你可以使用 Azure 资源管理器模板维护组织所需的 Azure 资源的安全配置。 

- [了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)

- [Azure 资源管理器模板概述](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.4 | 5.2 | 共享 |

遵循 Azure 安全中心关于在 Azure 计算资源上执行漏洞评估的建议。  此外，你可以使用 Azure 资源管理器模板、自定义操作系统映像或 Azure 自动化 State Configuration 来维护组织所需的操作系统的安全配置。   结合 Azure 自动化 Desired State Configuration，Microsoft 虚拟机模板可能有助于满足和维护安全要求。 

另请注意，由 Microsoft 发布的 Azure 市场虚拟机映像由 Microsoft 管理和维护。 

- [如何实现 Azure 安全中心漏洞评估建议](../../security-center/deploy-vulnerability-assessment-vm.md)

- [如何从 Azure 资源管理器模板创建 Azure 虚拟机](../../virtual-machines/windows/ps-template.md)

- [Azure 自动化 State Configuration 概述](../../automation/automation-dsc-overview.md)

- [在 Azure 门户中创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md)

- [有关如何下载 VM 模板的信息](/previous-versions/azure/virtual-machines/windows/download-template)

- [将 VHD 上传到 Azure 并创建新的 VM 的示例脚本](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.5 | 5.3 | 客户 |

使用 Azure DevOps 来安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板和所需的状态配置脚本。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。

- [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.6 | 5.3 | 客户 |

如果使用自定义映像，请使用 Azure 基于角色的访问控制 (Azure RBAC) 来确保只有授权用户才能访问映像。 使用共享映像库，可以将映像共享给组织内的不同用户、服务主体或 AD 组。  对于容器映像，请将其存储在 Azure 容器注册表中，并利用 Azure RBAC 确保只有授权用户才能访问这些映像。  

- [了解 Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [了解容器注册表的 Azure RBAC](../../container-registry/container-registry-roles.md)

- [如何配置 Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [共享映像库概述](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.7 | 5.4 | 客户 |

使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 还可以使用与特定资源相关的内置策略定义。  此外，你也可以使用 Azure 自动化来部署配置更改。

- [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.8 | 5.4 | 客户 |

Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。 

- [加入 Azure Automation State Configuration 管理的计算机](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.9 | 5.5 | 客户 |

使用 Azure 安全中心对 Azure 资源执行基线扫描。  此外，使用 Azure Policy 警告和审核 Azure 资源配置。

- [如何在 Azure 安全中心修正建议](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.10 | 5.5 | 客户 |

使用 Azure 安全中心对 OS 和容器的 Docker 设置执行基线扫描。

- [了解 Azure 安全中心容器建议](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.11 | 13.1 | 客户 |

将托管服务标识与 Azure Key Vault 结合使用，可以简化和保护云应用程序的机密管理。

- [如何与 Azure 托管标识集成](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何创建 Key Vault](../../key-vault/secrets/quick-create-portal.md)

- [如何向 Key Vault 进行身份验证](../../key-vault/general/authentication.md)

- [如何分配 Key Vault 访问策略](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.12 | 4.1 | 客户 |

使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

- [如何配置托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 7.13 | 18.1, 18.7 | 客户 |

实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[恶意软件防护](security-control-malware-defense.md)