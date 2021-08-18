---
title: 将 Palo Alto Networks 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Palo Alto Networks 数据连接器轻松地将 Palo Alto Networks 日志与 Azure Sentinel 连接。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4963e616b4a24ec502f0c571593e6433dcddb61c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253886"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>将 Palo Alto Networks 连接到 Azure Sentinel



本文介绍了如何将 Palo Alto Networks 设备连接到 Azure Sentinel。 通过 Palo Alto Networks 数据连接器，可以轻松地将 Palo Alto Networks 日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报和改进调查。 使用 Azure Sentinel 上的 Palo Alto Networks 可以更深入地了解组织的 Internet 使用情况，并增强其安全操作功能。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>将 Palo Alto Networks 日志转发到 Syslog 代理

将 Palo Alto Networks 配置为通过 Syslog 代理将 Syslog 消息以 CEF 格式转发到 Azure 工作区：
1.  转到[常见事件格式 (CEF) 配置指南](https://docs.paloaltonetworks.com/resources/cef)下载适用于你的设备类型的 PDF。 按照指南中的所有说明设置 Palo Alto Networks 设备以收集 CEF 事件。 

1.  转到[配置 Syslog 监视](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring)，然后按照步骤 2 和步骤 3 配置 CEF 事件转发（从 Palo Alto Networks 设备转发到 Azure Sentinel）。

    1. 确保将“Syslog 服务器格式”设置为“BSD” 。

       > [!NOTE]
       > PDF 中的复制/粘贴操作可能会更改文本并插入随机字符。 若要避免这种情况，请将文本复制到编辑器，并在粘贴之前删除任何可能会破坏日志格式的字符，如本示例中所示。
 
        ![CEF 文本复制问题](./media/connect-cef/paloalto-text-prob1.png)

1. 若要将 Log Analytics 中的相关架构用于 Palo Alto Networks 事件，请搜索 CommonSecurityLog。

1. 继续执行[步骤 3：验证连接性](connect-cef-verify.md)。




## <a name="next-steps"></a>后续步骤
在本文中，你已了解如何将 Palo Alto Networks 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。