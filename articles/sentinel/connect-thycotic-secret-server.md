---
title: 将 Thycotic Secret Server 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Thycotic Secret Server 数据连接器将 Thycotic Secret 服务器日志请求到 Azure Sentinel。 查看工作簿中的 Thycotic Secret Server 数据，创建警报，并改进调查。
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567873"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>将 Thycotic 密钥服务器连接到 Azure Sentinel

> [!IMPORTANT]
> Thycotic 机密服务器连接器当前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍了如何将 Thycotic Secret 服务器设备连接到 Azure Sentinel。 Thycotic Secret Server 数据连接器可让你轻松地将 Thycotic 机密服务器日志与 Azure Sentinel 连接，以便你可以查看工作簿中的数据，使用它创建自定义警报，并将其合并以改进调查。 Thycotic 和 Azure Sentinel 之间的集成利用 CEF 数据连接器正确分析和显示密钥服务器 Syslog 消息。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 您必须对工作区的共享密钥具有读取权限。

- 你的 Thycotic 机密服务器必须配置为通过 Syslog 导出日志。

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>将 Thycotic Secret 服务器日志发送到 Azure Sentinel

若要将其日志导入 Azure Sentinel，请将 Thycotic 机密服务器配置为以 CEF 格式将 Syslog 消息发送到运行 rsyslog 或 Syslog-ng)  (基于 Linux 的日志转发服务器。 此服务器上安装了 Log Analytics 代理，代理将日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **Thycotic Secret Server (预览")**，然后单击 "连接器" **页面**。

1. 按照 "**配置**" 下的 "**说明**" 选项卡中的说明进行操作：

    1. 小于 **1。Linux Syslog 代理配置** -如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更详细的说明和说明，请参阅第1步：在 Azure Sentinel 文档中 [部署日志转发器](connect-cef-agent.md) 。

    1. 低于 **2。将 CEF) 日志的常见事件 (格式转发到 Syslog 代理** -按照 Thycotic 的说明 [配置密钥服务器](https://thy.center/ss/link/syslog)。 此配置应包含以下元素：
        - 日志目标–日志转发服务器的主机名和/或 IP 地址
        - 协议和端口– **TCP 514** (否则，请确保在日志转发服务器上的 syslog 后台程序中进行并行更改) 
        - 日志格式– CEF
        - 日志类型-所有可用

    1. **3。验证连接**-通过复制 "连接器" 页上的命令并在日志转发器上运行，验证数据引入。 有关更详细的说明和说明，请参阅 [第3步：验证](connect-cef-verify.md) Azure Sentinel 文档中的连接。

        可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**日志**" 的 " *CommonSecurityLog* " 表中的 " **Azure Sentinel** " 部分下。

若要在 Log Analytics 中查询 Thycotic Secret Server data，请将以下内容复制到查询窗口中，并在选择时应用其他筛选器：

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的工作簿和查询示例。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Thycotic 密钥服务器连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。