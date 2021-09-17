---
title: 将 Fortinet 数据连接到 Azure Sentinel | Microsoft Docs
description: 将 Fortinet 设备连接到 Azure Sentinel 以查看仪表板、创建自定义警报和改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 9fb3de09195c8dd88110a7723cbe7b4d89e89309
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695111"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>将 Fortinet 连接到 Azure Sentinel



本文介绍了如何将 Fortinet 设备连接到 Azure Sentinel。 Fortinet 数据连接器可让你轻松地将 Fortinet 日志连接到 Azure Sentinel，查看仪表板、创建自定义警报和改进调查。 使用 Azure Sentinel 上的 Fortinet 可以更深入地了解组织的 Internet 使用情况，并增强其安全性操作功能。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>将 Fortinet 日志转发到 Syslog 代理

配置 Fortinet 以通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 在 Fortinet 设备上打开 CLI，并运行以下命令：

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - 将服务器“IP 地址”替换为代理 IP 地址。
    - 将“Syslog 端口”设置为“514”或在代理上设置的端口。
    - 若要在早期 FortiOS 版本中启用 CEF 格式，你可能需要运行命令集“csv 禁用”。
 
   > [!NOTE] 
   > 有关详细信息，请参阅 [Fortinet 文档库](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)。 选择版本，并使用“手册”和“日志消息参考”。

1. 若要在 Azure Monitor Log Analytics 中使用相关架构用于 Fortinet 事件，请搜索 `CommonSecurityLog`。

1. 继续[验证 CEF 连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)。


## <a name="next-steps"></a>后续步骤
本文章介绍了如何将 Fortinet 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。