---
title: 将 Barracuda 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Barracuda Web 应用程序防火墙 (WAF) 连接器将 Barracuda 日志与 Azure Sentinel 连接。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 187df87b841fdab877181ed6c381bd0d018660b4
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252674"
---
# <a name="connect-your-barracuda-waf-appliance"></a>连接 Barracuda WAF 设备 

通过 Barracuda Web 应用程序防火墙 (WAF) 连接器，可以轻松地将你的 Barracuda 日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报和改进调查。 这样，用户就可以更深入地了解组织的网络并改善安全操作功能。 Azure Sentinel 利用 Barracuda 与 Log Analytics 代理之间的本机集成，提供无缝集成。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-barracuda-waf"></a>配置和连接 Barracuda WAF

Barracuda Web 应用程序防火墙可通过 Log Analytics 代理直接将日志集成到 Azure Sentinel 并将其导出。

1. 请转到 [Barracuda WAF 配置流](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，然后按照说明使用以下参数设置连接：

    - 工作区 ID：从 Azure Sentinel Barracuda 连接器页面复制工作区 ID 的值。

    - 主键：从 Azure Sentinel Barracuda 连接器页面复制主键的值。

1. 若要将 Log Analytics 中的相关架构用于 Barracuda 事件，请搜索 CommonSecurityLog 和 barracuda_CL。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Barracuda 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。