---
title: 将 NXLog Windows DNS 日志数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 NXLog DNS 日志数据连接器将 Windows DNS 事件推送到 Azure Sentinel。 在工作簿中查看 Windows DNS 数据，创建警报，并改进调查。
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744545"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>将 NXLog Windows DNS 日志连接到 Azure Sentinel

> [!IMPORTANT]
> NXLog DNS 日志连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

通过 [NXLOG DNS 日志](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) 连接器，可以将所有 Windows DNS 服务器事件轻松地实时导出到 Azure Sentinel，使你能够深入了解组织中的域名服务器活动。 它采用 [Windows (ETW) 的高性能事件跟踪功能 ](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) ，实时收集审核和分析 DNS 服务器事件，并支持带有自定义字段的事件扩充。 NXLog DNS 日志和 Azure Sentinel 之间的集成可通过 REST API 来实现。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-nxlog-dns-logs"></a>配置并连接 NXLog DNS 日志

可以将 NXLog 配置为将事件以 JSON 格式直接发送到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，然后选择 " **NXLog DNS 日志** 连接器"。

1. 选择 " **打开连接器" 页面**。

1. 按照 *NXLog 用户指南* 集成主题中的分步说明 [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) 通过 REST API 配置转发。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**自定义日志**" 部分下的 "**日志**" 下的 " *DNS_Logs_CL* " 表中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 NXLog 将 Windows DNS 日志引入 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
