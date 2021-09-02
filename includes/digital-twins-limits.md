---
author: baanders
description: Azure 数字孪生限制的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: fae919afc12afd5a6169dd119b57ae72ae7c14f0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472862"
---
### <a name="functional-limits"></a>功能限制

下表列出了 Azure 数字孪生的功能限制。 

> [!TIP]
> 有关在这些功能限制内进行操作的建模建议，请参阅[建模最佳做法](../articles/digital-twins/concepts-models.md#modeling-best-practices)。

| 区域 | 功能 | 默认限制 | 可调？ |
| --- | --- | --- | --- |
| Azure 资源 | 区域中每个订阅的 Azure 数字孪生实例数 | 10 | 是 |
| 数字孪生 | Azure 数字孪生实例中的孪生体数 | 200,000 | 是 |
| 数字孪生 | 单个孪生体的传入关系数 | 5,000 | 否 |
| 数字孪生 | 单个孪生体的传出关系数 | 5,000 | 否 |
| 数字孪生 | 单个孪生体的（PUT 或 PATCH 请求中的 JSON 正文的）最大大小 | 32 KB | 否 |
| 数字孪生 | 请求有效负载最大大小 | 32 KB | 否 | 
| 路由 | 单个 Azure 数字孪生实例的终结点数 | 6 | 否 |
| 路由 | 单个 Azure 数字孪生实例的路由数 | 6 | 是 |
| 模型 | 单个 Azure 数字孪生实例中的模型数 | 10,000 | 是 |
| 模型 | 可在单个 API 调用中上传的模型数 | 250 | 否 |
| 模型 | 单个模型的（PUT 或 PATCH 请求中的 JSON 正文的）最大大小 | 1 MB | 否 |
| 模型 | 单个页面中返回的项数 | 100 | 否 |
| 查询 | 单个页面中返回的项数 | 100 | 是 |
| 查询 | 查询中 `AND` / `OR` 表达式的数目 | 50 | 是 |
| 查询 | `IN` / `NOT IN` 子句中的数组项数 | 50 | 是 |
| 查询 | 查询中的字符数 | 8,000 | 是 |
| 查询 | 查询中的 `JOINS` 数 | 5 | 是 |

### <a name="rate-limits"></a>速率限制

下表反映了各种 API 的速率限制。

| API | 功能 | 默认限制 | 可调？ |
| --- | --- | --- | --- |
| 模型 API | 每秒请求数 | 100 | 是 |
| 数字孪生 API | 每秒读取请求数 | 1,000 | 是 |
| 数字孪生 API | 每秒 PATCH 请求数 | 1,000 | 是 |
| 数字孪生 API | 每秒所有孪生体和关系上创建/删除操作的数量 | 50 | 是 |
| 数字孪生 API | 每秒单个孪生体或其关系上创建/更新/删除操作的数量 | 10 | 否 |
| 查询 API | 每秒请求数 | 500 | 是 |
| 查询 API | [查询单元数](../articles/digital-twins/concepts-query-units.md)/秒 | 4,000 | 是 |
| 事件路由 API | 每秒请求数 | 100 | 是 |

### <a name="other-limits"></a>其他限制

有关 Azure 数字孪生模型的 DTDL 文档中数据类型和字段的限制，可参阅 GitHub 中的相应规范文档：数字孪生定义语言 (DTDL) - 版本 2。
 
有关查询延迟的详细信息，请参阅[查询语言](../articles/digital-twins/concepts-query-language.md#considerations-for-querying)。 可以在[查询引用文档](../articles/digital-twins/concepts-query-language.md#reference-documentation)中找到特定查询语言功能的限制。
