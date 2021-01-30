---
title: 通过专用终结点建立索引器连接
titleSuffix: Azure Cognitive Search
description: 配置索引器连接以从通过专用终结点保护的其他 Azure 资源访问内容。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 762db9d165358f3347fc9b7f3aaaf39f0c762308
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063190"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>通过专用终结点建立索引器连接

许多 Azure 资源（例如 Azure 存储帐户）可配置为接受来自虚拟网络列表的连接，并拒绝来自公共网络的外部连接。 如果正在使用索引器对 Azure 认知搜索中的数据编制索引，并且数据源位于专用网络上，则可以创建用于访问数据的出站 [专用终结点连接](../private-link/private-endpoint-overview.md) 。

此索引器连接方法服从以下两个要求：

+ 提供内容或代码的 Azure 资源之前必须已注册到 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)。

+ Azure 认知搜索服务必须位于 "基本" 层或更高级别。 此功能在免费层上不可用。 此外，如果索引器具有技能组合，则层必须是标准 2 (S2) 或更高版本。 有关详细信息，请参阅 [服务限制](search-limits-quotas-capacity.md#shared-private-link-resource-limits)。

## <a name="shared-private-link-resources-management-apis"></a>共享的专用链接资源管理 Api

通过 Azure 认知搜索 Api 创建的安全资源的专用终结点称为 *共享的专用链接资源*。 这是因为你要对已与 [Azure Private Link service](https://azure.microsoft.com/services/private-link/)集成的资源（例如存储帐户）进行 "共享" 访问。

通过其管理 REST API，Azure 认知搜索提供 [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) 操作，可用于配置 Azure 认知搜索索引器的访问权限。

仅可使用搜索管理 API (版本 *2020-08-01-preview* 或更高版本) （在下表中指定为 *预览版* ）的预览版本创建到某些资源的专用终结点连接。 使用预览版或 (*2020-08-01* 或更高版本) ，可以创建不带 *预览* 标志的资源。

下表列出了可从 Azure 认知搜索创建出站专用终结点的 Azure 资源。 若要创建共享专用链接资源，请输入与在 API 中写入的 **组 ID** 值完全相同的值。 这些值区分大小写。

| Azure 资源 | 组 ID |
| --- | --- |
| Azure 存储-Blob (或) ADLS 第2代 | `blob`|
| Azure 存储-表 | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL 数据库 | `sqlServer`|
| Azure Database for MySQL (预览版)  | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (预览版)  | `sites` |

你还可以使用 [受支持的 api 列表](/rest/api/searchmanagement/privatelinkresources/listsupported)查询支持出站专用终结点连接的 Azure 资源。

在本文的剩余部分中，如果你更喜欢) 和[Postman](https://www.postman.com/) (或其他任何 HTTP 客户端[（例如，](https://curl.se/)如果你更希望) 用于演示 REST API 调用，则[Azure CLI](https://docs.microsoft.com/cli/azure/) (或[ARMClient](https://github.com/projectkudu/ARMClient) ）的混合。

> [!NOTE]
> 本文中的示例基于以下假设：
> * 搜索服务的名称是 _contoso-搜索_，后者位于订阅 ID 为 _00000000-0000-0000-0000-000000000000_ 的订阅的 _contoso_ 资源组中。 
> * 此搜索服务的资源 ID 为 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_。

其余示例显示了如何配置 _contoso 搜索_ 服务，以便其索引器可以从安全存储帐户 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage_ 访问数据。

## <a name="secure-your-storage-account"></a>保护你的存储帐户

将存储帐户配置为 [仅允许来自特定子网的访问](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)。 在 Azure 门户中，如果选择此选项并将设置为空，则表示不允许来自虚拟网络的流量。

   !["防火墙和虚拟网络" 窗格的屏幕截图，显示允许访问所选网络的选项。 ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> 可以使用 [受信任的 Microsoft 服务方法](../storage/common/storage-network-security.md#trusted-microsoft-services) ，绕过存储帐户的虚拟网络或 IP 限制。 还可以使搜索服务访问存储帐户中的数据。 为此，请参阅 [使用受信任的服务异常访问 Azure 存储的索引器](search-indexer-howto-access-trusted-service-exception.md)。 
>
> 但是，当你使用此方法时，Azure 认知搜索与存储帐户之间的通信将通过安全的 Microsoft 骨干网络通过存储帐户的公共 IP 地址进行。

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>步骤1：创建存储帐户的共享专用链接资源

若要请求 Azure 认知搜索创建到存储帐户的出站专用终结点连接，请执行以下 API 调用，如 [Azure CLI](https://docs.microsoft.com/cli/azure/)所示： 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

或者，如果想要使用 [ARMClient](https://github.com/projectkudu/ARMClient)：

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

文件 *上create-pe.js* 的内容表示 API 的请求正文，如下所示：

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`成功时返回响应。 创建出站专用终结点的过程是长时间运行的 (异步) 操作。 它包括部署以下资源：

* 专用终结点，使用状态中的专用 IP 地址分配 `"Pending"` 。 专用 IP 地址是从分配给搜索服务特定的专用索引器的执行环境的虚拟网络的地址空间中获得的。 获得专用终结点批准后，从 Azure 认知搜索到存储帐户的任何通信均源自专用 IP 地址和安全的专用链接通道。

* 基于的资源类型的专用 DNS 区域 `groupId` 。 通过部署此资源，可确保对专用资源的任何 DNS 查找都利用与专用终结点关联的 IP 地址。

请确保为要为 `groupId` 其创建专用终结点的资源的类型指定正确的。 任何不匹配都将导致不成功的响应消息。

与所有异步 Azure 操作一样， `PUT` 调用将返回一个 `Azure-AsyncOperation` 标头值，如下所示：

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

可以定期轮询此 URI 以获取操作的状态。 在继续操作之前，我们建议你等待，直到共享专用链接资源操作的状态达到终端状态 (即，操作的状态为 "已 *成功* ") 。

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

或使用 ARMClient：

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>步骤2a：为存储帐户批准专用终结点连接

> [!NOTE]
> 在本部分中，将使用 Azure 门户逐步完成到存储的专用终结点的审批流。 或者，可以使用通过存储资源提供程序提供的 [REST API](/rest/api/storagerp/privateendpointconnections) 。
>
> 其他提供程序（如 Azure Cosmos DB 或 Azure SQL Server）提供了类似的存储资源提供程序 Api 用于管理专用终结点连接。

1. 在 Azure 门户中，选择存储帐户的 " **专用终结点连接** " 选项卡。 异步操作成功后，应该会对专用终结点连接请求与之前的 API 调用中的请求消息进行请求。

   ![Azure 门户屏幕截图，显示 "专用终结点连接" 窗格。](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. 选择 Azure 认知搜索创建的专用终结点。 在 " **专用终结点** " 列中，按先前 API 中指定的名称标识专用终结点连接，选择 " **批准**"，然后输入相应的消息。 消息内容不重要。 

   请确保 "专用终结点连接" 出现，如以下屏幕截图所示。 可能需要一到两分钟的时间才能在门户中更新状态。

   ![Azure 门户的屏幕截图，显示 "专用终结点连接" 窗格上的 "已批准" 状态。](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

批准专用终结点连接请求后，流量 *可以* 流经专用终结点。 批准专用终结点后，Azure 认知搜索会在为其创建的 DNS 区域中创建所需的 DNS 区域映射。

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>步骤2b：查询共享的专用链接资源的状态

若要在批准后确认共享的专用链接资源已更新，请使用 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)获取其状态。

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

或使用 ARMClient：

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

如果 `properties.provisioningState` 资源的为 `Succeeded` 且 `properties.status` 为，则 `Approved` 表示共享的专用链接资源正常运行，并且可以将索引器配置为通过专用终结点进行通信。

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>步骤3：将索引器配置为在专用环境中运行

> [!NOTE]
> 在批准专用终结点连接之前，可以执行此步骤。 在批准专用终结点连接之前，任何尝试与安全 (资源（如存储帐户) ）进行通信的索引器都将在暂时性故障状态下结束。 将无法创建新的索引器。 一旦批准专用终结点连接，索引器就可以访问专用存储帐户。

1. [创建一个数据源，该数据源](/rest/api/searchservice/create-data-source) 指向安全存储帐户和存储帐户中的相应容器。 以下屏幕截图显示了 Postman 中的此请求。

   ![显示在 Postman 用户界面上创建数据源的屏幕截图。](media\search-indexer-howto-secure-access\create-ds.png )

1. 同样， [创建一个索引](/rest/api/searchservice/create-index) ，并根据需要使用 REST API [创建一个技能组合](/rest/api/searchservice/create-skillset) 。

1. [创建一个索引器，该索引器](/rest/api/searchservice/create-indexer) 指向在上一步中创建的数据源、索引和技能组合。 此外，通过将索引器的配置属性设置为，强制索引器在专用执行环境中运行 `executionEnvironment` `private` 。

   ![显示在 Postman 用户界面上创建索引器的屏幕截图。](media\search-indexer-howto-secure-access\create-idr.png)

   成功创建索引器后，它应从通过专用终结点连接的存储帐户开始索引内容。 您可以使用 [索引器状态 API](/rest/api/searchservice/get-indexer-status)来监视索引器的状态。

> [!NOTE]
> 如果已有索引器，则可以通过将设置为来通过 [PUT API](/rest/api/searchservice/create-indexer) 更新这些索引器 `executionEnvironment` `private` 。

## <a name="troubleshooting"></a>疑难解答

- 如果创建索引器失败，并出现错误消息，例如 "数据源凭据无效"，则表示尚未 *批准* 专用终结点连接的状态，或者连接不起作用。 若要解决此问题： 
  * 使用 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)获取共享的专用链接资源的状态。 如果状态为 "已 *批准*"，请检查 `properties.provisioningState` 资源的。 如果此处的状态为 `Incomplete` ，则表示资源的某些基本依赖项未能设置。 `PUT`重新发出请求以重新创建共享的专用链接资源应该可以解决此问题。 可能需要重新审核。 重新检查资源的状态以验证问题是否已修复。

- 如果创建索引器时未设置其 `executionEnvironment` 属性，则创建可能会成功，但其执行历史记录将显示索引器运行不成功。 若要解决此问题：
   * [更新索引器](/rest/api/searchservice/update-indexer) 以指定执行环境。

- 如果在未设置属性的情况下创建索引器 `executionEnvironment` 并成功运行，则意味着 Azure 认知搜索已决定其执行环境是特定于搜索服务的 *专用* 环境。 这可能会更改，具体取决于索引器使用的资源、搜索服务上的负载以及其他因素，稍后可能会失败。 若要解决此问题：
  * 我们强烈建议你将属性设置 `executionEnvironment` 为， `private` 以确保它不会在将来失败。

[配额和限制](search-limits-quotas-capacity.md) 确定可以创建多少共享的专用链接资源，以及依赖于搜索服务的 SKU。

## <a name="next-steps"></a>后续步骤

了解有关专用终结点的详细信息：

- [什么是专用终结点？](../private-link/private-endpoint-overview.md)
- [专用终结点所需的 DNS 配置](../private-link/private-endpoint-dns.md)
