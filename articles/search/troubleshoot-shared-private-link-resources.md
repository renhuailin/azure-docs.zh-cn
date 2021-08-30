---
title: 排查共享专用链接资源问题
titleSuffix: Azure Cognitive Search
description: 管理共享专用链接资源时常见问题的故障排除指南。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/30/2021
ms.openlocfilehash: 81bae18cdc4a977ef03ddf807f9277037d939bd0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195855"
---
# <a name="troubleshooting-common-issues-with-shared-private-link-resources"></a>排查共享专用链接资源的常见问题

共享专用链接资源允许 Azure 认知搜索建立安全的出站连接来访问客户资源。 但是，在管理（创建、删除或更新）这些资源的过程中，可能会发生几种不同类型的错误。

## <a name="creating-a-shared-private-link-resource"></a>创建共享专用链接资源

创建共享专用链接资源涉及四个不同的步骤：

1. 客户在搜索资源提供程序 (RP) 上调用管理平面 [CreateOrUpdate API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) 并提供有关要创建的共享专用链接资源的详细信息。

2. 搜索 RP 验证请求，验证是否开始异步 Azure 资源管理器操作（客户可以查询其进度）

3. 搜索完成操作的查询（通常需要几分钟）。 此时，共享专用链接资源的预配状态为“正在更新”。

4. 操作成功完成后，将创建一个专用终结点（以及所有 DNS 区域和映射）。 此时，如果客户查询共享专用链接资源的状态，则其预配状态为“已成功”。

![创建共享专用链接资源时所涉及的步骤 ](media\troubleshoot-shared-private-link-resources\shared-private-link-states.png)

下面列出了创建阶段的一些常见错误。

### <a name="request-validation-failures"></a>请求验证失败

+ 不支持 SKU：只能为付费 SKU 创建共享专用链接资源，不支持免费层服务。

+ 名称验证：共享专用链接资源名称仅限于一组特定的字符。 如果资源名称包含任何无效字符，将不会接受创建资源的请求。
共享专用链接资源的命名规则：
  
  + 长度应介于 1 和 60 个字符之间。
  + 应仅包含字母数字字符或下划线 (_)、句点 (.) 或连字符 (-) 等字符

+ `groupId` 验证：`groupId` 指定为创建共享专用链接资源的请求的一部分，应该与下表匹配（无论是拼写还是大小写）：

| Azure 资源 | 组 ID | 第一个可用的 API 版本 |
| --- | --- | --- |
| Azure 存储 - Blob（或）ADLS Gen 2 | `blob`| `2020-08-01` |
| Azure 存储 - 表 | `table`| `2020-08-01` |
| Azure Cosmos DB - SQL API | `Sql`| `2020-08-01` |
| Azure SQL 数据库 | `sqlServer`| `2020-08-01` |
| Azure Database for MySQL（预览版） | `mysqlServer`| `2020-08-01-Preview` |
| Azure Key Vault | `vault` | `2020-08-01` |
| Azure Functions（预览版） | `sites` | `2020-08-01-Preview` |

标记为“（预览版）”的资源仅在预览版管理平面 API 版本中提供，尚未正式发布。 其他任何 `groupId`（或在不支持它的 API 版本中使用的 `groupId`）将无法通过验证。

+ `privateLinkResourceId` 类型验证：类似于 `groupId`，Azure 认知搜索验证 `privateLinkResourceId` 中是否指定了正确的资源类型。 下面是有效的资源类型：

| Azure 资源 | 资源类型 | 第一个可用的 API 版本 |
| --- | --- | --- |
| Azure 存储 | `Microsoft.Storage/storageAccounts`| `2020-08-01` |
| Azure Cosmos DB | `Microsoft.DocumentDb/databaseAccounts`| `2020-08-01` |
| Azure SQL 数据库 | `Microsoft.Sql/servers`| `2020-08-01` |
| Azure Database for MySQL（预览版） | `Microsoft.DBforMySQL/servers`| `2020-08-01-Preview` |
| Azure Key Vault | `Microsoft.KeyVault/vaults` | `2020-08-01` |
| Azure Functions（预览版） | `Microsoft.Web/sites` | `2020-08-01-Preview` |

此外，指定的 `groupId` 需要对指定的资源类型有效。 例如，`groupId`“blob”对类型“Microsoft.Storage/storageAccounts”有效，不能用于任何其他资源类型。 对于给定的搜索管理 API 版本，客户可以使用[列出受支持的 API](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported)来找出受支持的 `groupId` 和资源类型的详细信息。

+ 强制执行配额限制：搜索服务对可创建的共享专用链接资源的不同数量和正在使用的各种目标资源类型的数量施加配额（基于 `groupId`）。 这些已记录在 Azure 认知搜索服务限制页面的[共享专用链接资源限制部分](search-limits-quotas-capacity.md#shared-private-link-resource-limits)中。

### <a name="azure-resource-manager-deployment-failures"></a>Azure 资源管理器部署失败

搜索服务发起创建共享专用链接的请求，但由 Azure 资源管理器执行实际工作。 可在门户中或通过查询[检查部署状态](search-indexer-howto-access-private.md#step-3-check-the-status-of-the-private-endpoint-creation)，并解决可能发生的所有错误。

Azure 资源管理器部署失败的共享专用链接资源将显示在 [List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service) 和 [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) API 调用中，但“预配状态”为 `Failed`。 确定 Azure 资源管理器部署失败的原因后，请删除 `Failed` 资源，并在应用下表中的相应解决方法后重新创建它。

| 部署失败原因 | 说明 | 解决方法 |
| --- | --- | --- |
| 网络资源提供程序未在目标资源的订阅上注册 | 通过 `Microsoft.Network` 资源提供程序 (RP) 为目标资源（存储帐户、CosmosDB、SQL Server 等）创建专用终结点（和关联的 DNS 映射）。 如果托管目标资源的订阅（“目标订阅”）未与 `Microsoft.Network` RP 注册，则 Azure 资源管理器部署可能会失败。 | 客户需要在目标订阅中注册此 RP。 通常，这可以通过 Azure 门户、PowerShell 或 CLI 完成，如[本指南](../azure-resource-manager/management/resource-providers-and-types.md)所述 |
| 目标资源的 `groupId` 无效 | 创建 CosmosDB 帐户后，客户可以指定数据库帐户的 API 类型。 尽管 CosmosDB 提供了几种不同的 API 类型，但 Azure 认知搜索仅支持将 SQL 作为共享专用链接资源的 `groupId`。 为指向非 SQL 数据库帐户的 `privateLinkResourceId` 创建SQL 共享专用链接资源时，Azure 资源管理器部署将因 `groupId` 不匹配而失败。 CosmosDB 帐户的 Azure 资源 ID 不足，无法确定所使用的 API 类型。 Azure 认知搜索尝试创建专用终结点，而该终结点随后被 CosmosDB 拒绝。 | 客户应确保所指定 CosmosDB 资源的 `privateLinkResourceId` 适用于 SQL API 类型的数据库帐户 |
| 找不到目标资源 | 仅在 Azure 资源管理器部署开始期间检查 `privateLinkResourceId` 中指定的目标资源是否存在。 如果目标资源不再可用，则部署将失败。 | 客户应确保目标资源存在于指定的订阅和资源组中，并且未被移动/删除 |
| 暂时性/其他错误 | Azure 资源管理器部署可能会因基础结构故障或其他意外原因而失败。 这种情况应该很少见，通常指示暂时性状态。 | 请稍后再尝试创建此资源。 如果问题仍然存在，请与 Azure 支持部门联系。 |

### <a name="resource-stuck-in-updating-or-incomplete-state"></a>资源卡在“正在更新”或“未完成”状态

通常，共享专用链接资源应在搜索 RP 接受请求后的几分钟内进入终端状态（`Succeeded` 或 `Failed`）。

在极少数情况下，Azure 认知搜索可能无法将共享专用链接资源的状态正确地标记为终端状态（`Succeeded` 或 `Failed`）。 这通常是由于搜索 RP 中出现意外或灾难性故障。 如果共享专用链接资源已卡在非终端状态超过 8 小时，则会自动转换为 `Failed`。

如果你发现共享专用链接资源尚未转换为终端状态，请等待 8 小时，确保它已处于 `Failed` 状态，然后删除它，再重新创建。 或者你不用等待，可以尝试使用其他名称创建另一个共享专用链接资源（其他所有参数保持不变）。

## <a name="updating-a-shared-private-link-resource"></a>更新共享专用链接资源

可以使用[创建或更新 API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update)更新现有的共享专用链接资源。 搜索 RP 只允许对共享专用链接资源进行小范围更新 - 通过此 API 只能修改请求消息。

+ 无法更新现有共享专用链接资源（例如 `privateLinkResourceId` 和 `groupId`）的任何核心属性，并且将始终不支持此操作。 如果需要更改除请求消息以外的任何其他属性，我们建议客户删除并重新创建共享专用链接资源。

+ 仅当共享专用链接资源已达到 `Succeeded` 预配状态时，才可以尝试更新其请求消息。

## <a name="deleting-a-shared-private-link-resource"></a>删除共享专用链接资源

客户可以通过[删除 API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/delete) 删除现有的共享专用链接资源。 与创建（或更新）过程相似，这也是一个异步操作，分为四个步骤：

1. 客户请求搜索 RP 删除共享专用链接资源。

2. 搜索 RP 验证资源是否存在且是否处于有效删除状态。 如果是，它会启动 Azure 资源管理器删除操作以删除资源。

3. 搜索完成操作的查询（通常需要几分钟）。 此时，共享专用链接资源的预配状态为“正在删除”。

4. 操作成功完成后，将删除备份专用终结点和任何关联的 DNS 映射。 该资源将不会显示为 [List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service) 操作的一部分，并且对该资源尝试执行 [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) 操作将导致出现“404 Not Found”错误。

![删除共享专用链接资源时所涉及的步骤 ](media\troubleshoot-shared-private-link-resources\shared-private-link-delete-states.png)

下面列出了删除阶段的一些常见错误。

| 失败类型 | 说明 | 解决方法 |
| --- | --- | --- |
| 资源处于非终端状态 | 不能删除未处于终端状态（`Succeeded` 或 `Failed`）的共享专用链接资源。 共享专用链接资源有可能（但很少）卡在非终端状态长达 8 个小时。 | 等待资源达到终端状态，然后重试删除请求。 |
| 删除操作失败，出现“冲突”错误 | 在删除共享专用链接资源的 Azure 资源管理器操作可以删除专用终结点和 DNS 映射之前，它会先联系 `privateLinkResourceId` 中指定的目标资源的资源提供者（“目标 RP”）。 客户可以利用 [Azure 资源锁](../azure-resource-manager/management/lock-resources.md)来防止其资源发生任何更改。 当 Azure 资源管理器联系目标 RP 时，它要求目标 RP 修改目标资源的状态（以从其元数据中删除有关专用终结点的详细信息）。 当目标资源（或其资源组/订阅）上配置了锁后，Azure 资源管理器操作将失败，并显示“冲突”（和相应的详细信息）。 不会删除共享专用链接资源。 | 客户应在重试删除操作之前移除目标资源的锁。 **注意**：当客户尝试删除具有指向锁定目标资源的共享专用链接资源的搜索服务时，也会发生此问题 |
| 删除操作失败 | 在极少数情况下，异步 Azure 资源管理器删除操作可能会失败。 如果此操作失败，查询异步操作的状态将向客户显示错误消息和相应的详细信息。 | 请稍后重试该操作，如果问题仍然存在，请联系 Azure 支持部门。
| 资源卡在“正在删除”状态 | 在极少数情况下，共享专用链接资源可能会卡在“正在删除”状态长达 8 个小时，这可能是由于搜索 RP 发生灾难性故障。 | 等待 8 小时后，资源将转换为 `Failed` 状态，然后请重新发出请求。|

## <a name="next-steps"></a>后续步骤

了解有关共享专用链接资源的详细信息，以及如何使用它来安全访问受保护的内容。

+ [通过索引器访问受保护的内容](search-indexer-howto-access-private.md)
+ [REST API 参考](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources)