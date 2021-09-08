---
title: Azure 事件网格中的异地灾难恢复 | Microsoft Docs
description: 介绍 Azure 事件网格如何自动支持异地灾难恢复 (GeoDR)。
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 47899ba8fe4dd114d1fc3c0fb729589abf3ebd92
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829971"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 事件网格中的服务器端异地灾难恢复
事件网格支持对主题、域和事件订阅的元数据自动异地灾难恢复。 事件网格会自动将与事件相关的基础结构同步到配对区域。 如果整个 Azure 区域出现故障，事件将开始流向地理位置配对区域，而无需人工干预。 

请注意，事件数据不会复制到配对区域。 仅复制元数据。 但是，如果某个区域支持可用性区域，则事件数据将跨可用性区域进行复制。 

根据两项指标衡量灾难恢复：

- 恢复点目标 (RPO)：可以丢失数据的分钟数或小时数。
- 恢复时间目标 (RTO)：服务可以关闭的分钟数或小时数。

事件网格的自动故障转移针对元数据（主题、域、事件订阅）和数据（事件）具有不同的 RPO 和 RTO。 如果所需的规范不同于下述规范，仍可以[使用主题运行状况 API 实现你自己的客户端故障转移](custom-disaster-recovery.md)。

## <a name="recovery-point-objective-rpo"></a>恢复点目标 (RPO)
- **元数据 RPO**：0 分钟。 每当在事件网格中创建某个资源时，该资源会立即跨区域复制。 发生故障转移时，不会丢失任何元数据。
- **数据 RPO**：如果系统正常，并且在发生区域故障转移时能够跟上现有流量的进度，则事件 RPO 大约为 5 分钟。

## <a name="recovery-time-objective-rto"></a>恢复时间目标 (RTO)
- **元数据 RTO**：事件网格在 60 分钟内即会开始接受对主题和订阅发出的创建/更新/删除调用，不过，此间隔通常要短得多。
- **数据 RTO**：与元数据类似，其发生速度通常要快得多，不过，在发生区域性故障转移后，事件网格在 60 分钟内即会开始接受新流量。

> [!IMPORTANT]
> - 服务器端灾难恢复没有服务级别协议 (SLA)。 如果配对区域没有可用于承担额外流量的额外容量，则事件网格无法启动故障转移。 服务级别目标只是“尽力而为”。 
> - 事件网格中元数据 GeoDR 的成本为：$0。


## <a name="next-steps"></a>后续步骤
若要实现自己的客户端故障转移逻辑，请参阅 [# 在事件网格为自定义主题构建自己的灾难恢复方案](custom-disaster-recovery.md)
