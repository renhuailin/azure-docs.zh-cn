---
title: Azure 服务总线异地灾难恢复 | Microsoft 文档
description: 如何在 Azure 服务总线中使用地理区域进行故障转移和灾难恢复
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 3e8050cdaaae7e16a0f5125292df4b89b3690ed3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035388"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure 服务总线异地灾难恢复

针对数据处理资源灾难性中断的复原能力是许多企业的一项要求，某些情况下甚至是行业法规要求。 

Azure 服务总线已在跨数据中心的多个故障域的各个群集之间提高了个别计算机的灾难性故障的风险，甚至跨群集实现了整个机架的风险，并且还实现了透明的故障检测和故障转移机制，这样，在发生此类故障时，服务将继续在有保证的服务级别内操作，并且通常不会出现中断 如果已使用 [可用性区域](../availability-zones/az-overview.md)的 enabled 选项创建了服务总线命名空间，则风险会进一步分散到三个物理上分离的设备，并且该服务有足够的容量预留，可立即应对整个设施的整个灾难性损失。 

在针对严重硬件故障甚至整个数据中心设施灾难性损失的恢复能力方面，具有可用性区域支持的完全活动 Azure 服务总线群集模型优于任何本地消息中转站产品。 尽管如此，还可能出现一些大的物理析构，甚至这些措施无法充分防范。 

服务总线异地灾难恢复功能旨在使你更轻松地从如此大规模的灾难中恢复，并永久放弃发生故障的 Azure 区域，且无需更改应用程序配置。 放弃 Azure 区域通常会涉及到几项服务，此功能主要是为了帮助保持复合应用程序配置的完整性。 此功能在全球范围内可用于服务总线高级 SKU。 

异地灾难恢复功能可确保命名空间（队列、主题、订阅、筛选器）的整个配置在配对时将从主命名空间连续复制到辅助命名空间，并允许你随时启动从主命名空间到辅助命名空间的一次性故障转移。 故障转移移动会将命名空间的所选别名 repoint 到辅助命名空间，然后打破配对。 故障转移几乎是启动后立即发生的。 

> [!IMPORTANT]
> 此功能可以实现具有相同配置的操作的即时连续性，但 **不会复制保留在队列或主题订阅或死信队列中的消息**。 若要保留队列语义，此类复制不仅需要复制消息数据，还需要复制中转站中的每个状态更改。 对于大多数服务总线命名空间，所需复制流量将远远超过应用程序流量并具有高吞吐量队列，大多数消息仍会在其已从主命名空间中删除的情况下复制到辅助命名空间，从而导致流量过度浪费。 对于高延迟复制路由（适用于为异地灾难恢复选择的许多配对），由于延迟导致的限制影响，复制流量也可能无法持续跟上应用程序流量。
 
> [!TIP]
> 若要复制队列和主题订阅的内容，并在主动/主动配置中操作相应的命名空间来应对中断和灾难，请不要依赖此异地灾难恢复功能集，而是要按照[复制指南](service-bus-federation-overview.md)进行操作。  

## <a name="outages-and-disasters"></a>中断和灾难

请务必注意“中断”和“灾难”的区别。 

中断是指 Azure 服务总线暂时不可用，可能会影响服务的某些组件，如消息存储，甚至是整个数据中心。 但在问题解决后，服务总线将恢复可用。 通常情况下，中断不会导致消息或其他数据丢失。 例如，数据中心的电源故障可能会导致此类中断。 某些中断由于暂时性故障或网络问题只是短时间丢失连接。 

根据定义，*灾难* 是指永久或长期丢失服务总线群集、Azure 区域或数据中心。 该区域或数据中心不一定会恢复可用，或可能停用数小时或数天。 例如，火灾、洪灾或地震等可能导致此类灾难。 永久性灾难可能会导致一些消息、事件或其他数据丢失。 不过，在大多数情况下，都不应该会有数据丢失，并且在数据中心备份后，便可以恢复消息。

Azure 服务总线的异地灾难恢复功能是一项面向灾难恢复的解决方案。 本文中所述的概念和工作流适用于灾难方案，而不适用于暂时或临时中断。 有关 Microsoft Azure 中的灾难恢复的详细讨论，请参阅[此文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。   

## <a name="basic-concepts-and-terms"></a>基本概念和术语

灾难恢复功能可实现元数据灾难恢复，并且依赖于主要和次要灾难恢复命名空间。 异地灾难恢复功能仅适用于 [高级 SKU](service-bus-premium-messaging.md) 。 不需要对连接字符串进行任何更改，因为连接是通过别名建立的。

本文涉及以下术语：

-  *别名*：所设置的灾难恢复配置的名称。 别名提供单个稳定的完全限定域名 (FQDN) 连接字符串。 应用程序使用此别名连接字符串连接到命名空间。 使用别名可确保连接字符串在触发故障转移后保持不变。

-  *主要/次要命名空间*：别名所对应的命名空间。 主要命名空间是“主动”的，并且会接收消息（该命名空间可以是现有的命名空间，也可以是新的命名空间）。 次要命名空间是“被动”的，不接收消息。 这两者之间的元数据保持同步，因此这两者可以无缝接受消息，而不必更改任何应用程序代码或连接字符串。 若要确保只有主动命名空间接收消息，必须使用别名。 
-  *元数据*：队列、主题、订阅等实体及其与命名空间关联的服务的属性。 仅自动复制实体及其设置。 不会复制消息。
-  *故障转移*：激活辅助命名空间的过程。

## <a name="setup"></a>设置

以下部分概述了如何在命名空间之间设置配对。

![1][]

首先，创建一个主要命名空间或使用现有的主要命名空间，并创建一个新的次要命名空间，然后将两者配对。 此配对提供可用于连接的别名。 由于使用别名，因此，无需更改连接字符串。 只有新的命名空间可以添加到故障转移配对。 

1. 创建主命名空间。
1. 在不同的区域中创建辅助命名空间。 此步骤是可选的。 在下一步中创建配对时，可以创建辅助命名空间。 
1. 在 Azure 门户中，导航到你的主命名空间。
1. 在左侧菜单中选择 " **异地恢复** "，然后在工具栏上选择 " **启动配对** "。 

    :::image type="content" source="./media/service-bus-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="从主命名空间启动配对":::    
1. 在 " **启动配对** " 页上，执行以下步骤：
    1. 选择现有的辅助命名空间，或在不同的区域中创建一个。 在此示例中，现有命名空间用作辅助命名空间。  
    1. 对于 " **别名**"，请输入异地 dr 配对的别名。 
    1. 然后选择“创建”  。 

        :::image type="content" source="./media/service-bus-geo-dr/initiate-pairing-page.png" alt-text="选择辅助命名空间":::        
1. 应会看到 " **服务总线异地灾难恢复别名** " 页，如下图所示。 还可以通过在左侧菜单中选择 "**异地恢复**"，从 "主命名空间" 页导航到 "**异地灾难恢复别名**" 页。 

    :::image type="content" source="./media/service-bus-geo-dr/service-bus-geo-dr-alias-page.png" alt-text="Service Bus 异地灾难恢复别名页":::
1. 在 " **异地灾难恢复别名** " 页上，选择左侧菜单上的 " **共享访问策略** " 来访问别名的主连接字符串。 使用此连接字符串，而不是直接使用主/辅助命名空间的连接字符串。 最初，别名指向主要命名空间。
1. 切换到“概览”页。  你可以执行以下操作： 
    1. 中断主命名空间和辅助命名空间之间的配对。 在工具栏上选择 " **中断配对** "。 
    1. 手动故障转移到辅助命名空间。 
        1. 在工具栏上选择 " **故障转移** "。 
        1. 通过在别名中键入来确认要故障转移到辅助命名空间。 
        1. 启用 " **安全故障转移** " 选项以安全故障转移到辅助命名空间。 此功能可确保在切换到辅助副本之前完成挂起的异地灾难恢复复制。 
        1. 然后选择 " **故障转移**"。 
        
            :::image type="content" source="./media/service-bus-geo-dr/failover-page.png" alt-text="{alt-text}":::
    
            > [!IMPORTANT]
            > 故障转移将激活辅助命名空间，并从 Geo-Disaster 恢复配对中删除主命名空间。 创建另一个命名空间以使用新的异地灾难恢复对。 

1. 最后，应添加一些监视功能，以检测是否有必要进行故障转移。 在大多数情况下，服务是一个大型生态系统的一部分，因此很少会发生自动故障转移，因为通常故障转移必须在与剩余子系统或基础结构保持同步的情况下进行。

### <a name="service-bus-standard-to-premium"></a>服务总线标准到高级
如果已将 [Azure 服务总线标准命名空间迁移到 Azure 服务总线高级命名空间](service-bus-migrate-standard-premium.md)，则必须使用预先存在的别名 (即，服务总线标准命名空间连接字符串) ，通过 **PS/CLI** 或 **REST API** 创建灾难恢复配置。

这是因为，在迁移过程中，Azure 服务总线标准命名空间连接字符串/dns/DNS 名称本身成为 Azure 服务总线高级命名空间的别名。

你的客户端应用程序必须使用此别名 (即，Azure 服务总线标准命名空间连接字符串) 连接到已设置灾难恢复配对的高级命名空间。

如果你使用门户来设置灾难恢复配置，则门户将从你处抽象出这一注意事项。


## <a name="failover-flow"></a>故障转移流程

故障转移由客户手动触发（通过命令显式触发，或者通过客户拥有的触发了该命令的业务逻辑），从不会由 Azure 触发。 它为客户提供对 Azure 主干上的中断解析的完全所有权和可见性。

![4][]

在故障转移触发后 -

1. “别名”连接字符串更新为指向次要高级命名空间。

2. 客户端（发送方和接收方）自动连接到次要命名空间。

3. 主要高级命名空间与次要高级命名空间之间的现有配对将被破坏。

在故障转移启动后 -

1. 如果又出现其他中断，则需要能够再次进行故障转移。 因此，请设置另一个被动命名空间，并更新配对。 

2. 一旦以前的主要命名空间恢复可用，请从该命名空间拉取消息。 在此之后，请使用该命名空间在异地恢复设置之外进行常规消息传送，或删除旧的主要命名空间。

> [!NOTE]
> 仅支持失败转发语义。 在此方案中，先进行故障转移，然后与新的命名空间重新配对。 不支持故障回复；例如，在 SQL 群集中不支持这样做。 

可以使用监视系统或定制监视解决方案自动执行故障转移。 但是，这种自动执行需要额外的规划和工作，它超出了本文的讨论范围。

![2][]

## <a name="management"></a>管理

如果出了错，例如在初始设置过程中将错误的区域配对，则随时可以中断这两个命名空间的配对。 如果想要使用配对命名空间作为常规命名空间，请删除别名。

## <a name="use-existing-namespace-as-alias"></a>使用现有的命名空间作为别名

如果你的方案无法更改创建者和使用者的连接，则可以重复使用命名空间名称作为别名。 请参阅[此处提供的 GitHub 上的示例代码](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)。

## <a name="samples"></a>示例

[GitHub 上的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/)演示如何设置和启动故障转移。 这些示例演示以下概念：

- Azure Active Directory 将 Azure 资源管理器用于服务总线、设置和启用异地灾难恢复中所需的 .NET 示例和设置。
- 执行示例代码所需的步骤。
- 如何使用现有的命名空间作为别名。
- 改用 PowerShell 或 CLI 启用异地灾难恢复的步骤。
- 使用别名从当前的主要或次要命名空间进行[发送和接收](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

## <a name="considerations"></a>注意事项

此版本需要注意以下事项：

1. 在故障转移规划中，还应考虑时间因素。 例如，如果失去连接的时间超过 15 到 20 分钟，你可能会决定启动故障转移。

2. 未复制数据是指未复制当前处于活动状态的会话。 此外，重复检测和计划消息可能无法正常工作。 新会话、新计划消息和新的重复项都将起作用。 

3. 故障转移复杂的分布式基础结构应至少[演练](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)一次。

4. 同步实体可能需要一些时间，每分钟大约 50-100 个实体。 订阅和规则也计为实体。

### <a name="availability-zones"></a>可用性区域

服务总线高级 SKU 支持[可用性区域](../availability-zones/az-overview.md)，在同一 Azure 区域内提供故障隔离位置。 服务总线管理消息存储的三个副本 (1 个主) 和2个辅助。 服务总线为数据和管理操作保留同步的所有三个副本。 如果主要副本发生故障，则会将其中一个辅助副本提升为主要副本，无需停机。 如果应用程序看到暂时性中断了服务总线，SDK 中的重试逻辑将自动重新连接到服务总线。 

使用可用性区域时，将在可用性区域中的数据中心之间复制元数据和数据 (消息) 。 

> [!NOTE]
> Azure 服务总线高级版的可用性区域支持仅适用于存在可用性区域的 [Azure 区域](../availability-zones/az-region.md)。

可以使用 Azure 门户仅在新的命名空间上启用可用性区域。 服务总线不支持迁移现有命名空间。 在命名空间上启用区域冗余之后，不能将其禁用。

![3][]

## <a name="private-endpoints"></a>专用终结点
本部分提供了有关使用具有专用终结点的命名空间的异地灾难恢复的更多注意事项。 若要总体了解有关将专用终结点与服务总线一起使用的信息，请参阅[将 Azure 服务总线与 Azure 专用链接集成](private-link-service.md)。

### <a name="new-pairings"></a>新建配对
如果尝试在具有专用终结点的主命名空间与没有专用终结点的辅助命名空间之间创建配对，则配对会失败。 仅当主命名空间和辅助命名空间都具有专用终结点时，配对才会成功。 建议在主命名空间和辅助命名空间以及创建了专用终结点的虚拟网络上使用相同的配置。 

> [!NOTE]
> 尝试将具有专用终结点的主命名空间与某个辅助命名空间配对时，验证过程仅检查辅助命名空间上是否存在专用终结点。 它不会检查在故障转移后终结点是否正常工作或是否将正常工作。 你需要负责确保在故障转移后，具有专用终结点的辅助命名空间能够按预期工作。
>
> 若要测试专用终结点配置是否相同，请从虚拟网络外部向辅助命名空间发送[获取队列](/rest/api/servicebus/stable/queues/get)请求，并验证是否收到来自服务的错误消息。

### <a name="existing-pairings"></a>现有配对
如果主命名空间和辅助命名空间之间已存在配对，则在主命名空间上创建专用终结点将失败。 若要解决此问题，请首先在辅助命名空间上创建专用终结点，然后为主命名空间创建专用终结点。

> [!NOTE]
> 尽管我们允许对辅助命名空间进行只读访问，但也允许对专用终结点配置进行更新。 

### <a name="recommended-configuration"></a>建议配置
在为应用程序和服务总线创建灾难恢复配置时，必须针对承载应用程序的主实例和辅助实例的虚拟网络，为主要和辅助服务总线命名空间创建专用终结点。

假设你有两个虚拟网络：VNET-1、VNET-2 以及以下主命名空间和辅助命名空间：ServiceBus-Namespace1-Primary、ServiceBus-Namespace2-Secondary。 需要执行以下步骤： 

- 在 ServiceBus-Namespace1-Primary 上创建两个专用终结点，这两个专用终结点使用 VNET-1 和 VNET-2 中的子网
- 在 ServiceBus-Namespace2-Secondary 上创建两个专用终结点，这两个专用终结点使用 VNET-1 和 VNET-2 中的相同子网 

![专用终结点和虚拟网络](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


此方法的优点是，可以在独立于服务总线命名空间的应用程序层上进行故障转移。 请考虑下列情形： 

**仅限应用程序的故障转移：** 此处，应用程序不会在 VNET-1 中，而是会移到 VNET-2 中。 由于主命名空间和辅助命名空间的 VNET-1 和 VNET 2 上都同时配置了这两个专用终结点，因此该应用程序将正常工作。 

**仅限服务总线命名空间的故障转移**：此处再次说明，因为在主命名空间和辅助命名空间的两个虚拟网络上都同时配置了这两个专用终结点，因此该应用程序将正常工作。 

> [!NOTE]
> 如需虚拟网络异地灾难恢复的相关指导，请参阅[虚拟网络 - 业务连续性](../virtual-network/virtual-network-disaster-recovery-guidance.md)。

## <a name="next-steps"></a>后续步骤

- 请参阅此处的异地灾难恢复 [REST API 参考](/rest/api/servicebus/stable/disasterrecoveryconfigs)。
- 在 GitHub 上运行异地灾难恢复[示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)。
- 请参阅异地灾难恢复[将消息发送到别名的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

若要了解有关服务总线消息传送的详细信息，请参阅以下文章：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
