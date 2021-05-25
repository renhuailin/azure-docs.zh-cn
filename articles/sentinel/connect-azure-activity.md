---
title: 将 Azure 活动数据连接到 Azure Sentinel |Microsoft Docs
description: 只需单击一次即可将 Azure 活动日志流式传输到 Azure Sentinel。 活动日志记录并显示 Azure 中的订阅级别事件。
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
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 8f0bac363e020826e34df3cb0b4e3d3582990302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595442"
---
# <a name="connect-data-from-azure-activity-log"></a>连接 Azure 活动日志中的数据

只需单击一次即可将日志从 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)流式传输到 Azure Sentinel。 活动日志是一种订阅日志，用于记录和显示 Azure 中的订阅级别事件，从 Azure 资源管理器操作数据到服务运行状况事件更新。 通过活动日志，可确定订阅中对资源执行的任何写入操作（PUT、POST、DELETE）的“内容、执行者和时间”等信息。 还可以了解该操作和其他相关属性的状态。 活动日志未包括读取 (GET) 操作或针对使用经典/“RDFE”模型的资源的操作。 

## <a name="prerequisites"></a>先决条件

- 用户必须在“Log Analytics 工作区”上具有参与者权限。
- 用户必须对想要将其日志流式传输到 Azure Sentinel 的任何订阅具有“读取者”权限。

## <a name="set-up-the-azure-activity-connector"></a>设置 Azure 活动连接器

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。 在连接器列表中，单击“Azure 活动”，然后单击右下角的“打开连接器页面”按钮。

2. 在“说明”选项卡下，单击“配置 Azure 活动日志”>“链接” 。

3. 在“Azure 活动日志”窗格中，选择要将其日志流式传输到 Azure Sentinel 的订阅。 

4. 在右侧打开的“订阅”窗格中，单击“连接”。

5. 若要对 Azure 活动警报使用 Log Analytics 中的相关架构，请在“查询”窗口中键入 `AzureActivity`。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解了如何将 Azure 活动日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用[内置](tutorial-detect-threats-built-in.md)或[自定义](tutorial-detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。
