---
title: Azure 中支持的 FHIR 功能 - Azure API for FHIR
description: 本文介绍在 Azure API for FHIR 中实现的 FHIR 规范Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 1ff2a069b7690817c8fc431d920e93a8cb6c3544
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656193"
---
# <a name="features"></a>功能

Azure API for FHIR Microsoft FHIR Server for Azure 的完全托管部署。 服务器是 [FHIR 标准的](https://hl7.org/fhir) 实现。 本文档列出了 FHIR 服务器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支持最新版本： `4.0.1`

目前还支持以前的版本，包括： `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支持 - PaaS | 支持 - OSS (SQL)  | 支持 - OSS (Cosmos DB)  | 评论                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 读取                           | 是       | 是       | 是       |                                                     |
| vread                          | 是       | 是       | 是       |                                                     |
| update                         | 是       | 是       | 是       |                                                     |
| 使用乐观锁定进行更新 | 是       | 是       | 是       |                                                     |
| 更新 (条件)            | 是       | 是       | 是       |                                                     |
| 修补程序                          | 否        | 否        | 否        |                                                     |
| delete                         | 是       | 是       | 是       |  请参阅下面的说明。                                   |
| 删除 (条件)            | 否        | 否        | 否        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| create                         | 是       | 是       | 是       | 支持 POST/PUT                               |
| 创建 (条件)            | 是       | 是       | 是       | 问题 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | 部分   | 部分   | 部分   | 请参阅 [FHIR 搜索概述](overview-of-search.md)。                           |
| 链式搜索                 | 部分       | 是       | 部分   | 请参阅下面的注释2。                                   |
| 反向链接搜索         | 部分       | 是       | 部分   | 请参阅下面的注释2。                                   |
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

## <a name="extended-operations"></a>扩展操作

支持扩展 RESTful API 的所有操作。

| 搜索参数类型 | 支持 - PaaS | 支持 - OSS (SQL)  | 支持 - OSS (Cosmos DB)  | 评论 |
|------------------------|-----------|-----------|-----------|---------|
| $export (整个系统)  | 是       | 是       | 是       |         |
| 患者/$export        | 是       | 是       | 是       |         |
| 组/$export          | 是       | 是       | 是       |         |
| $convert-data          | 是       | 是       | 是       |         |
| $validate          | 是       | 是       | 是       |         |

## <a name="persistence"></a>持久性

Microsoft FHIR 服务器具有可插入的持久性模块 (请参阅 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)) 。

目前，FHIR 服务器开放源代码包括 Azure Cosmos DB 和 SQL[数据库](../../cosmos-db/index-overview.md)[的实现](https://azure.microsoft.com/services/sql-database/)。

Cosmos DB SQL API、MongoDB API 等数据库 (分布式多模型) 数据库。 它支持不同的 [一致性级别](../../cosmos-db/consistency-levels.md)。 默认部署模板配置 FHIR 服务器的一致性，但可以使用请求标头 (根据请求) 来修改一致性 `Strong` `x-ms-consistency-level` 策略。

## <a name="role-based-access-control"></a>基于角色的访问控制

FHIR 服务器使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 进行访问控制。 具体而言，如果配置参数设置为 ，则强制执行基于角色的访问控制 (RBAC) ，并且除) 到 `FhirServer:Security:Enabled` `true` FHIR 服务器的所有 (请求都必须将请求标头设置为 `/metadata` `Authorization` `Bearer <TOKEN>` 。 令牌必须包含声明中定义的一个或多个 `roles` 角色。 如果令牌包含允许对指定资源执行指定操作的角色，则允许请求。

目前，给定角色允许的操作在 API *上全局* 应用。

## <a name="service-limits"></a>服务限制

* [**请求 (个)**](../../cosmos-db/concepts-limits.md) 个 10，000 个 10，000 个AZURE API FOR FHIR。 你将需要至少 400 ru 或 40 ru/GB，取两者中较大者。 如果你需要10000个多个 ru，则可以将其放在支持票证中，以增加此项。 可用的最大值为1000000。

* **并发连接** 和 **实例** -默认情况下，在群集中的两个实例上有5个并发连接 (总计10个并发请求) 。 如果你认为你需要更多的并发请求，请打开支持票证，详细了解你的需求。

* **束大小** -每个束限制为500个项目。

* **数据大小** -数据/文档每个都必须略微小于 2 MB。

## <a name="performance-expectations"></a>性能预期

系统的性能取决于多个 ru、并发连接以及您正在执行的操作的类型 (Put、Post 等 ) 。 下面是你可以基于已配置的 ru 获得的一些常规范围。 通常，性能会随 RUs 的增加而线性缩放：

| Ru 数 | 资源数/秒 |    最大存储 (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1,000    | 100-150       |      25  |
| 10,000   | 225-400       |      250  |
| 100,000  | 2500-4000   |      2,500  |

注意：根据 Cosmos DB 要求，每 GB 存储要求最小吞吐量为 40 RU/秒。 

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure API for FHIR 中支持的 FHIR 功能。 接下来，部署适用于 FHIR 的 Azure API。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
