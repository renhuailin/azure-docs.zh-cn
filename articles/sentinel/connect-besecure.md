---
title: 超越 Security beSECURE data to Azure Sentinel 的连接 |Microsoft Docs
description: 了解如何使用超出安全 beSECURE 的数据连接器将 beSECURE 日志请求到 Azure Sentinel。 查看工作簿中的 beSECURE 数据、创建警报并改善调查。
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102801"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>将超出安全 beSECURE 的连接连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中除 Security beSECURE 数据连接器目前正在公开预览版。 此功能在提供时没有服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

除 Security beSECURE connector 以外，还可以轻松地将所有 beSECURE 安全解决方案日志与 Azure Sentinel 连接起来，查看仪表板、创建自定义警报和改进调查。 BeSECURE 与 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-besecure"></a>配置并连接 beSECURE

beSECURE 可以将日志与 Azure Sentinel 直接集成和导出。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，并选择 " **安全 beSECURE" 之外的 " (预览")** ，然后单击 "连接器" **页** 。

1. 按照以下步骤配置你的 beSECURE 解决方案，以将扫描结果、扫描状态和审核跟踪日志发送到 Azure Sentinel。

    **访问 "集成" 菜单：**
    1. 单击 "更多" 菜单选项

    1. Select Server

    1. 选择集成

    1. 启用 Azure Sentinel 

    **为 beSECURE 提供 Azure Sentinel 设置。**
      - 从 Azure Sentinel 连接器页面复制 " *工作区 ID* " 和 " *主密钥* " 值，将其粘贴到 "beSECURE" 配置中，然后单击 " **修改** "。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志** " 下的 " **CustomLogs** " 部分中的一个或多个下表中：
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

若要查询 Log Analytics 中的 beSECURE 日志，请在 "查询" 窗口顶部输入以上表名称之一。

## <a name="validate-connectivity"></a>验证连接
可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 beSECURE 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
