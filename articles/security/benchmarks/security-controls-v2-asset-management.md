---
title: Azure 安全基准 V2 - 资产管理
description: Azure 安全基准 V2 资产管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32b0a7e31fc0d595eacc2bf5257f41e4ce35566b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735805"
---
# <a name="security-control-v2-asset-management"></a>安全控制 V2：资产管理

资产管理涵盖了各种控制，用于确保安全团队可以了解 Azure 资源的安全性以及对这些资源进行治理。 其中包括以下几方面的建议：安全人员的权限、对资产清单的安全访问，以及管理对服务和资源的审批（盘点、跟踪和更正）。

若要查看适用的内置 Azure 策略，请参阅 [Azure 安全基准管理法规符合性内置计划的详细信息：网络安全](../../governance/policy/samples/azure-security-benchmark#asset-management)

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：确保安全团队可以了解与资产相关的风险

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-1 | 1.1、1.2 | CM-8、PM-5 |

确保安全团队在 Azure 租户和订阅中被授予安全读取者权限，以便他们可以使用 Azure 安全中心来监视安全风险。

根据安全团队责任的构建方式，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，安全见解和风险必须始终在组织内集中聚合。 

安全读取者权限可以广泛应用于整个租户（根管理组），也可以限制到管理组或特定订阅。 

注意：若要了解工作负载和服务，可能需要更多权限。

- [安全读取者角色概述](../../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../../governance/management-groups/overview.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：确保安全团队有权访问资产清单和元数据

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-2 | 1.1、1.2、1.4、1.5、9.1、12。1 | CM-8、PM-5 |

确保安全团队有权访问 Azure 上持续更新的资产清单。 安全团队通常需要此清单，以评估其组织遭遇新兴风险的可能性，并根据它不断提高安全性。 

Azure 安全中心清单功能和 Azure Resource Graph 可以查询和发现订阅中的所有资源，包括 Azure 服务、应用程序和网络资源。

使用标记以及 Azure 中的其他元数据、Azure (名称、说明和类别) ，以逻辑方式组织资产。

- [如何使用 Azure Resource Graph 浏览器创建查询](../../governance/resource-graph/first-query-portal.md)

- [Azure 安全中心资产库存管理](../../security-center/asset-inventory.md)

- [有关标记资产的详细信息，请参阅资源命名和标记决策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3：仅使用已批准的 Azure 服务

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-3 | 2.3、2.4 | CM-7、CM-8 |

请使用 Azure Policy 来审核和限制用户可以在你的环境中预配哪些服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。 你也可以使用 Azure Monitor 来创建规则，以便在检测到未经批准的服务时触发警报。

- [配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](../../governance/policy/samples/index.md)

- [如何使用 Azure Resource Graph 浏览器创建查询](../../governance/resource-graph/first-query-portal.md)

**责任**：客户

客户安全利益干系人（[了解更多](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：确保资产生命周期管理的安全

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-4 | 2.3、2.4、2.5 | CM-7、CM-8、CM-10、CM-11 |

制定或更新安全策略，以便针对可能产生很大影响的修改来处理资产生命周期管理过程。 这些修改包括对以下内容的更改：标识提供者和访问权限、数据敏感度、网络配置，以及管理特权分配。

如果不再需要 Azure 资源，请将其删除。

- [删除 Azure 资源组和资源](../../azure-resource-manager/management/delete-resource-group.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5：限制用户与 Azure 资源管理器进行交互的能力

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

使用 Azure AD 条件性访问，通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6：仅使用计算资源中经过批准的应用程序

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-6 | 2.6、2.7 | AC-3、CM-7、CM-8、CM-10、CM-11 |

请确保在 Azure 虚拟机上只执行已经过授权的软件，并阻止执行所有未经授权的软件。

请使用 Azure 安全中心 (ASC) 自适应应用程序控制来发现并生成应用程序允许列表。 你也可以使用 ASC 自适应应用程序控制来确保在 Azure 虚拟机上只执行已经过授权的软件，并阻止执行所有未经授权的软件。

请使用 Azure 自动化更改跟踪和清单来自动收集 Windows 和 Linux VM 中的清单信息。 可从 Azure 门户获得软件名称、版本、发布者和刷新时间。 若要获取软件安装日期和其他信息，请启用来宾级诊断，并将 Windows 事件日志定向到 Log Analytics 工作区。

根据脚本类型的不同，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

也可以使用第三方解决方案来发现和标识未经批准的软件。

- [如何使用 Azure 安全中心自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

- [了解 Azure 自动化更改跟踪和库存](../../automation/change-tracking/overview.md)

- [如何在 Windows 环境中控制 PowerShell 脚本的执行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
