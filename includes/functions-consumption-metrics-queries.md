---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168098"
---
#### <a name="determine-memory-usage"></a>确定内存用量 

在“监视”下选择“日志(分析)”，复制以下遥测查询并将其粘贴到查询窗口中，然后选择“运行”。************ 此查询返回每个采样时间的总内存用量。

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

结果类似于以下示例：

| 时间戳 \[UTC\]          | name          | 值       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 AM | 专用字节数 | 209,932,288 |
| 9/12/2019, 1:06:14\.994 AM | 专用字节数 | 212,189,184 |
| 9/12/2019, 1:06:30\.010 AM | 专用字节数 | 231,714,816 |
| 9/12/2019, 1:07:15\.040 AM | 专用字节数 | 210,591,744 |
| 9/12/2019, 1:12:16\.285 AM | 专用字节数 | 216,285,184 |
| 9/12/2019, 1:12:31\.376 AM | 专用字节数 | 235,806,720 |

#### <a name="determine-duration"></a>确定持续时间 

Azure Monitor 可跟踪资源级指标，对于函数来说，就是跟踪函数应用指标。 Application Insights 集成会发出每个函数的指标。 下面是一个示例分析查询，可用于获取函数的平均持续时间：

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |
