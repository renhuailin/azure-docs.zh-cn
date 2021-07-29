---
title: 什么是 Azure 专用终结点？
description: 了解 Azure 专用终结点
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 7359dc8199c01bae7f7463b83079193397e40519
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110072885"
---
# <a name="what-is-azure-private-endpoint"></a>什么是 Azure 专用终结点？

Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效地引入 VNet 中。 服务可以是 Azure 存储、Azure Cosmos DB、SQL 等 Azure 服务，也可以是你自己的[专用链接服务](private-link-service-overview.md)。
  
## <a name="private-endpoint-properties"></a>专用终结点属性 
 专用终结点指定以下属性： 


|属性  |描述 |
|---------|---------|
|名称    |    资源组中的唯一名称。      |
|子网    |  虚拟网络中要部署和分配专用 IP 地址的子网。 有关子网要求，请参阅本文中的“限制”部分。         |
|专用链接资源    |   用于通过可用类型列表中的资源 ID 或别名建立连接的专用链接资源。 将为发送到此资源的所有流量生成唯一的网络标识符。       |
|目标子资源   |      要连接的子资源。 每个专用链接资源类型具有不同的选项，可根据偏好做出选择。    |
|连接批准方法    |  自动或手动。 根据 Azure 基于角色的访问控制 (Azure RBAC) 权限，可以自动批准专用终结点。 如果尝试在没有 Azure RBAC 的情况下连接到专用链接资源，请使用手动方法来允许资源所有者批准连接。        |
|请求消息     |  可为请求手动批准的连接指定消息。 此消息可用于标识特定的请求。        |
|连接状态   |   一个只读属性，指定专用终结点是否处于活动状态。 只能使用处于已批准状态的专用终结点发送流量。 可用的其他状态： <br>-**已批准**：连接已自动或手动批准，随时可供使用。</br><br>-**等待中**：连接是手动创建的，正等待由专用链接资源所有者批准。</br><br>-**已拒绝**：连接已被专用链接资源所有者拒绝。</br><br>-**已断开连接**：连接已被专用链接资源所有者删除。 专用终结点已变为参考性终结点，应将其删除以清理资源。 </br>|

下面是有关专用终结点的一些重要详细信息： 
- 专用终结点使同一 VNet、区域性对等互连的 Vnet、全局性对等互连的 Vnet 中的以及本地用户能够使用 [VPN](https://azure.microsoft.com/services/vpn-gateway/) 或 [Express 路由](https://azure.microsoft.com/services/expressroute/)和由专用链接提供支持的服务在用户之间实现互连。
 
- 只有连接到专用终结点的客户端能发起网络连接，服务提供程序不提供任何路由配置用于发起服务使用者之间的互连。 只能在单个方向建立连接。

- 创建专用终结点时，还会创建一个在资源的生命周期内有效的只读网络接口。 将从映射到专用链接资源的子网中向该接口动态分配专用 IP 地址。 专用 IP 地址的值在专用终结点的整个生命周期中保持不变。
 
- 专用终结点必须与虚拟网络部署在同一区域和订阅中。 
 
- 专用链接资源可部署在与虚拟网络和专用终结点不同的区域中。
 
- 可以使用同一个专用链接资源创建多个专用终结点。 对于使用常见 DNS 服务器配置的单个网络，建议的做法是对给定的专用链接资源使用单个专用终结点，以避免出现重复条目或 DNS 解析冲突。 
 
- 可以在同一虚拟网络中的相同或不同子网上创建多个专用终结点。 在一个订阅中可以创建的专用终结点数量有限制。 有关详细信息，请参阅  [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。

- 来自专用链接资源的订阅也必须注册到 Micosoft.Network 资源提供程序。 有关详细信息，请参阅 [Azure 资源提供程序](../azure-resource-manager/management/resource-providers-and-types.md)。

 
## <a name="private-link-resource"></a>专用链接资源 
专用链接资源是给定专用终结点的目标。 下面是可用的专用链接资源类型列表： 
 
|专用链接资源名称  |资源类型   |子资源  |
|---------|---------|---------|
|**专用链接服务**（你自己的服务）   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure 自动化** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL 数据库** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure 存储**  | Microsoft.Storage/storageAccounts    |  Blob（blob、blob_secondary）<BR> 表（table、table_secondary）<BR> 队列（queue、queue_secondary）<BR> 文件（file、file_secondary）<BR> Web（web、web_secondary）        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob（blob、blob_secondary）<BR> Data Lake File System Gen2（dfs、dfs_secondary）       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | SQL、MongoDB、Cassandra、Gremlin、表|
|**Azure Database for PostgreSQL - 单一服务器** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure IoT 中心** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | 保管库 |
|**Azure Kubernetes 服务 - Kubernetes API** | Microsoft.ContainerService/managedClusters    | 管理 |
|**Azure 搜索** | Microsoft.Search/searchService| searchService|  
|**Azure 容器注册表** | Microsoft.ContainerRegistry/registries    | 注册表 |
|**Azure 应用配置** | Microsoft.Appconfiguration/configurationStores    | configurationStores |
|**Azure 备份** | Microsoft.RecoveryServices/vaults    | 保管库 |
|**Azure 事件中心** | Microsoft.EventHub/namespaces    | 命名空间 |
|**Azure 服务总线** | Microsoft.ServiceBus/namespaces | 命名空间 |
|**Azure 中继** | Microsoft.Relay/namespaces | 命名空间 |
|**Azure 事件网格** | Microsoft.EventGrid/topics    | 主题 |
|**Azure 事件网格** | Microsoft.EventGrid/domains    | 域 |
|**Azure 应用服务** | Microsoft.Web/sites    | sites |
|**Azure 应用服务槽** | Microsoft.Web/sites    | sites-`<slot name>` |
|**Azure 机器学习** | Microsoft.MachineLearningServices/workspaces    | amlworkspace |
|**SignalR** | Microsoft.SignalRService/SignalR    | signalR |
|**Azure Monitor** | Microsoft.Insights/privateLinkScopes    | azuremonitor |
|**认知服务** | (Microsoft.CognitiveServices/accounts    | account |
|**Azure 文件同步** | Microsoft.StorageSync/storageSyncServices    | Afs |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>专用终结点的网络安全性 
使用 Azure 服务的专用终结点时，流量将受到特定专用链接资源的保护。 平台会执行访问控制，以验证网络连接是否仅抵达指定的专用链接资源。 若要访问同一 Azure 服务中的其他资源，需要附加的专用终结点。 
 
可以完全锁定工作负荷，使其无法访问公共终结点来连接受支持的 Azure 服务。 这种控制提供内置的渗透保护来防止访问同一 Azure 服务上托管的其他资源，因此可为资源提供额外的网络安全层。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用批准工作流访问专用链接资源 
可使用以下连接批准方法连接到专用链接资源：
- 当你拥有特定的专用链接资源或对其拥权限时 **自动** 批准。 所需的权限基于采用以下格式的专用链接资源类型：Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionApproval/action
- 没有所需的权限并想要请求访问权限时 **手动** 发出请求。 将发起批准工作流。 将会以“挂起”状态创建专用终结点和后续的专用终结点连接。 专用链接资源所有者负责审批该连接。 获得批准后，将启用专用终结点来正常发送流量，如以下批准工作流图中所示。  

![工作流批准](media/private-endpoint-overview/private-link-paas-workflow.png)
 
专用链接资源所有者可以针对专用终结点连接执行以下操作： 
- 评审所有专用终结点连接详细信息。 
- 批准专用终结点连接。 将启用相应的专用终结点，以将流量发送到专用链接资源。 
- 拒绝专用终结点连接。 相应的专用终结点将会更新以反映状态。
- 删除处于任何状态的专用终结点连接。 将使用已断开连接状态更新相应的专用终结点以反映操作，专用终结点所有者此时只能删除资源。 
 
> [!NOTE]
> 只有处于已批准状态的专用终结点才能将流量发送到给定的专用链接资源。 

### <a name="rbac-permissions"></a>RBAC 权限

下面是用户创建专用终结点所需的特定 RBAC 权限。 有关自定义角色的详细信息，请查看 [创建自定义角色的步骤](/azure/role-based-access-control/custom-roles#steps-to-create-a-custom-role)。

Microsoft.Resources/deployments/* Microsoft.Resources/subscriptions/resourcegroups/resources/read Microsoft.Network/virtualNetworks/read Microsoft.Network/virtualNetworks/subnets/read Microsoft.Network/virtualNetworks/subnets/write Microsoft.Network/virtualNetworks/subnets/join/action Microsoft.Network/privateEndpoints/read Microsoft.Network/privateEndpoints/write Microsoft.Network/locations/availablePrivateEndpointTypes/read

### <a name="connecting-using-alias"></a>使用别名进行连接
别名是当服务所有者在标准负载均衡器后面创建专用链接服务时，生成的唯一名字对象。 服务所有者可与其使用者脱机共享此别名。 使用者可以使用资源 URI 或别名请求连接到专用链接服务。 若要使用别名进行连接，必须使用手动连接批准方法创建专用终结点。 若要使用手动连接批准方法，请在专用终结点创建流期间将手动请求参数设置为 true。 有关详细信息，请查看 [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) 和 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)。 

## <a name="dns-configuration"></a>DNS 配置 
使用完全限定的域名 (FQDN) 作为连接字符串的一部分连接到专用链接资源时，必须正确配置 DNS 设置，以解析为分配的专用 IP 地址。 现有的 Azure 服务可能已有在通过公共终结点进行连接时要使用的 DNS 配置。 需要替代此配置，以使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含配置 DNS 所需的完整信息，其中包括为给定专用链接资源分配的 FQDN 和专用 IP 地址。 

有关为专用终结点配置 DNS 的最佳做法和建议的完整详细信息，请查看[专用终结点 DNS 配置文章](private-endpoint-dns.md)。



 
## <a name="limitations"></a>限制
 
下表列出了使用专用终结点时的已知限制： 


|限制 |说明 |缓解措施  |
|---------|---------|---------|
|网络安全组 (NSG) 规则和用户定义的路由不适用于专用终结点    |专用终结点不支持 NSG。 尽管包含专用终结点的子网可以有关联的 NSG，但这些规则不会针对专用终结点处理的流量生效。 必须[禁用网络策略的强制实施](disable-private-endpoint-network-policy.md)，才能在子网中部署专用终结点。 NSG 仍会在同一子网中托管的其他工作负荷上强制实施。 任何客户端子网上的路由将使用 /32 前缀，更改默认路由行为需要类似的 UDR  | 对源客户端上的出站流量使用 NSG 规则来控制流量。 部署具有 /32 前缀的单个路由，以替代专用终结点路由。 仍支持出站连接的 NSG 流日志和监视信息，并且可以使用这些信息        |


## <a name="next-steps"></a>后续步骤
- [使用门户为 Azure Web 应用创建专用终结点](create-private-endpoint-portal.md)
- [使用 PowerShell 为 Azure Web 应用创建专用终结点](create-private-endpoint-powershell.md)
- [使用 CLI 为 Azure Web 应用创建专用终结点](create-private-endpoint-cli.md)
- [使用门户创建存储帐户的专用终结点](./tutorial-private-endpoint-storage-portal.md)
- [使用门户创建 Azure Cosmos 帐户的专用终结点](../cosmos-db/how-to-configure-private-endpoints.md)
- [使用 Azure PowerShell 创建自己的专用链接服务](create-private-link-service-powershell.md)
- [使用门户创建自己的用于 Azure Database for PostgreSQL - 单个服务器的专用链接](../postgresql/howto-configure-privatelink-portal.md)
- [使用 CLI 创建自己的用于 Azure Database for PostgreSQL - 单个服务器的专用链接](../postgresql/howto-configure-privatelink-cli.md)
- [使用门户创建自己的用于 Azure Database for MySQL 的专用链接](../mysql/howto-configure-privatelink-portal.md)
- [使用 CLI 创建自己的用于 Azure Database for MySQL 的专用链接](../mysql/howto-configure-privatelink-cli.md)
- [使用门户创建自己的用于 Azure Database for MariaDB 的专用链接](../mariadb/howto-configure-privatelink-portal.md)
- [使用 CLI 创建自己的用于 Azure Database for MariaDB 的专用链接](../mariadb/howto-configure-privatelink-cli.md)
- [使用门户和 CLI 创建自己的用于 Azure Key Vault 的专用链接](../key-vault/general/private-link-service.md)
