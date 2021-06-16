---
title: 如何使用 Azure Web PubSub 服务诊断日志进行故障排除
description: 了解如何获取诊断日志以及如何使用这些日志进行故障排除
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 119591c4a337cf622b95fa4ed70a7508d8acb99e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963083"
---
# <a name="how-to-troubleshoot-with-diagnostic-logs"></a>如何使用诊断日志进行故障排除

本操作指南介绍了获取诊断日志的选项，以及如何使用这些日志进行故障排除。

## <a name="whats-the-diagnostic-logs"></a>诊断日志是什么？

诊断日志提供了更为丰富的视图，可清晰查看与 Azure Web PubSub 服务实例的连接以及传递至服务实例的信息。 诊断日志可用于识别问题、跟踪连接、追踪消息和分析。

诊断日志可分为两种类型：连接性日志和消息传递日志。

### <a name="connectivity-logs"></a>连接性日志

连接性日志提供 Azure Web PubSub 中心连接的详细信息， 例如，基本信息（用户 ID、连接 ID 等）和事件信息（连接、断开连接和中止事件等）。 因此，连接性日志有助于解决与连接相关的问题。 

### <a name="messaging-logs"></a>消息传递日志

消息传递日志为通过 Azure Web PubSub 服务接收和发送的 Azure Web PubSub 中心消息提供跟踪信息， 例如，消息的跟踪 ID 和消息类型。 通常，在消息到达或离开服务时，会对其进行记录。 因此，消息传递日志有助于解决与消息相关的问题。 

## <a name="capture-diagnostic-logs-with-azure-web-pubsub-service-live-trace-tool"></a>使用 Azure Web PubSub 服务实时跟踪工具捕获诊断日志 

Azure Web PubSub 服务实时跟踪工具能够实时收集诊断日志，并且有助于跟踪客户的开发环境。 实时跟踪工具可以捕获连接性日志和消息传递日志。

> [!NOTE]
> 实时跟踪工具捕获的实时诊断日志将作为消息（出站流量）计费。

> [!NOTE]
> 作为免费层创建的 Azure Web PubSub 服务实例具有每日消息（出站流量）限制。

### <a name="launch-the-live-trace-tool"></a>启动实时跟踪工具

1. 转到 Azure 门户。 
1. 在 Azure Web PubSub 服务实例的“诊断设置”页上，选择“打开实时跟踪工具”。 

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="启动实时跟踪工具。":::

### <a name="capture-the-diagnostic-logs"></a>捕获诊断日志

实时跟踪工具提供了一些基本功能，有助于捕获诊断日志以进行故障排除。

* 捕获：使用实时跟踪工具开始从 Azure Web PubSub 实例捕获实时诊断日志。
* 清除：清除捕获的实时诊断日志。
* 日志筛选器：使用实时跟踪工具，可以通过特定的关键字筛选捕获的实时诊断日志。 通用分隔符（如空格、逗号、分号等）可视为关键词。 
* 状态：“状态”显示实时跟踪工具与特定实例是已连接还是已断开连接。

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="使用实时跟踪工具捕获诊断日志。":::

实时跟踪工具捕获的实时诊断日志包含用于故障排除的详细信息。 

| 名称 | 说明 |
| ------------ |  ------------------------ | 
| 时间 | 日志事件时间 |
| 日志级别 | 日志事件级别（跟踪/调试/信息/警告/错误） |
| 事件名称 | 事件的操作名称 |
| Message | 日志事件的详细消息 |
| 异常 | Azure Web PubSub 服务的运行时异常 |
| 集线器 | 用户定义的中心名称 |
| 连接 ID | 连接的标识 |
| 用户 ID | 用户的标识 |
| IP | 客户端 IP 地址 | 

正式发布 Azure Web PubSub 服务后，实时跟踪工具还支持将日志导出为特定格式，并帮助你与他人共享以进行故障排除。 

## <a name="capture-diagnostic-logs-with-azure-monitor"></a>使用 Azure Monitor 捕获诊断日志

公共预览版不支持与 [Azure Monitor](https://azure.microsoft.com/services/monitor/)、[Azure 存储](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)和 [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) 集成以捕获诊断日志。

## <a name="troubleshoot-with-the-diagnostic-logs"></a>使用诊断日志进行故障排除

遇到连接意外增加或丢失的情况时，可以利用诊断日志进行故障排除。 典型问题通常与连接的意外数量更改、连接达到限制和授权失败有关。

### <a name="unexpected-connection-number-changes"></a>意外的连接数更改

#### <a name="unexpected-connection-dropping"></a>意外的连接丢失

如果连接断开，诊断日志将在 `operationName` 中使用 `ConnectionAborted` 或 `ConnectionEnded` 记录此断开连接事件。

`ConnectionAborted` 和 `ConnectionEnded` 之间的区别在于，`ConnectionEnded` 是客户端或服务器端触发的预期断开连接。 而 `ConnectionAborted` 通常是意外的连接丢失事件，中止原因会在 `message` 中提供。

下表列出了中止原因：

| Reason | 说明 |
| ------- | ------- |
| 连接计数达到限制 | 连接计数达到当前定价层的限制。 考虑纵向扩展服务单元
| 服务重新加载并重新连接 | 正在重新加载 Azure Web PubSub 服务。 需要实现自己的重新连接机制，或手动重新连接到 Azure Web PubSub 服务 |
| 内部服务器的暂时性错误 | Azure Web PubSub 服务中发生暂时性错误，会自动恢复

#### <a name="unexpected-connection-growing"></a>意外的连接增加

若要排查连接意外增加的问题，首先需要筛选出额外的连接。 可以向测试客户端连接添加唯一的测试用户 ID。 然后使用诊断日志验证该 ID，如果看到多个客户端连接使用同一测试用户 ID 或 IP，则可能表示客户端创建并建立了超过预期数目的连接。 检查客户端。

### <a name="authorization-failure"></a>授权失败

如果针对客户端请求返回了“401 未授权”，请检查诊断日志。 如果遇到 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`，则表示访问令牌中的所有受众无效。 尝试使用日志中建议的有效受众。

### <a name="throttling"></a>限制

如果发现无法在客户端与 Azure Web PubSub 服务之间建立连接，请检查诊断日志。 如果在诊断日志中遇到 `Connection count reaches limit`，则表示与 Azure Web PubSub 服务建立的连接过多，达到了连接计数限制。 请考虑纵向扩展 Azure Web PubSub 服务实例。 如果在诊断日志中遇到 `Message count reaches limit`，则表示使用的是免费层，并且消息配额已用完。 若要发送更多消息，请考虑将 Azure Web PubSub 服务实例更改为标准层以发送其他消息。 有关详细信息，请参阅 [Azure Web PubSub 服务定价](https://azure.microsoft.com/pricing/details/web-pubsub/)。