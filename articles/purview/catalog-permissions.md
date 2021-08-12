---
title: 目录权限（预览版）
description: 本文概述了如何在 Azure Purview 中配置基于角色的访问控制 (RBAC)
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 9c27287f661c133641f7b3da45784478c8fc597e
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108015998"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Azure Purview 的数据平面中基于角色的访问控制

本文介绍如何在 Azure Purview 的[数据平面](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)中实现基于角色的访问控制 (RBAC)。

> [!IMPORTANT]
> 创建 Purview 帐户的主体会自动获得所有数据平面权限，无论他们可能属于或不属于哪些数据平面角色。 如果任何其他用户要在 Azure Purview 中执行任何操作，他们必须至少具有一个预定义的数据平面角色。

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Azure Purview 的预定义数据平面角色

Azure Purview 定义了一组预定义的数据平面角色，可用于控制 Azure Purview 中的访问权限。 这些角色包括：

* **Purview 数据读取者角色** - 有权访问 Purview 门户，并可读取 Azure Purview 中除扫描绑定以外的所有内容
* **Purview 数据策展人角色** - 有权访问 Purview 门户，并可读取 Azure Purview 中除扫描绑定以外的所有内容，可编辑有关资产的信息，可编辑分类定义和术语表术语，并且可以将分类和术语表术语应用于资产。
* **Purview 数据源管理员角色** - 无权访问 Purview 门户（用户还需要具有数据读取者或数据策展人角色），可以管理将数据扫描到 Azure Purview 中的所有方面，但不具有对 Azure Purview 中的内容的读写权限（除了与扫描相关的内容）。

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>了解如何使用 Azure Purview 的数据平面角色

创建 Azure Purview 帐户后，创建者具有 Azure Purview 数据策展人和数据源管理员角色。 但在角色存储中，未将这些角色分配给帐户创建者。 Azure Purview 认为帐户创建者是主体，并基于其标识向其赋予了这些功能。

其他所有用户只有在至少具有其中一个角色时才能使用 Azure Purview 帐户。 这意味着，创建 Azure Purview 帐户后，在将其他所有用户分配到一个或多个先前定义的角色之前，仅创建者可访问帐户或使用其 API。

请注意，Purview 数据源管理员角色有两种受支持的方案。 第一种方案适用于已经是 Purview 数据读取者或 Purview 数据策展人的用户，这些用户也需要能够创建扫描。 这些用户需要具有两个角色：至少是 Purview 数据读取者或 Purview 数据策展人之一，以及 Purview 数据源管理员角色。

Purview 数据源管理员的另一种方案适用于编程进程（如服务主体），该进程需要能够设置和监视扫描但不应具有访问目录任何数据的权限。

通过使服务主体具有 Purview 数据源管理员角色而不具有任何其他两个角色，可以实现此方案。 该主体将无法访问 Purview 门户，但这无妨。 因为它是编程主体，并且仅通过 API 进行通信。

## <a name="putting-users-into-roles"></a>为用户分配角色

因此，在创建 Azure Purview 帐户之后，首先要做的就是为用户分配这些角色。

可通过 [Azure 的 RBAC](../role-based-access-control/overview.md) 管理角色分配。

Azure 中只有两个内置控制平面角色可以分配用户角色 - 所有者或用户访问管理员。 因此，若要为用户分配 Azure Purview 的这些角色，必须找到作为所有者或用户访问管理员的人员或自己成为他们。

### <a name="an-example-of-assigning-someone-to-a-role"></a>为用户分配角色的示例

1. 转到 https://portal.azure.com 并导航到 Azure Purview 帐户
1. 在左侧单击“访问控制(IAM)”
1. 然后，按照[此处](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)提供的一般说明进行操作

## <a name="role-definitions-and-actions"></a>角色定义和操作

角色定义为操作的集合。 有关如何定义角色的详细信息，请参阅[此处](../role-based-access-control/role-definitions.md)。 若要了解 Azure Purview 角色的角色定义，请参阅[此处](../role-based-access-control/built-in-roles.md)。

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>添加到 Azure Purview 帐户中的数据平面角色

如果你希望获得对 Azure Purview 帐户的访问权限，以便可以使用其工作室或调用其 API，则你需要添加到 Azure Purview 数据平面角色中。 只有 Azure Purview 帐户的所有者或用户访问管理员才能执行此操作。 对于大多数用户来说，下一步是找到一名本地管理员，该管理员可以帮助你找到可以为你提供访问权限的合适人员。

对于有权访问其公司 [Azure 门户](https://portal.azure.com)的用户，可以查看他们要加入的特定 Azure Purview 帐户，单击其“访问控制(IAM)”选项卡，然后查看作为所有者或用户访问管理员 (UAA) 的用户。 但请注意，在某些情况下，Azure Active Directory 组或服务主体可能用作所有者或 UAA，在这种情况下，可能无法直接联系这些组或服务主体。 而是需要寻求管理员的帮助。

## <a name="who-should-be-assigned-to-what-role"></a>应将用户分配到哪个角色？

|使用方案|合适的角色|
|-------------|-----------------|
|我只需要查找资产，不需要编辑任何内容|Purview 数据读取者角色|
|我需要编辑有关资产的信息，对其进行分类，将它们与术语表条目等相关联。|Purview 数据策展人角色|
|我需要编辑术语表或设置新的分类定义|Purview 数据策展人角色|
|我的应用程序服务主体需要将数据推送到 Azure Purview|Purview 数据策展人角色|
|我需要通过 Purview Studio 设置扫描|Purview 数据源管理员角色以及至少 Purview 数据读取者角色或 Purview 数据策展者角色之一|
|我需要启用服务主体或其他编程标识，以在不允许编程标识访问目录信息的情况下在 Azure Purview 中设置和监视扫描 |Purview 数据源管理员角色|
|我需要将用户分配到 Azure Purview 角色中 | 所有者或用户管访问理员 |

若要详细了解如何将安全主体添加到角色，请参阅[快速入门：创建安全 Azure Purview 帐户](create-catalog-portal.md)。

## <a name="next-steps"></a>后续步骤

* [数据见解](concept-insights.md)
