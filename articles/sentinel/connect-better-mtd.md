---
title: 将 BETTER Mobile Threat Defense (MTD) 连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 BETTER Mobile Threat Defense (MTD) 数据连接器将 MTD 日志拉取到 Azure Sentinel 中。 在工作簿中查看 MTD 数据，创建警报，并改进调查。
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: f3f5c2c2a22dd46d008a002db64c045af8132c8d
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252472"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>将 BETTER Mobile Threat Defense (MTD) 连接到 Azure Sentinel

> [!IMPORTANT]
> BETTER Mobile Threat Defense (MTD) 连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

BETTER Mobile Threat Defense (MTD) 连接器可让你轻松地将所有 BETTER MTD 安全解决方案日志连接到 Azure Sentinel，以查看仪表板、创建自定义警报，以及改进调查。 BETTER Mobile Threat Defense 与 Azure Sentinel 之间的集成使用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-better-mobile-threat-defense"></a>配置和连接 BETTER Mobile Threat Defense

BETTER MTD 可以直接将日志集成和导出到 Azure Sentinel。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在“数据连接器”库中，选择“BETTER Mobile Threat Defense (MTD) (预览版)”，然后选择“打开连接器页”。

1. 按照连接器页和 [BETTER MTD 文档中的此页](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration)上的步骤完成 BETTER MTD 控制台上的集成。

    当要求输入“工作区 ID”和“主键”值时，请从 Azure Sentinel 连接器页复制这些值并将其粘贴到 BETTER MTD 配置中。

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{工作区 ID 和主键}":::

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在下面一个或多个表的“CustomLogs”部分的“日志”中： 
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

若要在分析规则、搜寻查询或 Azure Sentinel 中的任何其他位置查询 BETTER MTD 日志，请在查询窗口顶部输入上述表名之一。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 BETTER Mobile Threat Defense (MTD) 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。