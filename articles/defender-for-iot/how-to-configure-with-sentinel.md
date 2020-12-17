---
title: '为 Defender (预览版配置 Azure Sentinel) '
description: 介绍如何配置 Azure Sentinel 以便接收来自你的用于 IoT 的 Defender 解决方案的数据。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/16/2020
ms.author: shhazam
ms.openlocfilehash: c033f6fc6f3fc40005d98bd48832c15792d6f484
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629079"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>将数据从用于 IoT 的 Defender 连接到 Azure Sentinel (预览) 

> [!IMPORTANT]
> 用于 IoT 数据连接器的 Defender 当前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用用于 IoT 连接器的 Defender 将所有的 Defender for IoT 事件流式传输到 Azure Sentinel。 

这种集成使组织能够快速检测到经常跨越 IT 和边界的多阶段攻击。 此外，使用适用于 Azure Sentinel 的安全业务流程、自动化和响应 (之忠诚度) 功能的 Defender 可以通过内置的 " 

## <a name="prerequisites"></a>先决条件

- 对部署了 Azure Sentinel 的工作区的 **读取** 和 **写入** 权限
- 必须在相关 IoT 中心 (s **启用****用于 iot 的 Defender**) 
- 您必须对要连接的 **订阅** 具有 **参与者** 权限

## <a name="connect-to-defender-for-iot"></a>连接到用于 IoT 的 Defender

1. 在 Azure Sentinel 中，选择 " **数据连接器** "，然后选择 " **用于 iot 的 Defender** " (可能仍被从库中称为 "Iot) 的 Azure 安全中心"。

1. 在右侧窗格的底部，单击 " **打开连接器" 页面**。

1. 单击要流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁边的 " **连接**"。
    - 如果在订阅中的至少一个 IoT 中心未启用 Defender for IoT，你将收到一条错误消息。 在 IoT 中心内为 IoT 启用 Defender 以删除错误。

1. 可以决定是否希望 IoT 中的警报在 Azure Sentinel 中自动生成事件。 在 " **创建事件**" 下，选择 " **启用** " 以启用默认分析规则，以便根据生成的警报自动创建事件。 可以在 "**分析**  >  **活动规则**" 下更改或编辑此规则。

> [!NOTE]
> 更改连接后， **订阅** 列表可能需要10秒钟或更长时间才能刷新。 

## <a name="log-analytics-alert-view"></a>Log Analytics 警报视图

若要在 Log Analytics 中使用相关架构来显示适用于 IoT 警报的 Defender：

1. 打开  >  **SecurityInsights**  >  **SecurityAlert** 的日志，或搜索 **SecurityAlert**。

1. 使用以下 kql 筛选器仅查看 IoT 生成的警报的 Defender：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>服务说明

连接 **订阅** 后，可以在 Azure Sentinel 中大约15分钟提供集线器数据。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将用于 IoT 的 Defender 连接到 Azure Sentinel。 若要了解有关威胁检测和安全数据访问的详细信息，请参阅以下文章：

- 了解如何使用 Azure Sentinel 来了解 [你的数据和潜在威胁](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)。
- 了解如何 [访问 IoT 安全数据](how-to-security-data-access.md)
