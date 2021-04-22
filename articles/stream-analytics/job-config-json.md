---
title: Azure 流分析 JobConfig.json 字段
description: 本文列出了用于在 Visual Studio Code 中创建作业的 Azure 流分析 JobConfig.json 文件支持的字段。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020479"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure 流分析 JobConfig.json 字段

用于[使用 Visual Studio Code 创建 Azure 流分析作业](quick-create-visual-studio-code.md)的 JobConfig.json 文件支持以下字段。

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|名称|类型|必选|值|
|----|----|--------|-----|
|DataLocale|string|否|流分析作业的数据区域设置。 值应为支持的区域设置的名称。 如果未指定，则默认为“en-US”。|
|OutputErrorPolicy|string|否|指示要应用于到达输出但由于格式错误（缺少列值、列值类型或大小错误）而无法写入外部存储的事件的策略。 - 停止或删除|
|EventsLateArrivalMaxDelayInSeconds|integer|否|可容忍的最大延迟（以秒为单位），其中可能包括迟到事件。 支持的范围为 -1 到 1814399（20.23:59:59 天），-1 用于指定无限期等待。 如果该属性不存在，则将其解释为具有值 -1。|
|EventsOutOfOrderMaxDelayInSeconds|integer|否|可容许的最大延迟（以秒为单位），其中无序事件可重新调整为有序事件。|
|EventsOutOfOrderPolicy|string|否|指示要应用于输入事件流中未按顺序到达的事件的策略。 - 调整或删除|
|StreamingUnits|integer|是|指定流式处理作业使用的流单元数。|
|CompatibilityLevel|string|否|控制流式处理作业的某些运行时行为。 - 可接受的值为 1.0、1.1、1.2|
|UseSystemAssignedIdentity|boolean|否|设置为 true 使该作业能与其他 Azure 服务通信，因为它本身使用的是 Azure Active Directory 托管标识。|
|GlobalStorage.AccountName|string|否|全局存储帐户用于存储与流分析作业相关的内容，例如 SQL 参考数据快照。|
|GlobalStorage.AccountKey|string|否|全局存储帐户的相应密钥。|
|DataSourceCredentialDomain|string|否|凭据本地存储的保留属性。|
|ScriptType|字符串|是|保留属性，用于指示此源文件的类型。 对于 JobConfig.json，可接受的值为“JobConfig”。|
|Tags|JSON 键值对|否|标记是名称/值对，可让你通过将相同的标记应用到多个资源和资源组，对资源进行分类并查看合并的账单。 标记名称不区分大小写，但标记值区分大小写。|

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)
* [通过 Visual Studio Code 使用示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 在本地对实时流输入测试流分析查询](visual-studio-code-local-run-live-input.md)
*[使用 CI/CD npm 包部署 Azure 流分析作业](./cicd-overview.md)