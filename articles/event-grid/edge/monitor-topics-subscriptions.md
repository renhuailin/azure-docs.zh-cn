---
title: 监视主题和事件订阅 - Azure 事件网格 IoT Edge | Microsoft Docs
description: 监视主题和事件订阅
ms.date: 05/10/2021
ms.topic: article
ms.subservice: iot-edge
ms.openlocfilehash: 02fa0daa54d7b5a079ee1d8dff5a104ca23cc56e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367790"
---
# <a name="monitor-topics-and-event-subscriptions"></a>监视主题和事件订阅

Edge 事件网格以 [Prometheus 阐释格式](https://prometheus.io/docs/instrumenting/exposition_formats/)展示了许多主题和事件订阅的指标。 本文将介绍可用的指标以及如何启用它们。

## <a name="enable-metrics"></a>启用指标

将该模块配置为发送指标，具体方法是：在容器创建选项中将 `metrics__reporterType` 环境变量设置为 `prometheus`：

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

该模块的 `5888/metrics` 将提供适用于 http 的指标，`4438/metrics` 提供适用于 https 的指标。 例如，`http://<modulename>:5888/metrics?api-version=2019-01-01-preview` 面向 http。 目前，指标模块可以轮询终结点来收集指标，如以下[示例体系结构](https://github.com/veyalla/ehm)中所示。

## <a name="available-metrics"></a>可用度量值

主题和事件订阅都会发送指标，让用户能够深入了解事件传递和模块性能。

### <a name="topic-metrics"></a>主题指标

| 指标 | 说明 |
| ------ | ----------- |
| EventsReceived | 发布到主题的事件数
| UnmatchedEvents | 已发布到主题但与事件订阅不符和被删除的事件数
| SuccessRequests | 主题收到的入站发布请求数
| SystemErrorRequests | 由于内部系统错误导致入站发布请求失败的次数
| UserErrorRequests | 由于用户错误（例如 JSON 格式错误）导致入站发布请求失败的次数
| SuccessRequestLatencyMs | 发布请求响应的延迟时间（毫秒）


### <a name="event-subscription-metrics"></a>事件订阅指标

| 指标 | 说明 |
| ------ | ----------- |
| DeliverySuccessCounts | 成功传递到配置的终结点的事件数
| DeliveryFailureCounts | 未能传递到配置的终结点的事件数
| DeliverySuccessLatencyMs | 已成功传递事件的延迟时间（毫秒）
| DeliveryFailureLatencyMs | 传递失败事件的延迟时间（毫秒）
| SystemDelayForFirstAttemptMs | 第一次传递尝试前事件的系统延迟时间（毫秒）
| DeliveryAttemptsCount | 事件传递尝试次数 - 成功和失败
| ExpiredCounts | 已过期且未传递到配置的终结点的事件数