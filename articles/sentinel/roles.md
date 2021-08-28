---
title: Azure Sentinel 中的权限 | Microsoft Docs
description: 本文介绍了 Azure Sentinel 如何使用 Azure 基于角色的访问控制将权限分配给用户，并确定每个角色允许的操作。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/11/2021
ms.author: yelevin
ms.openlocfilehash: 97c35aa059f4419ee951875e769b252016578963
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723501"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel 中的权限

Azure Sentinel 使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../role-based-access-control/built-in-roles.md)。

使用 Azure RBAC 在你的安全运营团队中创建和分配角色，以授予对 Azure Sentinel 的适当访问权限。 使用不同的角色，你可以对 Azure Sentinel 用户可以看到和执行的操作进行精细控制。 可以直接在 Azure Sentinel 工作区中分配 Azure 角色（参阅下文），也可以在工作区所属的订阅或资源组中进行分配，Azure Sentinel 会继承该订阅或资源组。

## <a name="roles-for-working-in-azure-sentinel"></a>在 Azure Sentinel 中使用的角色

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel 特定的角色

所有 Azure Sentinel 内置角色都授予对 Azure Sentinel 工作区中数据的读取访问权限。

- [Azure Sentinel 读取者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)可查看数据、事件、工作簿和其他 Azure Sentinel 资源。

- 除上述外，[Azure Sentinel 响应方](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)还可管理事件（分配、关闭等）

- 除此之外，[Azure Sentinel 参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)还可创建和编辑工作簿、分析规则及其他 Azure Sentinel 资源。

- [Azure Sentinel 自动化参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)允许 Azure Sentinel 向自动化规则中添加 playbook。 它不适用于用户帐户。

> [!NOTE]
>
> - 为获得最佳结果，应在包含 Azure Sentinel 工作区的资源组上分配这些角色。 这样，角色将应用于为支持 Azure Sentinel 而部署的所有资源，因为这些资源也应该放在同一个资源组中。
>
> - 另一种方法是直接在 Azure Sentinel 工作区上分配角色。 如果执行此操作，则还必须在该工作区中的 SecurityInsights 解决方案资源上分配相同的角色。 你可能还需要在其他资源上分配角色，并且需要持续地管理资源上的角色分配。

### <a name="additional-roles-and-permissions"></a>其他角色和权限

具有特定作业要求的用户可能需要分配有额外的角色或特定的权限才能完成其任务。

- 使用 playbook 自动应对威胁

    Azure Sentinel 使用 playbook 实现自动化威胁应对。 playbook 基于 Azure 逻辑应用构建，是一种独立的 Azure 资源。 你可能希望为安全运营团队的特定成员分配使用逻辑应用执行安全编排、自动化与响应 (SOAR) 操作的权限。 你可以使用[逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)角色来分配使用 playbook 的显式权限。

- 将数据源连接到 Azure Sentinel

    要使用户能够添加数据连接器，必须在 Azure Sentinel 工作区中为用户分配写入权限。 另外，请注意每个连接器所需的附加权限，如相关的连接器页中所列。

- 来宾用户分配事件

    如果来宾用户需要能够分配事件，则除了 Azure Sentinel 响应方角色外，还需要为该用户分配[目录读取者](../active-directory/roles/permissions-reference.md#directory-readers)角色。 请注意，此角色不是 Azure 角色，而是 Azure Active Directory 角色。默认情况下，常规（非来宾）用户分配有此角色。

- 创建和删除工作簿

    对于要创建和删除 Azure Sentinel 工作簿的用户，还需要为该用户分配 Azure Monitor 角色[监视参与者](../role-based-access-control/built-in-roles.md#monitoring-contributor)。 使用工作簿时不需要此角色，只有创建和删除时才需要此角色。

### <a name="other-roles-you-might-see-assigned"></a>你可能会看到的其他已分配角色

在分配特定于 Azure Sentinel 的 Azure 角色时，你可能会遇到可能已为其他用途分配给用户的其他 Azure 和 Log Analytics Azure 角色。 应当注意，以下角色将授予更广泛的权限集，其中包括对 Azure Sentinel 工作区和其他资源的访问权限：

- Azure 角色：[所有者](../role-based-access-control/built-in-roles.md#owner)、[参与者](../role-based-access-control/built-in-roles.md#contributor)和[读取者](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色授予对所有 Azure 资源（包括 Log Analytics 工作区和 Azure Sentinel 资源）的访问权限。

- Log Analytics 角色：[Log Analytics 参与者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)和 [Log Analytics 读取者](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics 角色授予对 Log Analytics 工作区的访问权限。

例如，当为用户分配了“Azure Sentinel 读取者”角色但未分配“Azure Sentinel 参与者”角色时，如果为用户分配了 Azure 级“参与者”角色，则该用户仍能够编辑 Azure Sentinel 中的项。 因此，如果你只想在 Azure Sentinel 中向用户授予权限，则应该小心地删除此用户以前的权限，确保不要破坏对其他资源的任何必需访问权限。

## <a name="azure-sentinel-roles-and-allowed-actions"></a>Azure Sentinel 角色和允许的操作

下表总结了 Azure Sentinel 中的角色以及允许这些角色在 Azure Sentinel 中执行的操作。

| 角色 | 创建和运行 Playbook| 创建和编辑分析规则和其他 Azure Sentinel 资源 [*](#workbooks) | 管理事件（关闭事件、分配事件，等等） | 查看数据、事件、工作簿和其他 Azure Sentinel 资源 |
|---|---|---|---|---|
| Azure Sentinel 读取者 | -- | -- | -- | &#10003; |
| Azure Sentinel 响应方 | -- | -- | &#10003; | &#10003; |
| Azure Sentinel 参与者 | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel 参与者 + 逻辑应用参与者 | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* 创建和删除工作簿需要额外的[监视参与者](../role-based-access-control/built-in-roles.md#monitoring-contributor)角色。 有关详细信息，请参阅[其他角色和权限](#additional-roles-and-permissions)。
## <a name="custom-roles-and-advanced-azure-rbac"></a>自定义角色和高级 Azure RBAC

- 自定义角色。 除了使用 Azure 内置角色之外，你还可以为 Azure Sentinel 创建 Azure 自定义角色，或者用创建的 Azure 自定义角色来替代 Azure 内置角色。 Azure Sentinel 的 Azure 自定义角色的创建方式与其他 [Azure 自定义角色](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)的创建方式相同，它们基于[特定于 Azure Sentinel 的权限](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)和[特定于 Azure Log Analytics 资源的权限](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)。

- Log Analytics RBAC。 可以对 Azure Sentinel 工作区中的数据使用 Log Analytics 高级 Azure 基于角色的访问控制。 这包括基于数据类型的 Azure RBAC 和资源上下文 Azure RBAC。 有关详细信息，请参阅：

    - [管理 Azure Monitor 中的日志数据和工作区](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Azure Sentinel 的资源上下文 RBAC](resource-context-rbac.md)
    - [表级 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    资源上下文和表级 RBAC 是两种方法，可提供对 Azure Sentinel 工作区中特定数据的访问权限，无需允许访问整个 Azure Sentinel 体验。

## <a name="role-recommendations"></a>角色建议

了解 Azure Sentinel 中角色和权限的工作原理后，可以根据以下最佳做法指南向用户应用角色：

|用户类型  |职位 |资源组  |说明  |
|---------|---------|---------|---------|
|安全分析师     | [Azure Sentinel 响应方](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)        | Azure Sentinel 的资源组        | 查看数据、事件、工作簿和其他 Azure Sentinel 资源。 <br><br>管理事件（例如分配或关闭事件）。        |
|     | [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Azure Sentinel 的资源组或存储 playbook 的资源组        | 将 playbook 附加到分析和自动化规则并运行 playbook。 <br><br>注意：此角色还允许用户修改 playbook。         |
|安全工程师     | [Azure Sentinel 参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)       |Azure Sentinel 的资源组         |   查看数据、事件、工作簿和其他 Azure Sentinel 资源。 <br><br>管理事件（例如分配或关闭事件）。 <br><br>创建和编辑工作簿、分析规则和其他 Azure Sentinel 资源。      |
|     | [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Azure Sentinel 的资源组或存储 playbook 的资源组        | 将 playbook 附加到分析和自动化规则并运行 playbook。 <br><br>注意：此角色还允许用户修改 playbook。         |
|  **Service Principal**   | [Azure Sentinel 参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)      |  Azure Sentinel 的资源组       | 管理任务的自动配置 |
|     |         |        | |

> [!TIP]
> 可能需要其他角色，具体取决于所引入或监视的数据。 例如，可能需要 Azure AD 角色（如“全局管理员”或“安全管理员”角色）才能为其他 Microsoft 门户中的服务设置数据连接器。
>

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何使用 Azure Sentinel 用户的角色，以及每个角色允许用户做什么。

在 [Azure Sentinel 博客](https://aka.ms/azuresentinelblog)上查找有关 Azure 安全性和合规性的博客文章。
