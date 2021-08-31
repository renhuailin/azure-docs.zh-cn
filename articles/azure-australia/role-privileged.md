---
title: Azure 基于角色的访问控制 (Azure RBAC) 和 Privileged Identity Management
titleSuffix: Azure Australia
description: 有关在澳大利亚区域根据澳大利亚政府的具体政策、法规和法律要求实施 Azure 基于角色的访问控制 (Azure RBAC) 和 Privileged Identity Management 的指导。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 819d8398395b9cccd4066b216cccd39a8a6fbfcc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "117028990"
---
# <a name="azure-role-based-access-control-azure-rbac-and-privileged-identity-management-pim"></a>Azure 基于角色的访问控制 (Azure RBAC) 和 Privileged Identity Management (PIM)

对管理权限的管理是确保任何 IT 环境安全的关键步骤。 通过使用最小特权安全性限制管理权限是 [ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm) 的要求，并且是 [ACSC Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) 安全建议列表的一部分。

Microsoft 提供一套控件来实现 Microsoft Azure 内的实时、充分访问。 了解这些控件对于云中有效安全态势必不可少。 本指南将概述这些控件本身以及实现这些控件的关键设计注意事项。

## <a name="azure-rbac"></a>Azure RBAC

Azure 基于角色的访问控制 (Azure RBAC) 是管理 Microsoft Azure 内所有资源的访问以及管理 Azure Active Directory (Azure AD) 的核心。 Azure RBAC 可与 Azure 中提供的许多补充功能一起实现。 本文重点介绍如何使用 Azure 管理组、Azure Active Directory 组和 Azure Privileged Identity Management (PIM) 实现有效的 RBAC。

概括而言，实现 Azure RBAC 需要三个组件：

![关系图显示了实现 RBAC 的三个必要组件，即安全主体、角色定义和作用域，所有三个部分都划入角色分配中。](media/rbac-overview.png)

* 安全主体：安全主体可以是这些项中的任意一个：用户、组、[服务主体](../active-directory/develop/app-objects-and-service-principals.md)或[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 安全主体应通过 Azure Active Directory 组进行特权分配。

* 角色定义：角色定义也称为角色，它是权限的集合。 这些权限用于定义分配给角色定义的安全主体可以执行的操作。 此功能由 Azure 资源角色和 Azure Active Directory 管理员角色提供。 Azure 提供一组内置角色（链接），可使用自定义角色进行扩充。

* 作用域：作用域是角色定义所适用的一组 Azure 资源。 Azure 角色可通过 Azure 管理组分配到 Azure 资源。

这三个组件组合在一起，向安全主体授予在角色定义中定义的对 Azure 管理组作用域内所有资源的访问权限，这称为角色分配。 可以将多个角色定义分配给一个安全主体，并将多个安全主体分配给单个作用域。

### <a name="azure-active-directory-groups"></a>Azure Active Directory 组

将特权分配给个人或团队时，应尽可能将分配链接到 Azure Active Directory 组，而不是直接分配给相关用户。 这与本地 Active Directory 实现继承的建议做法相同。 应尽可能按团队创建 Azure Active Directory 组，它是对已创建的 Azure 管理组的逻辑结构的补充。

在混合云方案中，本地 Windows Server Active Directory 安全组可以同步到 Azure Active Directory 实例。 如果已使用这些 Windows Server Active Directory 安全组在本地实现 Azure RBAC，则可在同步这些组后使用它们为 Azure 资源实现 Azure RBAC。 否则，可以将云环境视为一块白板，围绕 Azure Active Directory 实现来设计和实现可靠的特权管理计划。

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azure 资源角色与 Azure Active Directory 管理员角色

Microsoft Azure 为 [Azure 资源](../role-based-access-control/built-in-roles.md)和 [Azure Active Directory 管理](../active-directory/roles/permissions-reference.md)提供各种内置角色。 这两种类型的角色针对 Azure 资源或为 Azure AD 管理员提供特定粒度的访问权限。 请务必注意，不能使用 Azure 资源角色提供对 Azure AD 的管理访问权限，Azure AD 角色不提供对 Azure 资源的特定访问权限。

以下是一些可以使用内置角色分配给 Azure 资源的访问类型示例：

* 让一个用户管理订阅中的虚拟机，另一个用户管理虚拟网络
* 让 DBA 组管理订阅中的 SQL 数据库
* 让某个用户管理资源组中的所有资源，例如虚拟机、网站和子网
* 允许某个应用程序访问资源组中的所有资源

以下是可为 Azure AD 管理分配的访问类型示例：

* 允许支持人员重置用户密码
* 允许员工邀请外部用户使用 Azure AD 实例进行 B2B 协作
* 允许管理人员读取访问以登录和审核报告
* 允许人员管理所有用户和组，包括重置密码。

必须花时间了解内置角色提供的允许操作的完整列表，确保未授予不当的访问权限。 内置角色及其提供的访问权限的列表不断在完善，可以通过查看以上链接的文档或使用 Azure PowerShell cmdlet 来查看角色及其定义的完整列表：

```PowerShell
Get-AzRoleDefinition
```

```output
Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

或 Azure CLI 命令：

```azurecli-interactive
az role definition list
```

```output
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

还可以根据需要创建自定义 Azure 资源角色。 可以在 Azure 门户中以及通过 PowerShell 或 Azure CLI 创建这些自定义角色。 创建自定义角色时，务必确保角色的用途是唯一的，且其功能尚未由现有 Azure 资源角色提供。 这可减少持续管理的复杂性并降低安全主体收到不必要特权的风险。 例如，创建一个自定义 Azure 资源角色，使其介于内置 Azure 资源角色、“虚拟机参与者”和“虚拟机管理员登录”之间。

自定义角色可能基于现有参与者角色，该角色授予以下访问权限：

| Azure 资源 | 访问级别 |
| --- | --- |
| 虚拟机 | 可以管理但无法访问 |
| 附加到 VM 的虚拟网络 | 无法访问 |
| 附加到 VM 的存储 | 无法访问 |
|

自定义角色可保留此基本访问权限，但允许指定用户具有一些基本的附加特权来修改虚拟机的网络配置。

Azure 资源角色还有一个好处，即可通过 Azure 管理组分配给资源。

### <a name="azure-management-groups"></a>Azure 管理组

组织可以使用 Azure 管理组来管理 Azure 租户中所有订阅及其资源的角色分配。 Azure 管理组旨在支持创建管理层次结构，包括在 Azure 中按层次结构映射组织结构的功能。 将组织的业务部门创建为单独的逻辑实体后，便可以根据每个团队的特定要求在组织内应用权限。 Azure 管理组可用于定义最多包含六个级别的管理层次结构。

![管理组](media/management-groups.png)

将 Azure 管理组映射到 Azure 租户中的 Azure 订阅。 这使组织能够将属于特定业务部门的 Azure 资源隔离开来，并提供对成本管理和特权分配的精细控制。

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft 已通过 Azure Privileged Identity Management 实现了实时 (JIT) 和充分访问 (JEA)。 此服务使管理人员能够控制、管理和监视对 Azure 资源的特权访问权限。 通过 PIM，管理人员可以使安全主体“符合”角色条件，从而允许用户通过 Azure 门户或通过 PowerShell cmdlet 来请求激活角色。 默认情况下，可以在 1 到 72 小时之间激活角色分配。 必要时，用户可以请求延长角色分配时间，也可以选择将角色分配设为永久。 或者，在用户请求激活符合条件的角色时，可以强制实施多重身份验证要求。 分配的角色激活时间段到期后，安全主体将不再具有角色授予的特权访问权限。

使用 PIM 可防止环境中发生的常见特权分配问题，即未使用实时访问，或未执行特权分配的例行审核。 一个常见的问题是，在要求使用提升的特权的任务完成后，提升的特权分配被遗忘并长时间保留。 另一个问题是，在配置其他类似安全主体时，克隆分配到安全主体的访问权限导致环境中提升的特权激增。

## <a name="key-design-considerations"></a>关键设计考虑因素

在设计 Azure RBAC 策略旨在强制实施最小特权安全性时，应考虑以下安全要求：

* 验证特权访问权限请求
* 管理特权仅限于执行特定职责所需的最低访问权限
* 管理特权仅限于执行特定职责所需的最短时间段
* 对授予的管理特权进行定期评审

设计 Azure RBAC 策略的过程中，需要详细评审业务功能，以了解不同业务角色之间访问权限的差异，以及需要使用提升的特权的工作类型和频率。 备份操作员、安全管理员和审核者之间的功能差异要求在不同时间具有不同级别的访问权限，并执行不同级别的持续评审。

## <a name="validate-requests-for-access"></a>验证访问请求

必须显式审批提升的特权。 为了支持此操作，必须制定审批流程，并由相应人员负责验证其他特权的所有请求是否合法。 Privileged Identity Management 提供多个选项用于审批角色分配。 可以将角色激活请求配置为允许自审批，或将其封闭并要求指定审批者手动评审和审批所有角色激活请求。 还可以将激活请求配置为要求在激活请求中包含附加支持信息，例如票证编号。

### <a name="restrict-privilege-based-on-duties"></a>基于职责限制特权

限制授予安全主体的特权级别至关重要，因为过多特权分配是常见 IT 安全攻击途径。 必须对所管理资源的类型以及负责的团队进行评估，以便可以分配日常职责所需的最小特权级别。 除日常职责所需特权之外的其他特权应仅在执行特定任务所需的时间段内授予。 例如，向客户的管理员提供“参与者”访问权限，但允许他们在执行需要临时高级访问权限的特定任务时请求对 Azure 资源的“所有者”权限。

这可确保各个管理员仅在最短时间内拥有提升的访问权限。 遵循这些做法可整体减少任何组织 IT 基础结构的受攻击面。

### <a name="regular-evaluation-of-administrative-privilege"></a>定期评估管理特权

定期审核环境中的安全主体，以确保当前已分配正确的特权级别，这一点非常重要。 Microsoft Azure 提供许多方法来审核和评估分配给 Azure 安全主体的特权。 Privileged Identity Management 允许管理人员定期对授予安全主体的角色执行“访问评审”。 可以使用访问评审来审核 Azure 资源角色分配以及 Azure Active Directory 管理角色分配。 以下属性可用于配置访问评审：

* **评审名称以及评审开始和结束日期**：应将评审配置为足够长的时间，以供指定用户完成评审。

* **要评审的角色**：每个访问评审都聚焦于单个 Azure 角色。

* **指定评审者**：有三个选项用于执行评审。 可以将审阅分配给其他人完成、自行执行，也可以让每个用户审阅自己的访问。

* **要求用户提供访问理由**：在完成访问评审时，可以要求用户输入保持其特权级别的理由。

可以通过 Azure 门户中的仪表板随时监视待审批的访问评审的进度。 在访问评审完成之前，正在评审的角色访问权限将保持不变。 还可以在指定时间段内[审核](../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md)所有 PIM 用户分配和激活。

## <a name="next-steps"></a>后续步骤

查看 [Azure 澳大利亚中的系统监视](system-monitor.md)一文。
