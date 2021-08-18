---
title: 将 Cisco Meraki 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Cisco Meraki 连接器将 Cisco Meraki 日志拉取到 Azure Sentinel 中。 在工作簿中查看 Cisco Meraki 数据、创建警报，并改进调查。
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
ms.openlocfilehash: 64828011cc92145e6635c995294f234754999a8b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179378"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>将 Cisco Meraki 连接到 Azure Sentinel

> [!IMPORTANT]
> Cisco Meraki 连接器目前处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍了如何将 Cisco Meraki (MX/MS/MR) 设备连接到 Azure Sentinel。 使用 Cisco Meraki 数据连接器，可以将 Cisco Meraki 日志轻松连接到 Azure Sentinel，以查看仪表板、创建自定义警报并改进调查。 通过集成 Cisco Meraki 和 Azure Sentinel，可以使用安装了 Log Analytics 代理的 Syslog 服务器。 此外，还可以使用基于 Kusto 函数的定制日志分析程序。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- Cisco Meraki 解决方案必须配置为通过 Syslog 导出日志。

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>通过 Syslog 代理将 Cisco Meraki 日志发送到 Azure Sentinel  

将 Cisco Meraki 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在“数据连接器”库中，选择“Cisco Meraki (预览)”连接器，然后单击“打开连接器页”。

1. 按照“Cisco Meraki”连接器页上的说明操作：

    1. 安装并载入适用于 Linux 的代理

        - 选择 Azure Linux VM 或非 Azure Linux 虚拟机（物理或虚拟）。

    1. 配置要收集的日志

        - 在“工作区代理配置”中，选择设备和严重性。

    1. 配置并连接 Cisco Meraki 设备

        - 按照[这些说明](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API)操作，将 Cisco Meraki 设备配置为转发 syslog。 对于远程服务器，使用安装了 Linux 代理的 Linux 虚拟机的 IP 地址。

## <a name="validate-connectivity-and-find-your-data"></a>验证连接并查找数据

最长可能需要 20 分钟的时间，日志才会开始显示在 Azure Sentinel 中。 

在成功建立连接后，数据显示在“日志”中“日志管理”部分下的“Syslog”表内。

此数据连接器依赖基于 Kusto 函数的分析程序来按预期运行。 [按照这些步骤操作](https://aka.ms/sentinel-ciscomeraki-parser)，以创建 CiscoMeraki Kusto 函数别名。 然后，可以在查询窗口中键入“`CiscoMeraki`”来查询数据。

有关一些实用查询示例，请参阅连接器页中的“后续步骤”选项卡。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何将 Cisco Meraki 数据连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。