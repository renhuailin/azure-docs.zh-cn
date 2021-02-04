---
title: 将 Imperva WAF 网关设备连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Imperva WAF Gateway 连接器将 Imperva WAF 日志推送到 Azure Sentinel。 查看工作簿中的 Imperva WAF 数据、创建警报并改进调查。
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
ms.openlocfilehash: 3094d20a921f9aa13e111e7af60955ce934b91db
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566683"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>将 Imperva WAF 网关设备连接到 Azure Sentinel

> [!IMPORTANT]
> Imperva WAF 网关连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍了如何将 Imperva WAF 网关设备连接到 Azure Sentinel。 Imperva WAF Gateway 数据连接器可让你轻松地使用 Azure Sentinel 连接 Imperva WAF 网关日志，这样，你就可以在工作簿中查看数据，使用它创建自定义警报，并将其合并以改进调查。 Imperva WAF 网关与 Azure Sentinel 之间的集成利用了 CEF 格式的 Syslog、基于 Linux 的日志转发器和 Log Analytics 代理。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 您必须对工作区的共享密钥具有读取权限。 [了解有关工作区密钥的详细信息](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>将 Imperva WAF 网关日志发送到 Azure Sentinel

若要将其日志导入 Azure Sentinel，请将 Imperva WAF Gateway 设备配置为以 CEF 格式将 Syslog 消息发送到基于 Linux 的日志转发服务器 (运行 rsyslog 或 Syslog-ng) 。 此服务器上安装了 Log Analytics 代理，代理将日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **Imperva WAF Gateway (预览")**，然后单击 "连接器" **页**。

1. 按照 "**配置**" 下的 "**说明**" 选项卡中的说明进行操作：

    1. 小于 **1。Linux Syslog 代理配置** -如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更详细的说明和说明，请参阅第1步：在 Azure Sentinel 文档中 [部署日志转发器](connect-cef-agent.md) 。

    1. 低于 **2。将 CEF) 日志的常见事件 (格式转发到 Syslog 代理**-此连接器需要在 **IMPERVA SecureSphere MX** 管理控制台上创建 **操作接口** 和 **操作集**。 按照 Imperva 的说明， [启用 IMPERVA WAF 网关警报日志记录到 Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)。 此配置应包含以下元素：
        - 日志目标–日志转发服务器的主机名和/或 IP 地址
        - 协议和端口– TCP 514
        - 日志格式– CEF
        - 日志类型-所有可用

    1. **3。验证连接**-通过复制 "连接器" 页上的命令并在日志转发器上运行，验证数据引入。 有关更详细的说明和说明，请参阅 [第3步：验证](connect-cef-verify.md) Azure Sentinel 文档中的连接。

        可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**日志**" 的 " *CommonSecurityLog* " 表中的 " **Azure Sentinel** " 部分下。

若要在 Log Analytics 中查询 Imperva WAF Gateway 数据，请将以下内容复制到查询窗口中，并在选择时应用其他筛选器：

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

有关更有用的查询示例，请参阅连接器页中的 " **后续步骤** " 选项卡。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Imperva WAF 网关连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
