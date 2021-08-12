---
title: Azure Web PubSub 服务的计费模型
description: 概述 Azure Web PubSub 服务的计费模型的关键概念。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c7a7aaa47d40c9d49065f71bc9bebc998adbfc12
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111410470"
---
# <a name="billing-model-of-azure-web-pubsub-service"></a>Azure Web PubSub 服务的计费模型

Azure Web PubSub 服务的计费模型基于单元数量和出站流量（消息计数）的大小。 本文介绍如何定义单元和出站流量（消息计数）并进行计费。

## <a name="how-units-are-counted-with-billing-model"></a>如何用计费模型计算单元数

### <a name="what-is-the-connection"></a>什么是连接

连接是指客户端与服务器之间的连接。 你可以在 Azure 门户中监视连接计数。 “连接（最大数）”视图显示特定时间段内的最大连接数。 

### <a name="what-is-the-unit"></a>什么是单元

单元是描述 Azure Web PubSub 服务的功能的抽象概念。 每个单元最多支持 1,000 个并发连接。 每个 Azure Web PubSub 服务实例可以包含 1、2、5、10、20、50 或 100 个单元。 单元计数指定 Web PubSub 服务实例可以接受的连接数。

###  <a name="how-units-are-counted-with-billing-model"></a>如何用计费模型计算单元数

系统根据单元数和使用时间（秒）对单元进行计数，并按天计费。 

例如，假设有一个包含 5 个单元的 Azure Web PubSub 服务实例，该实例在上午 10:00 到下午 16:00 扩展到多达 10 个单元，然后在下午 16:00 后缩减为 5 个单元。 在特定的某一天中，该实例有 5 个单元使用了 18 个小时，有 10 个单元使用了 6 小时。

> 用于计费的单元使用量 =（5 个单元 * 18 小时 + 10 个单元 * 6 小时）/24 小时 = 6.25 单元/天

## <a name="how-outbound-traffic-is-counted-with-billing-model"></a>如何用计费模式计算出站流量

### <a name="what-is-inboundoutbound-traffic"></a>什么是入站/出站流量 

出站流量是从 Azure Web PubSub 服务发送的消息。 你可以通过 Azure 门户监视出站流量。 “出站流量（总计）”视图显示特定时间段内出站消息的合计大小（字节）。

- 通过服务向接收方广播的消息。
- 从服务发送到上游 Webhook 的消息。
- 使用[实时跟踪工具](./howto-troubleshoot-diagnostic-logs.md#capture-diagnostic-logs-with-azure-web-pubsub-service-live-trace-tool)记录的诊断日志。 

入站流量是发送到 Azure Web PubSub 服务的消息。 

- 从客户端发送到服务的消息。
- 从服务器或函数发送到服务的消息。

### <a name="what-is-message-count"></a>什么是消息计数

用于计费的消息计数是一个抽象概念，我们将其定义为用出站流量大小（字节）除以 2 KB。 例如，100-KB 流量计为 50 条消息。  

### <a name="how-traffic-is-counted-with-billing-model"></a>如何用计费模型计算流量

在计费时，只计算出站流量。 

例如，假设你有一个使用 Azure Web PubSub 服务和 Azure Functions 的应用程序。 一个用户向一组中的 10 个连接广播 4 KB 数据。 从服务发送到函数的上游流量为 4 KB，从服务向 10 个连接广播的流量为 40 KB。

> 计费的出站流量 = 4 KB（上游流量）+ 4 KB * 10（服务广播到客户端的流量）= 44 KB

> 等效的消息计数 = 44 KB/2 KB = 22

Azure Web PubSub 服务还会根据单元使用情况，提供出站流量（消息计数）的每日免费配额。 超出免费配额的出站流量（消息计数）为额外的出站流量（附加消息）。 以标准层为例，免费配额为每单元/天 2,000,000 KB 出站流量（1,000,000 条消息）。

以之前的单元使用为例，应用程序每天使用 6.25 个单元，以确保每日可用配额为 12,500,000 KB 出站流量（625 万条消息）。 假设每日出站流量为 30,000,000 KB（1500 万条消息），附加消息将为 17,500,000 KB 出站流量（875 万条消息）。 因此，系统将按当天有 6.25 个标准单元和 8.75 个附加消息单元向你收费。

## <a name="pricing"></a>定价 

Azure Web PubSub 服务提供具有不同定价标准的多个服务层级。 了解如何用计费模型计算单元数和出站流量大小（消息计数）后，可以从 [Azure Web PubSub 服务定价](https://azure.microsoft.com/pricing/details/web-pubsub)中详细了解定价信息。





