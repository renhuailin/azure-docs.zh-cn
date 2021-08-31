---
title: 使用 az search 模块的 Azure CLI 脚本
titleSuffix: Azure Cognitive Search
description: 使用 Azure CLI 创建和配置 Azure 认知搜索服务。 可以纵向扩展或缩减服务，对管理和查询 API 密钥进行管理，以及查询系统信息。
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: b5689b17bf2e4eace52e7c3cb28c40dc05e58ade
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179393"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>使用 Azure CLI 管理 Azure 认知搜索服务
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

可以在 Windows、macOS、Linux 上或 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行 Azure CLI 命令和脚本，以创建和配置 Azure 认知搜索。 [az search](/cli/azure/search) 模块扩展了 [Azure CLI](/cli/)，完全可以与[搜索管理 REST API](/rest/api/searchmanagement) 搭配使用，并且可以执行以下任务：

> [!div class="checklist"]
> * [列出订阅中的搜索服务](#list-search-services)
> * [返回服务信息](#get-search-service-information)
> * [创建或删除服务](#create-or-delete-a-service)
> * [创建具有专用终结点的服务](#create-a-service-with-a-private-endpoint)
> * [重新生成管理 API 密钥](#regenerate-admin-keys)
> * [创建或删除查询 API 密钥](#create-or-delete-query-keys)
> * [使用副本和分区进行纵向缩放](#scale-replicas-and-partitions)
> * [创建共享的专用链接资源](#create-a-shared-private-link-resource)

偶尔，系统会询问有关任务未  在上述列表中出现的问题。 目前，不能使用 az search 模块或管理 REST API 来更改服务器名称、区域或层。 创建服务时，会分配专用资源。 因此，更改底层硬件（位置或节点类型）需要新的服务。 类似地，没有任何工具或 API 可将一个服务中的内容（例如索引）传输到另一个服务。

在服务中，内容创建和管理是通过[搜索服务 REST API](/rest/api/searchservice/) 或 [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) 完成的。 尽管没有专用于内容的 PowerShell 命令，但可以编写脚本来调用 REST 或 .NET API 以创建并加载索引。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>列出订阅中的服务

以下命令摘自 [az resource](/cli/azure/resource)，它会返回有关订阅中已预配的现有资源和服务的信息。 如果你不知道已经创建了多少个搜索服务，则这些命令会返回该信息，省得要在门户中查找。

第一个命令返回所有搜索服务。

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

在服务列表中返回有关特定资源的信息。

```azurecli-interactive
az resource list --name <search-service-name>
```

## <a name="list-all-az-search-commands"></a>列出所有 az search 命令

你可以从 CLI 中查看 [az search](/cli/azure/search) 中提供的子组和命令的相关信息。 另外，你还可以查看[此文档](/cli/azure/search)。

若要查看 `az search` 中提供的子组，请运行以下命令。

```azurecli-interactive
az search --help
```

响应应当类似于以下输出。

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

每个子组中有多个可用命令。 可以通过运行以下行来查看 `service` 子组的可用命令。

```azurecli-interactive
az search service --help
```

你还可以查看可用于特定命令的参数。

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>获取搜索服务信息

如果你知道哪个资源组包含你的搜索服务，请运行 [az search service show](/cli/azure/search/service#az_search_service_show) 来返回服务定义，包括名称、区域、层级，以及副本和分区计数。 对于此命令，请提供包含搜索服务的资源组。

```azurecli-interactive
az search service show --name <service-name> --resource-group <search-service-resource-group-name>
```

## <a name="create-or-delete-a-service"></a>创建或删除服务

若要[创建新的搜索服务](search-create-service-portal.md)，请使用 [az search service create](/cli/azure/search/service#az_search_service_show) 命令。

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

结果应当类似于以下输出：

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>使用 IP 规则创建服务

根据你的安全需求，你可能希望创建一个[配置了 IP 防火墙](service-configure-firewall.md)的搜索服务。 为此，请向 `ip-rules` 参数传递公共 IP (v4) 地址或 CIDR 范围，如下所示。 规则应当以逗号 (`,`) 或分号 (`;`) 分隔。

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>使用系统分配的托管标识创建服务

在某些情况下，例如当[使用托管标识连接到数据源](search-howto-managed-identities-storage.md)时，需要启用[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 这可以通过在命令中添加 `--identity-type SystemAssigned` 来完成。

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>创建具有专用终结点的服务

Azure 认知搜索的[专用终结点](../private-link/private-endpoint-overview.md)允许虚拟网络上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问搜索索引中的数据。 专用终结点将[虚拟网络地址空间](../virtual-network/private-ip-addresses.md)中的 IP 地址用于你的搜索服务。 客户端与搜索服务之间的网络流量将穿过虚拟网络以及 Microsoft 主干网络上的专用链接，不会从公共 Internet 公开。 有关更多详细信息，请参阅有关[为 Azure 认知搜索创建专用终结点](service-create-private-endpoint.md)的文档

下面的示例演示了如何使用专用终结点创建搜索服务。

首先，部署一个搜索服务，将 `PublicNetworkAccess` 设置为 `Disabled`。

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

接下来，创建虚拟网络和专用终结点。

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <vnet-resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <vnet-resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <search-service-resource-group-name> \
    --name <search-service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <private-endpoint-resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

最后，创建一个专用 DNS 区域。 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <private-dns-resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <private-dns-resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <private-endpoint-resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

若要详细了解如何在 PowerShell 中创建专用终结点，请参阅[此专用链接快速入门](../private-link/create-private-endpoint-cli.md)

### <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

除了创建专用终结点连接之外，还可以 `show`、`update` 和 `delete` 连接。

若要检索专用终结点连接并查看其状态，请使用 [az search private-endpoint-connection show](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show)。

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

若要更新连接，请使用 [az search private-endpoint-connection update](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update)。 以下示例将专用终结点连接设置为“已拒绝”：

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

若要删除专用终结点连接，请使用 [az search private-endpoint-connection delete](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete)。

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>重新生成管理员密钥

若要滚动更新管理 [API 密钥](search-security-api-keys.md)，请使用 [az search admin-key renew](/cli/azure/search/admin-key#az_search_admin_key_renew)。 两个管理密钥是使用每个服务创建的，用于进行身份验证访问。 需要在每个请求中提供密钥。 这两个管理密钥在功能上是等效的，授予对搜索服务的完全写入访问权限，并可以检索任何信息，或创建和删除任何对象。 这两个密钥可以换用。 

一次只能重新生成其中的一个密钥（指定为 `primary` 或 `secondary` 密钥）。 为避免服务中断，在滚动更新主密钥时，请记得将所有客户端代码更新为使用辅助密钥。 请避免在操作是正在进行时更改密钥。

如果在不更新客户端代码的情况下重新生成密钥，使用旧密钥的请求预期将会失败。 重新生成所有新密钥不会永久性地将你锁定在服务之外，你仍可以通过门户访问服务。 重新生成主密钥和辅助密钥后，可将客户端代码更新为使用新密钥，而操作也会相应地恢复。

API 密钥的值由服务生成。 无法提供自定义密钥供 Azure 认知搜索使用。 同样，管理 API 密钥没有用户定义的名称。 对密钥的引用是固定的字符串：`primary` 或 `secondary`。 

```azurecli-interactive
az search admin-key renew \
    --resource-group <search-service-resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

结果应如以下输出所示。 即使每次只更改一个，也会同时返回两个密钥。

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>创建或删除查询密钥

若要创建查询 [API 密钥](search-security-api-keys.md)，以便从客户端应用对 Azure 认知搜索索引进行只读访问，请使用 [az search query-key create](/cli/azure/search/query-key#az_search_query_key_create)。 查询密钥用于对特定的索引进行身份验证，以检索搜索结果。 查询密钥不授予对服务中其他项（例如索引、数据源或索引器）的只读访问权限。

无法提供密钥供 Azure 认知搜索使用。 API 密钥由服务生成。

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <search-service-resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>缩放副本和分区

若要[增加或减少副本和分区](search-capacity-planning.md)，请使用 [az search service update](/cli/azure/search/service#az_search_service_update)。 增加副本或分区会增大费用，两者都提供固定和可变的费率。 如果你暂时需要更大的处理能力，可以增加副本和分区来处理工作负荷。 “概述”门户页中的监视区域提供有关查询延迟、每秒查询数和限制的磁贴，指示当前容量是否足够。

添加或删除资源可能需要一段时间。 容量调整在后台发生，使现有工作负荷能够继续运行。 附加容量准备就绪后，将立即用于处理传入的请求，无需进行额外的配置。 

删除容量可能会造成中断。 建议在减少容量之前先停止所有索引和索引器作业，以免请求遭到丢弃。 如果无法做到这一点，可以考虑以增量方式减少容量（每次删除一个副本和分区），直至达到新的目标级别。

提交命令后，没有任何办法可以中途终止该命令。 必须等到该命令完成才能修改计数。

```azurecli-interactive
az search service update \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

除了更新副本和分区计数之外，你还可以更新 `ip-rules`、`public-access` 和 `identity-type`。

## <a name="create-a-shared-private-link-resource"></a>创建共享的专用链接资源

通过 Azure 认知搜索 API 创建的安全资源的专用终结点称为共享的专用链接资源。 这是因为你正在“共享”对已与 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)集成的资源（例如存储帐户）的访问权限。

如果正使用索引器在 Azure 认知搜索中为数据编制索引，并且数据源位于专用网络上，则可以创建出站[专用终结点连接](../private-link/private-endpoint-overview.md)来访问数据。

可在[此处](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis)找到可从 Azure 认知搜索中为其创建出站专用终结点的 Azure 资源的完整列表，以及相关的“组 ID”值。

若要创建共享的专用链接资源，请使用 [az search shared-private-link-resource create](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)。 请记住，在运行此命令之前，可能需要对数据源进行一些配置。

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


若要检索共享的专用链接资源并查看其状态，请使用 [az search shared-private-link-resource list](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)。

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

需要先使用以下命令批准连接，然后才能使用该连接。 需要从子资源中检索专用终结点连接的 ID。 在本例中，我们通过 az storage 获取连接 ID。

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

若要删除共享的专用链接资源，请使用 [az search shared-private-link-resource delete](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete)。

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

有关设置共享专用链接资源的完整详细信息，请参阅[通过专用终结点建立索引器连接](search-indexer-howto-access-private.md)的文档。

## <a name="next-steps"></a>后续步骤

使用门户、REST API 或 .NET SDK 生成[索引](search-what-is-an-index.md)和[查询索引](search-query-overview.md)。

* [在 Azure 门户中创建 Azure 认知搜索索引](search-get-started-portal.md)
* [设置索引器以从其他服务加载数据](search-indexer-overview.md)
* [使用搜索资源管理器在 Azure 门户中查询 Azure 认知搜索索引](search-explorer.md)
* [如何在 .NET 中使用 Azure 认知搜索](search-howto-dotnet-sdk.md)