---
title: 将 Cisco 伞连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Cisco 伞数据连接器将伞数据拉入 Azure Sentinel。 在工作簿中查看伞数据、创建警报并改进调查。
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566726"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>将 Cisco 伞连接到 Azure Sentinel

> [!IMPORTANT]
> Cisco 伞连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

Cisco 伞连接器可让你轻松地将所有 Cisco 伞安全解决方案日志与 Azure Sentinel 连接起来，查看仪表板、创建自定义警报，并改善调查。 Cisco 伞和 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-cisco-umbrella"></a>配置和连接 Cisco 伞

Cisco 伞可以直接将日志集成到 Azure Sentinel 中并将其导出。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **Cisco 伞 (预览")** ，然后单击 "连接器" **页**。

1. 按照 "连接器" 页的 " **配置** " 部分中所述的步骤进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志**" 下的 " **CustomLogs** " 部分中的一个或多个下表中：
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Cisco 伞数据连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
