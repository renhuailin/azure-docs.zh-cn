---
title: '目录权限 (预览) '
description: '本文概述了如何在 Azure 监控范围中配置 Role-Based 访问控制 (RBAC) '
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: e87c9efc910d08307d40d42e58f8272a01902a41
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551748"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Azure 监控范围的数据平面中基于角色的访问控制

本文介绍如何在 Azure 监控范围的 [数据平面](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)中实现 (RBAC) Role-Based 访问控制。

> [!IMPORTANT]
> 系统会自动为创建监控范围帐户的主体提供所有数据平面权限，而不管它们可能处于或不属于哪些数据平面角色。 对于任何其他用户若要在 Azure 监控范围中执行任何操作，这些用户必须至少具有一个预定义的数据平面角色。

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Azure 监控范围的预定义数据平面角色

Azure 监控范围定义一组预定义的数据平面角色，这些角色可用于控制哪些用户可以访问 Azure 监控范围中的内容。 这些角色包括：

* **监控范围数据读取器角色** -有权访问监控范围门户，并可读取 Azure 监控范围中的所有内容，扫描绑定除外
* **监控范围 Data 陈列 Role** -有权访问监控范围门户，并可读取 Azure 监控范围中的所有内容，但扫描绑定除外，可以编辑有关资产的信息，可以编辑分类定义和词汇表术语，还可以将分类和词汇表术语应用到资产。
* **监控范围数据源管理员角色** -无权访问监控范围门户 (用户还需要位于数据读取器或数据陈列角色中) 并且可以管理将数据扫描到 azure 监控范围中的所有方面，但不会对 azure 监控范围中的内容进行读取或写入访问权限，而不是与扫描相关的内容。

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>了解如何使用 Azure 监控范围的数据平面角色

创建 Azure 监控范围帐户后，会将创建者视为监控范围 Data 陈列和监控范围数据源管理员角色中的角色。 但帐户创建者未分配到角色存储中的这些角色。 Azure 监控范围认识到，主体是该帐户的创建者，并根据用户的身份将这些功能扩展到它们。

如果所有其他用户至少位于其中一个角色中，则可以使用 Azure 监控范围帐户。 这意味着，在创建 Azure 监控范围帐户后，不能访问帐户，也不能使用其 Api，直到将其放入一个或多个以前定义的角色中。

请注意，监控范围数据源管理员角色具有两种受支持的方案。 第一种方案适用于已监控范围数据读取器或监控范围数据 Curators 的用户，这些用户还需要能够创建扫描。 这些用户需要在两个角色中，至少有一个监控范围数据读取器或监控范围 Data 陈列，并将其放在监控范围数据源管理员角色中。

监控范围数据源管理器的另一种方案适用于编程过程（如服务主体），该过程需要能够设置和监视扫描，但不应具有对任何目录数据的访问权限。

此方案可通过以下方式实现：将服务主体置于监控范围数据源管理员角色中，而无需将其放在其他两个角色中。 主体不具有访问监控范围门户的权限，但这是 o.k。 因为它是一个编程主体，并且仅通过 Api 通信。

## <a name="putting-users-into-roles"></a>将用户放入角色

因此，创建 Azure 监控范围帐户后的第一个业务顺序是将用户分配到这些角色中。

通过 [Azure 的 RBAC](../role-based-access-control/overview.md)管理角色分配。

只有 Azure 中的两个内置控制平面角色才能分配用户角色，这些角色是所有者或用户访问管理员。 因此，若要将人员加入到 Azure 监控范围的这些角色中，用户必须找到作为所有者或用户访问管理员的人员或成为一个 oneself。

### <a name="an-example-of-assigning-someone-to-a-role"></a>将用户分配到角色的示例

1. 转到 https://portal.azure.com 并导航到 Azure 监控范围帐户
1. 在左侧单击 "访问控制 (IAM) "
1. 然后，按照[此处](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)提供的一般说明进行操作

## <a name="role-definitions-and-actions"></a>角色定义和操作

角色定义为操作的集合。 有关如何定义角色的详细信息，请参阅 [此处](../role-based-access-control/role-definitions.md) 。 查看 [此处](../role-based-access-control/built-in-roles.md) ，了解 Azure 监控范围角色的角色定义。

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>添加到 Azure 监控范围帐户中的数据平面角色

如果希望获得对 Azure 监控范围帐户的访问权限，以便可以使用其 studio 或调用其 Api，则需要将其添加到 Azure 监控范围数据平面角色中。 只有那些是 Azure 监控范围帐户的所有者或用户访问管理员的用户才能执行此操作。 对于大多数用户来说，下一步是查找可帮助您找到正确用户的本地管理员。

对于有权访问其公司 [Azure 门户](https://portal.azure.com) 的用户可以查看他们要加入的特定 Azure 监控范围帐户，单击其 "访问控制 (IAM) " 选项卡，然后查看 "所有者" 或 "用户访问管理员" (UAAs) 的用户。 但请注意，在某些情况下 Azure Active Directory 组或服务主体可能用作所有者或 UAAs，在这种情况下，可能无法直接联系这些组或服务主体。 而是需要寻找管理员来帮助。

## <a name="who-should-be-assigned-to-what-role"></a>谁应分配到哪个角色？

|使用方案|适当的角色 (s) |
|-------------|-----------------|
|我只需要查找资产，不想编辑任何内容|监控范围数据读取器角色|
|我需要编辑有关资产的信息，对其进行分类，将它们与术语表条目等相关联。|监控范围 Data 陈列 Role|
|我需要编辑词汇表或设置新的分类定义|监控范围 Data 陈列 Role|
|我的应用程序的服务主体需要将数据推送到 Azure 监控范围|监控范围 Data 陈列 Role|
|我需要通过监控范围 Studio 设置扫描|监控范围数据源管理员角色以及至少一个监控范围数据读取器角色或监控范围 Data 陈列角色|
|我需要启用服务主体或其他编程标识，以在 Azure 监控范围中设置和监视扫描，而不允许编程标识访问目录的信息 |监控范围数据源管理员角色|
|我需要将用户放入 Azure 监控范围中的角色 | 所有者或用户访问管理员 |

转到下一篇文章，了解如何将安全主体添加到角色。

## <a name="next-steps"></a>后续步骤

* [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)
