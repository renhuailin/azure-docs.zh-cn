---
title: 将 Imperva WAF 网关设备连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Imperva WAF 网关连接器将 Imperva WAF 日志拉取到 Azure Sentinel 中。 在工作簿中查看 Imperva WAF 数据，创建警报，以及改进调查。
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: fea80f26a914fac6ff55fcaa2bea9447aaa490fb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694005"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>将 Imperva WAF 网关设备连接到 Azure Sentinel

> [!IMPORTANT]
> Imperva WAF 网关连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Imperva WAF 网关设备连接到 Azure Sentinel。 使用 Imperva WAF 网关数据连接器，可以轻松地将 Imperva WAF 网关日志与 Azure Sentinel 连接，这样，你就可以在工作簿中查看数据，使用这些数据来创建自定义警报，并整合这些数据来改进调查。 Imperva WAF 网关和 Azure Sentinel 之间的集成利用了设置为 CEF 格式的 Syslog、基于 Linux 的日志转发器以及 Log Analytics 代理。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区所在的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。 [详细了解工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>将 Imperva WAF 网关日志发送到 Azure Sentinel

若要将其日志拉取到 Azure Sentinel 中，请将 Imperva WAF 网关设备配置为将 CEF 格式的 Syslog 消息发送到基于 Linux 的日志转发服务器（运行 rsyslog 或 syslog-ng）。 此服务器上将会安装 Log Analytics 代理，该代理会将这些日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“Imperva WAF 网关(预览版)”，然后选择“打开连接器页”  。

1. 按照“配置”下的“说明”选项卡中的说明进行操作 ：

    1. 在“1. Linux Syslog 代理配置”下 — 如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更多详细说明和介绍，请参阅 Azure Sentinel 文档中的[步骤 1：部署日志转发器](connect-cef-agent.md)。

    1. 在“2. 将通用事件格式 (CEF) 日志转发到 Syslog 代理”下 — 此连接器需要在“Imperva SecureSphere MX”管理控制台上创建“操作接口”和“操作集”。    按照 Imperva 的说明[启用将 Imperva WAF 网关警报记录到 Azure Sentinel 的功能](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)。 此配置应该包含以下元素：
        - 日志目标 – 日志转发服务器的主机名和/或 IP 地址
        - 协议和端口 – TCP 514
        - 日志格式 – CEF
        - 日志类型 – 所有可用类型

    1. 在“3. 验证连接”下 — 通过复制连接器页上的命令并在日志转发器上运行以验证数据引入。 有关更详细的说明和介绍，请参阅 Azure Sentinel 文档中的“[验证 CEF 连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)”。

        可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会出现在“CommonSecurityLog”表的“Azure Sentinel”部分的“日志”中 。

若要在 Log Analytics 中查询 Imperva WAF 网关数据，请将以下内容复制到查询窗口中，根据所做的选择应用其他筛选器：

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

请参阅连接器页中的“后续步骤”选项卡，以获取更多有用的查询示例。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何将 Imperva WAF 网关连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。