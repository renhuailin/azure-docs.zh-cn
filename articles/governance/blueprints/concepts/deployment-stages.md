---
title: 蓝图部署的阶段
description: 了解 Azure 蓝图服务在创建蓝图分配时所经历的与安全和项目相关的步骤。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 7aeaa6fec08293ac52fea488b500b2c9e291f9e5
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323652"
---
# <a name="stages-of-a-blueprint-deployment"></a>蓝图部署的阶段

在蓝图被部署时，Azure 蓝图服务会执行一系列操作来部署蓝图中定义的资源。 本文详细介绍了每个步骤所涉及的内容。

通过将蓝图分配给订阅或[更新现有分配](../how-to/update-existing-assignments.md)，可以触发蓝图部署。 在部署期间，Azure 蓝图执行以下简要步骤：

> [!div class="checklist"]
> - Azure 蓝图被授予所有者权限
> - 创建蓝图分配对象
> - 可选 - Azure 蓝图创建系统分配的托管标识
> - 托管标识部署蓝图项目
> - 撤销 Azure 蓝图服务和系统分配的托管标识权限

## <a name="azure-blueprints-granted-owner-rights"></a>Azure 蓝图被授予所有者权限

当使用[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)时，Azure 蓝图服务主体被授予对分配的订阅或订阅的所有者权限。 借助被授予的角色，Azure 蓝图可以创建并稍后撤销系统分配的托管标识。 如果使用用户分配的托管标识，那么 Azure 蓝图服务主体不会获得、也不需要对订阅的所有者权限。

如果通过门户完成分配，则会自动授予权限。 但是，如果通过 REST API 完成分配，则需要通过单独的 API 调用来授予权限。 Azure 蓝图 AppId 为 `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`，但服务主体因租户而异。 使用 [Azure Active Directory 图形 API](/graph/migrate-azure-ad-graph-planning-checklist) 和 REST 终结点 [servicePrincipals](/graph/api/resources/serviceprincipal) 可以获取服务主体。 然后，通过[门户](../../../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../../../role-based-access-control/role-assignments-cli.md)、[Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)、[REST API](../../../role-based-access-control/role-assignments-rest.md) 或 [Azure 资源管理器模板](../../../role-based-access-control/role-assignments-template.md)向 Azure 蓝图授予所有者角色。

Azure 蓝图服务并不直接部署资源。

## <a name="the-blueprint-assignment-object-is-created"></a>创建蓝图分配对象

用户、组或服务主体将蓝图分配给订阅。 分配对象存在于分配蓝图的订阅级别。 由部署创建的资源不是在部署实体的上下文中完成的。

在创建蓝图分配时，选择的是[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)类型。 默认为系统分配的托管标识。 可以选择用户分配的托管标识。 当使用用户分配的托管标识时，必须在创建蓝图分配之前定义并授予权限。 [所有者](../../../role-based-access-control/built-in-roles.md#owner)和[蓝图操作员](../../../role-based-access-control/built-in-roles.md#blueprint-operator)内置角色都有必要的 `blueprintAssignment/write` 权限，可以创建使用用户分配的托管标识的分配。

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>可选 - Azure 蓝图创建系统分配的托管标识

如果在分配过程中选择了[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)，则 Azure 蓝图会创建标识，并向托管标识授予[所有者](../../../role-based-access-control/built-in-roles.md#owner)角色。 如果[现有分配已升级](../how-to/update-existing-assignments.md)，则 Azure 蓝图使用之前创建的托管标识。

与蓝图分配相关的托管标识用于部署或重新部署蓝图中定义的资源。 这种设计避免了分配之间无意间的相互干扰。
这种设计还通过从蓝图控制每个已部署资源的安全性来支持[资源锁定](./resource-locking.md)功能。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>托管标识部署蓝图项目

然后，托管标识触发资源管理器按定义的[序列顺序](./sequencing-order.md)部署蓝图内的项目。 可以调整顺序，以确保依赖于其他项目的项目按正确的顺序部署。

部署的访问失败通常是由于向托管标识授予的访问级别所致。 Azure 蓝图服务管理系统分配的托管标识的安全生命周期。 但是，用户负责管理用户分配的托管标识的权限和生命周期。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>撤销蓝图服务和系统分配的托管标识权限

在部署完成后，Azure 蓝图从订阅中撤销系统分配的托管标识的权限。 然后，Azure 蓝图服务从订阅中撤销其权限。 撤销权限可以防止 Azure 蓝图成为订阅的永久所有者。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[静态和动态参数](./parameters.md)。
- 了解如何自定义[蓝图排序顺序](./sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](./resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。