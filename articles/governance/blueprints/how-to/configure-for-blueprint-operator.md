---
title: 为蓝图操作员设置环境
description: 了解如何将 Azure 环境配置为与蓝图操作员 Azure 内置角色配合使用。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: 5c2da686a2a145c42e53260e96b95b2ae14c4770
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323873"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>为蓝图操作员配置环境

可以将蓝图定义管理和蓝图分配管理分配到不同的团队。 通常，架构师或治理团队负责蓝图定义的生命周期管理，而运营团队则负责管理那些集中控制的蓝图定义的分配。

蓝图操作员内置角色是专门为在此类方案中使用而设计的。 运营类型的团队可以通过该角色管理组织蓝图定义的分配，但不可修改这些定义。 执行此操作需要在 Azure 环境中进行一些配置，本文介绍了必要的步骤。

## <a name="grant-permission-to-the-blueprint-operator"></a>向蓝图操作员授予权限

第一步是向将要为其分配蓝图的帐户或安全组（建议）授予蓝图操作员角色。 应在管理组层次结构中的最高级别完成此操作，该级别包含运营团队应具有其蓝图分配访问权限的所有管理组和订阅。 建议在授予这些权限时遵循最低权限原则。

1. （建议）[创建安全组并添加成员](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 将蓝图操作员 [Azure 角色分配](../../../role-based-access-control/role-assignments-portal.md)到帐户或安全组

## <a name="user-assign-managed-identity"></a>用户分配的托管标识

蓝图定义可以使用系统分配的或用户分配的托管标识。 但是，在使用蓝图操作员角色时，需要将蓝图定义配置为使用用户分配的托管标识。 此外，对于被授予蓝图操作员角色的帐户或安全组，需要针对用户分配的托管标识向它们授予托管标识操作员角色 。 如果没有此权限，蓝图分配会由于缺少权限而失败。

1. [创建用户分配的托管标识](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)，供分配的蓝图使用。

1. 向用户分配的托管标识授予指定范围的蓝图定义所需的任何角色或权限。

1. 将托管标识操作员 [Azure 角色分配](../../../role-based-access-control/role-assignments-portal.md)到帐户或安全组。 将角色分配的范围限定为用户分配的新托管标识。

1. 作为蓝图操作员，[分配使用用户分配的新托管标识的蓝图](../create-blueprint-portal.md#assign-a-blueprint)。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。