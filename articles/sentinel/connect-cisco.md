---
title: 将 Cisco 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Cisco ASA 设备连接到 Azure Sentinel 以查看仪表板、创建自定义警报和改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: db10c9814c04f6e29a017b9a473cd117a0cbf2ae
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695197"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>将 Cisco ASA 连接到 Azure Sentinel



本文介绍了如何将 Cisco ASA 设备连接到 Azure Sentinel。 使用 Cisco ASA 数据连接器，可以将 Cisco ASA 日志轻松连接到 Azure Sentinel，以查看仪表板、创建自定义警报并改进调查。 使用 Azure Sentinel 上的 Cisco ASA 可以更深入地了解组织的 Internet 使用情况，并增强其安全性操作功能。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>将 Cisco ASA 日志转发到 Syslog 代理

Cisco ASA 不支持 CEF，因此会以 Syslog 的形式发送日志，Azure Sentinel 代理知道如何分析它们，就好像它们是 CEF 日志一样。 将 Cisco ASA 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure 工作区：

1. 转到[将 Syslog 消息发送到外部 Syslog 服务器](https://aka.ms/asi-syslog-cisco-forwarding)，并按照说明设置连接。 系统提示时使用以下参数：
    - 将“端口”设置为“514”或在代理中设置的端口。
    - 将 syslog_ip 设置为代理的 IP 地址。

1. 若要将 Log Analytics 中的相关架构用于 Cisco 事件，请搜索 `CommonSecurityLog`。

1. 继续执行[验证 CEF 连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Cisco ASA 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。