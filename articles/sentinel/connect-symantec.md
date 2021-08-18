---
title: 将 Symantec ICDx 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Symantec ICDx 连接器轻松地将所有 Symantec 安全解决方案日志与 Azure Sentinel 连接。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 22e01d8e7ba82b1be277a967036ac4a2deb4bf81
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253579"
---
# <a name="connect-your-symantec-icdx-appliance"></a>连接 Symantec ICDx 设备 



Symantec ICDx 连接器可让你轻松地将所有 Symantec 安全解决方案日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报，并改进调查。 这样，用户就可以更深入地了解组织的网络并改善安全操作功能。 Symantec ICDx 和 Azure Sentinel 之间的集成利用 REST API。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-symantec-icdx"></a>配置并连接 Symantec ICDx 

Symantec ICDx 可以直接将日志集成和导出到 Azure Sentinel。

1. 打开 ICDx 管理控制台，添加 Microsoft Azure Sentinel (Log Analytics) 转发器。
2. 单击 ICDx 导航栏中的“配置”。 
3. 在“配置”屏幕的顶部，单击“转发器”。
4. 在“转发器”下的“Microsoft Azure Sentinel (Log Analytics)”旁，单击“添加”。 
4. 在“Microsoft Azure Sentinel (Log Analytics)”窗口中，单击“显示”。 
5. 在展开到 Microsoft Azure Sentinel (Log Analytics) 的窗口顶部，执行以下操作：
    -   名称：键入不超过 30 个字符的转发器名称。 选择唯一且有意义的名称。 此名称显示在“配置”屏幕上的转发器列表和“仪表板”屏幕上的仪表板中。 例如：Microsoft Azure Log Analytics East。 此字段为必需字段。
    -   说明：键入转发器的说明。 此说明也出现在“配置”屏幕上的转发器列表中。 包括详细信息，例如要转发的事件类型和需要检查数据的组。
    -   启动类型：为转发器配置选择启动方法。 有手动和自动选项。<br>默认为“自动”。 
6. 在“事件”下，执行以下操作： 
    - 源：选择一个或多个要从中转发事件的存档。 你可以选择活动收集器存档（包括常见存档）、孤立收集器存档（即，已删除的收集器存档）、ICDx 接收程序存档或系统存档。 <br>默认为常见存档。
      > [!NOTE]
      > ICDx 接收程序存档按名称单独列出。 
 
    - 筛选器：添加指定要转发的事件子集的筛选器。 执行下列操作之一：
        - 若要选择筛选条件，请单击 Type、Attribute、Operator 和 Value。 
        - 在“筛选器”字段中，查看筛选条件。 可以在字段中直接编辑它或根据需要将其删除。
        - 单击“AND”或“OR”以添加到筛选条件。
        - 还可以单击“已保存的查询”以应用保存的查询。
    - 包含的属性：键入要包括在已转发数据中的以逗号分隔的属性列表。 包含的属性优先于排除的属性。
    - 排除的属性：键入要从已转发数据中排除的以逗号分隔的属性列表。
    - 批大小：选择每批要发送的事件数。 选项为 10、50、100、500 和 1000。<br>默认值为 100。 
    - 速率限制：选择转发事件的速率，以每秒事件数表示。 选项包括无限制、500、1000、5000、10000。 <br> 默认值为 5000。 
7. 在“Azure 目标”下，执行以下操作： 
    - 工作区 ID：从下面粘贴工作区 ID。 此字段为必需字段。
    - 主键：从下面粘贴主键。 此字段为必需字段。
    - 自定义日志名称：在要将事件转发到的 Microsoft Azure 门户 Log Analytics 工作区中键入自定义日志名称。 默认为 SymantecICDx。 此字段为必需字段。
8. 单击“保存”以完成转发器配置。 
9. 要启动转发器，请在“选项”下单击“更多”，然后单击“启动”。
10. 若要将 Log Analytics 中的相关架构用于 Symantec ICDx 事件，请搜索 SymantecICDx_CL。


## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Symantec ICDx 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。