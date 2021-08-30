---
title: 使用适用于设备生成器的 Defender for IoT 配置 Azure Sentinel
description: 本文介绍如何配置 Azure Sentinel 来接收适用于设备生成器的 Defender for IoT 解决方案中的数据。
ms.topic: how-to
ms.date: 05/26/2021
ms.openlocfilehash: 7f017dc155dcbfa50b3fac6712da1a11af08b65b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689156"
---
# <a name="connect-your-data-from-defender-for-iot-for-device-builders-to-azure-sentinel-public-preview"></a>将适用于设备生成器的 Defender for IoT 中的数据连接到 Azure Sentinel（公共预览版）

使用 Defender for IoT 连接器将所有的 Defender for IoT 事件都流式传输到 Azure Sentinel。 

这种集成使组织可以快速检测到通常跨 IT 和 OT 边界的多阶段攻击。 此外，通过将 Defender for IoT 与 Azure Sentinel 安全业务流程、自动化和响应 (SOAR) 功能集成，可以使用内置的已优化 OT 的 playbook 来实现自动响应和预防。 

## <a name="prerequisites"></a>先决条件

- 在部署了 Azure Sentinel 的工作区上有读取和写入权限 
- 必须在相关的 IoT 中心上启用 Defender for IoT 
- 必须对要连接的订阅具有参与者权限 

## <a name="connect-to-defender-for-iot"></a>连接到 Defender for IoT

1. 在 Azure Sentinel 中，选择“数据连接器”，然后从库中选择“Defender for IoT”（可能仍被称为“适用于 IoT 的 Azure 安全中心”） 。

1. 在右窗格底部，单击“打开连接器页”。

1. 在要将其警报和设备警报流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁，单击“连接”。
    - 如果未在订阅中至少一个 IoT 中心上启用 Defender for IoT，将出现错误消息。 请在 IoT 中心内启用 Defender for IoT，以消除该错误。

1. 可以决定是否希望 Defender for IoT 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下，选择“启用”，以使默认分析规则自动根据生成的警报创建事件 。 可以在“Analytics” > “活动规则”下更改或编辑此规则 。

> [!NOTE]
> 进行连接更改之后，可能需要 10 秒或更长时间“订阅”列表才会刷新。 

## <a name="log-analytics-alert-view"></a>Log Analytics 警报视图

若要在 Log Analytics 中使用相关架构显示 Defender for IoT 警报，请执行以下操作：

1. 打开“日志” > “SecurityInsights” > “SecurityAlert”，或搜索“SecurityAlert”。

1. 使用以下 kql 筛选器进行筛选，以便仅查看 Defender for IoT 生成的警报：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>服务说明

在连接订阅后，大约经过 15 分钟，Azure Sentinel 中会提供中心数据。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Defender for IoT 连接到 Azure Sentinel。 若要了解有关威胁检测和安全数据访问的详细信息，请参阅以下文章：

- 要了解如何使用 Azure Sentinel，请参阅[快速入门：Azure Sentinel 入门](/azure/sentinel/get-visibility)。
- 了解如何[访问 IoT 安全性数据](how-to-security-data-access.md)
