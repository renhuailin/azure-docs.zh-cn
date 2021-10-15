---
title: 在 Azure 数据工厂和 Synapse Analytics 中执行 Fail 活动（预览版）
titleSuffix: Azure Data Factory & Azure Synapse
description: 本文介绍 Azure 数据工厂和 Synapse Analytics 中的 Fail 活动如何特意引发管道中的错误。
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 2b8b012617633a7569c951e842d35b05a3b2740e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535866"
---
# <a name="execute-a-fail-activity-in-azure-data-factory-and-synapse-analytics-preview"></a>在 Azure 数据工厂和 Synapse Analytics 中执行 Fail 活动（预览版）

有时你可能需要特意引发管道中的错误。 也许 [Lookup 活动](control-flow-lookup-activity.md)没有返回任何匹配数据，或者 [Custom 活动](transform-data-using-dotnet-custom-activity.md)完成但引发了内部错误。 无论原因是什么，现在都可以在管道中使用 Fail 活动，并自定义错误消息和错误代码。

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>语法

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "message": "500",
        "errorCode": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>Type 属性

| 属性 | 说明 | 允许的值 | 必需 |
| --- | --- | --- | --- |
| name | Fail 活动的名称。 | 字符串 | 是 |
| type | 必须设置为“Fail”。 | 字符串 | 是 |
| message | Fail 活动中出现的错误消息。 可以是在运行时计算的动态内容。 | 字符串 | 是 |
| errorCode | 对 Fail 活动的错误类型进行分类的错误代码。 可以是在运行时计算的动态内容。 | 字符串 | 是 |
| | |

## <a name="understand-the-fail-activity-error-code"></a>了解 Fail 活动错误代码

Fail 活动的错误消息和错误代码通常由用户设置。 若要了解错误代码的具体含义，请联系管道开发人员。 但是，在以下边缘情况下，Azure 数据工厂会设置错误消息和/或错误代码。

| 情况说明 | 错误消息 | 错误代码 |
| --- | --- | --- |
已正确解释 `message` 和 `errorCode` 中的（动态）内容。 | 用户设置的错误消息 | 用户设置的错误代码 |
无法解释 `message` 和 `errorCode` 中的动态内容。 | “无法解释 <activity_name> 失败消息或错误代码” | `ErrorCodeNotString` |
| `message` 中的动态内容无法解释为字符串。 | “<activity_name> 失败消息参数无法解释为字符串” | 用户设置的错误代码 |
| `message` 中的动态内容解析为 null、空字符串或空格。 | “无法解释 <activity_name> 失败消息或错误代码” | 用户设置的错误代码 |
| `errorCode` 中的动态内容无法解释为字符串。 | 用户设置的错误消息 | `ErrorCodeNotString` |
| `errorCode` 中的动态内容解析为 null、空字符串或空格。 | 用户设置的错误消息 | `ErrorCodeNotString` |
| 由用户提供的 `message` 或 `errorCode` 的值不能为字符串。* | 管道失败并出现错误消息“属性 <`errorCode`/`message`> 的值无效” | |
| 缺少 `message` 字段。* | “未提供失败消息” | 用户设置的错误代码 |
| 缺少 `errorCode` 字段。* | 用户设置的错误消息 | `ErrorCodeNotString` |
| | |

\* 如果管道是通过数据工厂的 Web 用户界面 (UI) 开发的，则不应发生此情况。

## <a name="next-steps"></a>后续步骤

请参阅其他受支持的控制流活动，包括：

- [If 条件活动](control-flow-if-condition-activity.md)
- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
