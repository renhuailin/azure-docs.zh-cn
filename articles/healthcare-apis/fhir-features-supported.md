---
title: Azure 中支持的 FHIR 功能 - Azure API for FHIR
description: 本文介绍 Azure API for FHIR 中实现的 FHIR 规范功能
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 28c01e99c0e8708750341b445b4a31f6eaeab3ce
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747519"
---
# <a name="features"></a>功能

Azure API for FHIR 为适用于 Azure 的 Microsoft FHIR 服务器提供完全托管的部署。 该服务器是 [FHIR](https://hl7.org/fhir) 标准的实现。 本文档列出了 FHIR 服务器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支持的最新版本：`4.0.1`

目前还支持的旧版本包括：`3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支持 - PaaS | 支持 - OSS (SQL) | 支持 - OSS (Cosmos DB) | 注释                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 读取                           | 是       | 是       | 是       |                                                     |
| vread                          | 是       | 是       | 是       |                                                     |
| update                         | 是       | 是       | 是       |                                                     |
| 使用乐观锁定的更新 | 是       | 是       | 是       |                                                     |
| 更新（条件性）           | 是       | 是       | 是       |                                                     |
| 修补                          | 否        | 否        | 否        |                                                     |
| delete                         | 是       | 是       | 是       |                                                     |
| 删除（条件性）           | 否        | 否        | 否        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| create                         | 是       | 是       | 是       | 同时支持 POST/PUT                               |
| 创建 (条件)            | 是       | 是       | 是       | 问题 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | 部分   | 部分   | 部分   | 请参阅下文                                           |
| 链式搜索                 | 否        | 是       | 否        |                                           |
| 反向链接搜索         | 否        | 否        | 否        |                                            |
| capabilities                   | 是       | 是       | 是       |                                                     |
| 批处理                          | 是       | 是       | 是       |                                                     |
| transaction                    | 否        | 是       | 否        |                                                     |
| 分页                         | 部分   | 部分   | 部分   | `self``next`支持和                     |
| 中间人                 | 否        | 否        | 否        |                                                     |

## <a name="search"></a>搜索

支持所有搜索参数类型。 

| 搜索参数类型 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 注释 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | 是       | 是       | 是       |         |
| Date/DateTime         | 是       | 是       | 是       |         |
| 字符串                | 是       | 是       | 是       |         |
| 令牌                 | 是       | 是       | 是       |         |
| 引用             | 是       | 是       | 是       |         |
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
|`:in`（标记）          | 否        | 否        | 否        |         |
|`:below`（标记）       | 否        | 否        | 否        |         |
|`:above` (标记)        | 否        | 否        | 否        |         |
|`:not-in` (标记)       | 否        | 否        | 否        |         |
|`:[type]` (引用)   | 否        | 否        | 否        |         |
|`:below` (uri)          | 是       | 是       | 是       |         |
|`:not`                 | 否        | 否        | 否        |         |
|`:above` (uri)          | 否        | 否        | 否        | 问题 [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 常用搜索参数 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 注释 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 是       | 是       | 是       |         |
| `_lastUpdated`          | 是       | 是       | 是       |         |
| `_tag`                  | 是       | 是       | 是       |         |
| `_profile`              | 部分   | 部分   | 部分   | 仅在 STU3 中受支持，在 R4 中不支持 |
| `_security`             | 是       | 是       | 是       |         |
| `_text`                 | 否        | 否        | 否        |         |
| `_content`              | 否        | 否        | 否        |         |
| `_list`                 | 是       | 是       | 是       |         |
| `_has`                  | 否        | 否        | 否        |         |
| `_type`                 | 是       | 是       | 是       |         |
| `_query`                | 否        | 否        | 否        |         |
| `_filter`               | 否        | 否        | 否        |         |

| 搜索结果参数 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 注释 |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | 部分        | 部分   | 部分        |   支持 `_sort=_lastUpdated`       |
| `_count`                | 是       | 是       | 是       | `_count` 限制为100个字符。 如果设置为高于100，则仅返回100，并在捆绑包中返回警告。 |
| `_include`              | 是       | 是       | 是       |包含的项限制为100。 在 PaaS 上包含 PaaS，Cosmos DB 上的 OSS 不包括：循环访问支持。|
| `_revinclude`           | 是       | 是       | 是       | 包含的项限制为100。 在 PaaS 上包含 PaaS，Cosmos DB 上的 OSS 不包括：循环访问支持。|
| `_summary`              | 部分   | 部分   | 部分   | 支持 `_summary=count` |
| `_total`                | 部分   | 部分   | 部分   | _total = 非 _total = 准确      |
| `_elements`             | 是       | 是       | 是       |         |
| `_contained`            | 否        | 否        | 否        |         |
| `containedType`         | 否        | 否        | 否        |         |
| `_score`                | 否        | 否        | 否        |         |

## <a name="extended-operations"></a>扩展操作

支持扩展 RESTful API 的所有操作。

| 搜索参数类型 | 支持-PaaS | 支持-OSS (SQL)  | 支持-OSS (Cosmos DB)  | 注释 |
|------------------------|-----------|-----------|-----------|---------|
|  (整个系统的 $export)  | 是       | 是       | 是       |         |
| 患者/$export        | 是       | 是       | 是       |         |
| 组/$export          | 是       | 是       | 是       |         |

## <a name="persistence"></a>持久性

Microsoft FHIR 服务器具有可插接式持久性模块 (参阅 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)) 。

目前，FHIR 服务器开源包括 [Azure Cosmos DB](../cosmos-db/index-overview.md) 和 [SQL 数据库](https://azure.microsoft.com/services/sql-database/)的实现。

Cosmos DB 是一种全球分布的多模型 (SQL API、MongoDB API 等 ) 数据库。 它支持不同的 [一致性级别](../cosmos-db/consistency-levels.md)。 默认部署模板配置 FHIR 服务器 `Strong` 一致性，但可以使用 `x-ms-consistency-level` 请求标头，通过请求来修改一致性策略 (一般宽松地) 请求。

## <a name="role-based-access-control"></a>基于角色的访问控制

FHIR 服务器使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 进行访问控制。 具体而言，如果将配置参数设置为，则会强制实施基于角色的访问控制 (RBAC) ， `FhirServer:Security:Enabled` `true` 并且除 `/metadata`) 到 FHIR 服务器的所有 (请求都必须 `Authorization` 将请求标头设置为 `Bearer <TOKEN>` 。 令牌必须包含声明中定义的一个或多个角色 `roles` 。 如果令牌包含允许指定资源上指定操作的角色，则将允许请求。

目前，对给定角色允许的操作在 API 上 *全局* 应用。

## <a name="service-limits"></a>服务限制

* [**请求单位 (ru)**](../cosmos-db/concepts-limits.md) -可在门户中为 FHIR 的 Azure API 配置最多10000个 ru。 你将需要至少 400 ru 或10个 ru/GB，取两者中较大者。 如果你需要10000个多个 ru，则可以将其放在支持票证中，以增加此项。 可用的最大值为1000000。

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

注意：根据 Cosmos DB 要求，每 GB 存储要求的最小吞吐量为 10 RU/秒。 有关详细信息，请查看 [Cosmos DB 的服务配额](../cosmos-db/concepts-limits.md)。

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure API for FHIR 中支持的 FHIR 功能。 接下来，部署适用于 FHIR 的 Azure API。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
