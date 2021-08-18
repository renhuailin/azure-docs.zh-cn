---
title: Azure 事件网格 - 合作伙伴事件
description: 通过 Azure 事件网格将第三方事件网格 SaaS 和 PaaS 合作伙伴的事件直接发送到 Azure 服务。
ms.topic: conceptual
ms.date: 06/15/2021
ms.openlocfilehash: 5a215d8d007f411066d25d8751299ae6a73038dc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183989"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Azure 事件网格中的合作伙伴事件（预览版）
利用合作伙伴事件功能，第三方 SaaS 提供商可从它的服务发布事件，以便使用者可以订阅这些事件。 此功能通过公开[主题](concepts.md#topics)类型（合作伙伴主题）为第三方事件源提供第一方体验。 订阅者会创建对此主题的订阅以使用事件。 它还会通过分离事件发布者和订阅者使用的资源的问题和所有权来提供干净的“发布-订阅”模型。

> [!NOTE]
> 如果你是刚开始使用事件网格，请参阅[概述](overview.md)、[概念](concepts.md)和[事件处理程序](event-handlers.md)。

## <a name="what-is-partner-events-to-a-publisher"></a>对于发布者而言，什么是合作伙伴事件？
对于事件发布者，合作伙伴事件功能使发布者可以执行以下任务：

- 将事件源加入事件网格
- 创建可以将事件发布到的命名空间（终结点）
- 在 Azure 中创建订阅者拥有并用于使用事件的合作伙伴主题

## <a name="what-is-partner-events-to-a-subscriber"></a>对于订阅者而言，什么是合作伙伴事件？
对于订阅者，合作伙伴事件功能使其可以在 Azure 中创建合作伙伴主题，以使用来自第三方事件源的事件。 事件使用通过创建将事件发送（推送）到订阅者事件处理程序的事件订阅来实现。

## <a name="why-should-i-use-partner-events"></a>使用合作伙伴事件的理由是什么？
如果你有以下一个或多个要求，则可能要使用合作伙伴事件。

### <a name="for-publishers"></a>对于发布者

- 你需要一种机制，用于在 Azure 上提供事件。 用户可以使用其拥有和管理的合作伙伴主题和事件订阅来筛选和路由这些事件。 你可以使用其他集成方法，如[主题](custom-topics.md)和[域](event-domains.md)。 但是，这些方法不允许在发布者与订阅者之间干净地分离资源（合作伙伴主题）、所有权、管理和计费。 此外，此方法可提供更直观的用户体验，使用户能够轻松发现和使用合作伙伴主题。
- 你要将事件发布到单个终结点，即命名空间的终结点。 而且需要能够筛选这些事件，以便只提供一部分事件。 
- 你要了解与已发布事件相关的指标。
- 你要为事件使用[云事件 1.0](https://cloudevents.io/) 架构。

### <a name="for-subscribers"></a>对于订阅者

- 你要订阅来自[第三方发布者](#available-third-party-event-publishers)的事件，并使用处于 Azure 或其他位置的事件处理程序来处理事件。
- 你要利用一组丰富的路由功能和[目标/事件处理程序](overview.md#event-handlers)来处理来自第三方源的事件。 
- 你要实现松耦合体系结构，其中的订阅者/事件处理程序不知道是否存在所使用的消息代理。 
- 你需要具有发送重试支持和至少一次语义的可复原推送传递机制。
- 你要为事件使用[云事件 1.0](https://cloudevents.io/) 架构。 


## <a name="available-third-party-event-publishers"></a>可用的第三方事件发布者
第三方事件发布者必须先经历[加入过程](partner-onboarding-overview.md)，订阅者才能开始使用其事件。 


### <a name="auth0"></a>Auth0
Auth0 是可用的第一个合作伙伴发布者。 可以创建 [Auth0 合作伙伴主题](auth0-overview.md)以连接 Auth0 和 Azure 帐户。 此集成使你可以实时响应、记录和监视 Auth0 事件。 若要试用，请参阅[将 Azure 事件网格与 Auto0 集成](auth0-how-to.md)

 
## <a name="resources-managed-by-event-publishers"></a>事件发布者管理的资源
事件发布者会创建和管理以下资源：

### <a name="partner-registration"></a>合作伙伴注册
注册包含与发布者相关的常规信息。 它定义了一种合作伙伴主题类型，当用户尝试创建合作伙伴主题时，该类型会作为选项显示在 Azure 门户中。 发布者可以公开多个或更多合作伙伴主题类型，以满足其订阅者的需求。 即，发布者可以为来自不同服务的事件创建单独注册（合作伙伴主题类型）。 例如，对于人力资源 (HR) 服务，发布者可以为员工加入、员工提升和员工离职等事件定义合作伙伴主题。 

请注意以下几点：

- 只有 Azure 批准的合作伙伴注册才可见。 
- 注册是全局的。 即，它们不关联到特定 Azure 区域。
- 注册是可选资源。 但是，建议你（作为发布者）创建注册。 它使用户可以在 [Azure 门户](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic)的“创建合作伙伴主题”页上发现主题。 用户随后可以在创建事件订阅时选择事件类型（例如，员工加入、员工离职等）。

### <a name="namespace"></a>命名空间
与[自定义主题](custom-topics.md)和[域](event-domains.md)一样，合作伙伴命名空间是用于发布事件的区域终结点。 发布者正是通过命名空间来创建和管理事件通道。 命名空间还用作事件通道的容器资源。

### <a name="event-channels"></a>事件通道
事件通道是合作伙伴主题的镜像资源。 发布者在发布者 Azure 订阅中创建事件通道时，也会在订阅者 Azure 订阅下创建合作伙伴主题。 对事件通道执行的操作（GET 除外）会应用于相应的订阅者合作伙伴主题（甚至是删除）。 但是，只有合作伙伴主题才是可配置订阅和事件传递的资源类型。

## <a name="resources-managed-by-subscribers"></a>订阅者管理的资源 
订阅者可以使用发布者定义的合作伙伴主题，这是他们看到和管理的唯一资源类型。 创建合作伙伴主题后，订阅者用户可以创建向[目标/事件处理程序](overview.md#event-handlers)定义筛选规则的事件订阅。 对于订阅者，合作伙伴主题及其关联事件订阅提供了与[自定义主题](custom-topics.md)及其相关订阅相同的丰富功能，不过有一个显著区别：合作伙伴主题仅支持[云事件 1.0 架构](cloudevents-schema.md)，该架构可提供一组比其他受支持架构更丰富的功能。

下图显示控制平面操作流。

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="合作伙伴事件 - 控制平面流":::

1. 发布者创建合作伙伴注册。 合作伙伴注册是全局的。 即，它们不与特定 Azure 区域关联。 此步骤是可选的。
1. 发布者在特定区域中创建合作伙伴命名空间。
1. 当订阅者 1 尝试创建合作伙伴主题时，会首先在发布者 Azure 订阅中创建一个事件通道（事件通道 1）。
1. 随后在订阅者 Azure 订阅中创建一个合作伙伴主题（合作伙伴主题 1）。 订阅者需要激活该合作伙伴主题。 
1. 订阅者 1 创建针对合作伙伴主题 1 的 Azure 逻辑应用订阅。
1. 订阅者 1 创建针对合作伙伴主题 1 的 Azure Blob 存储订阅。 
1. 当订阅者 2 尝试创建合作伙伴主题时，会首先在发布者 Azure 订阅中创建另一个事件通道（事件通道 2）。 
1. 随后在第二个订阅者 Azure 订阅中创建一个合作伙伴主题（合作伙伴主题 2）。 订阅者需要激活该合作伙伴主题。 
1. 订阅者 2 创建针对合作伙伴主题 2 的 Azure Functions 订阅。 

## <a name="pricing"></a>定价
合作伙伴主题按使用事件网格时完成的操作数收费。 有关用作计费基础的所有操作类型的详细信息和详细价格信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="limits"></a>限制
有关对合作伙伴主题实施的限制的详细信息，请参阅[事件网格服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)。


## <a name="next-steps"></a>后续步骤

- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)
- [成为事件网格合作伙伴](partner-onboarding-overview.md)