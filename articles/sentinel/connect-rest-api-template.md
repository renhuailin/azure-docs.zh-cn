---
title: 将数据源连接到 Azure Sentinel 的数据收集器 API 以引入数据 | Microsoft Docs
description: 了解如何将外部系统连接到 Azure Sentinel 的数据收集器 API，以将其日志数据引入工作区中的自定义日志。
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
ms.date: 08/17/2021
ms.author: yelevin
ms.openlocfilehash: cda5595eb2c8b72f6b38ade1feefe84904312412
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260958"
---
# <a name="connect-your-data-source-to-azure-sentinels-data-collector-api-to-ingest-data"></a>将数据源连接到 Azure Sentinel 的数据收集器 API 以引入数据

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

由第三方供应商构建的 API 集成从其产品的数据源中提取数据，并连接到 Azure Sentinel 的 [Azure Monitor 数据收集器 API](../azure-monitor/logs/data-collector-api.md)，以将数据推送到 Azure Sentinel 工作区中的自定义日志表。

大多数情况下，可以在每个供应商的文档中找到配置这些数据源以连接到 Azure Sentinel 所需的所有信息。

查看[数据连接器参考](data-connectors-reference.md)页中的产品部分，了解其中可能出现的任何额外说明，并获得供应商说明的链接。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。 [详细了解工作区密钥](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)。

## <a name="configure-and-connect-your-data-source"></a>配置和连接数据源

1. 在 Azure Sentinel 门户中，在导航菜单中选择“数据连接器”。

1. 从数据连接器库中选择你的产品项，然后选择“打开连接器页面”按钮。

1. 按照连接器页面上显示的任何步骤进行操作，或者按照页面上显示的供应商说明的链接进行操作。

1. 当系统要求输入工作区 ID 和主键时，请从数据连接器页复制它们并按照供应商的说明将它们粘贴到配置中。 请参阅以下示例。

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="工作区 ID 和主键":::

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在“自定义日志”部分的“日志”中 。 有关表名称的信息，请查看[数据连接器参考](data-connectors-reference.md)页的产品部分。

若要查询产品中的数据，请在查询中使用这些表名称。

此过程可能需要长达 20 分钟，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将外部数据源连接到 Azure Sentinel 的数据收集器 API。 若要充分利用这些数据连接器内置的功能，请选择“数据连接器”页上的“后续步骤”选项卡。 可以在其中找到一些现成的示例查询、工作簿和分析规则模板，以便开始查找有用的信息。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。
