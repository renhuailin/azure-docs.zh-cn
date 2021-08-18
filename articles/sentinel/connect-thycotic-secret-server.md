---
title: 将 Thycotic Secret Server 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Thycotic Secret Server 数据连接器将 Thycotic Secret Server 日志拉取至 Azure Sentinel 中。 查看工作簿中的 Thycotic Secret Server 数据，创建警报，并改进调查。
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
ms.openlocfilehash: b023745d8882d639cf9a39fd483dd7b82d1a782d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778203"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>将 Thycotic Secret Server 连接到 Azure Sentinel

> [!IMPORTANT]
> Thycotic Secret Server 连接器当前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Thycotic Secret Server 设备连接到 Azure Sentinel。 使用 Thycotic Secret Server 数据连接器，可以轻松地将 Thycotic Secret Server 日志与 Azure Sentinel 连接，这样，你就可以查看工作簿中的数据，使用这些数据来创建自定义警报，并整合这些数据来改进调查。 Thycotic 与 Azure Sentinel 之间的集成可利用 CEF 数据连接器正确分析和显示 Secret Server Syslog 消息。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。

- 必须将 Thycotic Secret Server 配置为通过 Syslog 导出日志。

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>将 Thycotic Secret Server 日志发送到 Azure Sentinel

要将其日志发送到 Azure Sentinel，请将 Thycotic Secret Server 配置为以 CEF 格式将 Syslog 消息发送到基于 Linux 的日志转发服务器（运行 rsyslog 或 syslog-ng）。 此服务器上将会安装 Log Analytics 代理，该代理会将这些日志转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“Thycotic Secret Server (预览版)”，然后选择“打开连接器页”  。

1. 在以下“配置”下，按照“说明”选项卡中的说明进行操作 ：

    1. 在“1. Linux Syslog 代理配置”下 — 如果你还没有运行日志转发器，或者如果你需要其他日志转发器，请执行此步骤。 有关更详细的说明和介绍，请参阅 Azure Sentinel 文档中的[步骤 1：部署日志转发器](connect-cef-agent.md)。

    1. 具体请参见“2. 将通用事件格式 (CEF) 日志转发到 Syslog 代理”- 按照 Thycotic 的说明来[配置 Secret Server](https://docs.thycotic.com/ssi/1.0.0/splunk/splunk-on-prem/config/syslog-events.md)。 此配置应该包含以下元素：
        - 日志目标 – 日志转发服务器的主机名和/或 IP 地址
        - 协议和端口 – TCP 514（如果使用建议的其他协议和端口，请确保在日志转发服务器上的 syslog 守护程序中进行并行更改）
        - 日志格式 – CEF
        - 日志类型 – 所有可用类型

    1. 在“3. 验证连接”下 — 通过复制连接器页上的命令并在日志转发器上运行以验证数据引入。 有关更详细的说明和介绍，请参阅 Azure Sentinel 文档中的[步骤 3：验证连接性](connect-cef-verify.md)。

        可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

在成功建立连接后，数据会出现在“CommonSecurityLog”表的“Azure Sentinel”部分下的“日志”中 。

若要在 Log Analytics 中查询 Thycotic Secret Server 数据，请将以下内容复制到查询窗口中，根据所做的选择应用其他筛选器：

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

请参阅连接器页中的“后续步骤”选项卡，获取一些有用的工作簿和查询示例。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Thycotic Secret Server 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。
