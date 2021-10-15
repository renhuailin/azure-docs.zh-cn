---
title: 通过专用终结点的索引器连接
titleSuffix: Azure Cognitive Search
description: 配置索引器连接以访问通过专用终结点保护的其他 Azure 资源中的内容。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 484f52656c5e49113d50f25a94a33b94ed886ab0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359197"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>建立通过专用终结点的索引器连接

> [!NOTE]
> 可以使用[受信任 Microsoft 服务方法](../storage/common/storage-network-security.md#trusted-microsoft-services)绕过存储帐户的虚拟网络或 IP 限制。 还可以启用搜索服务以访问存储帐户中的数据。 为此，请参阅[使用受信任的服务异常对 Azure 存储进行索引器访问](search-indexer-howto-access-trusted-service-exception.md)。
> 
> 但是使用此方法时，Azure 认知搜索与存储帐户之间的通信会在安全的 Microsoft 骨干网络上通过存储帐户的公共 IP 地址进行。

许多 Azure 资源（例如 Azure 存储帐户）可配置为接受来自一组虚拟网络的连接，并拒绝来自公共网络的外部连接。 如果正使用索引器在 Azure 认知搜索中为数据编制索引，并且数据源位于专用网络上，则可以创建出站[专用终结点连接](../private-link/private-endpoint-overview.md)来访问数据。

此索引器连接方法需符合以下两个要求：

+ 必须预先向 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)注册提供内容或代码的 Azure 资源。

+ Azure 认知搜索服务必须位于基本层或更高层。 此功能在免费层上不可用。 此外，如果你的索引器具有技能组，层级必须是标准 2 (S2) 或更高层级。 有关详细信息，请参阅[服务限制](search-limits-quotas-capacity.md#shared-private-link-resource-limits)。

## <a name="shared-private-link-resources-management-apis"></a>共享的专用链接资源管理 API

通过 Azure 认知搜索 API 创建的安全资源的专用终结点称为共享的专用链接资源。 这是因为你正在“共享”对已与 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)集成的资源（例如存储帐户）的访问权限。

Azure 认知搜索通过管理 REST API 提供 [CreateOrUpdate](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) 操作，你可将该操作用于配置来自 Azure 认知搜索索引器的访问。

你可以只使用搜索管理 API 的预览版本（2020-08-01 预览版或更高预览版）创建与某些资源（下表中指定为预览版）的专用终结点连接 。 可以使用预览版或正式版 API（2020-08-01 或更高版本）创建未标记“预览”二字的资源 。

下表列出了可从 Azure 认知搜索创建出站专用终结点的 Azure 资源。 若要创建共享专用链接资源，请输入与在 API 中写入的组 ID 值完全相同的值。 这些值区分大小写。

| Azure 资源 | 组 ID |
| --- | --- |
| Azure 存储 - Blob | `blob`|
| Azure 存储 - Data Lake Storage Gen2 | `dfs` 和 `blob` |
| Azure 存储 - 表 | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Azure SQL 数据库 | `sqlServer`|
| Azure Database for MySQL（预览版） | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions（预览版） | `sites` |

你还可以使用[受支持的 API 列表](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported)查询支持出站专用终结点连接的 Azure 资源。

在本文的剩余部分中，会结合使用 Azure 门户（如果愿意也可使用 [Azure CLI](/cli/azure/)）和 [Postman](https://www.postman.com/)（如果愿意，也可使用类似于 [curl](https://curl.se/) 的任何其他 HTTP 客户端）来演示 REST API 调用。

> [!NOTE]
> 某些 Azure 认知搜索数据源和其他配置需要创建多个共享专用链接才能正常工作。 下面是存在此要求的配置列表，以及每个配置所需的组 ID：
> * Azure Data Lake Storage Gen2 数据源 - 创建两个共享专用链接：一个共享专用链接的 groupID 为“dfs”，另一个共享专用链接的 groupID 为“blob”。
> * 配置了知识存储的技能组 - 需要一个或两个共享专用链接，具体取决于为知识存储设置的投影：
>   * 如果使用 Blob 和/或文件投影，请创建一个 groupID 为“blob”的共享专用链接。 
>   * 如果使用表投影，请创建一个 groupID 为“table”的共享专用链接。 
>   * 如果使用 Blob/文件投影和表投影，请创建两个共享专用链接：一个共享专用链接的 groupID 为“blob”，另一个共享专用链接的 groupID 为“table”。 
> * 启用了缓存的索引器 - 创建两个共享专用链接：一个共享专用链接的 groupID 为“table”，另一个共享专用链接的 groupID 为“blob”。

## <a name="set-up-indexer-connection-through-private-endpoint"></a>通过专用终结点设置索引器连接

按照以下说明，设置通过专用终结点连接到安全 Azure 资源的索引器。

本文中的示例基于以下假设：
* 搜索服务的名称是 contoso-search，它存在于订阅 ID 为 00000000-0000-0000-0000-000000000000 的订阅的 contoso 资源组中  。 
* 此搜索服务的资源 ID 为 /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search。

### <a name="step-1-secure-your-azure-resource"></a>步骤 1：保护 Azure 资源

限制访问的步骤因资源而异。 以下方案显示了三种更常见的资源类型。

- 方案 1：Azure 存储

    以下示例演示如何配置 Azure 存储帐户防火墙。 若选择此选项并将页面留空，则表示不允许来自虚拟网络的流量。

    ![Azure 存储“防火墙和虚拟网络”窗格的屏幕截图，显示允许访问所选网络的选项。](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

- 方案 2：Azure Key Vault

    以下示例演示如何配置 Azure 密钥保管库防火墙。
 
    ![Azure　Key Vault“防火墙和虚拟网络”窗格的屏幕截图，显示允许访问所选网络的选项。](media\search-indexer-howto-secure-access\key-vault-firewall-noaccess.png)
    
- 方案 3：Azure Functions

    无需为 Azure Functions 防火墙更改网络设置。 在后续步骤中创建共享专用终结点时，创建连接到 Functions 的共享专用终结点后，Functions 将自动变为只允许通过专用链接访问。

### <a name="step-2-create-a-shared-private-link-resource-to-the-azure-resource"></a>步骤 2：创建 Azure 资源的共享专用链接资源

以下部分介绍如何使用 Azure 门户或 Azure CLI 创建共享专用链接资源。 

#### <a name="option-1-portal"></a>选项 1：门户

> [!NOTE]
> 门户仅支持使用 GA 的组 ID 值创建共享专用终结点。 对于 MySQL 和 Azure Functions，请使用选项 2 中所述的 Azure CLI 步骤，如下所示。

若要请求 Azure 认知搜索以创建出站专用终结点连接，请通过“共享专用访问”边栏选项卡，单击“添加共享专用访问”。 在右侧打开的边栏选项卡中，可选择“连接到目录中的 Azure 资源”或“按资源 ID 或别名连接到 Azure 资源”。

使用第一个选项时（推荐使用），边栏选项卡将有助于选择适当的 Azure 资源，且会自动填充其他属性，如资源的组 ID 和资源类型。

   ![“添加共享专用访问”窗格的屏幕截图，其中显示了创建共享专用链接资源的引导式体验。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource.png)

使用第二个选项时，你可手动输入 Azure 资源 ID，并选择适当的组 ID。 本文开头列出了组 ID。

![“添加共享专用访问”窗格的屏幕截图，其中显示了创建共享专用链接资源的手动操作体验。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-manual.png)

#### <a name="option-2-azure-cli"></a>选项 2：Azure CLI

或者，你也可通过 [Azure CLI](/cli/azure/) 进行以下 API 调用。 若使用的组 ID 为预览状态，请使用 2020-08-01-preview API 版本。 例如，组 ID 站点和 mysqlServer 为预览状态，并要求你使用预览版 API。

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/<search service subscription ID>/resourceGroups/<search service resource group name>/providers/Microsoft.Search/searchServices/<search service name>/sharedPrivateLinkResources/<shared private endpoint name>?api-version=2020-08-01 --body @create-pe.json
```

下面是 create-pe.jso 文件内容的示例：

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

如果成功，返回响应 `202 Accepted`。 创建出站专用终结点的过程是一个长期（异步）操作。 它包括部署以下资源：

+ 专用终结点，分配的专用 IP 地址处于 `"Pending"` 状态。 专用 IP 地址是从地址空间（已分配给搜索服务特定专用索引器的执行环境的虚拟网络）获得的。 获得专用终结点批准后，从 Azure 认知搜索到存储帐户的任何通信都源自专用 IP 地址和安全的专用链接通道。

+ 基于 `groupId` 的资源类型的专用 DNS 区域。 通过部署此资源，可确保对专用资源的任何 DNS 查找都利用与专用终结点关联的 IP 地址。

务必为要创建专用终结点的这一类资源指定正确的 `groupId`。 任何不匹配都将导致得到表示失败的响应消息。

### <a name="step-3-check-the-status-of-the-private-endpoint-creation"></a>步骤 3：检查专用终结点创建状态

在此步骤中，你将确认资源的预配状态更改为“已成功”。

#### <a name="option-1-portal"></a>选项 1：门户

> [!NOTE]
> 对于预览状态中的 GA 和组 ID，预配状态将在门户中可见。

门户将显示共享专用终结点的状态。 下例显示“正在更新”的状态。

![“添加共享专用访问”窗格的屏幕截图，其中显示正在创建资源。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-progress.png)

成功创建资源后，用户将收到门户通知，并且资源的预配状态将更改为“成功”。

![“添加共享专用访问”窗格的屏幕截图，其中显示已完成资源创建。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-success.png)

#### <a name="option-2-azure-cli"></a>选项 2：Azure CLI

用于创建共享专用终结点的 `PUT` 调用将返回如下所示的 `Azure-AsyncOperation` 标头值：

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

你可手动查询 `Azure-AsyncOperationHeader` 值来轮询状态。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01
```

### <a name="step-4-approve-the-private-endpoint-connection"></a>步骤 4：审批专用终结点连接

> [!NOTE]
> 在本部分中，你会使用 Azure 门户逐步完成连接到 Azure 资源的专用终结点审批流。 或者，可以使用通过存储资源提供程序提供的 [REST API](/rest/api/storagerp/privateendpointconnections)。
>
> 其他提供程序（如 Azure Cosmos DB 或 Azure SQL Server）提供了类似的存储资源提供程序 API 来管理专用终结点连接。

1. 在 Azure 门户，导航到要连接的 Azure 资源，并选择“网络”选项卡。随后导航到列出专用终结点连接的部分。 下面是存储帐户的示例。 异步操作成功后，应该会有一个专用终结点连接的请求，请求消息来自于之前的 API 调用。

   ![Azure 门户的屏幕截图，其中显示“专用终结点”窗格。](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. 选择 Azure 认知搜索创建的专用终结点。 在“专用终结点”列中，使用在前面的 API 中指定的名称标识专用终结点连接，选择“批准”，然后输入相应的消息 。 消息内容不重要。

   请确保显示的专用终结点连接如以下屏幕截图所示。 状态可能需要一到两分钟的时间才能在门户中更新。

   ![Azure 门户的屏幕截图，显示“专用终结点连接”窗格上的“已批准”状态。](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

批准专用终结点连接请求后，流量就能通过专用终结点传输。 批准专用终结点后，Azure 认知搜索会在为其创建的 DNS 区域中创建所需的 DNS 区域映射。

### <a name="step-5-query-the-status-of-the-shared-private-link-resource"></a>步骤 5：查询共享专用链接资源的状态

若要在批准后确认共享专用链接资源已更新，请在 Azure 门户上重新访问搜索服务的“共享专用访问”边栏选项卡，并检查“连接状态”。

   ![Azure 门户的屏幕截图，其中显示“已批准”共享专用链接资源。](media\search-indexer-howto-secure-access\new-shared-private-link-resource-approved.png)

或者，也可以使用 [GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) 获取“连接状态”。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01
```

这会返回一个 JSON，其中连接状态会在“属性”部分下显示为“状态”。 下面是存储帐户的示例。

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

如果资源的“预配状态”(`properties.provisioningState`) 为 `Succeeded`，“连接状态”(`properties.status`) 为 `Approved`，则表示共享专用链接资源正常工作，索引器可以配置为通过专用终结点进行通信。

### <a name="step-6-configure-the-indexer-to-run-in-the-private-environment"></a>步骤 6：将索引器配置为在专用环境中运行

> [!NOTE]
> 你可以在批准专用终结点连接之前执行此步骤。 在批准专用终结点连接之前，任何尝试与安全资源（例如存储帐户）通信的索引器最终都将处于暂时失败状态。 新索引器的创建将失败。 一旦批准了专用终结点连接，索引器就可以访问专用存储帐户。

以下步骤演示如何使用 REST API 将索引器配置为在专用环境中运行。 你也可在门户中使用 JSON 编辑器设置执行环境。

1. 如常创建数据源定义、索引和技能组（若正在使用某个技能组）。 使用共享专用终结点时，这些定义中的属性不会发生任何变化。

1. [创建一个索引器](/rest/api/searchservice/create-indexer)，该索引器指向在前面的步骤中创建的数据源、索引和技能组。 此外，通过将索引器的 `executionEnvironment` 配置属性设置为 `private`，强制索引器在专用执行环境中运行。

    ```json
    {
        "name": "indexer",
        "dataSourceName": "blob-datasource",
        "targetIndexName": "index",
        "parameters": {
            "configuration": {
                "executionEnvironment": "private"
            }
        },
        "fieldMappings": []
    }
    ```

    下面是 Postman 的请求示例。
    
    ![显示在 Postman 用户界面上创建索引器的屏幕截图。](media\search-indexer-howto-secure-access\create-indexer.png)    

成功创建索引器后，它应通过专用终结点连接到 Azure 资源。 你可以使用[索引器状态 API](/rest/api/searchservice/get-indexer-status) 来监视索引器的状态。

> [!NOTE]
> 若已具备现有索引器，你可将 `executionEnvironment` 设置为 `private`或在门户中使用 JSON 编辑器，以便通过 [PUT API](/rest/api/searchservice/create-indexer) 进行更新。

## <a name="troubleshooting"></a>故障排除

+ 如果索引器创建失败并显示“数据源凭据无效”等错误消息，说明要么专用终结点连接的状态还不是“已批准”，要么连接不起作用。 解决此问题的方法： 
  + 使用 [GET API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) 获取共享专用链接资源的状态。 如果状态为“已批准”，请检查资源的 `properties.provisioningState`。 如果此处的状态为 `Incomplete`，表示资源的某些基本依赖项设置失败。 重新发出 `PUT` 请求以重新创建共享的专用链接资源，这样做应该能解决此问题。 可能需要重新审批。 重新检查资源的状态以验证问题是否已得到解决。

+ 如果创建索引器时未设置其 `executionEnvironment` 属性，创建可能会成功，但其执行历史记录会显示索引器运行不成功。 解决此问题的方法：
  + [更新索引器](/rest/api/searchservice/update-indexer)以指定执行环境。

+ 如果在未设置 `executionEnvironment` 属性的情况下创建了索引器并且它成功运行，说明 Azure 认知搜索已确定其执行环境是特定于搜索服务的专用环境。 根据索引器消耗的资源、搜索服务的负载和其他因素，该环境可能会改变，而且之后可能会失败。 解决此问题的方法：
  + 我们强烈建议你将 `executionEnvironment` 属性设置为 `private`，以确保它将来不会失败。

+ 如果在 Azure 门户中查看数据源的网络页面时，选择为 Azure 认知搜索服务创建的专用终结点来访问此数据源，可能会收到“无访问权限”错误。 这是正常情况。 可以通过目标服务的门户页面更改连接请求的状态，但要进一步管理共享专用链接资源，需要在 Azure 门户的搜索服务网络页面中查看共享专用链接资源。

[配额和限制](search-limits-quotas-capacity.md)确定可创建的共享专用链接资源数，这取决于搜索服务的 SKU。

## <a name="next-steps"></a>后续步骤

详细了解专用终结点：

+ [排查共享专用链接资源的问题](troubleshoot-shared-private-link-resources.md)
+ [什么是专用终结点？](../private-link/private-endpoint-overview.md)
+ [专用终结点所需的 DNS 配置](../private-link/private-endpoint-dns.md)