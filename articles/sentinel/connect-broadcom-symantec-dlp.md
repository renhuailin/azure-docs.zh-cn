---
title: 将 Broadcom Symantec 数据丢失防护 (DLP) 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Broadcom Symantec DLP 数据连接器将 Symantec DLP 日志拉取到 Azure Sentinel。 在工作簿中查看 Symantec DLP 数据，创建警报，并改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: e7eb8e2319d00f862b293cbb15482cdefde231ed
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252218"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>将 Broadcom Symantec 数据丢失防护 (DLP) 连接到 Azure Sentinel

> [!IMPORTANT]
> Broadcom Symantec DLP 连接器目前以预览版提供。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Broadcom Symantec DLP 设备连接到 Azure Sentinel。 通过 Broadcom Symantec DLP 数据连接器，可以将 Symantec DLP 日志轻松连接到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。 通过此功能，可以更深入地了解组织的信息、信息的传播位置，并改进安全操作功能。 Broadcom Symantec DLP 网关和 Azure Sentinel 之间的集成利用了设置为 CEF 格式的 Syslog、基于 Linux 的日志转发器以及 Log Analytics 代理。 它还使用了基于 Kusto 函数的定制日志分析程序。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。 [详细了解工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>向 Azure Sentinel 发送 Broadcom Symantec DLP 日志

若要将其日志拉取到 Azure Sentinel 中，请将 Broadcom Symantec DLP 设备配置为将 CEF 格式的 Syslog 消息发送到基于 Linux 的日志转发服务器（运行 rsyslog 或 syslog-ng）。 此服务器上将会安装 Log Analytics 代理，该代理会将这些日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在“数据连接器”库中，选择“Broadcom Symantec DLP(预览版)”连接器，然后选择“打开连接器页”。

1. 按照“配置”下的“说明”选项卡中的说明进行操作 ：

    1. 在“1. Linux Syslog 代理配置”下 — 如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更多详细说明和介绍，请参阅 Azure Sentinel 文档中的[步骤 1：部署日志转发器](connect-cef-agent.md)。

    1. 在“2. 将 Symantec DLP 日志转发到 Syslog 代理”下，按照 Broadcom 的说明[配置 Symantec DLP](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US)。 此配置应该包含以下元素：
        - 日志目标 – 日志转发服务器的主机名和/或 IP 地址
        - 协议和端口 - TCP 514（如果使用建议的其他协议和端口，请确保在日志转发服务器上的 syslog 守护程序中进行并行更改）
        - 日志格式 – CEF
        - 日志类型 – 所有可用的或所有适用的

    1. 在“3. 验证连接”下 — 通过复制连接器页上的命令并在日志转发器上运行以验证数据引入。 有关更详细的说明和介绍，请参阅 Azure Sentinel 文档中的[步骤 3：验证连接性](connect-cef-verify.md)。

        可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

在成功建立连接后，数据会出现在“CommonSecurityLog”表的“Azure Sentinel”部分下的“日志”中 。

此数据连接器必须依赖于基于 Kusto 函数的分析程序才能按预期方式工作。 [按照这些步骤操作](https://aka.ms/sentinel-symantecdlp-parser)，以创建 SymantecDLP Kusto 函数别名。

然后，若要在 Log Analytics 中查询 Broadcom Symantec DLP 数据，请在查询窗口顶部输入 `SymantecDLP`。

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的查询示例。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Symantec DLP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。