---
title: Azure IoT 中心的高可用性和灾难恢复 | Microsoft Docs
description: 介绍了Azure 和 IoT 中心功能，这些功能有助于构建带灾难恢复功能的 Azure IoT 高可用性解决方案。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 6b7fea611eeb3701bc624be8354b4639966dfaa6
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860342"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT 中心高可用性和灾难恢复

作为实施弹性 IoT 解决方案的第一步，架构师、开发人员和企业主必须定义要构建的解决方案的运行时间目标。 可以主要根据每个方案的具体业务目标定义这些目标。 针对这种环境，[Azure 业务连续性技术指南](/azure/architecture/framework/resiliency/app-design)一文介绍了一个常规框架来帮助你思考业务连续性和灾难恢复。 [Azure 应用程序的灾难恢复和高可用性](/azure/architecture/reliability/disaster-recovery)一文针对 Azure 应用程序的高可用性 (HA) 和灾难恢复 (DR) 实现策略提供了体系结构指导。

本文介绍 IoT 中心服务专门提供的 HA 和 DR 功能。 从广义上讲，本文讨论的领域包括：

- 区域内部 HA
- 跨区域 DR
- 实现跨区域 HA

根据你为 IoT 解决方案定义的运行时间目标，应确定下面所述的哪些选项最适合业务目标。 将其中的任何 HA/DR 备选方案整合到 IoT 解决方案需要仔细权衡以下方面的利弊：

- 所需的复原能力级别 
- 实施和维护的复杂性
- COGS 影响

## <a name="intra-region-ha"></a>区域内部 HA

IoT 中心服务通过在几乎所有服务层中实现冗余来提供区域内部 HA。 [IoT 中心服务发布的 SLA](https://azure.microsoft.com/support/legal/sla/iot-hub) 是利用这些冗余实现的。 IoT 解决方案开发人员无需完成任何额外工作就能利用这些 HA 功能。 尽管 IoT 中心提供相当高的运行时间保证，但与任何分布式计算平台一样，暂时性的故障仍有可能出现。 如果刚刚开始将解决方案从本地解决方案迁移到云，则需要将重心从优化“平均故障时间”改为优化“平均恢复时间”。 换而言之，以混合模式操作云时，暂时性故障被视为正常。 必须在与云应用程序交互的组件中内置[重试策略](iot-hub-reliability-features-in-sdks.md)，以处理暂时性故障。

> [!NOTE]
> 某些 Azure 服务还通过与[可用性区域 (AZ)](../availability-zones/az-overview.md) 集成，在区域中提供附加的可用性层。 IoT 中心服务目前不支持 AZ。

## <a name="cross-region-dr"></a>跨区域 DR

在极少见的情况下，电源故障或其他涉及到实物资产的故障会导致数据中心遇到长时间的服务中断。 此类事件非常罕见，在此期间，上述区域内部 HA 不一定总能发挥作用。 IoT 中心提供多种解决方案，用于在发生此类长时间服务中断后进行恢复。 

出现此类情况时为客户提供的恢复选项包括 [Microsoft 发起的故障转移](#microsoft-initiated-failover)和[手动故障转移](#manual-failover)。 两者之间的根本差别在于，前者由 Microsoft 发起，后者由用户发起。 此外，与 Microsoft 发起的故障转移选项相比，手动故障转移提供更低的恢复时间目标 (RTO)。 以下部分讨论了每个选项提供的具体 RTO。 执行上述任一选项从主要区域故障转移 IoT 中心时，中心将在对应的 [Azure 异地配对区域](../best-practices-availability-paired-regions.md)完全正常运行。

这两个故障转移选项提供以下恢复点目标 (RPO)：

| 数据类型 | 恢复点目标 (RPO) |
| --- | --- |
| 标识注册表 |丢失 0-5 分钟的数据 |
| 设备孪生数据 |丢失 0-5 分钟的数据 |
| 云到设备的消息<sup>1</sup> |丢失 0-5 分钟的数据 |
| 父作业<sup>1</sup>和设备作业 |丢失 0-5 分钟的数据 |
| 设备到云的消息 |所有未读的消息都丢失 |
| 操作监视消息 |所有未读的消息都丢失 |
| 云到设备的反馈消息 |所有未读的消息都丢失 |

<sup>1</sup>手动故障转移期间无法恢复云到设备的消息和父作业。

完成 IoT 中心的故障转移操作后，来自设备和后端应用程序的所有操作预期可继续进行，无需人工干预。 这意味着，设备到云的消息应会继续正常工作，并且整个设备注册表会保持不变。 可以借助前面配置的相同订阅来使用通过事件网格发出的事件，前提是这些事件网格订阅仍然可用。 自定义终结点无需进行其他处理。

> [!CAUTION]
> - 故障转移后，IoT 中心内置事件终结点的事件中心兼容名称和终结点会发生变化。 使用事件中心客户端或事件处理程序主机从内置终结点接收遥测消息时，应[使用 IoT 中心连接字符串](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint)建立连接。 这可以确保在故障转移后，后端应用程序可继续工作，而无需人工干预。 如果在应用程序中直接使用事件中心兼容的名称和终结点，则需在故障转移后[提取新的事件中心兼容终结点](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint)，这样才能继续操作。 有关详细信息，请参阅[手动故障转移和事件中心](#manual-failover-and-event-hub)。
>
> - 如果使用 Azure Functions 或 Azure 流分析来连接内置事件终结点，则可能需要执行重启操作。 这是因为在故障转移过程中，上一个偏移量不再有效。
>
> - 路由到存储时，我们建议列出 blob 或文件，然后循环访问它们，以确保在不进行分区的情况下读取所有 blob 或文件。 在 Microsoft 发起的故障转移或手动故障转移期间，分区范围可能发生变化。 可以使用 [List Blobs API](/rest/api/storageservices/list-blobs) 枚举 blob 列表，或使用 [List ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/filesystem/list) 枚举文件列表。 若要了解详细信息，请查看 [Azure 存储作为路由终结点](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)。

## <a name="microsoft-initiated-failover"></a>Microsoft 发起的故障转移

在少数情况下，Microsoft 会执行 Microsoft 发起的故障转移，以将所有 IoT 中心从受影响的区域故障转移到对应的异地配对区域。 该过程是默认选项（用户无法选择禁用），且无需用户的干预。 Microsoft 有权决定何时执行此选项。 在故障转移用户的中心之前，此机制不要求用户许可。 Microsoft 发起的故障转移的恢复时间目标 (RTO) 为 2 到 26 小时。 

RTO 较高的原因是，Microsoft 必须代表该区域中所有受影响的客户执行故障转移操作。 如果运行的某个较不关键 IoT 解决方案可以承受大约一天时间的停机，则可以依赖此选项来满足 IoT 解决方案的总体灾难恢复目标。 “恢复时间”部分介绍了触发此过程后，使运行时操作完全正常所需的总时间。

## <a name="manual-failover"></a>手动故障转移

如果 Microsoft 发起的故障转移提供的 RTO 无法满足企业的正常运行时间目标，请考虑使用手动故障转移来自行触发故障转移过程。 此选项的 RTO 大致在 10 分钟到几个小时。 目前，RTO 取决于针对故障转移的 IoT 中心实例注册的设备数。 托管大约 100,000 台设备的中心的 RTO 大致是 15 分钟。 “恢复时间”部分介绍了触发此过程后，使运行时操作完全正常所需的总时间。

不管主要区域是否遇到停机，手动故障转移选项始终可用。 因此，用户可能会使用此选项来执行计划内故障转移。 计划内故障转移的一个示例用途是执行定期的故障转移演练。 需要注意的是，计划内故障转移操作会导致中心在此选项的 RTO 定义的时间段内停机，同时会导致数据丢失（由上面的 RPO 表定义）。 可以考虑设置一个测试 IoT 中心实例来定期执行计划内故障转移选项，以便在发生实际灾难时，自信地让端到端解决方案正常运行。

对于 2017 年 5 月 18 日之后创建的 IoT 中心，用户无需额外付费便可使用手动故障转移功能

有关分步说明，请参阅[教程：为 IoT 中心执行手动故障转移](tutorial-manual-failover.md)

## <a name="manual-failover-and-event-hub"></a>手动故障转移和事件中心

如前面“警告”部分所述，手动故障转移后，IoT 中心内置事件终结点的与事件中心兼容的名称和终结点会更改。 这是因为事件中心客户端无法查看 IoT 中心事件。 Functions 和 Azure 流分析等其他基于云的客户端也是如此。 若要检索终结点和名称，可以使用 Azure 门户或利用包含的示例。

### <a name="use-the-portal"></a>使用门户

有关使用门户检索与事件中心兼容的终结点和名称的信息，请参阅[从内置终结点中读取](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint)。

### <a name="use-the-included-sample"></a>使用包含的示例

若要使用 IoT 中心连接字符串重新捕获与事件中心兼容的终结点，请使用位于 [https://github.com/Azure/azure-sdk-for-net/tree/main/samples/iothub-connect-to-eventhubs](https://github.com/Azure/azure-sdk-for-net/tree/main/samples/iothub-connect-to-eventhubs) 的示例，该示例演示如何使用 IoT 中心连接字符串重新捕获与事件中心兼容的终结点。 该代码示例使用连接字符串获取新的事件中心终结点，然后重新建立连接。 必须安装 Visual Studio。

### <a name="running-test-drills"></a>运行测试演练

不应针对生产环境中使用的 IoT 中心执行测试演练。

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>不要使用手动故障转移将 IoT 中心迁移到另一个区域

不应使用手动故障转移作为在 Azure 异地配对区域之间永久迁移中心的机制。 否则，会增大从驻留在旧主要区域中的设备针对 IoT 中心执行的操作的延迟。

## <a name="failback"></a>故障回复

再次触发故障转移操作即可故障回复到旧的主要区域。 如果执行了原始故障转移操作，以便在原始主要区域发生长时间服务中断后进行恢复，我们建议在原始位置从服务中断恢复后，将中心故障回复到原始位置。

> [!IMPORTANT]
> - 每天只允许用户执行 2 次成功的故障转移和 2 次成功的故障回复操作。
>
> - 不允许背靠背（连续）的故障转移/故障回复操作。 必须在这些操作之间等待 1 小时。

## <a name="time-to-recover"></a>恢复时间

尽管 IoT 中心实例的 FQDN（因此也包括连接字符串）在故障转移后保持不变，但基础 IP 地址会发生变化。 因此，可使用以下函数来表示触发故障转移过程后，针对 IoT 中心实例执行的运行时操作完全正常所需的总时间。

恢复时间 = RTO [手动故障转移为 10 分钟到 2 小时 | Microsoft 发起的故障转移为 2 到 26 小时] + DNS 传播延迟 + 客户端应用程序刷新任何缓存 IoT 中心 IP 地址花费的时间。

> [!IMPORTANT]
> IoT SDK 不会缓存 IoT 中心的 IP 地址。 我们建议，与 SDK 交互的用户代码不应缓存 IoT 中心的 IP 地址。

## <a name="achieve-cross-region-ha"></a>实现跨区域 HA

如果 Microsoft 发起的故障转移或手动故障转移选项提供的 RTO 无法满足企业的运行时间目标，应考虑在每个设备上实施自动跨区域故障转移机制。
本文不讨论 IoT 解决方案中部署拓扑的完整处理方式。 本文讨论了用于实现高可用性和灾难恢复的 *区域故障转移* 部署模型。

在区域故障转移模型中，解决方案后端主要在一个数据中心位置运行。 辅助 IoT 中心和后端部署在另一个数据中心位置。 如果主要区域中的 IoT 中心遭遇服务中断或者从设备到主要区域的网络连接中断，设备将使用辅助服务终结点。 可以通过实现跨区域故障转移模型而不是保留在单个区域中来提高解决方案可用性。 

概括而言，为了实现 IoT 中心的区域故障转移模型，需要执行以下步骤：

* **辅助 IoT 中心和设备路由逻辑**：如果主要区域的服务中断，设备必须开始连接到次要区域。 由于大多数服务状态感知的性质，解决方案管理员通常触发区域间的故障转移过程。 若要实现新终结点与设备间的通信并掌控此过程，最好让其定期检查  服务中是否存在当前活动的终结点。 该监护服务可以是 Web 应用程序，可使用 DNS 重定向技术将它复制并使其可访问（例如，使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)）。

   > [!NOTE]
   > IoT 中心服务不是 Azure 流量管理器中受支持的终结点类型。 我们建议在提议的监护服务中实现终结点运行状况探测 API，使之与 Azure 流量管理器集成。

* **标识注册表复制**：若要进行使用，次要 IoT 中心必须包含所有可连接到解决方案的设备标识。 解决方案应该保留设备标识的异地复制备份，并在切换设备的活动终结点之前将其上传到辅助 IoT 中心。 IoT 中心的设备标识导出功能在此情景中很有用。 有关详细信息，请参阅 [IoT 中心开发人员指南 - 标识注册表](iot-hub-devguide-identity-registry.md)。

* **合并逻辑**：当主要区域再次可供使用时，所有在辅助站点中创建的状态和数据都必须迁移回到主要区域。 此状态和数据主要与设备标识和应用程序元数据相关，必须与主要 IoT 中心以及主要区域中的任何其他应用程序特定存储合并。 

可使用幂等操作简化此步骤。 幂等操作可最大程度降低事件的最终一致分布以及事件的重复项/失序传送所造成的副作用。 此外，应用程序逻辑应该设计为能够容许潜在的不一致或稍微过期的状态。 之所以发生此情况是因为系统需要额外的时间来根据恢复点目标 (RPO) 修复自身。

## <a name="choose-the-right-hadr-option"></a>选择适当的 HA/DR 选项

下面汇总了本文所述的 HA/DR 选项，可将其用作参考框架来选择适用于解决方案的选项。

| HA/DR 选项 | RTO | RPO | 是否需要人工干预？ | 实施复杂性 | 附加成本影响|
| --- | --- | --- | --- | --- | --- |
| Microsoft 发起的故障转移 |2 - 26 小时|参考上面的 RPO 表|否|无|无|
| 手动故障转移 |10 分钟 - 2 小时|参考上面的 RPO 表|是|极低。 只需从门户触发此操作。|无|
| 跨区域 HA |小于 1 分钟|取决于自定义 HA 解决方案的复制频率|否|高|超过 1 个 IoT 中心的 1 倍|

## <a name="next-steps"></a>后续步骤

* [Azure IoT 中心是什么？](about-iot-hub.md)
* [IoT 中心入门（快速入门）](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)
* [教程：为 IoT 中心执行手动故障转移](tutorial-manual-failover.md)
