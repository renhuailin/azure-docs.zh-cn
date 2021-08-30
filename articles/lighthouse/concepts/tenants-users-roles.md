---
title: Azure Lighthouse 方案中的租户、用户和角色
description: 了解如何在 Azure Lighthouse 场景中使用 Azure Active Directory 租户、用户和角色。
ms.date: 06/23/2021
ms.topic: conceptual
ms.openlocfilehash: cdf8c10d52e0add4513d42a99d2054e1af0ed796
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112542249"
---
# <a name="tenants-users-and-roles-in-azure-lighthouse-scenarios"></a>Azure Lighthouse 方案中的租户、用户和角色

在将客户加入 [Azure Lighthouse](../overview.md) 之前，请务必了解 Azure Active Directory (Azure AD) 租户、用户和角色的工作原理以及如何在 Azure Lighthouse 方案中使用它们。

租户是 Azure AD 的专用受信任的实例。 通常，每个租户表示一个组织。 Azure Lighthouse 支持将资源从一个租户[逻辑投影](architecture.md#logical-projection)到另一个租户。 这样一来，管理租户中的用户（例如属于服务提供商的用户）可以访问客户租户中的委派资源，或者让[具有多个租户的企业集中其管理操作](enterprise.md)。

为了实现此逻辑投影，必须将客户租户中的订阅（或订阅中的一个或多个资源组）加入 Azure Lighthouse。 可以[通过 Azure 资源管理器模板](../how-to/onboard-customer.md)或[将公共或私有产品/服务发布到 Azure 市场](../how-to/publish-managed-services-offers.md)来完成此加入过程。

无论选择哪种加入方法，都需要定义授权。 每个授权指定可以访问委派资源的 principalId，还指定内置角色，用于设置其中每个用户对这些资源的权限。 principalId 定义管理租户中的 Azure AD 用户、组或服务主体。

> [!NOTE]
> 除非显式指定，否则 Azure Lighthouse 文档中对“用户”的提及可能会适用于授权中的 Azure AD 用户、组或服务主体。

## <a name="best-practices-for-defining-users-and-roles"></a>定义用户和角色的最佳做法

创建授权时，建议使用以下最佳做法：

- 在大多数情况下，需要将权限分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。
- 请务必遵循最低权限原则，使用户仅具有完成作业所需的权限，从而帮助减少意外错误发生的几率。 有关详细信息，请参阅[建议的安全做法](../concepts/recommended-security-practices.md)。
- 包括具有[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的用户，以便以后可以随时[删除对委派的访问权限](../how-to/remove-delegation.md)。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。
- 确保需要[查看 Azure门户中的“我的客户”页](../how-to/view-manage-customers.md)的所有用户都具有[读者](../../role-based-access-control/built-in-roles.md#reader)角色（或包含读者访问权限的其他内置角色）。

> [!IMPORTANT]
> 如果要为 Azure AD 组添加权限，“组类型”必须设置为“安全性”。 此选项是在创建组时选择的。 有关详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="role-support-for-azure-lighthouse"></a>Azure Lighthouse 的角色支持

定义授权时，必须为每个用户帐户分配其中某个 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。 不支持自定义角色和[经典订阅管理员角色](../../role-based-access-control/classic-administrators.md)。

Azure Lighthouse 目前支持所有[内置角色](../../role-based-access-control/built-in-roles.md)，但有以下例外：

- 不支持[所有者](../../role-based-access-control/built-in-roles.md#owner)角色。
- 不支持具有 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 权限的任何内置角色。
- 支持[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)内置角色，但仅限用于[向客户租户中的托管标识分配角色](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)。 此角色通常会授予的其他权限不适用。 如果定义具有此角色的用户，则还必须指定该用户可以分配给托管标识的内置角色。

> [!NOTE]
> 向 Azure 添加新的适用内置角色后，可以在[使用 Azure 资源管理器模板加入客户](../how-to/onboard-customer.md)时分配该角色。 [发布托管服务产品/服务](../how-to/publish-managed-services-offers.md)时，新添加的角色在合作伙伴中心内变得可用之前可能会出现延迟。

## <a name="transferring-delegated-subscriptions-between-azure-ad-tenants"></a>在 Azure AD 租户之间传输委派订阅

如果订阅[转移到另一个 Azure AD 租户帐户](../../cost-management-billing/manage/billing-subscription-transfer.md#transfer-a-subscription-to-another-azure-ad-tenant-account)，则将保留通过 [Azure Lighthouse 载入流程](../how-to/onboard-customer.md)创建的[注册定义和注册分配资源](architecture.md#delegation-resources-created-in-the-customer-tenant)。 这意味着，通过 Azure Lighthouse 授予管理租户的访问权限对该订阅（或该订阅中的委派资源组）仍然有效。

唯一的例外情况是订阅转移到之前已委派给的 Azure AD 租户。 在这种情况下，将删除该租户的委派资源，且通过 Azure Lighthouse 授予的访问权限将不再适用，因为订阅现在直属于该租户（而非通过 Azure Lighthouse 委派给该租户）。 但是，如果该订阅也委派给了其他管理租户，则其他管理租户将保留对订阅的相同访问权限。

## <a name="next-steps"></a>后续步骤

- 了解[针对 Azure Lighthouse 的建议安全做法](recommended-security-practices.md)。
- 要将客户加入 Azure Lighthouse，可以[使用 Azure 资源管理器模板](../how-to/onboard-customer.md)，或[将专用或公共托管服务发布到 Azure 市场](../how-to/publish-managed-services-offers.md)。
