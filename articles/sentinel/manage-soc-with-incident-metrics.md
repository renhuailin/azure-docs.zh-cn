---
title: 利用 Azure Sentinel 中的事件指标更好地管理 SOC | Microsoft Docs
description: 使用 Azure Sentinel 事件指标屏幕和工作簿中的信息来帮助管理安全运营中心 (SOC)。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2021
ms.author: yelevin
ms.openlocfilehash: 5aa912e89fbbb1c219c15df8cbf3fed25868ff1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747143"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>利用事件指标更好地管理 SOC

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

作为安全运营中心 (SOC) 管理员，需要随时可以获得总体效率指标和度量值，以衡量团队的表现。 需要按许多不同的条件来查看一段时间内的事件操作，如严重性、MITRE 技巧、平均会审时间、平均解决时间等等。 Azure Sentinel 现在使用 Log Analytics 中新的“SecurityIncident”表和架构以及附带的“安全运营效率”工作簿提供此数据。  你将能够直观显示团队在一段时间内的表现，并使用此见解来提高效率。 还可以针对事件表来写入和使用自己的 KQL 查询，以创建符合特定审核需求和 KPI 的自定义工作簿。

## <a name="use-the-security-incidents-table"></a>使用安全事件表

“SecurityIncident”表内置于 Azure Sentinel 中。 可以在“日志”下的“SecurityInsights”集合中找到该表及其他表。  可以像在 Log Analytics 中的任何其他表一样对其进行查询。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="安全事件表":::

每次创建或更新事件时，都会向表中添加一个新的日志条目。 这样可以跟踪对事件所做的更改，并允许更强大的 SOC 指标，但为此表构造查询时需要注意此点，因为可能需要删除事件的重复条目（取决于所运行的确切查询）。 

例如，如果想要返回按事件编号排序的所有事件的列表，但只想返回每个事件的最新日志，则可以结合使用 KQL [summarize 运算符](/azure/data-explorer/kusto/query/summarizeoperator)和 `arg_max()` [聚合函数](/azure/data-explorer/kusto/query/arg-max-aggfunction)来执行此操作：

```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>更多示例查询

事件状态 - 给定时间框架内按状态和严重性显示所有事件：

```Kusto
let startTime = ago(14d);
let endTime = now();
SecurityIncident
| where TimeGenerated >= startTime
| summarize arg_max(TimeGenerated, *) by IncidentNumber
| where LastModifiedTime  between (startTime .. endTime)
| where Status in  ('New', 'Active', 'Closed')
| where Severity in ('High','Medium','Low', 'Informational')
```

平均关闭时间：
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

平均会审时间：
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>安全运营效率工作簿

为了补充 **SecurityIncidents** 表，我们提供了一个现成的 **安全运营效率** 工作簿模板，可用于监视 SOC 操作。 工作簿包含以下指标： 
- 随时间创建的事件 
- 通过关闭分类、严重性、所有者和状态创建的事件 
- 平均会审时间 
- 平均关闭时间 
- 随时间按严重性、所有者、状态、产品和策略创建的事件 
- 会审时间百分位数 
- 关闭时间百分位数 
- 每个所有者的平均会审时间 
- 最近活动 
- 最近关闭分类  

通过从 Azure Sentinel 导航菜单选择“工作簿”并选择“模板”选项卡，可找到此新工作簿模板。从库中选择“安全运营效率”并单击“查看保存的工作簿”和“查看模板”按钮    。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="安全事件工作簿库":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="安全事件工作簿完成":::

可以使用模板来创建自己的自定义工作簿，以满足特定需求。

## <a name="securityincidents-schema"></a>SecurityIncidents 架构

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>后续步骤

- 若要开始使用 Azure Sentinel，需要订阅 Microsoft Azure。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/free/)。
- 了解如何[将数据载入到 Azure Sentinel](quickstart-onboard.md)，以及[获取数据和潜在威胁的见解](get-visibility.md)。
