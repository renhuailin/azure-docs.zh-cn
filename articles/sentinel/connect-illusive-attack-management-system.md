---
title: 将 Illusive Attack Management System 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Illusive Attack Management System 数据连接到 Azure Sentinel。
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: a2c4f48f77442d888be006c878a7e55d9a2b4fd2
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254047"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>将 Illusive Attack Management System 连接到 Azure Sentinel。

> [!IMPORTANT]
> Azure Sentinel 中的 Illusive Attack Management System 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 [Illusive Attack Management System](https://www.illusivenetworks.com/technology/platform/attack-detection-system) 连接到 Azure Sentinel。 借助 Illusive Attack Management System 数据连接器，可以将 Illusive 的攻击面分析数据和事件日志与 Azure Sentinel 共享，并在专用仪表板中查看此信息，从而在 ASM 仪表板中深入了解组织的攻击面风险，并在 ADS 仪表板中跟踪组织网络中未经授权的横向移动。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>将 Illusive Attack Management System 日志转发到 Syslog 代理  

将 Attack Management System 配置为通过 Syslog 代理将 Syslog 消息以 CEF 格式转发到 Azure 工作区。

1. 登录到 Illusive 控制台，导航到“设置”->“报告”。

1. 查找 Syslog 服务器。

1. 提供以下信息：
   - 主机名：Linux Syslog 代理 IP 地址或 FQDN 主机名
   - 端口：514
   - 协议：TCP
   - 审核消息：将审核消息发送到服务器

1. 若要添加 syslog 服务器，请单击“添加”。

1. 若要将“日志”中的相关架构用于 Illusive Attack Management System，请搜索“CommonSecurityLog”。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Illusive Attack Management System 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。