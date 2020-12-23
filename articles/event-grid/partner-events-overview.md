---
title: Azure 事件网格-合作伙伴事件
description: 通过 Azure 事件网格将第三方事件网格 SaaS 和 PaaS 合作伙伴的事件直接发送到 Azure 服务。
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506136"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Azure 事件网格中的合作伙伴事件 (预览) 
使用 **合作伙伴活动** 功能，第三方 SaaS 提供程序可以从其服务发布事件，以便使用者可以订阅这些事件。 此功能通过公开 [主题](concepts.md#topics) 类型（ **合作伙伴主题** ）向第三方事件源提供第一方体验。 订阅者创建对此主题的订阅以使用事件。 它还通过分离事件发布者和订阅服务器所使用的资源的问题和所有权，来提供干净的 pub 子模型。

> [!NOTE]
> 如果你是使用事件网格的新手，请参阅 [概述](overview.md)、 [概念](concepts.md)和 [事件处理程序](event-handlers.md)。

## <a name="what-is-partner-events-to-a-publisher"></a>什么是发布者的合作伙伴事件？
对于事件发布者，Partner Events 功能允许发布者执行以下任务：

- 将其事件源载入事件网格
- 创建可向其发布事件 (终结点) 的命名空间
- 在 Azure 中创建订户拥有并使用事件的合作伙伴主题

## <a name="what-is-partner-events-to-a-subscriber"></a>什么是订阅服务器的合作伙伴事件？
对于订阅者来说，合作伙伴活动功能允许他们在 Azure 中创建合作伙伴主题，以使用来自第三方事件源的事件。 通过创建将 (推送) 事件发送到订阅服务器的事件处理程序的事件订阅来实现事件使用。

## <a name="why-should-i-use-partner-events"></a>为什么应使用合作伙伴事件？
如果你有以下一个或多个要求，你可能想要使用合作伙伴事件。

### <a name="for-publishers"></a>对于发布者

- 你需要一种机制来使你的事件在 Azure 上可用。 你的用户可以使用他们所拥有和管理的合作伙伴主题和事件订阅来筛选和路由这些事件。 您可以使用其他集成方法，例如 [主题](custom-topics.md) 和 [域](event-domains.md)。 但是，它们不允许在发布服务器和订阅服务器之间完全分离资源 (合作伙伴主题) 所有权、管理和计费。 此外，此方法提供了更直观的用户体验，使用户能够轻松发现和使用合作伙伴主题。
- 需要将事件发布到单个终结点，即命名空间的终结点。 而且，您还希望能够筛选这些事件，以便只提供其中的一个子集。 
- 您希望了解与已发布事件相关的指标。
- 你需要为事件使用 [Cloud Events 1.0](https://cloudevents.io/) 架构。

### <a name="for-subscribers"></a>对于订阅者

- 你需要订阅 [第三方发布者](#available-third-party-event-publishers) 的事件，并使用 Azure 或其他位置上的事件处理程序处理事件。
- 需要利用一组丰富的路由功能和 [目标/事件处理程序](overview.md#event-handlers) 来处理来自第三方源的事件。 
- 要实现松耦合体系结构，在该体系结构中，订户/事件处理程序不知道是否存在使用的消息代理。 
- 需要使用发送重试支持和至少一次语义的弹性推送传递机制。
- 你需要为事件使用 [Cloud Events 1.0](https://cloudevents.io/) 架构。 


## <a name="available-third-party-event-publishers"></a>可用的第三方事件发布者
第三方事件发布者必须经历 [加入过程](partner-onboarding-overview.md) ，订户才能开始使用其事件。 

如果你是订阅者，并希望第三方服务通过事件网格公开其事件， 

### <a name="auth0"></a>Auth0
**Auth0** 是第一个合作伙伴发布者。 你可以创建一个 [Auth0 合作伙伴主题](auth0-overview.md) 来连接 Auth0 和 Azure 帐户。 通过这种集成，你可以实时响应、记录和监视 Auth0 事件。 若要试用，请参阅将 [Azure 事件网格与 Auto0 集成](auth0-how-to.md)

如果希望第三方服务通过 "事件网格" 公开其事件，请在 [用户语音门户](https://feedback.azure.com/forums/909934-azure-event-grid)中提交该想法。
 
## <a name="resources-managed-by-event-publishers"></a>事件发布者管理的资源
事件发布者创建和管理以下资源：

### <a name="partner-registration"></a>合作伙伴注册
注册包含与发布者相关的一般信息。 它定义了一种在 "Azure 门户" 中显示的合作伙伴主题，可在用户尝试创建合作伙伴主题时使用。 发布者可能会公开多个或多个合作伙伴主题类型以满足其订户的需求。 也就是说，发布者可以为不同服务中的事件 (的伙伴主题) 类型创建单独的注册。 例如，对于人事资源 (HR) 服务，发布者可以为活动（如已加入员工、员工促销和员工离开公司）定义合作伙伴主题。 

请记住以下几点：

- 只有 Azure 批准的合作伙伴注册才可见。 
- 注册是全局的。 也就是说，它们不会关联到特定的 Azure 区域。
- 注册是一个可选资源。 但建议您 (为发布者) 创建注册。 它允许用户在 [Azure 门户](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic)中的 " **创建合作伙伴" 主题** 页上发现主题。 然后，用户可以选择 "事件类型" (例如，"联接的雇员"、"employee" 等。创建事件订阅时，) 。

### <a name="namespace"></a>命名空间
与 [自定义主题](custom-topics.md) 和 [域](event-domains.md)一样，伙伴命名空间是用于发布事件的区域终结点。 它通过发布者创建和管理事件通道的命名空间。 命名空间还充当事件通道的容器资源。

### <a name="event-channels"></a>事件通道
事件通道是合作伙伴主题的镜像资源。 当发布者在发布者的 Azure 订阅中创建事件通道时，它还会在订阅者的 Azure 订阅下创建合作伙伴主题。 针对事件通道执行的操作 (除了 GET) 之外，还将应用于相应的订阅者伙伴主题，甚至删除。 不过，只有合作伙伴主题是可在其上配置订阅和事件传递的资源类型。

## <a name="resources-managed-by-subscribers"></a>由订阅服务器管理的资源 
订阅者可以使用发布者定义的合作伙伴主题，它是他们看到和管理的唯一资源类型。 创建合作伙伴主题后，订阅者用户可以创建事件订阅来定义针对 [目标/事件处理程序](overview.md#event-handlers)的筛选规则。 对于订阅者来说，合作伙伴主题及其关联的事件订阅提供了与 [自定义主题](custom-topics.md) 和其相关订阅相同的丰富功能， (的) 具有显著的区别：合作伙伴主题仅支持 [Cloud Events 1.0 架构](cloudevents-schema.md)，该架构提供的功能比其他支持的架构更丰富。

下图显示控制平面操作的流。

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="合作伙伴活动-控制平面流":::

1. 发布者创建 **合作伙伴注册** 。 合作伙伴注册是全局性的。 也就是说，它们不与特定的 Azure 区域相关联。 此步骤是可选的。
1. 发布者在特定区域中创建 **合作伙伴命名空间** 。
1. 当订阅服务器1尝试创建合作伙伴主题时，将首先在发布者的 Azure 订阅中创建一个 **事件通道** （事件通道1）。
1. 然后，在订阅者的 Azure 订阅中创建 **合作伙伴** 主题 "合作伙伴 1"。 订阅者需要激活合作伙伴主题。 
1. 订户1创建了一个适用于合作伙伴的 **Azure 逻辑应用订阅** 。
1. 订户1创建了一个到合作伙伴的 **Azure Blob 存储订阅** 。 
1. 当订阅服务器2尝试创建合作伙伴主题时，将首先在发布者的 Azure 订阅中创建另一个 **事件通道** （事件通道2）。 
1. 然后，在第二个订阅者的 Azure 订阅中创建 **合作伙伴主题** 2。 订阅者需要激活合作伙伴主题。 
1. 订户2创建一个 **Azure Functions 订阅** 到合作伙伴的第2方。 

## <a name="pricing"></a>定价
合作伙伴主题按使用事件网格时完成的操作数量收费。 有关用作计费和详细价格信息的基础的所有类型的操作的详细信息，请参阅 [事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="limits"></a>限制
有关合作伙伴的限制的详细信息，请参阅 [事件网格服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) 。


## <a name="next-steps"></a>后续步骤

- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)
- [成为事件网格合作伙伴](partner-onboarding-overview.md)