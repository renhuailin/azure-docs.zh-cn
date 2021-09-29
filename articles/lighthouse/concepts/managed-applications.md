---
title: Azure Lighthouse 和 Azure 托管应用程序
description: 了解 Azure Lighthouse 和 Azure 托管应用程序如何配合使用。
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 629f9f1f6b6db69816d26cc7eed1e305dd55923b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768565"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 和 Azure 托管应用程序

Azure 托管应用程序和 Azure Lighthouse 都可以使服务提供商能够访问客户租户中的资源。 了解它们工作方式的差异、它们可帮助实现的方案，以及它们可以配合使用的方式可能会有帮助。

> [!TIP]
> 尽管本主题中只是提到了服务提供商和客户，但[管理多个租户的企业](enterprise.md)也可以使用相同的过程和工具。

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>比较 Azure Lighthouse 和 Azure 托管应用程序

下表说明了一些大概差异，这些差异可能会影响你是选择使用 Azure Lighthouse 还是 Azure 托管应用程序。 如下所述，你还可以设计一种将它们配合使用的解决方案。

|注意事项  |Azure Lighthouse  |Azure 托管应用程序  |
|---------|---------|---------|
|典型用户     |管理多个租户的服务提供商或企业         |独立软件供应商 (ISV)         |
|跨租户访问的范围     |订阅或资源组         |资源组（范围为单个应用程序）         |
|Azure 市场中可购买     |否（产品/服务可以发布到 Azure 市场，但客户需要单独计费）        |是         |
|IP 保护     |是（IP 可以保留在服务提供商的租户中）        |是（根据设计，资源组已锁定到客户）         |
|拒绝分配     |否         |是        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

借助 [Azure Lighthouse](../overview.md)，服务提供商可以直接在客户的订阅（或资源组）上执行各种管理任务。 这种访问是通过逻辑投影实现的，它允许服务提供商登录到自己的租户并访问属于客户租户的资源。 客户可以确定要委派给服务提供商的订阅或资源组，并且可以保持对这些资源的完全访问权限。 他们还可以随时删除服务提供商的访问权限。

若要使用 Azure Lighthouse，客户可通过[部署 ARM 模板](../how-to/onboard-customer.md)或通过 [Azure 市场中的托管服务](managed-services-offers.md)来加入。 你可以通过[关联合作伙伴 ID](../how-to/partner-earned-credit.md) 来跟踪对客户参与度的影响。

服务提供商为客户持续执行管理任务时通常会使用 Azure Lighthouse。 若要详细了解 Azure Lighthouse 在技术级别的工作原理，请参阅 [Azure Lighthouse 体系结构](architecture.md)。

### <a name="azure-managed-applications"></a>Azure 托管应用程序

[Azure 托管应用程序](../../azure-resource-manager/managed-applications/overview.md)允许服务提供商或 ISV 提供便于客户在自己的订阅中进行部署和使用的云解决方案。

在托管应用程序中，该应用程序使用的资源被捆绑在一起并部署到由发布者管理的资源组中。 此资源组在客户的订阅中，但发布者租户中的标识有权访问该资源组。 ISV 继续管理和维护托管的应用程序，而客户不能直接访问其资源组中的工作，也不能访问其资源。

托管应用程序支持[自定义的 Azure 门户体验](../../azure-resource-manager/managed-applications/concepts-view-definition.md)和[与自定义提供程序的集成](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md)。 这些方案可用于提供自定义和集成程度更高的体验，使客户自己能够更轻松地执行一些管理任务。

可以将托管应用程序[发布到 Azure 市场](../../marketplace/azure-app-offer-setup.md)，作为专用产品/服务供特定客户使用，或作为多个客户可以购买的公共产品/服务。 还可以通过[将托管应用程序发布到服务目录](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)，将它们交付给组织中的用户。 可以使用 ARM 模板部署服务目录和市场实例，其中可以包含商业市场合作伙伴的唯一标识符，以跟踪[客户使用情况归属](../../marketplace/azure-partner-customer-usage-attribution.md)。

Azure 托管应用程序通常用于特定客户需求，这可通过完全由服务提供商管理的统包式解决方案来实现。

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>配合使用 Azure Lighthouse 和 Azure 托管应用程序

尽管 Azure Lighthouse 和 Azure 托管应用程序使用不同的访问机制来实现不同的目标，但在某些场景下，服务提供商可以将这两者同时用于同一客户。

例如，客户可能想要服务提供商通过 Azure Lighthouse 提供托管服务，以便他们可以了解合作伙伴的行为，同时继续控制它们委派的订阅。 但是，服务提供商可能不希望客户访问将存储在客户的租户中的某些资源，并且不允许对这些资源进行任何自定义操作。 为了满足这些目标，服务提供商可以将专用产品/服务作为托管应用程序发布。 托管应用程序可以包括部署在客户的租户中的资源组，但客户不能直接访问该资源组。

客户可能还对多个服务提供商提供的托管应用程序感兴趣，无论他们是否也通过任何这些服务提供商提供的 Azure Lighthouse 使用托管服务。 此外，云解决方案提供商 (CSP) 计划中的合作伙伴可以将其他 ISV 发布的某些托管应用程序转售给他们通过 Azure Lighthouse 支持的客户。 借助各种方案，服务提供商可以通过适当的平衡来满足客户的需求，同时在适当的时候限制对资源的访问。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 托管应用程序](../../azure-resource-manager/managed-applications/overview.md)。
- 了解如何[将订阅加入 Azure Lighthouse](../how-to/onboard-customer.md)。
- 了解 [Azure Lighthouse 的 ISV 方案](isv-scenarios.md)。