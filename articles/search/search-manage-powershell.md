---
title: 使用 Az.Search 模块的 PowerShell 脚本
titleSuffix: Azure Cognitive Search
description: 使用 PowerShell 创建和配置 Azure 认知搜索服务。 可以纵向扩展或缩减服务，对管理和查询 API 密钥进行管理，以及查询系统信息。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65df8c53522bb971bcd089967047f8a86de55f44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749362"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>使用 PowerShell 管理 Azure 认知搜索服务
> [!div class="op_single_selector"]
> * [门户](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

可以在 Windows、Linux 或 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行 PowerShell cmdlet 和脚本，以创建和配置 Azure 认知搜索。 **Az.Search** 模块扩展了 [Azure PowerShell](/powershell/)，完全可与 [搜索管理 REST API](/rest/api/searchmanagement) 搭配使用，并可执行以下任务：

> [!div class="checklist"]
> * [列出订阅中的搜索服务](#list-search-services)
> * [返回服务信息](#get-search-service-information)
> * [创建或删除服务](#create-or-delete-a-service)
> * [创建具有专用终结点的服务](#create-a-service-with-a-private-endpoint)
> * [重新生成管理 API 密钥](#regenerate-admin-keys)
> * [创建或删除查询 API 密钥](#create-or-delete-query-keys)
> * [使用副本和分区进行纵向缩放](#scale-replicas-and-partitions)
> * [创建共享的专用链接资源](#create-a-shared-private-link-resource)

偶尔，系统会询问有关任务未  在上述列表中出现的问题。 目前，不能使用 **Az.Search** 模块或管理 REST API 来更改服务器名称、区域或层。 创建服务时，会分配专用资源。 因此，更改底层硬件（位置或节点类型）需要新的服务。 类似地，没有任何工具或 API 可将一个服务中的内容（例如索引）传输到另一个服务。

在服务中，内容创建和管理通过[搜索服务 REST API](/rest/api/searchservice/) 或 [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) 完成。 尽管没有专用的 PowerShell 命令可用于内容，但可以编写 PowerShell 脚本来调用 REST 或 .NET API 以创建并加载索引。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>检查版本并加载模块

本文中的示例是交互式，需要提升的权限。 必须安装 Azure PowerShell（**Az** 模块）。 有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/)。

### <a name="powershell-version-check-51-or-later"></a>PowerShell 版本检查（5.1 或更高版本）

本地 PowerShell 必须是在任何受支持操作系统上运行的 5.1 或更高版本。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>加载 Azure PowerShell

如果你不确定是否已安装 **Az**，请运行以下命令作为验证步骤。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

某些系统不会自动加载模块。 如果运行上述命令时出错，请尝试加载该模块，如果失败，请重新查看安装说明，以检查是否遗漏了某个步骤。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>使用浏览器登录令牌连接到 Azure

可以在 PowerShell 中使用门户登录凭据连接到订阅。 或者，可以[使用服务主体以非交互方式进行身份验证](../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

```azurepowershell-interactive
Connect-AzAccount
```

如果你持有多个 Azure 订阅，请设置 Azure 订阅。 若要查看当前订阅的列表，请运行以下命令。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定订阅，请运行以下命令。 在以下示例中，订阅名为 `ContosoSubscription`。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>列出订阅中的服务

以下命令摘自 [**Az.Resources**](/powershell/module/az.resources)，它会返回有关订阅中已预配的现有资源和服务的信息。 如果你不知道已经创建了多少个搜索服务，则这些命令会返回该信息，省得要在门户中查找。

第一个命令返回所有搜索服务。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

在服务列表中返回有关特定资源的信息。

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

结果应如以下输出所示。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>导入 Az.Search

[**Az.Search**](/powershell/module/az.search) 中的命令只有在加载该模块之后才可用。

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>列出所有 Az.Search 命令

作为验证步骤，返回模块中提供的命令列表。

```azurepowershell-interactive
Get-Command -Module Az.Search
```

结果应如以下输出所示。

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

如果你使用的包版本较旧，请更新模块以获取最新功能。

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>获取搜索服务信息

导入 **Az.Search** 之后，如果你知道哪个资源组包含你的搜索服务，请运行 [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) 返回服务定义，包括名称、区域、层级、副本计数和分区计数。 对于此命令，请提供包含搜索服务的资源组。

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

结果应如以下输出所示。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>创建或删除服务

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchservice) 用于 [创建新的搜索服务](search-create-service-portal.md)。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
结果应如以下输出所示。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>使用 IP 规则创建服务

根据你的安全需求，你可能希望创建一个[配置了 IP 防火墙](service-configure-firewall.md)的搜索服务。 为此，请先定义 IP 规则，然后将它们传递给 `IPRuleList` 参数，如下所示。

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>使用系统分配的托管标识创建服务

在某些情况下，例如当[使用托管标识连接到数据源](search-howto-managed-identities-storage.md)时，需要启用[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 这可以通过在命令中添加 `-IdentityType SystemAssigned` 来完成。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>创建具有专用终结点的服务

Azure 认知搜索的[专用终结点](../private-link/private-endpoint-overview.md)允许虚拟网络上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问搜索索引中的数据。 专用终结点将[虚拟网络地址空间](../virtual-network/private-ip-addresses.md)中的 IP 地址用于你的搜索服务。 客户端与搜索服务之间的网络流量将穿过虚拟网络以及 Microsoft 主干网络上的专用链接，不会从公共 Internet 公开。 有关更多详细信息，请参阅[为 Azure 认知搜索创建专用终结点](service-create-private-endpoint.md)

下面的示例演示了如何使用专用终结点创建搜索服务。 

首先，部署一个搜索服务，将 `PublicNetworkAccess` 设为 `Disabled`。

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <search-service-resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

接下来，创建虚拟网络、专用网络连接和专用终结点。

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <vm-resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <private-endpoint-resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

最后，创建专用 DNS 区域。 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <private-dns-resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <private-dns-link-resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <private-dns-zone-resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

若要详细了解如何在 PowerShell 中创建专用终结点，请参阅此[专用链接快速入门](../private-link/create-private-endpoint-powershell.md)

### <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

除了创建专用终结点连接之外，还可以 `Get``Set` 和 `Remove` 连接。

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) 用于检索专用终结点连接并查看其状态。

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name>
```

[Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) 用于更新连接。 以下示例将专用终结点连接设置为“已拒绝”：

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) 用于删除专用终结点连接。

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>重新生成管理员密钥

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) 用于滚动更新管理 [API 密钥](search-security-api-keys.md)。 两个管理密钥是使用每个服务创建的，用于进行身份验证访问。 需要在每个请求中提供密钥。 这两个管理密钥在功能上是等效的，授予对搜索服务的完全写入访问权限，并可以检索任何信息，或创建和删除任何对象。 这两个密钥可以换用。 

一次只能重新生成其中的一个密钥（指定为 `primary` 或 `secondary` 密钥）。 为避免服务中断，在滚动更新主密钥时，请记得将所有客户端代码更新为使用辅助密钥。 请避免在操作是正在进行时更改密钥。

如果在不更新客户端代码的情况下重新生成密钥，使用旧密钥的请求预期将会失败。 重新生成所有新密钥不会永久性地将你锁定在服务之外，你仍可以通过门户访问服务。 重新生成主密钥和辅助密钥后，可将客户端代码更新为使用新密钥，而操作也会相应地恢复。

API 密钥的值由服务生成。 无法提供自定义密钥供 Azure 认知搜索使用。 同样，管理 API 密钥没有用户定义的名称。 对密钥的引用是固定的字符串：`primary` 或 `secondary`。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

结果应如以下输出所示。 即使每次只更改一个，也会同时返回两个密钥。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>创建或删除查询密钥

使用 [**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) 创建查询 [API 密钥](search-security-api-keys.md)，用于从客户端应用对 Azure 认知搜索索引进行只读访问。 查询密钥用于对特定的索引进行身份验证，以检索搜索结果。 查询密钥不授予对服务中其他项（例如索引、数据源或索引器）的只读访问权限。

无法提供密钥供 Azure 认知搜索使用。 API 密钥由服务生成。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>缩放副本和分区

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) 用于 [增加或减少副本与分区](search-capacity-planning.md)，以调整服务中的可计费资源。 增加副本或分区会增大费用，两者都提供固定和可变的费率。 如果你暂时需要更大的处理能力，可以增加副本和分区来处理工作负荷。 “概述”门户页中的监视区域提供有关查询延迟、每秒查询数和限制的磁贴，指示当前容量是否足够。

添加或删除资源可能需要一段时间。 容量调整在后台发生，使现有工作负荷能够继续运行。 附加容量准备就绪后，将立即用于处理传入的请求，无需进行额外的配置。 

删除容量可能会造成中断。 建议在减少容量之前先停止所有索引和索引器作业，以免请求遭到丢弃。 如果无法做到这一点，可以考虑以增量方式减少容量（每次删除一个副本和分区），直至达到新的目标级别。

提交命令后，没有任何办法可以中途终止该命令。 必须等到该命令完成才能修改计数。

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <search-service-resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

结果应如以下输出所示。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>创建共享的专用链接资源

通过 Azure 认知搜索 API 创建的安全资源的专用终结点称为共享的专用链接资源。 这是因为你正在“共享”对已与 [Azure 专用链接服务](https://azure.microsoft.com/services/private-link/)集成的资源（例如存储帐户）的访问权限。

如果正使用索引器在 Azure 认知搜索中为数据编制索引，并且数据源位于专用网络上，则可以创建出站[专用终结点连接](../private-link/private-endpoint-overview.md)来访问数据。

可在[此处](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis)找到可从 Azure 认知搜索中为其创建出站专用终结点的 Azure 资源的完整列表，以及相关“组 ID”值。

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) 用于创建共享的专用链接资源。 请记住，在运行此命令之前，可能需要对数据源进行一些配置。

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-serviceresource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<storage-resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

[Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) 用于检索共享的专用链接资源并查看其状态。

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

需要先使用以下命令批准连接，然后才能使用该连接。

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <search-service-resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) 用于删除共享的专用链接资源。

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

有关设置共享专用链接资源的完整详细信息，请参阅[通过专用终结点建立索引器连接](search-indexer-howto-access-private.md)的文档。

## <a name="next-steps"></a>后续步骤

使用门户、REST API 或 .NET SDK 生成[索引](search-what-is-an-index.md)和[查询索引](search-query-overview.md)。

* [在 Azure 门户中创建 Azure 认知搜索索引](search-get-started-portal.md)
* [设置索引器以从其他服务加载数据](search-indexer-overview.md)
* [使用搜索资源管理器在 Azure 门户中查询 Azure 认知搜索索引](search-explorer.md)
* [如何在 .NET 中使用 Azure 认知搜索](search-howto-dotnet-sdk.md)
