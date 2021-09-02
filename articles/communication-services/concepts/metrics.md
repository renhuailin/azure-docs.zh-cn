---
title: Azure 通信服务的指标定义
titleSuffix: An Azure Communication Services concept document
description: 本文档介绍 Azure 门户中可用指标的定义。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 959ae65e5faf7830a780726dc1c29f5d9bfb880c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255379"
---
# <a name="metrics-overview"></a>指标概述

Azure 通信服务目前提供聊天和短信指标。 你可使用 [Azure 指标资源管理器](../../azure-monitor/essentials/metrics-getting-started.md)绘制自己的图表，调查指标值中的异常，并通过使用聊天和短信请求发出的指标数据来了解 API 流量。

## <a name="where-to-find-metrics"></a>查找指标的位置

Azure 通信服务中的聊天和短信服务发出 API 请求的指标。 可以在通信服务资源下的“指标”边栏选项卡中找到这些指标。 还可以使用通信服务资源下的工作簿边栏选项卡创建永久仪表板。

## <a name="metric-definitions"></a>指标定义

通信服务指标中提供了两种类型的请求：“聊天 API 请求”和“短信API 请求” 。

聊天和短信 API 请求指标都包含可用于筛选指标数据的三个维度。 这些维度可以通过 `Count` 聚合类型聚合在一起，并支持所有标准的 Azure 聚合时序，包括 `Sum`、`Average`、`Min` 和 `Max`。

有关支持的聚合类型和时序聚合的详细信息，请参阅 [Azure 指标资源管理器的高级功能](../../azure-monitor/essentials/metrics-charts.md#aggregation)

- **操作** - 可在 ACS 聊天网关上调用的所有操作或路由。
- **状态代码** - 请求后发送的状态代码响应。
- **StatusSubClass** - 响应后发送的状态代码系列。 


### <a name="chat-api-request-metric-operations"></a>聊天 API 请求指标操作

以下操作适用于聊天 API 请求指标：

| 操作/路由    | 说明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | 按消息 ID 获取消息。 |
| ListChatMessages     | 从线程获取聊天消息列表。 |
| SendChatMessage      | 向线程发送聊天消息。 |
| UpdateChatMessage    | 更新聊天消息。 |
| DeleteChatMessage    | 删除聊天消息。 |
| GetChatThread        | 获取聊天线程。 |
| ListChatThreads      | 获取用户的聊天线程列表。 |
| UpdateChatThread     | 更新聊天线程的属性。 |
| CreateChatThread     | 创建聊天线程。 |
| DeleteChatThread     | 删除线程。 |
| GetReadReceipts      | 获取线程的读取回执。 |
| SendReadReceipt      | 代表用户向线程发送一个读取回执事件。 |
| SendTypingIndicator           | 代表用户将键入事件发送到线程。 |
| ListChatThreadParticipants    | 获取线程的成员。 |
| AddChatThreadParticipants     | 向线程添加线程成员。 如果成员已存在，则不会发生任何更改。 |
| RemoveChatThreadParticipant   | 从线程中删除成员。 |

:::image type="content" source="./media/chat-metric.png" alt-text="聊天 API 请求指标。":::

如果对未识别的操作发出请求，会收到“错误路由”值响应。

### <a name="sms-api-requests"></a>短信 API 请求

以下操作适用于短信 API 请求指标：

| 操作/路由    | 说明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | 发送短信。 |
| SMSDeliveryReportsReceived     | 获取短信发送报告 |
| SMSMessagesReceived      | 获取短信消息。 |


:::image type="content" source="./media/sms-metric.png" alt-text="短信 API 请求指标。":::

### <a name="authentication-api-requests"></a>身份验证 API 请求

以下操作适用于身份验证 API 请求指标：

| 操作/路由    | 说明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | 创建表示单个用户的标识。 |
| DeleteIdentity       | 删除标识。 |
| CreateToken          | 创建访问令牌。 |
| RevokeToken          | 撤消在指定时间之前为标识创建的所有访问令牌。 |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="身份验证请求指标。":::

## <a name="next-steps"></a>后续步骤

- 了解有关[数据平台指标](../../azure-monitor/essentials/data-platform-metrics.md)的详细信息
