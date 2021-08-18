---
title: Azure 市场中的托管服务产品
description: 通过 Azure 市场中的托管服务产品/服务向客户提供 Azure Lighthouse 管理服务。
ms.date: 05/11/2021
ms.topic: conceptual
ms.openlocfilehash: 10b32445fcf6d014219dd8559c9c1ac9b2905044
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114391669"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure 市场中的托管服务产品

本文介绍了 [Azure 市场](https://azuremarketplace.microsoft.com)中的 **托管服务** 产品类型。 有了托管服务产品，可通过 [Azure Lighthouse](../overview.md) 向客户提供资源管理服务。 可以向所有潜在客户提供这些产品，也可以仅向一个或多个特定客户提供。 由于直接向客户收取与这些托管服务相关的成本，因此 Microsoft 不收取任何费用。

## <a name="understand-managed-service-offers"></a>了解托管服务产品

托管服务产品简化了将客户加入 Azure Lighthouse 的过程。 客户在 Azure 市场中购买产品/服务时，他们将能够指定应加入哪些订阅和/或资源组。

之后，你所在组织中的用户将能够根据你在创建产品时定义的访问权限，通过 [Azure 委托资源管理](architecture.md)从你的管理租户中对这些资源执行操作。 这主要通过清单来完成，该清单指定了有权访问客户资源的 Azure Active Directory (Azure AD) 用户、组和服务主体，以及定义其访问级别的[角色](tenants-users-roles.md)。

> [!NOTE]
> 在 Azure 政府版和其他国家云中，托管服务产品可能不可用。

## <a name="public-and-private-offers"></a>公共和专用产品/服务

每个托管服务产品均包含一个或多个计划。 这些计划可以是专用的，也可以是公用的。

如果要将产品/服务限制为特定客户，可以发布专用计划。 执行此操作时，只能针对你所提供的特定订阅 ID 购买计划。 有关详细信息，请参阅[专用产品/服务](../../marketplace/private-offers.md)。

> [!NOTE]
> 通过云解决方案提供商 (CSP) 计划的经销商建立的订阅不支持专用产品/服务。

可以利用公共计划将服务推广给新客户。 当只需要对客户租户的有限访问权限时，这些信息通常更合适。 建立与客户的关系后，如果他们决定向你的组织授予其他访问权限，你可以通过为该客户仅发布新的专用计划来完成此操作，或者通过[使用 Azure 资源管理器模板进行进一步访问来载入](../how-to/onboard-customer.md)。

如果需要，可以在同一个产品/服务中包括公用和专用计划。

> [!IMPORTANT]
> 一旦某个计划已发布为公用，就无法将其更改为专用。 若要控制哪些客户可以接受产品/服务并委派资源，请使用专用计划。 使用公用计划时，不能将其可用性限制为特定客户，甚至不能限制特定数量的客户（不过，如果选择这样做，可完全停止销售计划）。 仅当在发布产品/服务时，包含将“角色定义”设置为[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的“授权”的情况下，才能在客户接受产品/服务后[删除对委派的访问权限](../how-to/remove-delegation.md)。 你还可以与客户联系，要求他们[删除你的访问权限](../how-to/view-manage-service-providers.md#remove-service-provider-offers)。

## <a name="publish-managed-service-offers"></a>发布托管服务产品

若要了解如何发布托管服务产品，请参阅[将托管服务产品发布到 Azure 市场](../how-to/publish-managed-services-offers.md)。

## <a name="next-steps"></a>后续步骤

- 了解 Azure Lighthouse [体系结构](architecture.md)和[跨租户管理体验](cross-tenant-management-experience.md)。
- 向 Azure 市场[发布托管服务产品](../how-to/publish-managed-services-offers.md)。
