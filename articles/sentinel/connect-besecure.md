---
title: 将 Beyond Security beSECURE 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Beyond Security beSECURE 数据连接器将 beSECURE 日志提取到 Azure Sentinel。 在工作簿中查看 beSECURE 数据，创建警报，并改进调查。
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
ms.openlocfilehash: c7daf98effad685d5e0044df3c416f3023fe42ba
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253184"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>将 Beyond Security beSECURE 连接到 Azure Sentinel

> [!IMPORTANT]
> Beyond Security beSECURE 连接器当前为“预览版”。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

使用 Beyond Security beSECURE 连接器，可以轻松将所有 beSECURE 安全解决方案日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报及改进调查。 beSECURE 和 Azure Sentinel 之间利用 REST API 集成。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-besecure"></a>配置和连接 beSECURE

beSECURE 可以与日志集成，并可将日志直接导出到 Azure Sentinel。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在“数据连接器”库中，选择“Beyond Security beSECURE(预览版)”，然后选择“打开连接器页”  。

1. 请按照以下步骤将 beSECURE 解决方案配置为向 Azure Sentinel 发送扫描结果、扫描状态和审核日志。

    **访问“集成”菜单：**
    1. 单击“更多”菜单选项

    1. Select Server

    1. 选择“集成”

    1. 启用 Azure Sentinel 

    **为 beSECURE 提供 Azure Sentinel 设置：**

      从 Azure Sentinel 连接器页复制“工作区 ID”和“主键” 值，将它们粘贴到 beSECURE 配置中，然后单击“修改”。
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{工作区 ID 和主键}":::

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在“日志”中“CustomLogs”部分下的一个或多个表中，如下所示： 
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

要在分析规则、搜寻查询、调查或 Azure Sentinel 中的任何其他位置查询 beSECURE 日志，请在查询窗口顶部输入上述表名称之一。

## <a name="validate-connectivity"></a>验证连接
可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤
在本文档中，你学习了如何将 beSECURE 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。