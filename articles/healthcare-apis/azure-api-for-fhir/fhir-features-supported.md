---
title: Azure 中支持的 FHIR 功能 - Azure API for FHIR
description: 本文介绍了 Azure API for FHIR 中实现的 FHIR 规范相关功能
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 6/16/2021
ms.author: cavoeg
ms.openlocfilehash: cbc4b8b81bf2732c3646e4bd04e0652a4b8abf1d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778087"
---
# <a name="features"></a>功能

Azure API for FHIR 提供对 Microsoft FHIR Server for Azure 的完全托管部署。 该服务器是 [FHIR](https://hl7.org/fhir) 标准的一种实现。 本文档列出了 FHIR 服务器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支持的最新版本：`4.0.1`

目前还支持以前的版本，包括：`3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支持 - PaaS | 支持 - OSS (SQL) | 支持 - OSS (Cosmos DB) | 注释                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 读取                           | 是       | 是       | 是       |                                                     |
| vread                          | 是       | 是       | 是       |                                                     |
| update                         | 是       | 是       | 是       |                                                     |
| 使用乐观锁定更新 | 是       | 是       | 是       |                                                     |
| 更新（条件）           | 是       | 是       | 是       |                                                     |
| 修补程序                          | 否        | 否        | 否        |                                                     |
| delete                         | 是       | 是       | 是       |  请参阅下方注释。                                   |
| 删除（条件）           | 是       | 是        | 是        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| create                         | 是       | 是       | 是       | 支持 POST/PUT                               |
| 创建（条件）           | 是       | 是       | 是       | 问题 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | 部分   | 部分   | 部分   | 请参阅 [FHIR 搜索概述](overview-of-search.md)。                           |
| 链式搜索                 | 部分       | 是       | 部分   | 请参阅下方注释 2。                                   |
| 反向链式搜索         | 部分       | 是       | 部分   | 请参阅下方注释 2。                                   |
| capabilities                   | 是       | 是       | 是       |                                                     |
| 批处理                          | 是       | 是       | 是       |                                                     |
| transaction                    | 否        | 是       | 否        |                                                     |
| 分页                         | 部分   | 部分   | 部分   | 支持 `self` 和 `next`                     |
| 中介                 | 否        | 否        | 否        |                                                     |

> [!Note]
> 由 FHIR 规范定义的删除要求在删除后，对资源进行后续非特定版本读取时必须返回 410 HTTP 状态代码，且无法再通过搜索找到该资源。 通过 Azure API for FHIR 还可完全删除资源（包括所有历史记录）。 若要完全删除资源，可将参数设置 `hardDelete` 传递为 true (`DELETE {server}/{resource}/{id}?hardDelete=true`)。 如果未传递此参数或将 `hardDelete` 设置为 false，则资源的历史版本仍然可用。
> 
> Azure API for FHIR 和由 Cosmos 支持的开源 FHIR 服务器中，链式搜索和反向链式搜索均属于 MVP 实现。 为在 Cosmos DB 上完成链式搜索，该实现会逐步执行搜索表达式并发出子查询，从而解析匹配的资源。 此操作将针对表达式的各个级别执行。 如果查询返回超过 100 个结果，则将引发错误。 默认情况下，链式搜索位于功能标志之后。 若要在 Cosmos DB 上使用链式搜索，请使用标头 `x-ms-enable-chained-search: true`。 有关详细信息，请参阅 [PR 1695](https://github.com/microsoft/fhir-server/pull/1695)。

## <a name="extended-operations"></a>扩展操作

支持扩展 RESTful API 的所有操作。

| 搜索参数类型 | 支持 - PaaS | 支持 - OSS (SQL) | 支持 - OSS (Cosmos DB) | 注释 |
|------------------------|-----------|-----------|-----------|---------|
| $export（整个系统） | 是       | 是       | 是       |         |
| 患者/$export        | 是       | 是       | 是       |         |
| 组/$export          | 是       | 是       | 是       |         |
| $convert-data          | 是       | 是       | 是       |         |
| $validate              | 是       | 是       | 是       |         |
| $member-match          | 是       | 是       | 是       |         |
| $patient-everything    | 是       | 是       | 是       |         |

## <a name="persistence"></a>持久性

Microsoft FHIR 服务器配备一个可插入持久性模块（请参阅 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)）。

目前，FHIR 服务器开源代码包括 [Azure Cosmos DB](../../cosmos-db/index-overview.md) 和 [SQL 数据库](https://azure.microsoft.com/services/sql-database/)的实现。

Cosmos DB 是一种全局分布式多模型（SQL API、MongoDB API 等）数据库。 它支持不同的[一致性级别](../../cosmos-db/consistency-levels.md)。 默认部署模板配置具有 `Strong` 一致性的 FHIR 服务器，但可通过 `x-ms-consistency-level` 请求头按请求依次修改（通常较为宽松）。

## <a name="role-based-access-control"></a>基于角色的访问控制

FHIR 服务器使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 进行访问控制。 具体而言，若 `FhirServer:Security:Enabled` 配置参数设置为 `true`，则强制执行基于角色的访问控制 (RBAC)，且发送到 FHIR 服务器的所有请求（`/metadata` 除外）的 `Authorization` 请求标头必须设置为 `Bearer <TOKEN>`。 令牌内必须包含 `roles` 声明中定义的一个或多个角色。 如果令牌内包含允许对指定资源执行指定操作的角色，则请求将获得允许。

目前，允许给定角色进行的操作在 API 上全局应用。

## <a name="service-limits"></a>服务限制

* [**请求单位 (RU)** ](../../cosmos-db/concepts-limits.md) - 可在门户为 Azure API for FHIR 配置多达 10,000 个 RU。 需要至少 400 个 RU 或 40 RU/GB（取较大者）。 如果需要 10,000 个以上的 RU，则可提交支持票证来增加数量。 最大值为 1,000,000。

* 捆绑大小 - 每个捆绑限制为 500 个项。

* 数据大小 - 每个数据/文档必须略小于 2 MB。

* 订阅限制 - 默认情况下，每个订阅限制为最多 10 个 FHIR 服务器实例。 如果每个订阅需要更多实例，请开具支持票证并提供详细需求信息。

* 并发连接和实例 - 默认情况下，群集中的两个实例上具有 15 个并发连接（共 30 个并发请求）。 如果需要更多并发请求，请开具支持票证并提供详细需求信息。

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure API for FHIR 中支持的 FHIR 功能。 接下来将介绍如何部署 Azure API for FHIR。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
