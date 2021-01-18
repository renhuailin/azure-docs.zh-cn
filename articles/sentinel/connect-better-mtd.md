---
title: 将更好的移动威胁防御 (MTD) 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用更好的移动威胁防御 (MTD) 数据连接器将 MTD 日志请求到 Azure Sentinel。 查看工作簿中的 MTD 数据、创建警报并改善调查。
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
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541535"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>将更好的移动威胁防御 (MTD) 连接到 Azure Sentinel

> [!IMPORTANT]
>  (MTD) 连接器的更好的移动威胁防御目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

利用更好的移动威胁防御 (MTD) 连接器，可以轻松地将所有更好的 MTD 安全解决方案日志与 Azure Sentinel 连接起来，查看仪表板，创建自定义警报，并改善调查。 更好的移动威胁防御和 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-better-mobile-threat-defense"></a>配置和连接更好的移动威胁防御

更好的 MTD 可以将日志直接集成到 Azure Sentinel。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **更好的移动威胁防御 (MTD) " () 预览** "，然后单击" **连接器 "页面**。

1. 按照 [更好的 MTD 文档中](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) 的 "连接器" 页上的步骤进行操作，并在更好的 MTD 控制台上完成集成。

    当请求输入 **工作区 ID** 和 **主密钥** 值时，请从 Azure Sentinel 连接器页面复制这些值，并将其粘贴到更好的 MTD 配置中。

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{工作区 ID 和主密钥}":::

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志**" 下的 " **CustomLogs** " 部分中的一个或多个下表中：
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

若要在分析规则中查询更好的 MTD 日志、搜寻查询或 Azure Sentinel 中的任何其他位置，请在 "查询" 窗口的顶部输入以上表名称之一。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将更好的移动威胁防御 (MTD) 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
