---
title: DICOM 更改源概述 - Azure Healthcare API
description: 本文介绍 DICOM 更改源的概念。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fc23c4cedbb6ee94c4f253cf53e0cdde27531fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778478"
---
# <a name="change-feed-overview"></a>更改源概述

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

更改源提供 DICOM 服务中发生的所有更改的日志。 更改源提供这些更改的有序、有保证、不可变和只读日志。 更改源使客户能够浏览 DICOM 服务历史记录，并针对服务中的创建和删除事件采取行动。

客户端应用程序可以在流式处理或批处理模式下随时读取这些日志。 使用更改源可以生成高效且可缩放的解决方案，因此能够处理 DICOM 服务中发生的更改事件。

可通过异步、增量或整体方式处理这些更改事件。 任意数目的客户端应用程序都可以按照自身的步调单独并行读取更改源。

在发出请求时，请确保将版本指定为 URL 的一部分。 有关详细信息，请参阅 [DICOM 服务的 API 版本控制文档](api-versioning-dicom-service.md)。

## <a name="api-design"></a>API 设计

API 公开两个 `GET` 终结点，用于与更改源交互。 [下面提供了](#example-usage-flow)使用更改源的典型流。

谓词 | 路由              | 返回     | 说明
:--- | :----------------- | :---------- | :---
GET  | /changefeed        | JSON 数组  | [读取更改源](#read-change-feed)
GET  | /changefeed/latest | JSON 对象 | [读取更改源中的最新条目](#get-latest-change-feed-item)

### <a name="object-model"></a>对象模型

字段               | 类型      | 说明
:------------------ | :-------- | :---
序列            | int       | 可用于分页（通过偏移量）或锚定的序列 ID
StudyInstanceUid    | string    | 研究实例 UID
SeriesInstanceUid   | string    | 时序实例 UID
SopInstanceUid      | string    | sop 实例 UID
操作              | string    | 执行的操作 - `create` 或 `delete`
时间戳           | datetime  | 执行操作的日期和时间（采用 UTC 格式）
State               | 字符串    | [元数据的当前状态](#states)
元数据            | object    | （可选）当前 DICOM 元数据（如果实例存在）

#### <a name="states"></a>状态

状态    | 说明
:------- | :---
当前  | 此实例为当前版本。
替换的内容 | 此实例已替换为新版本。
deleted  | 此实例已删除，在服务中不再可用。

### <a name="read-change-feed"></a>读取更改源

路由：/changefeed?offset={int}&limit={int}&includemetadata={true|false}
```
[
    {
        "Sequence": 1,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-04T01:03:08.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    },
    {
        "Sequence": 2,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-05T07:13:16.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    }
    ...
]
```
#### <a name="parameters"></a>参数

名称            | 类型 | 说明
:-------------- | :--- | :---
offset          | int  | 要返回的值之前要跳过的记录数
limit           | int  | 要返回的记录数：（默认值：10，最小值：1，最大值：100）
includemetadata | bool | 是否包含元数据（默认值：true）

### <a name="get-latest-change-feed-item"></a>获取最新的更改源项

路由：/changefeed/latest?includemetadata={true|false}

```
{
    "Sequence": 2,
    "StudyInstanceUid": "{uid}",
    "SeriesInstanceUid": "{uid}",
    "SopInstanceUid": "{uid}",
    "Action": "create|delete",
    "Timestamp": "2020-03-05T07:13:16.4834Z",
    "State": "current|replaced|deleted",
    "Metadata": {
        "actual": "metadata"
    }
}
```

#### <a name="parameters"></a>参数

名称            | 类型 | 说明
:-------------- | :--- | :---
includemetadata | bool | 是否包含元数据（默认值：true）

## <a name="usage"></a>使用情况

### <a name="example-usage-flow"></a>使用流示例

下面是对 DICOM 服务中的实例执行其他处理的示例应用程序的使用流。

1. 要监视更改源的应用程序将启动。
2. 它确定当前状态是否以以下内容开头：
   * 如果它具有状态，则使用存储的偏移量（序列）。
   * 如果它从未启动过并且想要从头开始，则使用 `offset=0`。  
   * 如果只想从现在开始处理，它将查询 `/changefeed/latest` 以获取最后一个序列。
3. 它使用给定的偏移量 `/changefeed?offset={offset}` 查询更改源
4. 如果存在条目：
   * 它会执行额外的处理。  
   * 它会更新其当前状态。  
   * 它再次从上面的步骤 2 开始。
5. 如果没有条目，它将休眠配置的一段时间，并返回步骤 2。

### <a name="other-potential-usage-patterns"></a>其他潜在的使用模式

更改源支持非常适合基于已更改的对象处理数据的方案。 例如，它可用于：

* 生成连接的应用程序管道，以便根据创建的或删除的对象来响应更改事件或计划执行。
* 基于对象发生的更改提取业务分析见解和指标。
* 轮询更改源以创建推送通知的事件源。

## <a name="summary"></a>摘要

本文介绍了更改源的 REST API 设计以及可能的使用方案。 有关更改源的信息，请参阅[从更改源拉取更改](pull-dicom-changes-from-change-feed.md)。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)

