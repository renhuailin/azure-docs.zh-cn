---
title: 将 Orca 安全警报连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Orca 安全警报数据连接到 Azure Sentinel，以查看仪表板、创建自定义警报并改进调查。
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
ms.openlocfilehash: 18a50e3d06135f2c17b7e7c5b2969be33f408c62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093074"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>将 Orca 安全警报连接到 Azure Sentinel 

> [!IMPORTANT]
> Azure Sentinel 中的 Orca 安全警报连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Orca 安全警报连接器，可以轻松地将 [Orca 警报](https://orca.security/)安全解决方案警报与 Azure Sentinel 连接，这样，你就可以在工作簿中查看警报、使用它们创建自定义警报，并进行整合以改进调查。 Orca 安全警报与 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-orca-security-alerts"></a>配置和连接 Orca 安全警报

Orca 安全警报可以直接将日志集成和导出到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后依次选择“Orca 安全警报”和“打开连接器页”。

2. 请参阅 https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration 以从 Orca 平台完成集成。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在“OrcaAlerts_CL”表的“CustomLogs”下的 Log Analytics 中。
若要将 Log Analytics 中的相关架构用于 Orca 警报，请搜索 `OrcaAlerts_CL`。

## <a name="validate-connectivity"></a>验证连接
可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Orca 安全警报连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

