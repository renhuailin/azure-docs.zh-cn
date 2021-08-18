---
title: 将 Citrix WAF 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Citrix WAF 数据连接器将其日志拉取到 Azure Sentinel 中。 在工作簿中查看 Citrix WAF 数据，创建警报，并改进调查。
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: eaee16d865a0d208dbf0c41665d590e7ecb14e89
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253591"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>将 Citrix WAF 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Citrix Web 应用程序防火墙 (WAF) 数据连接器当前是公开预览版。 服务级别协议未随此功能一起提供。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Citrix Web 应用程序防火墙 (WAF) 设备连接到 Azure Sentinel。 通过 Citrix WAF 数据连接器，可以将 Citrix WAF 日志轻松连接到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。 通过将 Citrix WAF CEF 日志连接到 Azure Sentinel，可以针对每个日志使用搜索和关联、警报以及威胁情报扩充功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>将 Citrix WAF 日志转发到 Syslog 代理  

Citrix WAF 使用 CEF 格式将 Syslog 消息发送到安装了 Log Analytics 代理的基于 Linux 的日志转发服务器（运行 rsyslog 或 syslog-ng），然后该代理会将日志转发到 Azure Sentinel 工作区。

1. 如果没有这样的日志转发服务器，请参阅[这些说明](connect-cef-agent.md)。

1. 按照 Citrix 提供的说明[配置 WAF](https://support.citrix.com/article/CTX234174)，[配置 CEF 日志记录](https://support.citrix.com/article/CTX136146)，并[配置将日志发送到日志转发器](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)。 请确保将日志发送到日志转发器计算机 IP 地址上的 TCP 端口 514。

1. 验证连接并使用[这些说明](connect-cef-verify.md)验证数据引入。 可能需要长达 20 分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在“CommonSecurityLog”表中的“Azure Sentinel”部分下的“日志”中。

若要在 Log Analytics 中查询 Citrix WAF 日志，请在查询窗口顶部输入 `CommonSecurityLog`。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Citrix WAF 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。