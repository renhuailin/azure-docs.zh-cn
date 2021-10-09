---
title: ISV 应场景中的 Azure Lighthouse
description: ISV 可以使用 Azure Lighthouse 的功能，以更灵活地向客户提供产品/服务。
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 9266b8d026446600cf25d2f141785c3a461ad0e8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768679"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>ISV 场景中的 Azure Lighthouse

[Azure Lighthouse](../overview.md) 的常见应用场景包括：服务提供商使用 Azure Lighthouse 来管理其客户的 Azure Active Directory (Azure AD) 租户中的资源。 独立软件供应商 (ISV) 也可以利用 Azure Lighthouse 的功能为客户提供基于 SaaS 的产品/服务。 Azure Lighthouse 对于提供需要访问订阅范围的托管服务或支持的 ISV 来说尤其有用。

## <a name="managed-service-offers-in-azure-marketplace"></a>Azure 市场中的托管服务产品

作为 ISV，你可能已经将解决方案发布到 Azure 市场。 如果为客户提供托管服务，可以通过发布托管服务产品来实现。 这些产品简化了加入过程，使服务更加能够根据所需的用户数进行缩放。 Azure Lighthouse 支持各种可用于为客户提供价值的[管理任务和方案](cross-tenant-management-experience.md#enhanced-services-and-scenarios)。

有关详细信息，请参阅[将托管服务产品发布到 Azure 市场](../how-to/publish-managed-services-offers.md)。

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>将 Azure Lighthouse 和 Azure 托管应用程序配合使用

[Azure 托管应用程序](../../azure-resource-manager/managed-applications/overview.md)是 ISV 可以为客户提供服务的另一种方式。 可以将 Azure Lighthouse 与 Azure 托管应用程序配合用于增强方案。

有关详细信息，请参阅 [Azure Lighthouse 和 Azure 托管应用程序](managed-applications.md)。

## <a name="saas-based-multi-tenant-offerings"></a>基于 SaaS 的多租户产品/服务

另一种情况是 ISV 在自己租户中托管订阅中的资源，然后使用 Azure Lighthouse 允许客户访问这些资源。 然后，客户可以登录到自己的租户，并根据需要访问这些资源。 ISV 可以在自己的租户中维护其 IP，并且可以使用自己的支持计划而不是客户的计划来提出与租户中托管的解决方案相关的票证。 由于资源在 ISV 的租户中，因此 ISV 可以直接执行所有操作，例如登录到 VM、安装应用以及执行维护任务。

在这种情况中，客户租户中的用户实质上是作为“管理租户”被授予访问权限，即使客户不管理 ISV 的资源。 因为他们会直接访问 ISV 的租户，所以必须仅授予必要的最小权限，避免客户在无意中更改解决方案或其他 ISV 资源。

若要启用此体系结构，ISV 需要获取客户 Azure AD 租户中的用户组的对象 ID 及其租户 ID。 然后，ISV 会生成一个用于向此用户组授予适当权限的 ARM 模板，并[将该模板部署到 ISV 订阅中](../how-to/onboard-customer.md)（该订阅包含客户将访问的资源）。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 详细了解 [Azure Lighthouse 体系结构](architecture.md)。
