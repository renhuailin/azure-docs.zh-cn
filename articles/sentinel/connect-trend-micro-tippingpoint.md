---
title: 将走向微 TippingPoint 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用走向微 TippingPoint 数据连接器将 TippingPoint SMS 日志请求到 Azure Sentinel。 查看工作簿中的 TippingPoint 数据、创建警报并改善调查。
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
ms.openlocfilehash: 989520e079988e1821d8bb9a936f857e1f62c11a
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567769"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>将趋势微 TippingPoint 解决方案连接到 Azure Sentinel

> [!IMPORTANT]
> 走向微 TippingPoint 连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍如何将走向微 TippingPoint 威胁防护系统解决方案连接到 Azure Sentinel。 走向微 TippingPoint 数据连接器可让你轻松地将 TippingPoint 安全管理系统与 Azure Sentinel 连接 (SMS) 日志，以便查看工作簿中的数据、使用它创建自定义警报并将其合并以改善调查。 

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 您必须对工作区的共享密钥具有读取权限。

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>向 Azure Sentinel 发送走向微 TippingPoint 日志

若要将其日志导入 Azure Sentinel，请将 TippingPoint TPS 解决方案配置为以 CEF 格式将 Syslog 消息发送到运行 rsyslog 或 Syslog-ng)  (基于 Linux 的日志转发服务器。 此服务器上安装了 Log Analytics 代理，代理将日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **走向微 TippingPoint (预览")**，然后单击 " **连接器" 页**。

1. 按照 "**配置**" 下的 "**说明**" 选项卡中的说明进行操作：

    1. 小于 **1。Linux Syslog 代理配置** -如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更详细的说明和说明，请参阅第1步：在 Azure Sentinel 文档中 [部署日志转发器](connect-cef-agent.md) 。

    1. 低于 **2。向前走向微 TippingPoint 将 SMS 日志发送到 Syslog 代理** -此配置应包含以下元素：
        - 日志目标–日志转发服务器的主机名和/或 IP 地址
        - 协议和端口– **TCP 514** (否则，请确保在日志转发服务器上的 syslog 后台程序中进行并行更改) 
        - 日志格式– **ARCSIGHT CEF 格式 4.2**
        - 日志类型-所有可用

    1. **3。验证连接**-通过复制 "连接器" 页上的命令并在日志转发器上运行，验证数据引入。 有关更详细的说明和说明，请参阅 [第3步：验证](connect-cef-verify.md) Azure Sentinel 文档中的连接。

        可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**日志**" 的 " *CommonSecurityLog* " 表中的 " **Azure Sentinel** " 部分下。

若要在 Log Analytics 中查询 TrendMicro TippingPoint 数据，请将以下内容复制到查询窗口中，并在选择时应用其他筛选器：

```kusto
CommonSecurityLog 
| where DeviceVendor == "TrendMicroTippingPoint"
```

有关更多查询示例，请参阅连接器页中的 " **后续步骤** " 选项卡。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将走向微 TippingPoint 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。