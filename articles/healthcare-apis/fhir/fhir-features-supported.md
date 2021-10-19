---
title: FHIR 服务中支持的 FHIR 功能
description: 本文介绍在医疗保健 API 中实现的 FHIR 规范的功能
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: c1f40d6129f08b8369885631232b394d66f1d260
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045836"
---
# <a name="supported-fhir-features"></a>支持的 FHIR 功能

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure 医疗保健 API (中的 FHIR 服务称为 FHIR 服务) 提供开源 FHIR 服务器的完全托管部署，是 &reg; [FHIR](https://hl7.org/fhir)[](https://github.com/microsoft/fhir-server)标准的实现。 本文档列出了 FHIR 服务的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支持的最新版本：`4.0.1`

目前还支持以前的版本，包括：`3.0.2`

## <a name="rest-api"></a>REST API

| API    | 适用于 FHIR 的 Azure API | 医疗保健 API 中的 FHIR 服务 | 评论 |
|--------|--------------------|---------------------------------|---------|
| 读取   | 是                | 是                             |         |
| vread  | 是                | 是                             |         |
| update | 是                | 是                             |         | 
| 使用乐观锁定更新 | 是       | 是       |
| 更新（条件）           | 是       | 是       |
| 修补程序                          | 是       | 是       | 仅支持 [JSON 修补程序](https://www.hl7.org/fhir/http.html#patch) 。 我们在此 PR 的捆绑包中包含了一种使用 JSON 修补程序 [的解决方法](https://github.com/microsoft/fhir-server/pull/2143)。|
| 修补 (条件)             | 是       | 是       |
| delete                         | 是       | 是       | 请参阅下面的删除部分的详细信息 |
| 删除（条件）           | 是       | 是       | 请参阅下面的删除部分的详细信息 |
| history                        | 是       | 是       |
| create                         | 是       | 是       | 支持 POST/PUT |
| 创建（条件）           | 是       | 是       | 问题 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | 部分   | 部分   | 请参阅 [FHIR 搜索概述](overview-of-search.md)。 |
| 链式搜索                 | 是       | 是       | |
| 反向链式搜索         | 是       | 是       | |
| 批处理                          | 是       | 是       |
| transaction                    | 否        | 是       |
| 分页                         | 部分   | 部分   | 支持 `self` 和 `next`                     |
| 中介                 | 否        | 否        |

### <a name="delete-and-conditional-delete"></a>删除和条件删除

由 FHIR 规范定义的删除要求在删除后，对资源进行后续非特定版本读取时必须返回 410 HTTP 状态代码，且无法再通过搜索找到该资源。 使用 Azure API for FHIR 和 FHIR 服务，还可以完全删除 (，包括资源) 历史记录。 若要完全删除资源，可将参数设置 `hardDelete` 传递为 true (`DELETE {server}/{resource}/{id}?hardDelete=true`)。 如果未传递此参数或将 `hardDelete` 设置为 false，则资源的历史版本仍然可用。

除了删除之外，Azure API for FHIR和 FHIR 服务还支持条件删除，这允许你传递搜索条件以删除资源。 默认情况下，条件删除将允许你一次删除一个项。 还可以指定 参数 `_count` ，一次最多删除 100 个项。 下面是使用条件删除的一些示例。

若要使用条件删除删除单个项，必须指定返回单个项的搜索条件。
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704
```

可以执行相同的搜索，但包括 hardDelete=true 以同时删除所有历史记录。
```JSON 
DELETE https://{{hostname}}/Patient?identifier=1032704&hardDelete=true
```

如果要删除多个资源，可以包括 ，这将删除最多 `_count=100` 100 个与搜索条件匹配的资源。 
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704&_count=100
```

## <a name="extended-operations"></a>扩展操作

支持的所有操作扩展了REST API。

| 搜索参数类型 | 适用于 FHIR 的 Azure API | 医疗保健 API 中的 FHIR 服务| 注释 |
|------------------------|-----------|-----------|---------|
| $export（整个系统） | 是       | 是       |         |
| 患者/$export        | 是       | 是       |         |
| 组/$export          | 是       | 是       |         |
| $convert-data          | 是       | 是       |         |
| $validate              | 是       | 是       |         |
| $member-match          | 是       | 是       |         |
| $patient-everything    | 是       | 是       |         |
| $purge-历史记录         | 是       | 是       |         |

## <a name="role-based-access-control"></a>基于角色的访问控制

FHIR 服务使用[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)进行访问控制。 

## <a name="service-limits"></a>服务限制

* 捆绑大小 - 每个捆绑限制为 500 个项。

* **订阅限制** -默认情况下，每个订阅最多只能有10个 FHIR 服务。 此限制可用于一个或多个工作区。 

## <a name="next-steps"></a>后续步骤

本文介绍了 FHIR 服务中支持的 FHIR 功能。 接下来，部署 FHIR 服务。
 
>[!div class="nextstepaction"]
>[部署 FHIR 服务](fhir-portal-quickstart.md)
