---
title: 将 Squadra Technologies secRMM 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Squadra Technologies secRMM 数据连接到 Azure Sentinel。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: bd599fbfc712d85ec5fac0a3aa6f9f1cd320d91c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253415"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>将 Squadra Technologies secRMM 数据连接到 Azure Sentinel 

Squadra Technologies secRMM 连接器可让你轻松地将 Squadra Technologies secRMM 安全解决方案日志连接到 Azure Sentinel。 它允许你查看仪表板、创建自定义警报和改进调查。 此连接器可让你深入了解 USB 可移动存储事件。 Squadra Technologies secRMM 和 Azure Sentinel 之间的集成利用 REST API。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>配置并连接 Squadra Technologies secRMM 

Squadra Technologies secRMM 可以直接将日志集成到 Azure Sentinel 并将其导出。
1. 在 Azure Sentinel 门户中，单击数据连接器，然后选择 Squadra Technologies secRMM 并打开连接器页。

2. 按照 [Azure Sentinel 的 Squadra Technologies 加入指南](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)中所述的步骤，在 Azure Sentinel 中获取 Squadra secRMM 数据。   

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 `secRMM_CL` 表的“自定义日志”部分的“日志”中。

若要查询 Squadra Technologies secRMM 日志，请在查询窗口的顶部输入表名称。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Squadra Technologies secRMM 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。