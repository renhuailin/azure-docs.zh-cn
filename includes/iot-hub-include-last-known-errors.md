---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 54f4835a904b897370cf9f075ae3c005b1114992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557324"
---
REST API 中的 [Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)（获取终结点运行状况）提供终结点的运行状况以及上一个已知错误，以确定终结点不正常的原因。 下表列出了最常见的错误。

|上一个已知错误|说明/发生时间|可能的缓解操作|
|-----|-----|-----|
|暂时性|出现暂时性错误，IoT 中心将重试该操作。|观察[路由资源日志](../articles/iot-hub/monitor-iot-hub-reference.md#routes)。|
|InternalError|将消息传递到终结点时出错。|这是一个内部异常，但也应查看[路由资源日志](../articles/iot-hub/monitor-iot-hub-reference.md#routes)。|
|未授权|IoT 中心无权向指定终结点发送消息。|验证该终结点的连接字符串是否为最新。 如果已更改，请考虑在 IoT 中心上更新。 如果终结点使用托管标识，请检查 IoT 中心主体是否对目标具有所需的权限。|
|已中止|将消息写入到终结点时，将中止 IoT 中心。|查看受影响的终结点的中止限制。 修改终结点的配置以纵向扩展（如果需要）。|
|超时|操作超时。|请重试操作即可。|
|未找到|目标资源不存在。|请确保目标资源存在。|
|未找到容器|存储容器不存在。|请确保存储容器存在。|
|已禁用容器|已禁用存储容器。|请确保存储容器已启用。|
|MaxMessageSizeExceeded|消息路由的消息大小限制为 256 Kb。要路由的消息大小超过了此限制。|请检查是否可以通过使用更少的应用程序属性或更少的消息扩充来减小消息大小。|
|PartitioningAndDuplicateDetectionNotSupported|服务总线可能未启用重复检测。|从服务总线禁用重复检测，或考虑使用不带重复检测的实体。|
|SessionfulEntityNotSupported|服务总线可能未启用会话。|从服务总线禁用会话，或考虑使用不带会话的实体。|
|NoMatchingSubscriptionsForMessage|没有要编写服务总线主题消息的订阅。|创建要将 IoT 中心消息路由到的订阅。|
|EndpointExternallyDisabled|终结点未处于活动状态，因此 IoT 中心可以向其发送消息。|启用终结点以使其恢复为活动状态。|
|DeviceMaximumQueueDepthExceeded|已达到服务总线大小限制。|请考虑从目标事件中心删除消息，以允许将新消息引入到事件中心。|