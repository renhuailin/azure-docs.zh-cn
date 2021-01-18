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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541150"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>将超出安全 beSECURE 的连接连接到 Azure Sentinel

> [!IMPORTANT]
> Security beSECURE connector 之外目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

除 Security beSECURE 连接器之外，你还可以轻松地将所有 beSECURE 安全解决方案日志与 Azure Sentinel 连接起来，查看仪表板、创建自定义警报，以及改进调查。 BeSECURE 与 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-besecure"></a>配置并连接 beSECURE

beSECURE 可以将日志与 Azure Sentinel 直接集成和导出。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中选择 " **安全 beSECURE" 之外 (预览 ")** ，然后单击" **连接器 "页**。

1. 按照以下步骤配置你的 beSECURE 解决方案，以将扫描结果、扫描状态和审核跟踪日志发送到 Azure Sentinel。

    **访问 "集成" 菜单：**
    1. 单击 "更多" 菜单选项

    1. Select Server

    1. 选择集成

    1. 启用 Azure Sentinel 

    **通过 Azure Sentinel 设置提供 beSECURE：**

      从 Azure Sentinel 连接器页面复制 " *工作区 ID* " 和 " *主密钥* " 值，将其粘贴到 "beSECURE" 配置中，然后单击 " **修改**"。
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{工作区 ID 和主密钥}":::

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志**" 下的 " **CustomLogs** " 部分中的一个或多个下表中：
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

若要在 Azure Sentinel 中查询分析规则中的 beSECURE 日志、搜寻查询、调查或其他任何位置，请在 "查询" 窗口的顶部输入以上表名称之一。

## <a name="validate-connectivity"></a>验证连接
可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 beSECURE 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
