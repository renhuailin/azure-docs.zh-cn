---
title: 可用性区域
description: 媒体服务支持可用性区域提供故障隔离
services: media-services
author: johndeu
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: johndeu
ms.openlocfilehash: d50af7acadb17fe060ede90ad93896e465d3bad6
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746944"
---
# <a name="availability-zones"></a>可用性区域

Azure 媒体服务使用[可用性区域](../../availability-zones/az-overview.md)，在同一 Azure 区域内提供故障隔离位置。 默认情况下，媒体服务在[可用位置](../../availability-zones/az-region.md#azure-regions-with-availability-zones)是区域冗余的，无需对帐户进行额外的配置来启用此功能。  媒体服务将媒体数据存储在关联的存储帐户中。  这些存储帐户应作为区域冗余存储 (ZRS) 或异地区域冗余存储 (GZRS) 创建，以提供与媒体服务帐户相同的冗余级别。 若要详细了解如何在关联的存储帐户中配置副本，请参阅[如何更改存储帐户的复制方式](../../storage/common/redundancy-migration.md)。

## <a name="how-media-services-components-handle-an-availability-zone-fault"></a>媒体服务组件如何处理可用性区域错误

| 组件             | 可用性区域错误的行为 |
|-----------            |----------------------|
| 控制平面（Azure 资源管理） | 继续正常工作 |
| 密钥传送            | 继续正常工作 |
| 作业                    | 作业将重新安排在另一个可用性区域中。 处理时间将会存在延迟，因为发布外部测试版处理的作业将重新安排何时在可用性区域中重新开始 |
| 直播活动             | 流式处理和引入实时事件继续正常工作。 当前在可用性区域故障期间不支持对实时事件调“重置”。 建议先停止并重启实时事件，以替换“重置”操作。 如果实时事件在区域关闭事件期间转换为“正在运行”状态，客户可能会看到实时事件停滞在“正在启动”状态。 在这种情况下，建议启动新的实时事件，并清理区域恢复后的“正在启动”的实时事件。  |
| 流式处理终结点     | 继续正常工作 |


## <a name="high-availability-streaming-and-encoding-for-vod"></a>VOD 的高可用性流式处理和编码

可用性区域增加单个区域内的故障隔离。 若要为按需流式处理和编码提供高可用性，你可以使用其他 Azure 服务创建涵盖冗余、运行状况监视、负载均衡和数据备份与恢复等事项的体系结构。 [媒体服务点播视频的高可用性](architecture-high-availability-encoding-concept.md)一文中提供了一个此类体系结构。
本文和示例代码提供了一个解决方案，用于了解如何使用单个区域媒体服务帐户为你的 VOD 应用程序创建高可用性体系结构。

## <a name="media-services-support-for-availability-zones-by-region"></a>媒体服务按区域支持可用性区域

目前仅在某些 Azure 区域内支持可用性区域。 若要详细了解可用性区域区域支持，请参阅[带有可用性区域的 Azure 区域](../../availability-zones/az-region.md#azure-regions-with-availability-zones)

## <a name="further-reading"></a>进一步阅读

有关可用性区域的详细信息，请参阅 [Azure 中的区域和可用性区域](../../availability-zones/az-overview.md)。

若要详细了解高可用性编码和流式处理，请参阅[媒体服务点播视频的高可用性](architecture-high-availability-encoding-concept.md)。

若要了解如何正确配置存储帐户副本以支持可用性区域，请参阅[更改存储帐户的复制方式](../../storage/common/redundancy-migration.md)。
