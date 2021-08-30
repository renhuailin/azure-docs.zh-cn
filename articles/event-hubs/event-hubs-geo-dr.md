---
title: 异地灾难恢复 - Azure 事件中心| Microsoft Docs
description: 如何使用地理区域进行故障转移并在 Azure 事件中心执行灾难恢复
ms.topic: article
ms.date: 06/21/2021
ms.openlocfilehash: 42057f88d76fb0822207ecaf0ece340101c6ec6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733903"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure 事件中心 - 异地灾难恢复 

针对数据处理资源灾难性中断的复原能力是许多企业的一项要求，某些情况下甚至是行业法规要求。 

Azure 事件中心已将各计算机甚至整个机架的灾难性故障风险分散到数据中心内跨多个故障域的群集中，并且实现了透明的故障检测和故障转移机制，使服务将继续在有保证的服务级别内运行，而通常不会在此类故障发生时出现明显中断。 如果创建的事件中心命名空间包含启用[可用性区域](../availability-zones/az-overview.md)的选项，则中断风险会进一步分散到三个物理上分离的设备，并且该服务有足够的容量预留，可立即应对整个设施的全部灾难性损失。 

全面启用后支持高可用性区域的 Azure 事件中心群集，可敏捷应对严重的硬件故障，乃至整个数据中心设施的灾难性损失。 然而，仍有可能出现严重的情况，造成广泛的物理破坏，即使采取这些措施也无法充分防范。 

事件中心异地灾难恢复功能旨在让用户能够更轻松地从如此大规模的灾难中恢复，并永久放弃发生故障的 Azure 区域，且无需更改应用程序配置。 放弃 Azure 区域通常会涉及到几项服务，此功能主要是为了帮助保持复合应用程序配置的完整性。  

异地灾难恢复功能可确保命名空间（事件中心、使用者组和设置）的整个配置在配对后能够从主命名空间连续复制到辅助命名空间，并允许你随时启动从主命名空间到辅助命名空间的一次性故障转移。 故障转移移动会将命名空间的所选别名重新指向辅助命名空间，然后中断配对。 故障转移几乎是启动后立即发生的。 

> [!IMPORTANT]
> - 该功能可使用相同的配置即时实现操作连续性，但不会复制事件数据。 除非灾难导致所有区域丢失，否则在故障转移后可恢复主事件中心内保留的事件数据，并且在恢复访问后，还可以从中获取历史事件。 要在主动/主动配置中复制事件数据和操作相应的命名空间以应对中断和灾难，请不要依赖此异地灾难恢复功能集，而要按照[复制指南](event-hubs-federation-overview.md)进行操作。  
> - 在主命名空间中实体的 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 分配不会复制到辅助命名空间。 在辅助命名空间中手动创建角色分配，以保护对它们的访问。 

## <a name="outages-and-disasters"></a>中断和灾难

请务必注意“中断”和“灾难”的区别。 **中断** 是指 Azure 事件中心暂时不可用，可能会影响服务的某些组件，如消息存储，甚至是整个数据中心。 但是，问题解决后，事件中心将恢复可用。 通常情况下，中断不会导致消息或其他数据丢失。 例如，数据中心的电源故障可能会导致此类中断。 某些中断由于暂时性故障或网络问题只是短时间丢失连接。 

根据定义，*灾难* 是指永久或长期丢失事件中心群集、Azure 区域或数据中心。 该区域或数据中心不一定会恢复可用，或可能停用数小时或数天。 例如，火灾、洪灾或地震等可能导致此类灾难。 永久性灾难可能会导致一些消息、事件或其他数据丢失。 不过，在大多数情况下，都不应该会有数据丢失，并且在数据中心备份后，便可以恢复消息。

Azure 事件中心的异地灾难恢复功能是一种灾难恢复解决方案。 本文中所述的概念和工作流适用于灾难方案，而不适用于暂时或临时中断。 有关 Microsoft Azure 中的灾难恢复的详细讨论，请参阅[此文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。

## <a name="basic-concepts-and-terms"></a>基本概念和术语

灾难恢复功能可实现元数据灾难恢复，并且依赖于主要和次要灾难恢复命名空间。 

异地灾难恢复功能仅适用于[标准、高级和专用 SKU](https://azure.microsoft.com/pricing/details/event-hubs/)。 不需要对连接字符串进行任何更改，因为连接是通过别名建立的。

本文涉及以下术语：

-  *别名*：所设置的灾难恢复配置的名称。 别名提供单个稳定的完全限定域名 (FQDN) 连接字符串。 应用程序使用此别名连接字符串连接到命名空间。 

-  *主要/次要命名空间*：别名所对应的命名空间。 主要命名空间是“主动”的，并且会接收消息（可以是现有的命名空间，也可以是新的命名空间）。 次要命名空间是“被动”的，不接收消息。 这两者之间的元数据保持同步，因此这两者可以无缝接受消息，而不必更改任何应用程序代码或连接字符串。 若要确保只有主动命名空间接收消息，必须使用别名。
-  *元数据*：事件中心和使用者组等实体以及其与命名空间关联的服务的属性。 仅自动复制实体及其设置。 不会复制消息和事件。 
-  *故障转移*：激活辅助命名空间的过程。

## <a name="supported-namespace-pairs"></a>支持的命名空间对
支持以下主要和辅助命名空间的组合：  

| 主命名空间层 | 允许辅助命名空间层 |
| ----------------- | -------------------- |
| Standard | 标准、专用 | 
| 高级 | 高级 | 
| 专用 | 专用 | 

> [!NOTE]
> 不能将位于同一专用群集中的命名空间进行配对。 可以将不同群集中的命名空间进行配对。 

## <a name="setup-and-failover-flow"></a>设置和故障转移流程

以下部分概述了故障转移流程，并说明了如何设置初始故障转移。 

:::image type="content" source="./media/event-hubs-geo-dr/geo1.png" alt-text="显示故障转移过程概述的图像":::


### <a name="setup"></a>设置

首先，创建一个主要命名空间或使用现有的主要命名空间，并创建一个新的次要命名空间，然后将两者配对。 此配对提供可用于连接的别名。 由于使用别名，因此，无需更改连接字符串。 只有新的命名空间可以添加到故障转移配对。 

1. 创建主命名空间。
1. 在另一区域中创建辅助命名空间。 此步骤是可选的。 可以在下一步中创建配对时创建辅助命名空间。 
1. 在 Azure 门户中，导航到你的主命名空间。
1. 在左侧菜单中选择“异地恢复”，然后在工具栏中选择“启动配对”。 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="从主命名空间启动配对":::    
1. 在“启动配对”页上执行以下步骤：
    1. 选择现有的辅助命名空间，或在不同区域中创建一个。 在此示例中，我们选择了一个现有命名空间。  
    1. 对于“别名”，请输入异地灾难恢复配对的别名。 
    1. 然后选择“创建”。 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="选择辅助命名空间":::        
1. 你应会看到“异地灾难恢复别名”页面。 此外，还可以通过在左侧菜单中选择“异地恢复”，从主命名空间导航到此页面。

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="“异地灾难恢复别名”页":::    
1. 在“异地灾难恢复别名”页上，在左侧菜单上选择“共享访问策略”来访问该别名的主连接字符串。 使用此连接字符串，而不是直接使用主/辅助命名空间的连接字符串。 
1. 在此“概述”页上，可以执行以下操作： 
    1. 中断主命名空间和辅助命名空间之间的配对。 在工具栏上选择“中断配对”。 
    1. 手动故障转移到辅助命名空间。 在工具栏上选择“故障转移”。 
    
        > [!WARNING]
        > 故障转移将激活辅助命名空间，并从异地灾难恢复配对中删除主命名空间。 创建另一个命名空间，以创建新的异地灾难恢复配对。 

最后，应添加一些监视功能，以检测是否有必要进行故障转移。 在大多数情况下，服务是一个大型生态系统的一部分，因此很少会发生自动故障转移，因为通常故障转移必须在与剩余子系统或基础结构保持同步的情况下进行。

### <a name="example"></a>示例

在此方案的一个示例中，请考虑发出消息或事件的销售点 (POS) 解决方案。 事件中心将这些事件传递给某个映射或重格式化解决方案，该解决方案然后将映射的数据转发到另一个系统进一步处理。 此时，所有这些系统可能托管在同一 Azure 区域中。 有关何时进行故障转移以及对哪些部分进行故障转移，则取决于基础结构中的数据流。 

可以使用监视系统或定制监视解决方案自动执行故障转移。 但是，这种自动执行需要额外的规划和工作，它超出了本文的讨论范围。

### <a name="failover-flow"></a>故障转移流程

如果启动故障转移，则需要两个步骤：

1. 如果又出现其他中断，则需要能够再次进行故障转移。 因此，请设置另一个被动命名空间，并更新配对。 

2. 一旦以前的主要命名空间恢复可用，请从该命名空间拉取消息。 在此之后，请使用该命名空间在异地恢复设置之外进行常规消息传送，或删除旧的主要命名空间。

> [!NOTE]
> 仅支持失败转发语义。 在此方案中，先进行故障转移，然后与新的命名空间重新配对。 不支持故障回复；例如，在 SQL 群集中不支持这样做。 

:::image type="content" source="./media/event-hubs-geo-dr/geo2.png" alt-text="显示故障转移流的图像":::

## <a name="management"></a>管理

如果出了错，例如在初始设置过程中将错误的区域配对，则随时可以中断这两个命名空间的配对。 如果想要使用配对命名空间作为常规命名空间，请删除别名。

## <a name="considerations"></a>注意事项

请谨记以下注意事项：

1. 按照设计，事件中心异地灾难恢复不会复制数据，因此，无法在辅助事件中心重复使用主事件中心的旧偏移值。 建议通过以下方法之一重启事件接收器：

   - EventPosition.FromStart() - 如果想要读取辅助事件中心上的所有数据。
   - EventPosition.FromStart - 如果想要读取自连接到辅助事件中心开始算起的所有新数据。
   - EventPosition.FromEnqueuedTime(dateTime) - 如果想要读取自给定的日期和时间开始算起辅助事件中心收到的所有数据。

2. 在故障转移规划中，还应考虑时间因素。 例如，如果失去连接的时间超过 15 到 20 分钟，你可能会决定启动故障转移。 
 
3. 未复制任何数据这一事实意味着，未复制当前的活动会话。 此外，重复检测和计划消息可能无法正常工作。 新的会话、计划消息和新的重复项可以正常工作。 

4. 故障转移复杂的分布式基础结构应至少[演练](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)一次。 

5. 同步实体可能需要一些时间，每分钟大约 50-100 个实体。

## <a name="availability-zones"></a>可用性区域 
事件中心支持[可用性区域](../availability-zones/az-overview.md)，在 Azure 区域内提供故障隔离位置。 可用性区域支持仅适用于[具有可用性区域的 Azure 区域](../availability-zones/az-region.md#azure-regions-with-availability-zones)。 在可用性区域的数据中心中同时复制元数据和数据（事件）。 

创建命名空间时，在选择具有可用性区域的区域时，将看到以下突出显示的消息。 

:::image type="content" source="./media/event-hubs-geo-dr/eh-az.png" alt-text="显示具有可用性区域的区域的“创建命名空间”页的图像":::

## <a name="private-endpoints"></a>专用终结点
本部分针对使用专用终结点的命名空间执行异地灾难恢复，提供了更多注意事项。 若要概括性了解如何将专用终结点与事件中心一起使用，请参阅[配置专用终结点](private-link-service.md)。

### <a name="new-pairings"></a>新建配对
如果尝试在具有专用终结点的主命名空间与没有专用终结点的辅助命名空间之间创建配对，则配对会失败。 仅当主命名空间和辅助命名空间都具有专用终结点时，配对才会成功。 建议在主命名空间和辅助命名空间以及创建了专用终结点的虚拟网络上使用相同的配置。  

> [!NOTE]
> 尝试将具有专用终结点的主命名空间与某个辅助命名空间配对时，验证过程仅检查辅助命名空间上是否存在专用终结点。 它不会检查在故障转移后终结点是否正常工作或是否将正常工作。 你需要负责确保在故障转移后，具有专用终结点的辅助命名空间能够按预期工作。
>
> 若要测试主命名空间和辅助命名空间上的专用终结点配置是否相同，请发送一个从虚拟网络外部读取辅助命名空间的请求（例如：[获取事件中心](/rest/api/eventhub/get-event-hub)），并验证是否收到来自服务的错误消息。

### <a name="existing-pairings"></a>现有配对
如果主命名空间和辅助命名空间之间已存在配对，则在主命名空间上创建专用终结点将失败。 若要解决此问题，请首先在辅助命名空间上创建专用终结点，然后为主命名空间创建专用终结点。

> [!NOTE]
> 尽管我们允许对辅助命名空间进行只读访问，但也允许对专用终结点配置进行更新。 

### <a name="recommended-configuration"></a>建议的配置
在为应用程序和事件中心命名空间创建灾难恢复配置时，必须针对同时承载应用程序的主实例和辅助实例的虚拟网络，同时为主要和辅助事件中心命名空间创建专用终结点。 

假设你有两个虚拟网络：VNET-1、VNET-2 以及以下主命名空间和辅助命名空间：EventHubs-Namespace1-Primary、EventHubs-Namespace2-Secondary。 需要执行以下步骤： 

- 在 EventHubs-Namespace1-Primary 上创建两个专用终结点，这两个专用终结点使用 VNET-1 和 VNET-2 中的子网
- 在 EventHubs-Namespace2-Secondary 上创建两个专用终结点，这两个专用终结点使用 VNET-1 和 VNET-2 中的相同子网 

![专用终结点和虚拟网络](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

此方法的优点是，故障转移可以在独立于事件中心命名空间的应用程序层上发生。 请考虑下列情形： 

**仅限应用程序的故障转移：** 此处，应用程序不会在 VNET-1 中，而是会移到 VNET-2 中。 由于主命名空间和辅助命名空间的 VNET-1 和 VNET 2 上都同时配置了这两个专用终结点，因此该应用程序将正常工作。 

**仅限事件中心命名空间的故障转移**：此处再次说明，因为在主命名空间和辅助命名空间的两个虚拟网络上都同时配置了这两个专用终结点，因此该应用程序将正常工作。 

> [!NOTE]
> 如需虚拟网络异地灾难恢复的相关指导，请参阅[虚拟网络 - 业务连续性](../virtual-network/virtual-network-disaster-recovery-guidance.md)。

## <a name="role-based-access-control"></a>基于角色的访问控制
在主命名空间中实体的 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 分配不会复制到辅助命名空间。 在辅助命名空间中手动创建角色分配，以保护对它们的访问。
 
## <a name="next-steps"></a>后续步骤
查看以下示例或参考文档。 
- [.NET GeoDR 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Java GeoDR 示例](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [.NET - Azure.Messaging.EventHubs 示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [.NET - Microsoft.Azure.EventHubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java - azure-messaging-eventhubs 示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Java - azure-eventhubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Python 示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [JavaScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/javascript)
- [TypeScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/event-hubs/samples/v5/typescript)
- [REST API 参考](/rest/api/eventhub/)

[2]: ./media/event-hubs-geo-dr/geo2.png
