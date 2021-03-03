---
title: 将 Aruba ClearPass 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Aruba ClearPass 连接器将 Aruba ClearPass 日志推送到 Azure Sentinel。 查看工作簿中的 Aruba ClearPass 数据、创建警报并改进调查。
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 1b03e2d8715b3d80425ef181dd908978d5df4cf2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744474"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>将 Aruba ClearPass 连接到 Azure Sentinel

> [!IMPORTANT]
> Aruba ClearPass 连接器目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍了如何将 Aruba ClearPass 设备连接到 Azure Sentinel。 Aruba ClearPass 数据连接器可让你轻松地通过 Azure Sentinel 连接 Aruba ClearPass 日志，这样，你就可以查看工作簿中的数据，对其进行查询以创建自定义警报，并结合它来改善调查。 Aruba ClearPass 和 Azure Sentinel 之间的集成利用了 CEF 格式的 Syslog、基于 Linux 的日志转发器和 Log Analytics 代理。 它还使用基于 Kusto 函数的自定义的日志分析器。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 您必须对工作区的共享密钥具有读取权限。 [了解有关工作区密钥的详细信息](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>将 Aruba ClearPass 日志发送到 Azure Sentinel

若要将其日志导入 Azure Sentinel，请将 Aruba ClearPass 设备配置为以 CEF 格式将 Syslog 消息发送到基于 Linux 的日志转发服务器 (运行 rsyslog 或 Syslog-ng) 。 此服务器上安装了 Log Analytics 代理，代理将日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **Aruba ClearPass (预览")**，然后单击 "连接器" **页**。

1. 按照 "**配置**" 下的 "**说明**" 选项卡中的说明进行操作：

    1. 小于 **1。Linux Syslog 代理配置** -如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更详细的说明和说明，请参阅第1步：在 Azure Sentinel 文档中 [部署日志转发器](connect-cef-agent.md) 。

    1. 低于 **2。将 Aruba ClearPass 日志转发到 Syslog 代理** -按照 Aruba 的说明 [配置 ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)。 此配置应包含以下元素：
        - 日志目标–日志转发服务器的主机名和/或 IP 地址
        - 协议和端口– TCP 514 (否则，请确保在日志转发服务器上的 syslog 后台程序中进行并行更改) 
        - 日志格式– CEF
        - 日志类型-所有可用或全部适用

    1. **3。验证连接**-通过复制 "连接器" 页上的命令并在日志转发器上运行，验证数据引入。 有关更详细的说明和说明，请参阅 [第3步：验证](connect-cef-verify.md) Azure Sentinel 文档中的连接。

        可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**日志**" 的 " *CommonSecurityLog* " 表中的 " **Azure Sentinel** " 部分下。

此数据连接器依赖于基于 Kusto 函数的分析器来按预期方式工作。 [按照以下步骤](https://aka.ms/sentinel-arubaclearpass-parser) 创建 **ArubaClearPass** Kusto 函数别名。

然后，若要在 Log Analytics 中查询 Aruba ClearPass 数据，请 `ArubaClearPass` 在 "查询" 窗口顶部输入。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的查询示例。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Aruba ClearPass 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
