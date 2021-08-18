---
title: 将 Symantec ProxySG 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Symantec ProxySG 数据连接到 Azure Sentinel。
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
ms.openlocfilehash: 3a04db6700af508114e22a33200da3d7d2ebc0c3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253163"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>将 Symantec ProxySG 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Symantec ProxySG 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 [Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) 设备连接到 Azure Sentinel。 通过 Symantec ProxySG 数据连接器，可以将 Symantec ProxySG 日志轻松连接到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。 Symantec ProxySG 和 Azure Sentinel 之间的集成会使用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>将 Symantec ProxySG 日志转发到 Syslog 代理  

将 Symantec ProxySG 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure 工作区。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后选择“Symantec ProxySG”连接器 。

1. 选择“打开连接器页”。

1. 按照“Symantec ProxySG”页上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在 Syslog 下的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Symantec ProxySG 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。