---
title: Azure 中支持的 FHIR 功能-Azure API for FHIR
description: 本文介绍了在 Azure API for FHIR 中实现的 FHIR 规范的哪些功能
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: 9bd61d65d6d64dac6081d3491deb8a15efc4a45b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048413"
---
# <a name="features"></a>功能

适用于 FHIR 的 azure API 为 Azure 的 Microsoft FHIR 服务器提供完全托管的部署。 服务器是 [FHIR](https://hl7.org/fhir) 标准的实现。 本文档列出了 FHIR 服务器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支持的最新版本： `4.0.1`

当前还支持以前的版本，包括： `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 评论                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 读取                           | 是       | 是       | 是       |                                                     |
| vread                          | 是       | 是       | 是       |                                                     |
| update                         | 是       | 是       | 是       |                                                     |
| 具有开放式锁定的更新 | 是       | 是       | 是       |                                                     |
| 更新 (条件)            | 是       | 是       | 是       |                                                     |
| 修补程序                          | 否        | 否        | 否        |                                                     |
| delete                         | 是       | 是       | 是       |  请参阅下面的说明。                                   |
| 删除 (条件)            | 否        | 否        | 否        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| create                         | 是       | 是       | 是       | 同时支持 POST/PUT                               |
| 创建 (条件)            | 是       | 是       | 是       | 问题 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | 部分   | 部分   | 部分   | 请参阅下面的搜索部分。                           |
| 链式搜索                 | 是       | 是       | 部分   | 请参阅下面的注释2。                                   |
| 反向链接搜索         | 是       | 是       | 部分   | 请参阅下面的注释2。                                   |
| capabilities                   | 是       | 是       | 是       |                                                     |
| 批处理                          | 是       | 是       | 是       |                                                     |
| transaction                    | 否        | 是       | 否        |                                                     |
| 分页                         | 部分   | 部分   | 部分   | `self``next`支持和                     |
| 中间人                 | 否        | 否        | 否        |                                                     |

> [!Note]
> FHIR 规范定义的删除在删除后，对资源的后续非特定于版本的读取将返回 410 HTTP 状态代码，并且不再通过搜索找到资源。 通过用于 FHIR 的 Azure API，还可以完全删除 (包括资源) 所有历史记录。 若要完全删除资源，可以将参数设置传递 `hardDelete` 给 true (`DELETE {server}/{resource}/{id}?hardDelete=true`) 。 如果未传递此参数或将其设置 `hardDelete` 为 false，则资源的历史版本仍将可用。


 **注释 2**
* 在 CosmosDB 中添加对链式和反向链接 FHIR 搜索的 MVP 支持。 

  在用于 FHIR 的 Azure API 和 Cosmos 支持的开源 FHIR 服务器中，链式搜索和反向链接搜索是 MVP 实现。 若要在 Cosmos DB 上完成链式搜索，实现会遍历搜索表达式，并发出子查询来解析匹配的资源。 这适用于表达式的每个级别。 如果任何查询返回的结果超过100，则将引发错误。 默认情况下，链式搜索位于功能标志之后。 若要在 Cosmos DB 上使用链式搜索，请使用标头 `x-ms-enable-chained-search: true` 。 有关更多详细信息，请参阅 [PR 1695](https://github.com/microsoft/fhir-server/pull/1695)。

## <a name="search"></a>搜索

支持所有搜索参数类型。 

| 搜索参数类型 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 评论 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | 是       | 是       | 是       |         |
| Date/DateTime         | 是       | 是       | 是       |         |
| 字符串                | 是       | 是       | 是       |         |
| 令牌                 | 是       | 是       | 是       |         |
| 参考             | 是       | 是       | 是       |         |
| 合成             | 是       | 是       | 是       |         |
| 数量              | 是       | 是       | 是       |         |
| URI                   | 是       | 是       | 是       |         |
| 特殊               | 否        | 否        | 否        |         |


| 修饰符             | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 注释 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | 是       | 是       | 是       |         |
|`:exact`               | 是       | 是       | 是       |         |
|`:contains`            | 是       | 是       | 是       |         |
|`:text`                | 是       | 是       | 是       |         |
|`:[type]` (引用)   | 是       | 是       | 是       |         |
|`:not`                 | 是       | 是       | 是       |         |
|`:below` (uri)          | 是       | 是       | 是       |         |
|`:above` (uri)          | 否        | 否        | 否        | 问题 [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (标记)           | 否        | 否        | 否        |         |
|`:below` (标记)        | 否        | 否        | 否        |         |
|`:above` (标记)        | 否        | 否        | 否        |         |
|`:not-in` (标记)       | 否        | 否        | 否        |         |

| 常用搜索参数 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 注释 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 是       | 是       | 是       |         |
| `_lastUpdated`          | 是       | 是       | 是       |         |
| `_tag`                  | 是       | 是       | 是       |         |
| `_list`                 | 是       | 是       | 是       |         |
| `_type`                 | 是       | 是       | 是       | 问题 [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | 是       | 是       | 是       |         |
| `_profile`              | 部分   | 部分   | 部分   | 在 STU3 中受支持。 如果在2021年2月20日 **之后** 创建了数据库，则还会在 R4 中提供支持。 我们正在努力在2021年2月20日之前创建的数据库上启用 _profile。 |
| `_text`                 | 否        | 否        | 否        |         |
| `_content`              | 否        | 否        | 否        |         |
| `_has`                  | 否        | 否        | 否        |         |
| `_query`                | 否        | 否        | 否        |         |
| `_filter`               | 否        | 否        | 否        |         |

| 搜索结果参数 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 注释 |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | 是       | 是       | 是       | 问题 [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | 是       | 是       | 是       | `_count` 限制为1000个字符。 如果设置为高于1000，则仅返回1000，并在捆绑包中返回警告。 |
| `_include`              | 是       | 是       | 是       |包含的项限制为100。 在 PaaS 上包含 PaaS，Cosmos DB 上的 OSS 不包括：循环访问支持。|
| `_revinclude`           | 是       | 是       | 是       | 包含的项限制为100。 在 PaaS 上包含 PaaS，Cosmos DB 上的 OSS [不包括：循环访问支持](https://github.com/microsoft/fhir-server/issues/1313)。 问题 [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | 部分   | 部分   | 部分   | 支持 `_summary=count` |
| `_total`                | 部分   | 部分   | 部分   | `_total=none` 和 `_total=accurate`      |
| `_sort`                 | 部分   | 部分   | 部分   |   支持 `_sort=_lastUpdated`       |
| `_contained`            | 否        | 否        | 否        |         |
| `containedType`         | 否        | 否        | 否        |         |
| `_score`                | 否        | 否        | 否        |         |

## <a name="extended-operations"></a>扩展操作

支持扩展 RESTful API 的所有操作。

| 搜索参数类型 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 评论 |
|------------------------|-----------|-----------|-----------|---------|
|  (整个系统的 $export)  | 是       | 是       | 是       |         |
| 患者/$export        | 是       | 是       | 是       |         |
| 组/$export          | 是       | 是       | 是       |         |
| $convert 数据          | 是       | 是       | 是       |         |


## <a name="persistence"></a>持久性

Microsoft FHIR 服务器具有可插接式持久性模块 (参阅 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)) 。

目前，FHIR 服务器开源包括 [Azure Cosmos DB](../../cosmos-db/index-overview.md) 和 [SQL 数据库](https://azure.microsoft.com/services/sql-database/)的实现。

Cosmos DB 是一种全球分布的多模型 (SQL API、MongoDB API 等 ) 数据库。 它支持不同的 [一致性级别](../../cosmos-db/consistency-levels.md)。 默认部署模板配置 FHIR 服务器 `Strong` 一致性，但可以使用 `x-ms-consistency-level` 请求标头，通过请求来修改一致性策略 (一般宽松地) 请求。

## <a name="role-based-access-control"></a>基于角色的访问控制

FHIR 服务器使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 进行访问控制。 具体而言，如果将配置参数设置为，则会强制实施基于角色的访问控制 (RBAC) ， `FhirServer:Security:Enabled` `true` 并且除 `/metadata`) 到 FHIR 服务器的所有 (请求都必须 `Authorization` 将请求标头设置为 `Bearer <TOKEN>` 。 令牌必须包含声明中定义的一个或多个角色 `roles` 。 如果令牌包含允许指定资源上指定操作的角色，则将允许请求。

目前，对给定角色允许的操作在 API 上 *全局* 应用。

## <a name="service-limits"></a>服务限制

* [**请求单位 (ru)**](../../cosmos-db/concepts-limits.md) -可在门户中为 FHIR 的 Azure API 配置最多10000个 ru。 你将需要至少 400 ru 或10个 ru/GB，取两者中较大者。 如果你需要10000个多个 ru，则可以将其放在支持票证中，以增加此项。 可用的最大值为1000000。

* **并发连接** 和 **实例** -默认情况下，在群集中的两个实例上有5个并发连接 (总计10个并发请求) 。 如果你认为你需要更多的并发请求，请打开支持票证，详细了解你的需求。

* **束大小** -每个束限制为500个项目。

* **数据大小** -数据/文档每个都必须略微小于 2 MB。

## <a name="performance-expectations"></a>性能预期

系统的性能取决于多个 ru、并发连接以及您正在执行的操作的类型 (Put、Post 等 ) 。 下面是你可以基于已配置的 ru 获得的一些常规范围。 通常，性能会随 RUs 的增加而线性缩放：

| Ru 数 | 资源数/秒 |    最大存储 (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1,000    | 100-150       |      100  |
| 10,000   | 225-400       |      1,000  |
| 100,000  | 2500-4000   |      10,000  |

注意：根据 Cosmos DB 要求，每 GB 存储要求的最小吞吐量为 10 RU/秒。 有关详细信息，请查看 [Cosmos DB 的服务配额](../../cosmos-db/concepts-limits.md)。

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure API for FHIR 中支持的 FHIR 功能。 接下来，部署适用于 FHIR 的 Azure API。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
