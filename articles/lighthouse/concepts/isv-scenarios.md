---
title: ISV 方案中的 Azure Lighthouse
description: Isv 可以使用 Azure Lighthouse 的功能，以获得更多的客户服务灵活性。
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696329"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>ISV 方案中的 Azure Lighthouse

[Azure Lighthouse](../overview.md)的常见方案是服务提供商管理其客户 Azure Active Directory (Azure AD) 租户中的资源。 独立软件供应商也可以使用 Azure Lighthouse 的功能， (Isv) 使用基于 SaaS 的产品及其客户。 对于提供需要访问订阅范围的托管服务或支持的 Isv，Azure Lighthouse 特别有用。

## <a name="managed-service-offers-in-azure-marketplace"></a>Azure 市场中的托管服务产品

作为 ISV，你可能已将解决方案发布到 Azure Marketplace。 如果为客户提供托管服务，则可以通过发布托管服务产品/服务来执行此操作。 这些产品/服务简化了载入过程，使你的服务可根据需要更好地缩放到多个客户。 Azure Lighthouse 支持多种 [管理任务和方案](cross-tenant-management-experience.md#enhanced-services-and-scenarios) ，可用于为客户提供价值。

有关详细信息，请参阅 [将托管服务产品/服务发布到 Azure Marketplace](../how-to/publish-managed-services-offers.md)。

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>将 Azure Lighthouse 与 Azure 托管应用程序配合使用

[Azure 托管应用程序](../../azure-resource-manager/managed-applications/overview.md) 是 isv 可以为其客户提供服务的另一种方式。 可以将 Azure Lighthouse 与 Azure 托管应用程序一起使用，以启用增强的方案。

有关详细信息，请参阅 [Azure Lighthouse 和 azure 托管应用程序](managed-applications.md)。

## <a name="saas-based-multi-tenant-offerings"></a>基于 SaaS 的多租户产品

另一种方案是 ISV 在其自己的租户中托管订阅中的资源，然后使用 Azure Lighthouse 让客户访问这些资源。 然后，客户可以登录到自己的租户，并根据需要访问这些资源。 Isv 在自己的租户中维护其 IP，并可使用其自己的支持计划来产生与租户中托管的解决方案相关的票证，而不是使用客户的计划。 由于资源位于 ISV 的租户中，因此所有操作都可以由 ISV 直接执行，例如登录到 Vm、安装应用以及执行维护任务。

在此方案中，客户租户中的用户实质上被授予了 "管理租户" 的访问权限，即使客户不管理 ISV 的资源也是如此。 因为他们直接访问 ISV 的租户，所以仅授予必要的最小权限是非常重要的，因此，客户不会无意中对解决方案或其他 ISV 资源进行更改。

若要启用此体系结构，ISV 需要获取客户 Azure AD 租户中的用户组的对象 ID 及其租户 ID。 然后，ISV 会构建一个 ARM 模板，该模板向此用户组授予适当的权限，并将其部署到包含客户将访问的资源的 [ISV 订阅](../how-to/onboard-customer.md) 。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解 [Azure 委派资源管理](azure-delegated-resource-management.md)。