---
title: 将 Trend Micro TippingPoint 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Trend Micro TippingPoint 数据连接器将 TippingPoint SMS 日志拉取到 Azure Sentinel。 在工作簿中查看 TippingPoint 数据，创建警报，并改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 9305ea9263c78886ca49ef7254813d225be0c882
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693750"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>将 Trend Micro TippingPoint 解决方案连接到 Azure Sentinel

> [!IMPORTANT]
> Trend Micro TippingPoint 连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Trend Micro TippingPoint 威胁防护系统解决方案连接到 Azure Sentinel。 使用 Trend Micro TippingPoint 数据连接器，可以轻松地将 TippingPoint 安全管理系统 (SMS) 日志与 Azure Sentinel 连接，这样，你就可以在工作簿中查看数据，使用这些数据来创建自定义警报，并整合这些数据来改进调查。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>向 Azure Sentinel 发送 Trend Micro TippingPoint 日志

若要将其日志拉取到 Azure Sentinel 中，请将 TippingPoint TPS 解决方案配置为将 CEF 格式的 Syslog 消息发送到基于 Linux 的日志转发服务器（运行 rsyslog 或 syslog-ng）。 此服务器上将会安装 Log Analytics 代理，该代理会将这些日志转发到 Azure Sentinel 工作区。 连接器使用分析器函数将其接收的数据转换为规范化架构。 

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“Trend Micro TippingPoint(预览版)”，然后选择“打开连接器页”  。

1. 在以下“配置”下，按照“说明”选项卡中的说明进行操作 ：

    1. 在“1. Linux Syslog 代理配置”下 — 如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更详细的说明和介绍，请参阅 Azure Sentinel 文档中的[步骤 1：部署日志转发器](connect-cef-agent.md)。

    1. 在“2. 将 Trend Micro TippingPoint SMS 日志转发到 Syslog 代理”下 - 此配置应包含以下元素：
        - 日志目标 - 日志转发服务器的主机名和/或 IP 地址
        - 协议和端口 – TCP 514（如果使用建议的其他协议和端口，请确保在日志转发服务器上的 syslog 守护程序中进行并行更改）
        - 日志格式 - ArcSight CEF 格式 v4.2
        - 日志类型 - 所有可用类型

    1. 在“3. 验证连接”下 — 通过复制连接器页上的命令并在日志转发器上运行以验证数据引入。 有关更详细的说明和介绍，请参阅 Azure Sentinel 文档中的“[验证 CEF 连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)”。

        可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会出现在“CommonSecurityLog”表的“Azure Sentinel”部分的“日志”中 。

若要获取 Log Analytics 中的 Trend Micro TippingPoint 数据，请查询分析器函数而非表。 将以下内容复制到查询窗口，并在选择时应用其他筛选器：

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

有关更多查询示例，请参阅连接器页中的“后续步骤”标签页。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Trend Micro TippingPoint 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。