---
title: 将 NXLog Windows DNS 日志数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 NXLog DNS 日志数据连接器将 Windows DNS 事件拉取到 Azure Sentinel 中。 在工作簿中查看 Windows DNS 数据、创建警报，并改进调查。
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
ms.openlocfilehash: b80141d79807aea89e328b166d419e583a646ad5
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253501"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>将 NXLog Windows DNS 日志连接到 Azure Sentinel

> [!IMPORTANT]
> NXLog DNS 日志连接器目前处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

使用 [NXLog DNS 日志](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html)连接器，可以轻松地将所有 Windows DNS 服务器事件实时导出到 Azure Sentinel 中，以便深入了解整个组织的域名服务器活动。 它使用高性能 [Windows 事件跟踪 (ETW)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) 实时收集审核和分析 DNS 服务器事件，并支持使用自定义字段来扩充事件。 NXLog DNS 日志和 Azure Sentinel 的集成是通过 REST API 实现。

> [!NOTE]
> 数据将会存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-nxlog-dns-logs"></a>配置并连接 NXLog DNS 日志

NXLog 可以配置为直接向 Azure Sentinel 发送 JSON 格式的事件。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后选择“NXLog DNS 日志”连接器。

1. 选择“打开连接器页”。

1. 按照“NXLog 用户指南”集成主题 [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) 中的分步说明操作，以配置通过 REST API 进行转发。

## <a name="find-your-data"></a>查找数据

在成功建立连接后，数据显示在“日志”中“自定义日志”部分下的“DNS_Logs_CL”表内。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何使用 NXLog 将 Windows DNS 日志引入到 Azure Sentinel 中。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。