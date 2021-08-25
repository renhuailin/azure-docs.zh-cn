---
title: 将 CyberArk EPV 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 CyberArk 企业密码保管库 (EPV) 数据连接器将其日志拉取到 Azure Sentinel。 查看工作簿中的 CyberArk EPV 数据，创建警报并改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 0b8db5dfa399aaef087adb0ec5c2d67b91fceab9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694041"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>将 CyberArk 企业密码保管库 (EPV) 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 CyberArk 企业密码保管库 (EPV) 数据连接器目前为公共预览版。 服务级别协议未随此功能一起提供。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

CyberArk Syslog 连接器可让你轻松地将所有 CyberArk 安全解决方案日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报，并改进调查。 CyberArk 与 Azure Sentinel 之间的集成可利用 CEF 数据连接器正确分析和显示 CyberArk Syslog 消息。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-cyberark-epv"></a>配置并连接 CyberArk EPV

CyberArk EPV 日志会从保管库发送到安装了 Log Analytics 代理的基于 Linux 的日志转发服务器（运行 rsyslog 或 syslog-ng），并将日志导出到 Azure Sentinel。 如果没有这样的日志转发服务器，请参阅[这些说明](connect-cef-agent.md)以获取一个并运行。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，选择“CyberArk 企业密码保管库(EPV)事件(预览)”，然后选择“打开连接器页面”  。

1. 按照 [CyberArk EPV 说明](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm)，配置将 syslog 数据发送到日志转发服务器。

1. 使用[这些说明](troubleshooting-cef-syslog.md#validate-cef-connectivity)验证连接并验证数据引入。 可能需要长达 20 分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

在成功建立连接后，数据会出现在“CommonSecurityLog”表的“Azure Sentinel”部分下的“日志”中 。

若要在 Log Analytics 中查询 CyberArk EPV 日志，请在查询窗口顶部输入 `CommonSecurityLog`。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 CyberArk 企业密码保管库日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。