---
title: 将 Cisco Umbrella 连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Cisco Umbrella 数据连接器将 Umbrella 数据拉取到 Azure Sentinel 中。 在工作簿中查看 Umbrella 数据，创建警报，并改进调查。
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566726"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>将 Cisco Umbrella 连接到 Azure Sentinel

> [!IMPORTANT]
> Cisco Umbrella 连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Cisco Umbrella 连接器可让你轻松地将所有 Cisco Umbrella 安全性解决方案日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报，并改进调查。 Cisco Umbrella 和 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-cisco-umbrella"></a>配置和连接 Cisco Umbrella

Cisco Umbrella 可以直接将日志集成和导出到 Azure Sentinel。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“Cisco Umbrella（预览版）”，然后选择“打开连接器页”。

1. 按照连接器页的“配置”部分中所述的步骤进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在下列一个或多个表的“CustomLogs”部分下的“日志”中： 
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Cisco Umbrella 数据连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
