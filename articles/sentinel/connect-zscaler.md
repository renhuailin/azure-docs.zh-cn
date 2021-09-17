---
title: 将 Zscaler 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Zscaler 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a08004abc62bfe0e01c42ea7d3e5d6eeecb89170
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695026"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>将 Zscaler Internet 访问连接到 Azure Sentinel

本文介绍了如何将 Zscaler Internet 访问设备连接到 Azure Sentinel。 通过 Zscaler 数据连接器，可以将 Zscaler Internet 访问 (ZIA) 日志轻松连接到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。 使用 Azure Sentinel 上的 Zscaler 可以更深入地了解组织的 Internet 使用情况，并增强其安全操作功能。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="configure-your-zscaler-to-send-cef-messages"></a>配置 Zscaler 以发送 CEF 消息

1. 在 Zscaler 设备上，需要设置这些值，以便设备根据 Log Analytics 代理，以要求的格式将必要的日志发送到 Azure Sentinel Syslog 代理。 你可以在设备中修改这些参数，只要你在 Azure Sentinel 代理上的 Syslog 守护程序中也修改了这些参数。
    - 协议 = TCP
    - 端口 = 514
    - 格式 = CEF
    - IP 地址 - 确保将 CEF 消息发送到专用于此目的的虚拟机 IP 地址。
 有关详细信息，请参阅 [Zscaler 和 Azure Sentinel 部署指南](https://aka.ms/ZscalerCEFInstructions)。
 
   > [!NOTE]
   > 此解决方案支持 Syslog RFC 3164 或 RFC 5424。


1. 若要将 Log Analytics 中的相关架构用于 CEF 事件，请搜索 `CommonSecurityLog`。
1. 继续[验证 CEF 连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)。


## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何将 Zscaler Interne 访问连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。