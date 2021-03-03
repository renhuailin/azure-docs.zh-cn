---
title: Azure 专用终结点 DNS 配置
description: 了解 Azure 专用终结点 DNS 配置
services: private-link
author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: d0085dc1cd7afa1fd8f557db27d30fd76ca05fac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732728"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 专用终结点 DNS 配置

务必正确配置 DNS 设置，以将专用终结点 IP 地址解析为连接字符串 (FQDN) 的完全限定域名。

现有 Microsoft Azure 服务可能已有一个公共终结点的 DNS 配置。 必须重写此配置才能使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含用于配置 DNS 的信息。 网络接口信息包括专用链接资源的 FQDN 和专用 IP 地址。 
 
可使用以下选项来配置专用终结点的 DNS 设置： 
- **使用主机文件（仅建议用于测试）** 。 可以使用虚拟机上的主机文件来替代 DNS。  
- **使用专用 DNS 区域**。 可以使用 [专用 DNS 区域](../dns/private-dns-privatednszone.md) 替代专用终结点的 DNS 解析。 可将专用 DNS 区域链接到虚拟网络，以解析特定的域。
- **使用 DNS 转发器（可选）** 。 可以使用 DNS 转发器替代专用链接资源的 DNS 解析。 创建 DNS 转发规则以在虚拟网络中托管的 [dns 服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 上使用专用 dns 区域。

> [!IMPORTANT]
> 不建议替代正在用于解析公共终结点的区域。 在不 DNS 转发到公共 DNS 的情况下，与资源的连接无法正确解析。 若要避免出现问题，请创建不同的域名，或对以下每个服务采用建议的名称。 

## <a name="azure-services-dns-zone-configuration"></a>Azure 服务 DNS 区域配置
Azure 会在公共 DNS 上 (CNAME) 创建规范名称 DNS 记录。 CNAME 记录将解析重定向到专用域名。 可以用专用终结点的专用 IP 地址替代解析。 
 
应用程序无需更改连接 URL。 解析到公共 DNS 服务时，DNS 服务器将解析为你的专用终结点。 此过程不会影响现有应用程序。 

> [!IMPORTANT]
> 已将专用 DNS 区域用于给定类型的专用网络如果没有任何专用终结点连接，只能连接到公共资源，否则需要在专用 DNS 区域上进行相应的 DNS 配置才能完成 DNS 解析序列。 

对于 Azure 服务，请根据下表中所述使用建议的区域名称：

| 专用链接资源类型/子资源 |专用 DNS 区域名称 | 公共 DNS 区域转发器 |
|---|---|---|
| Azure 自动化 /(Microsoft.Automation/automationAccounts)/Webhook、DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL 数据库 (Microsoft.Sql/servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts)/Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts)/表 (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts)/队列 (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts)/文件 (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| 存储帐户 (Microsoft.Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake 文件系统 Gen2 (Gen2) /Data Lake 文件系统 (dfs，dfs_secondary)  | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL - 单一服务器 (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / vault | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes 服务 - Kubernetes API (Microsoft.ContainerService/managedClusters)/management | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure 搜索 (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Azure 容器注册表 (Microsoft.ContainerRegistry/registries) / registry | privatelink.azurecr.io | azurecr.io |
| Azure 应用程序配置 (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure 备份 (Microsoft.RecoveryServices/vaults) / vault | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Site Recovery (Microsoft.recoveryservices/保管库) /保管库 | {region}. privatelink. azurerm.recoveryservices.siterecovery. windowsazure.storage | {region}. hypervrecoverymanager. windowsazure.storage |
| Azure 事件中心 (Microsoft.EventHub/namespaces)/namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 服务总线 (Microsoft.ServiceBus/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT 中心 (Microsoft.Devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Azure 中继 (Microsoft.Relay/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 事件网格 (Microsoft.EventGrid/topics) / topic | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure 事件网格 (Microsoft.EventGrid/domains) / domain | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web 应用 (Microsoft.Web/sites)/sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure 机器学习 (MachineLearningServices/工作区) /amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>aznbcontent.net |
| SignalR (SignalRService/SignalR) /signalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft.Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| 认知服务 (Microsoft.CognitiveServices/accounts)/account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure 文件同步 (Storagesync.sys/storageSyncServices) /afs |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure 数据工厂 (DataFactory/工厂) /dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure 数据工厂 (DataFactory/工厂) /门户 |  privatelink.azure.com  |  azure.com  |
| 适用于 Redis 的 Azure Cache (Redis) /redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup>用于 IoT 中心内置的与事件中心兼容的终结点。 若要了解详细信息，请参阅 [IoT 中心内置终结点的私有链接支持](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint)

### <a name="china"></a>中国

| 专用链接资源类型/子资源 |专用 DNS 区域名称 | 公共 DNS 区域转发器 |
|---|---|---|
| Azure SQL 数据库 (Microsoft.Sql/servers)/SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Table | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Azure Database for PostgreSQL - 单一服务器 (Microsoft.DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Azure Database for MySQL (Microsoft.DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Azure Database for MariaDB (Microsoft.DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |


## <a name="dns-configuration-scenarios"></a>DNS 配置方案

服务的 FQDN 自动解析为公共 IP 地址。 若要解析到专用终结点的专用 IP 地址，请更改 DNS 配置。

DNS 是通过成功解析专用终结点 IP 地址使应用程序正常工作的一个关键组件。

根据你的偏好，以下方案适用于集成的 DNS 解析：

- [不带自定义 DNS 服务器的虚拟网络工作负荷](#virtual-network-workloads-without-custom-dns-server)
- [使用 DNS 转发器的本地工作负荷](#on-premises-workloads-using-a-dns-forwarder)
- [使用 DNS 转发器的虚拟网络和本地工作负载](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> [Azure 防火墙 DNS 代理](../firewall/dns-settings.md#dns-proxy) 可用作 [本地工作](#on-premises-workloads-using-a-dns-forwarder) 负荷的 dns 转发器和 [使用 dns 转发器的虚拟网络工作负荷](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)。

## <a name="virtual-network-workloads-without-custom-dns-server"></a>不带自定义 DNS 服务器的虚拟网络工作负荷

此配置适用于不带自定义 DNS 服务器的虚拟网络工作负载。 在此方案中，客户端会向 Azure 提供的 DNS 服务 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 查询专用终结点 IP 地址。 Azure DNS 将负责专用 DNS 区域的 DNS 解析。

> [!NOTE]
> 此方案使用 Azure SQL 数据库建议的专用 DNS 区域。 对于其他服务，可以使用以下参考来调整模型：[Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)。

若要正确进行配置，需要以下资源：

- 客户端虚拟网络

- 使用[类型 A 记录](../dns/dns-zones-records.md#record-types)专用 DNS 区域[privatelink.database.windows.net](../dns/private-dns-privatednszone.md)

- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

以下屏幕截图显示了使用专用 DNS 区域的虚拟网络工作负载中的 DNS 解析序列：

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="单个虚拟网络和 Azure 提供的 DNS":::

可以将此模型扩展到与同一专用终结点关联的对等互连虚拟网络。 [将新的虚拟网络链接添加](../dns/private-dns-virtual-network-links.md) 到所有对等互连虚拟网络的专用 DNS 区域。

> [!IMPORTANT]
> 此配置需要使用单个专用 DNS 区域。 为不同的虚拟网络创建具有相同名称的多个区域时，需要通过手动操作来合并 DNS 记录。

> [!IMPORTANT]
> 如果要在不同的订阅中使用中心辐射型模型中的专用终结点，请在中心重复使用相同的专用 DNS 区域。

在此方案中，有一个 [集线器和辐射](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 网络拓扑。 辐射网络共享专用终结点。 辐射虚拟网络链接到相同的专用 DNS 区域。 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="具有 Azure 提供的 DNS 的中心辐射型拓扑":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>使用 DNS 转发器的本地工作负荷

对于本地工作负荷，若要解析专用终结点的 FQDN，请使用 DNS 转发器解析 Azure 中的 Azure 服务 [公共 DNS 区域](#azure-services-dns-zone-configuration) 。

以下方案适用于在 Azure 中具有 DNS 转发器的本地网络。 此转发器通过服务器级转发器将 DNS 查询解析为 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)。 

> [!NOTE]
> 此方案使用 Azure SQL 数据库建议的专用 DNS 区域。 对于其他服务，可以使用以下参考来调整模型：[Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)。

若要正确进行配置，需要以下资源：

- 本地网络
- 虚拟网络 [已连接到本地](/azure/architecture/reference-architectures/hybrid-networking/)
- 部署在 Azure 中的 DNS 转发器 
- 使用[类型 A 记录](../dns/dns-zones-records.md#record-types)专用 DNS 区域[privatelink.database.windows.net](../dns/private-dns-privatednszone.md)
- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图说明了本地网络中的 DNS 解析顺序。 此配置使用 Azure 中部署的 DNS 转发器。 此解决方案是通过 [链接到虚拟网络](../dns/private-dns-virtual-network-links.md)的专用 DNS 区域进行的：

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="使用 Azure DNS 的本地网络":::

可以为已有 DNS 解决方案的本地网络扩展此配置。 本地 DNS 解决方案配置为通过 [条件转发器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)将 DNS 流量转发到 Azure DNS。 条件转发器引用在 Azure 中部署的 DNS 转发器。

> [!NOTE]
> 此方案使用 Azure SQL 数据库建议的专用 DNS 区域。 对于其他服务，可以使用以下参考内容调整模型： [Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)

若要正确进行配置，需要以下资源：

- 具有自定义 DNS 解决方案的本地网络 
- 虚拟网络 [已连接到本地](/azure/architecture/reference-architectures/hybrid-networking/)
- 部署在 Azure 中的 DNS 转发器
- 使用[类型 A 记录](../dns/dns-zones-records.md#record-types)专用 DNS 区域[privatelink.database.windows.net](../dns/private-dns-privatednszone.md)
- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图说明了本地网络中的 DNS 解析。 DNS 解析按条件转发到 Azure。 此解决方案由 [链接到虚拟网络](../dns/private-dns-virtual-network-links.md)的专用 DNS 区域进行。

> [!IMPORTANT]
> 条件转发必须指向建议的[公共 DNS 区域转发器](#azure-services-dns-zone-configuration)。 例如： `database.windows.net` 而不是 **privatelink**. database.windows.net。

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="本地转发到 Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>使用 DNS 转发器的虚拟网络和本地工作负载

对于访问虚拟网络和本地网络中的专用终结点的工作负荷，请使用 DNS 转发器解析在 Azure 中部署的 Azure 服务 [公共 DNS 区域](#azure-services-dns-zone-configuration) 。

以下方案适用于在 Azure 中使用虚拟网络的本地网络。 这两个网络都访问位于共享中心网络中的专用终结点。

此 DNS 转发器负责通过服务器级转发器将所有 DNS 查询解析为 Azure 提供的 DNS 服务 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)。

> [!IMPORTANT]
> 此配置需要使用单个专用 DNS 区域。 所有从本地和[对等虚拟网络](../virtual-network/virtual-network-peering-overview.md)建立的客户端连接也必须使用同一专用 DNS 区域。

> [!NOTE]
> 此方案使用 Azure SQL 数据库建议的专用 DNS 区域。 对于其他服务，可以使用以下参考来调整模型：[Azure 服务 DNS 区域配置](#azure-services-dns-zone-configuration)。

若要正确进行配置，需要以下资源：

- 本地网络
- 虚拟网络 [已连接到本地](/azure/architecture/reference-architectures/hybrid-networking/)
- [对等的虚拟网络](../virtual-network/virtual-network-peering-overview.md) 
- 部署在 Azure 中的 DNS 转发器
- 使用[类型 A 记录](../dns/dns-zones-records.md#record-types)专用 DNS 区域[privatelink.database.windows.net](../dns/private-dns-privatednszone.md)
- 专用终结点信息（FQDN 记录名称和专用 IP 地址）

下图显示了网络、本地和虚拟网络的 DNS 解析。 解决方法是使用 DNS 转发器。 此解决方案是通过 [链接到虚拟网络](../dns/private-dns-virtual-network-links.md)的专用 DNS 区域进行的：

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="混合方案":::

## <a name="next-steps"></a>后续步骤
- [了解专用终结点](private-endpoint-overview.md)
