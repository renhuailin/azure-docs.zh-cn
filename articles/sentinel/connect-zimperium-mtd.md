---
title: 将 Zimperium Mobile Threat Defense 连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Zimperium Mobile Threat Defense 连接到 Azure Sentinel。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 840d87fe1bf14e60cdfdd2438189763bcc548c3a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252466"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>将 Zimperium Mobile Threat Defense 连接到 Azure Sentinel


> [!IMPORTANT]
> Azure Sentinel 中的 Zimperium Mobile Threat Defense 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


借助 Zimperium Mobile Threat Defense 连接器，可以将 Zimperium 威胁日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 这样，可以更深入地了解组织的移动威胁态势，并增强安全操作能力。

> [!NOTE]
> 数据存储在运行 Azure Sentinel 的工作区的地理位置上。

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>配置并连接 Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense 可以将日志直接集成并导出到 Azure Sentinel 中。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后选择“Zimperium Mobile Threat Defense”。
2. 选择“打开连接器页”。
3. 按照“Zimperium Mobile Threat Defense”连接器页上的说明操作（总结如下）。
 1. 在 zConsole 中，单击导航栏上的“管理”。
 2. 单击“集成”选项卡。
 3. 依次单击“威胁报告”按钮和“添加集成”按钮。
 4. 通过从可用集成中选择“Microsoft Azure Sentinel”来创建集成，然后输入工作区 ID 和主密钥来连接到 Azure Sentinel。
 5. 也可以视需要选择将威胁数据推送到 Azure Sentinel 的筛选器级别。 

4. 有关更多信息，请参阅 [Zimperium 客户支持门户](https://support.zimperium.com)。


## <a name="find-your-data"></a>查找数据

成功建立连接后，数据显示在 Log Analytics 中的 CustomLogs ZimperiumThreatLog_CL 和 ZimperiumMitigationLog_CL 下。

若要将 Log Analytics 中的相关架构用于 Zimperium Mobile Threat Defense，请搜索 ZimperiumThreatLog_CL 和 ZimperiumMitigationLog_CL。


## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何将 Zimperium Mobile Threat Defense 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。

- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。

- [使用工作簿](monitor-your-data.md)监视数据。

若要详细了解 Zimperium，请参阅以下：

- [Zimperium](https://zimperium.com)

- [Zimperium 移动安全博客](https://blog.zimperium.com)

- [Zimperium 客户支持门户](https://support.zimperium.com)