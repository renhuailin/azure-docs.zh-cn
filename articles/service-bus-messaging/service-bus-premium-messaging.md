---
title: Azure 服务总线高级层和标准层
description: 本文介绍 Azure 服务总线的标准层和高级层。 比较这些层并提供技术差异。
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: f0cc6b6d7b9026d9be23e36a587b7ce667ba1652
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111811255"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>服务总线高级和标准消息传送层

服务总线消息传送（包括队列和主题等实体）将企业消息传送功能与丰富的云规模发布-订阅语义结合在一起。 对于许多复杂的云解决方案，服务总线消息传送用作通信中枢。

服务总线消息传送的 *高级* 层处理有关任务关键应用程序的规模、性能和可用性的常见客户请求。 高级层建议用于生产方案。 虽然功能集几乎完全相同，但这两个层的服务总线消息传送旨在满足不同的使用情形。

下表突出显示了部分高层差异。

| 高级 | 标准 |
| --- | --- |
| 高吞吐量 |可变吞吐量 |
| 可预测性能 |可变滞后时间 |
| 固定定价 |即用即付可变定价 |
| 增加和减少工作负荷的能力 |空值 |
| 消息大小最大为 1 MB。 此上限将来可能会提高。 有关服务的最新重要更新，请参阅 [Azure 上的消息传送博客](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog)。 |消息大小最大为 256 KB |

**服务总线高级消息传送** 在 CPU 和内存级别提供资源隔离，以便每个客户工作负荷以隔离方式运行。 此资源容器称为 *消息传送单元*。 每个高级命名空间至少会分配一个消息传送单元。 可以为每个服务总线高级命名空间购买 1、2、4、8 或 16 个消息传送单元。 单一工作负荷或实体可以跨多个消息传送单元，可以随意更改消息传送单元数。 这会为基于服务总线的解决方案提供可预测和稳定的性能。

此性能不仅更易于预测和实现，而且速度更快。 使用高级消息传送，峰值性能比使用标准层快得多。

> [!NOTE]
> 高级消息传送的批大小限制为 1 MB。

## <a name="premium-messaging-technical-differences"></a>高级消息传送技术差异

以下部分介绍高级和标准消息传送层之间的一些差异。

### <a name="partitioned-queues-and-topics"></a>分区队列和主题

高级消息传送不支持分区队列和主题。 有关分区的详细信息，请参阅 [分区的队列和主题](service-bus-partitioning.md)。

### <a name="express-entities"></a>快速实体

由于高级消息传送在一个隔离的运行时环境中运行，因此高级命名空间中不支持快速实体。 快速实体先将消息暂时保存在内存中，然后将其写入永久性存储。 如果你的代码在标准消息传递下运行，并且想要将其移植到高级层，请确保已禁用快速实体功能。

## <a name="premium-messaging-resource-usage"></a>高级消息传送资源使用情况
通常，对实体进行的任何操作都可能导致 CPU 和内存使用率增高。 下面是一些这样的操作： 

- 管理操作，例如对队列、主题和订阅的 CRUD（创建、检索、更新和删除）操作。
- 运行时操作（发送和接收消息）
- 监视操作和警报

但是，额外的 CPU 和内存使用量并不额外定价。 对于“高级消息传送”层，消息单元有一个单价。

由于以下原因，系统会跟踪并显示 CPU 和内存使用情况： 

- 让你透彻了解系统内部情况
- 让你了解所购资源的容量。
- 让你通过容量计划来决定是进行纵向扩展还是缩减。

## <a name="messaging-unit---how-many-are-needed"></a>消息传送单元 - 需要多少？

预配 Azure 服务总线高级命名空间时，必须指定分配的消息传送单元数。 这些消息传送单元是分配给命名空间的专用资源。

分配给服务总线高级命名空间的消息传送单元数可以 **动态调整**，将工作负荷的变化（增加或减少）因素考虑进去。

为体系结构确定消息传送单元数量时，需要考虑以下几个因素：

- 从分配给命名空间的 1 个或 2 个消息传送单元开始。
- 在命名空间的[资源使用情况指标](monitor-service-bus-reference.md#resource-usage-metrics)中研究 CPU 使用情况指标。
    - 如果 CPU 使用率*低于 20%_，则可_纵向缩减*分配给命名空间的消息传送单元数。
    - 如果 CPU 使用率*超过 70%_，则_增加*分配给命名空间的消息传送单元数将有益于应用程序。

要了解如何将服务总线命名空间配置为自动缩放（增加或减少消息传送单元），请参阅[自动更新消息传送单元](automate-update-messaging-units.md)。

> [!NOTE]
> **缩放** 分配给命名空间的资源的操作可以抢先进行，也可以被动进行。
>
>  * **抢先**：如果预期会有额外的工作负荷（考虑到季节性或趋势），可以在达到工作负荷限制之前向命名空间分配额外的消息传送单元。
>
>  * **被动**：如果通过研究资源使用情况指标确定了额外的工作负荷，则可将额外的资源分配给命名空间来应对不断增长的需求。
>
> 服务总线的费用按小时计。 进行纵向扩展时，只需按额外资源的使用小时数付费。
>

## <a name="get-started-with-premium-messaging"></a>高级消息传送入门

高级消息传送很容易入门，其操作过程类似于标准消息传送。 一开始时，请在 [Azure 门户](https://portal.azure.com)中[创建命名空间](service-bus-create-namespace-portal.md)。 确保在“定价层”下选择“高级”。 单击“查看完整的定价详细信息”以查看有关每个层级的详细信息。

![create-premium-namespace][create-premium-namespace]

也可以[使用 Azure 资源管理器模板创建高级命名空间](https://azure.microsoft.com/resources/templates/servicebus-pn-ar/)。


## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下链接：

- [自动更新消息传送单元](automate-update-messaging-units.md)。
- [Azure 服务总线高级消息传送简介（博客文章）](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure 服务总线高级消息传送简介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
