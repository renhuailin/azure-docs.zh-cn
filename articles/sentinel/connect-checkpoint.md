---
title: 将 Check Point 数据连接到 Azure Sentinel | Microsoft Docs
description: 将 Check Point 设备配置为通过 Syslog 代理将 Syslog 消息以 CEF 格式转发到 Azure Sentinel 工作区。
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
ms.openlocfilehash: e3527e098e282d9c8f31069f7b747aa33f9d0349
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253767"
---
# <a name="connect-check-point-to-azure-sentinel"></a>将 Check Point 连接到 Azure Sentinel



本文介绍如何将 Check Point 设备连接到 Azure Sentinel。 使用 Check Point 数据连接器，可以将 Check Point 日志轻松连接到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。 使用 Azure Sentinel 上的 Check Point 可以更深入地了解组织的 Internet 使用情况，并增强其安全操作功能。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="forward-check-point-logs-to-the-syslog-agent"></a>将 Check Point 日志转发到 Syslog 代理

将 Check Point 设备配置为通过 Syslog 代理将 Syslog 消息以 CEF 格式转发到 Azure 工作区。

1. 转到 [Check Point 日志导出](https://aka.ms/asi-syslog-checkpoint-forwarding)。
1. 向下滚动到“基本部署”，按照说明根据以下准则设置连接：
   - 将“Syslog 端口”设置为“514”或在代理上设置的端口。
     - 将 CLI 中的“名称”和“目标服务器 IP 地址”替换为 Syslog 代理名称和 IP 地址。
     - 将格式设置为“CEF”。
1. 如果使用的是 R77.30 或 R80.10 版本，请向上滚动至“安装”，然后按照说明为你的版本安装日志导出程序。
1. 继续执行[步骤 3：验证连接性](connect-cef-verify.md)。
 

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Check Point 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- [验证连接性](connect-cef-verify.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。