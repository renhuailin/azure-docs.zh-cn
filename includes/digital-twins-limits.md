---
author: baanders
description: Azure 数字孪生限制的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 4a69b2ff15fc4857e9fb292d2f753aa68ed875d4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370083"
---
### <a name="functional-limits"></a>功能限制

下表列出了 Azure 数字孪生的功能限制。 

> [!TIP]
> 有关在这些功能限制内操作的建模建议，请参阅 [设计模型的最佳实践](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models)。

| 区域 | 功能 | 默认限制 | 可调? |
| --- | --- | --- | --- |
| Azure 资源 | 区域中每个订阅的 Azure 数字孪生实例数 | 10 | 是 |
| 数字孪生 | Azure 数字孪生实例中的孪生数 | 200,000 | 是 |
| 数字孪生 | 与单个克隆的传入关系数 | 5,000 | 否 |
| 数字孪生 | 单个克隆的传出关系数 | 5,000 | 否 |
| 数字孪生 | PUT 或 PATCH 请求中的 JSON 正文 (大小上限) 单个克隆的大小 | 32 KB | 否 |
| 数字孪生 | 最大请求负载大小 | 32 KB | 否 | 
| 路由 | 单个 Azure 数字孪生实例的终结点数 | 6 | 否 |
| 路由 | 单个 Azure 数字孪生实例的路由数 | 6 | 是 |
| 模型 | 单个 Azure 数字孪生实例内的模型数 | 10,000 | 是 |
| 模型 | 可在单个 API 调用中上载的模型数 | 250 | 否 |
| 模型 | PUT 或 PATCH 请求中 JSON 正文的最大大小 (单个模型)  | 1 MB | 否 |
| 模型 | 在单个页面中返回的项目数 | 100 | 否 |
| 查询 | 在单个页面中返回的项目数 | 100 | 是 |
| 查询 | `AND`  /  `OR` 查询中的表达式数 | 50 | 是 |
| 查询 | 子句中的数组项数 `IN`  /  `NOT IN` | 50 | 是 |
| 查询 | 查询中的字符数 | 8,000 | 是 |
| 查询 | `JOINS`查询中的数目 | 5 | 是 |

### <a name="rate-limits"></a>速率限制

下表反映了不同 Api 的速率限制。

| API | 功能 | 默认限制 | 可调? |
| --- | --- | --- | --- |
| 模型 API | 每秒的请求数 | 100 | 是 |
| 数字孪生 API | 每秒的请求数 | 2,000 | 是 |
| 数字孪生 API | **所有孪生和关系** 中每秒的创建/删除操作数 | 50 | 是 |
| 数字孪生 API | 每 **秒在一个或其** 关系上创建/更新/删除操作的次数 | 10 | 否 |
| 查询 API | 每秒的请求数 | 500 | 是 |
| 查询 API | [查询单位](../articles/digital-twins/concepts-query-units.md) /秒 | 4,000 | 是 |
| 事件路由 API | 每秒的请求数 | 100 | 是 |

### <a name="other-limits"></a>其他限制

可在 GitHub 中的规范文档中找到有关 Azure 数字孪生模型的 DTDL 文档中的数据类型和字段的限制： [*数字孪生定义语言 (DTDL) 版本 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。
 
在 [*操作方法：查询克隆图形*](../articles/digital-twins/how-to-query-graph.md)中，可以找到查询延迟详细信息和其他查询限制。
