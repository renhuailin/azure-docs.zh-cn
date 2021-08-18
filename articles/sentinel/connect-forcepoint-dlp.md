---
title: 将 Forcepoint DLP 连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Forcepoint DLP 连接到 Azure Sentinel。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 56ed14cf3010b967965eddf0605d36bafebdbb57
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253539"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>将 Forcepoint DLP 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Forcepoint 数据丢失防护 (DLP) 数据连接器目前为公共预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


Forcepoint DLP 连接器可让你将 DLP 事件数据自动导出到 Azure Sentinel。 你可以使用它来了解用户活动和数据丢失事件。 它还支持与来自 Azure 工作负荷和其他源的数据进行关联，并通过 Azure Sentinel 内的工作簿改进监视功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-forcepoint-dlp"></a>配置并连接 Forcepoint DLP

将 Forcepoint DLP 配置为通过 REST API 以 JSON 格式将事件数据转发到你的 Azure 工作区，如 [Forcepoint DLP 集成指南](https://frcpnt.com/dlp-sentinel)所述。


## <a name="find-your-data"></a>查找数据

设置 Forcepoint DLP 连接器后，数据将出现在 Log Analytics 中的 CustomLogs ForcepointDLPEvents_CL 下。


若要将 Log Analytics 中的相关架构用于 Forcepoint DLP，请搜索“ForcepointDLPEvents_CL”。


## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Forcepoint DLP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。