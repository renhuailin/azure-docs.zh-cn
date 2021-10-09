---
title: 企业应用场景中的 Azure Lighthouse
description: Azure Lighthouse 的功能可以简化使用多个 Azure AD 租户的企业中的跨租户管理。
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: f83e5d84eee0c3ed7f159778630e3598274e4916
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768774"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>企业应用场景中的 Azure Lighthouse

[Azure Lighthouse](../overview.md) 的常见场景是服务提供商管理 Azure Active Directory (Azure AD) 租户中属于客户的资源。 Azure Lighthouse 的功能还可以简化使用多个 Azure AD 租户的企业中的跨租户管理。

## <a name="single-vs-multiple-tenants"></a>单个租户与多个租户

对于大多数组织而言，单个 Azure AD 租户的管理比较轻松。 将所有资源部署在一个租户中可按该租户中的指定用户、用户组或服务主体来集中处理管理任务。 我们建议尽量对组织使用一个租户。

某些组织可能需要使用多个 Azure AD 租户。 这可能是一种暂时性的情况，比如发生收购后并尚未定义长期租户合并策略。 其他情况下，由于子公司完全独立、地理位置、法律要求或其他注意事项，组织可能需要持续维护多个租户。

如果需要多租户体系结构，Azure Lighthouse 可帮助集中处理和简化管理操作。 通过使用 Azure Lighthouse，一个管理租户中的用户可以采用集中、可缩放的方式执行[跨租户管理功能](cross-tenant-management-experience.md)。

## <a name="tenant-management-architecture"></a>租户管理体系结构

若要在企业中使用 Azure Lighthouse，需要确定哪个租户将包含对其他租户执行管理操作的用户。 换句话说，你需要指定一名租户作为其他租户的管理租户。

例如，假设你的组织有一个租户，我们称之为“租户 A”，。然后你的组织又织获得“租户 B”和“租户 C”，而你出于业务原因需要将它们作为单独的租户进行维护。 但是，你想要为所有租户使用相同的策略定义、备份做法和安全过程，并由同一组用户执行管理任务。

由于租户 A 已经包含组织中的用户，这些用户已在租户 A 中执行这些任务，因此，你可以在租户 B 和租户 C 内加入订阅，这允许租户 A 中的相同用户在所有租户中执行这些任务。

![显示租户 A 中的用户管理租户 B 和租户 C 中资源的关系图。](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>安全和访问注意事项

在大多数企业场景中，需要将整个订阅委托到 Azure Lighthouse。 也可选择仅委托订阅中的特定资源组。

无论采用哪种方式，都需要确保[在定义哪些用户拥有委托的资源访问权限时遵循最低特权的原则](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)。 这有助于确保用户只拥有执行所需任务而需要的权限，并可以减少意外出错的可能性。

Azure Lighthouse 只在管理租户和被管理租户之间提供逻辑链接，而不会以物理方式移动数据或资源。 此外，访问始终只朝一个方向进行，即，从管理租户访问被管理租户。 在对被管理租户资源执行管理操作时，管理租户中的用户和组应继续使用多重身份验证。

实施内部或外部监管与合规性准则的企业可以使用 [Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)来满足其透明性要求。 如果企业租户已在管理租户与被管理租户之间建立了关系，每个租户中的用户可通过查看记录的活动来了解管理租户中用户所执行的操作。

## <a name="onboarding-considerations"></a>加入注意事项

可部署 Azure 资源管理器模板，或者通过发布到 Azure 市场的托管服务套餐，将订阅（或订阅中的资源组）加入到 Azure Lighthouse。

由于企业用户通常具有对企业租户的直接访问权限，并且无需营销或促销管理套餐，因此，部署 Azure 资源管理器模板通常速度更快且更简单。 尽管[加入指南](../how-to/onboard-customer.md)面向的是服务提供商和客户，但企业可以使用相同的过程来加入其租户。

如果需要，可以通过[将托管服务套餐发布到 Azure 市场](../how-to/publish-managed-services-offers.md)来登记企业中的租户。 为了确保该套餐仅适用于相应的租户，请务必将计划标记为“专用”。 使用专用计划时，可为你打算加入的每个租户提供订阅 ID，而其他任何人都无法获取你的套餐。

## <a name="terminology-notes"></a>术语注释

对于企业中的跨租户管理，可将 Azure Lighthouse 文档中的服务提供商参考内容理解为适用于企业中的管理租户 - 即，包含要通过 Azure Lighthouse 来管理其他租户中资源的用户的租户。 同理，可将客户的所有参考内容理解为适用于委托要通过管理租户中用户管理的资源的租户。

例如，在上述示例中，可将租户 A 视为服务提供商租户（管理租户），将租户 B 和租户 C 视为客户租户。

继续使用此示例，具有相应权限的租户 A 用户可以在 Azure 门户的“我的客户”页中[查看和管理委托的资源](../how-to/view-manage-customers.md)。 同理，具有相应权限的租户 B 和租户 C 用户可以在 Azure 门户的“服务提供商”页中[查看和管理委托给租户 A 的资源](../how-to/view-manage-service-providers.md)。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 详细了解 [Azure Lighthouse 的工作原理](architecture.md)。
