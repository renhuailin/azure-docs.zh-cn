---
title: 有关知识库的分析-QnA Maker
titleSuffix: Azure Cognitive Services
description: 如果你在创建 QnA Maker 服务期间已启用了 App Insights，则 QnA Maker 会存储所有聊天日志和其他遥测数据。 运行示例查询来从 App Insights 获取聊天日志。
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 5f149dd6db82b66b45a4c995e2004936481af786
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352416"
---
# <a name="get-analytics-on-your-knowledge-base"></a>获取有关知识库的分析

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

如果在 [创建 QnA Maker 服务](./set-up-qnamaker-service-azure.md)期间启用了 Application Insights，QnA Maker 将存储所有聊天日志和其他遥测。 运行示例查询，从 Application Insights 获取聊天日志。

1. 中转到 Application Insights 资源。

    ![选择你的 Application Insights 资源](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. 选择 " **日志 (分析")**。 此时将打开一个新窗口，可以在其中查询 QnA Maker 遥测数据。

3. 粘贴以下查询并运行它。

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    选择“运行”以运行查询。

    [![运行查询，确定用户的问题、解答和评分](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

QnA Maker 托管 (预览版) 使用 Azure 诊断日志记录来存储遥测数据和聊天日志。 按照以下步骤运行示例查询，以获取 QnA Maker 知识库的使用情况分析。

1. 为 QnA Maker 托管 (预览版) 服务[启用诊断日志记录](../../diagnostic-logging.md)。

2. 在上一步中，选择 " **跟踪** "， **并选择 "审核"、"RequestResponse" 和 "** 用于日志记录"

    ![在 QnA Maker managed (预览版中启用跟踪日志记录) ](../media/qnamaker-how-to-analytics-kb/qnamaker-v2-enable-trace-logging.png)

---

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>运行查询来获取有关 QnA Maker 知识库的其他分析

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

### <a name="total-90-day-traffic"></a>90 天流量总计

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>给定时间段内的问题流量总计

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>用户流量

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>问题的延迟分布

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>未答复的问题

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

### <a name="all-qna-chat-log"></a>所有 QnA chat 日志

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>某个时间段内每个知识库和用户的流量计数

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>GenerateAnswer API 的延迟

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>所有操作的平均延迟

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>未答复的问题

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择 capactiy](./improve-knowledge-base.md)