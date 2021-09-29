---
title: 排查 Azure 事件中心中的 AMQP 错误 | Microsoft Docs
description: 提供使用 Azure 事件中心时可能收到的 AMQP 错误以及这些错误的原因的列表。
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 7e73c85f65a6ea9edef36ab810110e576b7ea0c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649390"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Azure 事件中心中的 AMQP 错误
本文提供了将 AMQP 与 Azure 事件中心一起使用时收到的一些错误。 这些错误都是服务的标准行为。 可以通过在连接/链路上发出发送/接收调用来避免这些错误，这会自动重新创建连接/链路。

## <a name="link-is-closed"></a>链路已关闭 
当 AMQP 连接和链接处于活动状态，但 30 分钟内没有使用该链接发出调用（例如发送或接收）时，将出现以下错误。 因此，该链路将关闭。 连接仍处于打开状态。

“AMQP:link: detach-forced: 由于发布服务器(link164614)中出现错误，因此中转站强制拆离了链接 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000'。 拆离源: AmqpMessagePublisher.IdleTimerExpired: 空闲超时: 00:30:00。 TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:主题:MyTopic, 时间戳: 2018/2/16 11:10:40 PM”

## <a name="connection-is-closed"></a>连接已关闭
由于没有活动（空闲）并且未在 5 分钟内创建新链接，当连接中的所有链接都已关闭时，AMQP 连接上会出现以下错误。

“Error{condition=amqp:connection:forced, description='连接处于非活动状态的时间超过了允许的 300000 毫秒，该连接被容器 'LinkTracker' 关闭。 TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, 时间戳:2019-03-06T17:32:00', info=null}”

## <a name="link-isnt-created"></a>未创建链接 
如果创建了新的 AMQP 连接，但在 AMQP 连接创建后的 1 分钟内未创建链接，则会出现此错误。

“Error{condition=amqp:connection:forced, description='连接处于非活动状态的时间超过了允许的 60000 毫秒，该连接被容器 'LinkTracker' 关闭。 TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, 时间戳:2019-03-06T18:41:51', info=null}”

## <a name="next-steps"></a>后续步骤
若要详细了解 AMQP，请参阅 [AMQP 1.0 协议指南](../service-bus-messaging/service-bus-amqp-protocol-guide.md)。
