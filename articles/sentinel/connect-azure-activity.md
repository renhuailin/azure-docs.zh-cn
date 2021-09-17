---
title: 将 Azure 活动日志数据连接到 Azure Sentinel | Microsoft Docs
description: 只需单击一次即可将 Azure 活动日志事件流式传输到 Azure Sentinel。 活动日志服务会记录并显示 Azure 中的订阅级别事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2021
ms.author: yelevin
ms.openlocfilehash: 874c4d520b3d41282d74f047b34fbe7148279a65
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606734"
---
# <a name="connect-data-from-azure-activity-log"></a>连接 Azure 活动日志中的数据

只需单击一次即可将事件从 Azure [活动日志服务](../azure-monitor/essentials/activity-log.md)流式传输到 Azure Sentinel。 活动日志是 Azure 中的[平台日志](../azure-monitor/essentials/platform-logs-overview.md)，可提供对从 Azure 资源管理器操作数据到服务运行状况事件的更新等订阅级别事件的见解。 通过活动日志，可确定订阅中对资源执行的任何写入操作（PUT、POST、DELETE）的“内容、执行者和时间”等信息。 还可以了解该操作和其他相关属性的状态。 该服务不会记录读取 (GET) 操作，也不会记录对不使用 Azure 资源管理器 (ARM) 模型的资源的操作。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>必备条件

- 必须对 Log Analytics 工作区拥有读取和写入权限。

- 对于要启动或停止将其日志流式处理到 Azure Sentinel 的任何订阅，必须具有“所有者”权限。

- 若要使用 Azure Policy 将日志流式处理策略应用于 Azure 活动日志订阅，必须具有策略分配范围的“所有者”角色。

## <a name="data-structure-changes"></a>数据结构更改

此连接器正在更改后端用于收集活动日志事件的方法。 它现在使用较新的连接器（例如 Azure Key Vault 和 Azure Kubernetes 服务连接器）所用的诊断设置管道。 如果仍在使用此连接器的旧方法，强烈建议升级到新版本，这将提供更好的功能，并增强与资源日志的一致性。 请参阅以下说明。

诊断设置方法发送的数据与旧方法从活动日志服务发送的数据相同，尽管对 AzureActivity 表的[结构进行了一些更改](../azure-monitor/essentials/activity-log.md#data-structure-changes) 。

下面是迁移到诊断设置管道后的一些关键改进：
- 改进了引入延迟（在事件发生后 2-3 分钟内而不是 15-20 分钟内引入事件）。
- 提高了可靠性。
- 改进的性能。
- 支持活动日志服务记录的所有类别的事件（旧机制仅支持部分类别，例如不支持服务运行状况事件）。
- 使用 Azure Policy 进行大规模管理。

请参阅 [Azure Monitor 文档](../azure-monitor/logs/data-platform-logs.md)，深入了解 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)和[诊断设置管道](../azure-monitor/essentials/diagnostic-settings.md)。

## <a name="set-up-the-azure-activity-log-connector"></a>设置 Azure 活动日志连接器

设置新的 Azure 活动日志连接器包含两个阶段：
1. 断开现有订阅与旧方法的连接。

1. 使用 Azure Policy 将所有相关订阅连接到新的诊断设置管道。

### <a name="disconnect-from-old-pipeline"></a>断开与旧管道的连接

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。 在连接器列表中，选择“Azure 活动”，然后单击右下角的“打开连接器页面”按钮 。

1. 在“说明”选项卡下“配置”部分的步骤 1 中，查看连接到旧方法的现有订阅列表（以便知道要将哪些订阅添加到新方法），然后单击下面的“全部断开”按钮，一次性断开所有连接  。

### <a name="connect-to-new-pipeline"></a>连接到新管道

1. 在步骤 2 下，选择“启动 Azure Policy 分配向导”按钮。 这将打开策略分配向导，便于可创建一个名为“配置 Azure 活动日志以流式传输到指定 Log Analytics 工作区”的新策略。

1. 在“基本信息”选项卡中，单击“范围”下的省略号按钮打开“范围”面板，在其中选择你的订阅（也可根据需要选择资源组）  。 还可以添加说明。

1. 在“参数”选项卡中，将“效果”和“启用日志”字段保留原样  。 从“Log Analytics 工作区”下拉列表中选择你的 Azure Sentinel 工作区。

1. 此策略将应用于将来添加的资源。 若也要对现有资源应用该策略，请选择“修正”选项卡，然后勾选“创建修正任务”复选框 。

1. 在“查看 + 创建”选项卡中，单击“创建”   。 你的策略现已分配给所选的作用域。

> [!NOTE]
>
> 对于此特定数据连接器，仅当在过去 14 天内的某个时间点引入数据时，连接状态指示器（数据连接器库中的色带以及数据类型名称旁边的连接图标）才显示为“已连接”（绿色）。 一旦 14 天过去但未引入数据，连接器将显示为已断开连接。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="find-your-data"></a>查找数据

若要在 Log Analytics 中查询 Azure 活动警报的相关架构，请在查询窗口顶部键入 `AzureActivity`。

请参阅连接器页面中的“后续步骤”选项卡，了解一些有用的示例查询和工作簿模板，以帮助分析和可视化数据。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解了如何将 Azure 活动日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 使用[内置](detect-threats-built-in.md)或[自定义](detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。
