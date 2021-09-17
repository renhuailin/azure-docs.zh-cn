---
title: 将 Forcepoint 产品连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Forcepoint 产品连接到 Azure Sentinel。
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
ms.openlocfilehash: b0b734bcc540651debad35606be496fdc4069b3c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695107"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>将 Forcepoint 产品连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Forcepoint 产品数据连接器目前为公共预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍了如何将 Forcepoint 产品连接到 Azure Sentinel。 

通过 Forcepoint 数据连接器，可使用 Azure Sentinel 连接 Forcepoint Cloud Access Security Broker 和 Forcepoint Next Generation Firewall 日志。 通过这种方式，可将用户定义的日志实时地自动导出到 Azure Sentinel。 通过连接器，你可更深入地了解 Forcepoint 产品记录的用户活动。 它还支持与来自 Azure 工作负载和其他源的数据进一步关联，并通过 Azure Sentinel 内的工作簿改进监视功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>将 Forcepoint 产品日志转发到 Syslog 代理 

配置 Forcepoint 产品，通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 按照以下安装指南中所述，将 Forcepoint 产品设置为 Azure Sentinel 集成项：
 - [Forcepoint CASB 集成指南](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW 集成指南](https://frcpnt.com/ngfw-sentinel)

2. 搜索“CommonSecurityLog”，使用 Log Analytics 中 DeviceVendor 名称包含“Forcepoint”的相关架构。 

3. 继续[验证 CEF 连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)。



## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Forcepoint 产品连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。

- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。

- [使用工作簿](monitor-your-data.md)监视数据。