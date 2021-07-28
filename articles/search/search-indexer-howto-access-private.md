---
title: 通过专用终结点的索引器连接
titleSuffix: Azure Cognitive Search
description: 配置索引器连接以访问通过专用终结点保护的其他 Azure 资源中的内容。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0de817d2d18105b3f1a27ccd938f85bc62504867
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108770366"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>建立通过专用终结点的索引器连接

许多 Azure 资源（例如 Azure 存储帐户）可配置为接受来自一组虚拟网络的连接，并拒绝来自公共网络的外部连接。 如果正使用索引器在 Azure 认知搜索中为数据编制索引，并且数据源位于专用网络上，则可以创建出站[专用终结点连接](../private-link/private-endpoint-overview.md)来访问数据。

此索引器连接方法需符合以下两个要求：

+ 必须预先向 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)注册提供内容或代码的 Azure 资源。

+ Azure 认知搜索服务必须位于基本层或更高层。 此功能在免费层上不可用。 此外，如果你的索引器具有技能组，层级必须是标准 2 (S2) 或更高层级。 有关详细信息，请参阅[服务限制](search-limits-quotas-capacity.md#shared-private-link-resource-limits)。

## <a name="shared-private-link-resources-management-apis"></a>共享的专用链接资源管理 API

通过 Azure 认知搜索 API 创建的安全资源的专用终结点称为共享的专用链接资源。 这是因为你正在“共享”对已与 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)集成的资源（例如存储帐户）的访问权限。

Azure 认知搜索通过管理 REST API 提供 [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) 操作，你可将该操作用于配置来自 Azure 认知搜索索引器的访问。

你可以只使用搜索管理 API 的预览版本（2020-08-01 预览版或更高预览版）创建与某些资源（下表中指定为预览版）的专用终结点连接 。 可以使用预览版或正式版 API（2020-08-01 或更高版本）创建未标记“预览”二字的资源 。

下表列出了可从 Azure 认知搜索创建出站专用终结点的 Azure 资源。 若要创建共享专用链接资源，请输入与在 API 中写入的组 ID 值完全相同的值。 这些值区分大小写。

| Azure 资源 | 组 ID |
| --- | --- |
| Azure 存储 - Blob（或）ADLS Gen 2 | `blob`|
| Azure 存储 - 表 | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Azure SQL 数据库 | `sqlServer`|
| Azure Database for MySQL（预览版） | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions（预览版） | `sites` |

你还可以使用[受支持的 API 列表](/rest/api/searchmanagement/privatelinkresources/listsupported)查询支持出站专用终结点连接的 Azure 资源。

在本文的剩余部分中，会结合使用 Azure 门户（如果愿意也可使用 [Azure CLI](/cli/azure/)）和 [Postman](https://www.postman.com/)（如果愿意，也可使用类似于 [curl](https://curl.se/) 的任何其他 HTTP 客户端）来演示 REST API 调用。

> [!NOTE]
> 本文中的示例基于以下假设：
> * 搜索服务的名称是 contoso-search，它存在于订阅 ID 为 00000000-0000-0000-0000-000000000000 的订阅的 contoso 资源组中  。 
> * 此搜索服务的资源 ID 为 /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search。

后文的示例展示如何配置 contoso-search 服务，使其索引器可以从安全存储帐户 /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage 访问数据 。

## <a name="secure-your-storage-account"></a>保护存储帐户

将存储帐户配置为[仅允许从特定子网访问](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)。 在 Azure 门户中，如果选择此选项并使这组子网为空，表示不允许来自虚拟网络的流量。

   ![“防火墙和虚拟网络”窗格的屏幕截图，显示允许访问所选网络的选项。 ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> 可以使用[受信任 Microsoft 服务方法](../storage/common/storage-network-security.md#trusted-microsoft-services)绕过存储帐户的虚拟网络或 IP 限制。 还可以启用搜索服务以访问存储帐户中的数据。 为此，请参阅[使用受信任的服务异常对 Azure 存储进行索引器访问](search-indexer-howto-access-trusted-service-exception.md)。
>
> 但是使用此方法时，Azure 认知搜索与存储帐户之间的通信会在安全的 Microsoft 骨干网络上通过存储帐户的公共 IP 地址进行。

可以通过 Azure 门户管理 Azure 认知搜索服务的共享专用链接资源。 导航到搜索服务 ->“网络”->“共享专用访问”，通过门户管理这些资源。

   ![“网络”窗格的屏幕截图，其中显示了共享专用链接管理边栏选项卡。 ](media\search-indexer-howto-secure-access\shared-private-link-portal-blade.png)

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>步骤 1：创建存储帐户的共享专用链接资源

若要请求 Azure 认知搜索创建到存储帐户的出站专用终结点连接，请通过“共享专用访问”边栏选项卡，单击“添加共享专用访问”。 在右侧打开的对话框中，可以选择“连接到目录中的 Azure 资源”或“按资源 ID 或别名连接到 Azure 资源”。

使用第一个选项时（建议），对话框窗格将有助于选择适当的存储帐户，并有助于填写其他属性，如资源的组 ID 和资源类型。

   ![“添加共享专用访问”窗格的屏幕截图，其中显示了创建共享专用链接资源的引导式体验。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource.png)

使用第二个选项时，可以手动输入目标存储帐户的 Azure 资源 ID，并选择适当的组 ID（在这种情况下为“blob”）

![“添加共享专用访问”窗格的屏幕截图，其中显示了创建共享专用链接资源的手动操作体验。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-manual.png)

或者，也可通过 [Azure CLI](/cli/azure/) 进行以下 API 调用：

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json
```

代表 API 的请求正文的 create-pe.json 文件的内容如下：

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

+ 专用终结点，分配的专用 IP 地址处于 `"Pending"` 状态。 专用 IP 地址是从地址空间（已分配给搜索服务特定专用索引器的执行环境的虚拟网络）获得的。 专用终结点获得批准后，从 Azure 认知搜索到存储帐户的任何通信均源自该专用 IP 地址和安全的专用链接通道。

+ 基于 `groupId` 的资源类型的专用 DNS 区域。 通过部署此资源，可确保对专用资源的任何 DNS 查找都利用与专用终结点关联的 IP 地址。

务必为要创建专用终结点的这一类资源指定正确的 `groupId`。 任何不匹配都将导致得到表示失败的响应消息。

与所有异步 Azure 操作一样，`PUT` 调用返回一个 `Azure-AsyncOperation` 标头值，如下所示：

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

可以定期轮询此 URI 以获取操作状态。

如果使用 Azure 门户创建共享专用链接资源，则门户会自动执行此轮询（资源预配状态标记为“正在更新”）。

![“添加共享专用访问”窗格的屏幕截图，其中显示正在创建资源。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-progress.png)

成功创建资源后，用户将收到门户通知，并且资源的预配状态将更改为“成功”。

![“添加共享专用访问”窗格的屏幕截图，其中显示已完成资源创建。 ](media\search-indexer-howto-secure-access\new-shared-private-link-resource-success.png)

如果使用的是 CLI，可以通过手动查询 `Azure-AsyncOperationHeader` 值来轮询状态。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01
```

等到资源的预配状态更改为“成功”，再继续执行后续步骤。

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>步骤 2a：批准存储帐户的专用终结点连接

> [!NOTE]
> 在本部分中，你将使用 Azure 门户逐步完成存储的专用终结点的审批流程。 或者，可以使用通过存储资源提供程序提供的 [REST API](/rest/api/storagerp/privateendpointconnections)。
>
> 其他提供程序（如 Azure Cosmos DB 或 Azure SQL Server）提供了类似的存储资源提供程序 API 来管理专用终结点连接。

1. 在 Azure 门户中，选择存储帐户的“网络”选项卡，然后导航到“专用终结点连接”。 异步操作成功后，应该会有一个专用终结点连接的请求，请求消息来自于之前的 API 调用。

   ![Azure 门户的屏幕截图，其中显示“专用终结点”窗格。](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. 选择 Azure 认知搜索创建的专用终结点。 在“专用终结点”列中，使用在前面的 API 中指定的名称标识专用终结点连接，选择“批准”，然后输入相应的消息 。 消息内容不重要。

   请确保显示的专用终结点连接如以下屏幕截图所示。 状态可能需要一到两分钟的时间才能在门户中更新。

   ![Azure 门户的屏幕截图，显示“专用终结点连接”窗格上的“已批准”状态。](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

批准专用终结点连接请求后，流量就能通过专用终结点传输。 批准专用终结点后，Azure 认知搜索会在为其创建的 DNS 区域中创建所需的 DNS 区域映射。

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>步骤 2b：查询共享专用链接资源的状态

若要在批准后确认共享专用链接资源已更新，请在 Azure 门户上重新访问搜索服务的“共享专用访问”边栏选项卡，并检查“连接状态”。

   ![Azure 门户的屏幕截图，其中显示“已批准”共享专用链接资源。](media\search-indexer-howto-secure-access\new-shared-private-link-resource-approved.png)

或者，也可以使用 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get) 获取“连接状态”。

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01
```

这会返回一个 JSON，其中连接状态会在“属性”部分下显示为“状态”。

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

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>步骤 3：将索引器配置为在专用环境中运行

> [!NOTE]
> 你可以在批准专用终结点连接之前执行此步骤。 在批准专用终结点连接之前，任何尝试与安全资源（例如存储帐户）通信的索引器最终都将处于暂时失败状态。 新索引器的创建将失败。 一旦批准了专用终结点连接，索引器就可以访问专用存储帐户。

1. [创建一个数据源](/rest/api/searchservice/create-data-source)，该数据源指向安全存储帐户和存储帐户中的相应容器。 以下屏幕截图显示了 Postman 中的此请求。

   ![显示在 Postman 用户界面上创建数据源的屏幕截图。](media\search-indexer-howto-secure-access\create-ds.png )

1. 同样，[创建一个索引](/rest/api/searchservice/create-index)，并根据需要使用 REST API[ 创建一个技能组](/rest/api/searchservice/create-skillset)。

1. [创建一个索引器](/rest/api/searchservice/create-indexer)，该索引器指向在前面的步骤中创建的数据源、索引和技能组。 此外，通过将索引器的 `executionEnvironment` 配置属性设置为 `private`，强制索引器在专用执行环境中运行。

   ![显示在 Postman 用户界面上创建索引器的屏幕截图。](media\search-indexer-howto-secure-access\create-idr.png)

   成功创建索引器后，它应开始通过专用终结点连接为存储帐户中的内容编制索引。 你可以使用[索引器状态 API](/rest/api/searchservice/get-indexer-status) 来监视索引器的状态。

> [!NOTE]
> 如果已有索引器，可以通过 [PUT API](/rest/api/searchservice/create-indexer) 更新它们，方法是将 `executionEnvironment` 设置为 `private`。

## <a name="troubleshooting"></a>故障排除

+ 如果索引器创建失败并显示“数据源凭据无效”等错误消息，说明要么专用终结点连接的状态还不是“已批准”，要么连接不起作用。 解决此问题的方法： 
  + 使用 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get) 获取共享专用链接资源的状态。 如果状态为“已批准”，请检查资源的 `properties.provisioningState`。 如果此处的状态为 `Incomplete`，表示资源的某些基本依赖项设置失败。 重新发出 `PUT` 请求以重新创建共享的专用链接资源，这样做应该能解决此问题。 可能需要重新审批。 重新检查资源的状态以验证问题是否已得到解决。

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