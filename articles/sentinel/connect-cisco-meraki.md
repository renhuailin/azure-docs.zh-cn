---
title: 将 Cisco Meraki 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Cisco Meraki 连接器将 Cisco Meraki 日志请求到 Azure Sentinel。 查看工作簿中的 Cisco Meraki 数据，创建警报，并改进调查。
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
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744546"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>将 Cisco Meraki 连接到 Azure Sentinel

> [!IMPORTANT]
> Cisco Meraki 连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍如何将 Cisco Meraki (MX/MS/MR) 设备 () 连接到 Azure Sentinel。 Cisco Meraki 数据连接器可让你轻松地将 Cisco Meraki 日志连接到 Azure Sentinel，查看仪表板、创建自定义警报，以及改进调查。 Cisco Meraki 与 Azure Sentinel 之间的集成利用安装了 Log Analytics 代理的 Syslog 服务器。 它还使用基于 Kusto 函数的自定义的日志分析器。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的 "读取" 和 "写入" 权限。

- 必须将 Cisco Meraki 解决方案配置为通过 Syslog 导出日志。

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>通过 Syslog 代理将 Cisco Meraki 日志发送到 Azure Sentinel  

将 Cisco Meraki 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **Cisco Meraki (预览")** 连接器，然后单击 " **连接器" 页**。

1. 按照 **Cisco Meraki** connector 页面上的说明进行操作：

    1. 安装并载入适用于 Linux 的代理

        -  (物理或虚拟) 选择 Azure Linux VM 或非 Azure Linux 计算机。

    1. 配置要收集的日志

        - 选择 **工作区代理配置** 中的功能和严重性。

    1. 配置并连接 Cisco Meraki 设备 (s) 

        - 按照 [以下说明](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) 配置 Cisco Meraki 设备 (s) 转发 syslog。 对于远程服务器，请使用安装 Linux 代理的 Linux 计算机的 IP 地址。

## <a name="validate-connectivity-and-find-your-data"></a>验证连接和查找数据

可能需要长达20分钟的时间才会开始在 Azure Sentinel 中显示日志。 

成功建立连接后，数据将 **显示在 "***日志管理*" 部分中的 "日志管理" 部分下的 " *Syslog* " 表中。

此数据连接器依赖于基于 Kusto 函数的分析器来按预期方式工作。 [按照以下步骤](https://aka.ms/sentinel-ciscomeraki-parser) 创建 **CiscoMeraki** Kusto 函数别名。 然后，您可以 `CiscoMeraki` 在 "查询" 窗口中键入查询数据。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的查询示例。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Cisco Meraki 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
