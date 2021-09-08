---
title: 什么是 Azure 专用终结点？
description: 了解 Azure 专用终结点
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: allensu
ms.openlocfilehash: f816ae15ddba9f56f1b504b2e4ccc52efdc09249
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099970"
---
# <a name="what-is-azure-private-endpoint"></a>什么是 Azure 专用终结点？

Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效地引入 VNet 中。 

该服务可以是 Azure 服务，比如：

* Azure 存储
* Azure Cosmos DB
* Azure SQL 数据库
* 使用[专用链接服务](private-link-service-overview.md)的专属服务。
  
## <a name="private-endpoint-properties"></a>专用终结点属性 
 专用终结点指定以下属性： 


|属性  |说明 |
|---------|---------|
|名称    |    资源组中的唯一名称。      |
|子网    |  要部署的子网以及分配专用 IP 地址的子网。 有关子网要求，请参阅本文中的“限制”部分。         |
|专用链接资源    |   用于通过可用类型列表中的资源 ID 或别名建立连接的专用链接资源。 将为发送到此资源的所有流量生成唯一的网络标识符。       |
|目标子资源   |      要连接的子资源。 每个专用链接资源类型具有不同的选项，可根据偏好做出选择。    |
|连接批准方法    |  自动或手动。 根据 Azure 基于角色的访问控制权限，可以自动批准专用终结点。 如果尝试在没有 Azure 基于角色的访问控制的情况下连接到专用链接资源，请使用手动方法来允许资源所有者批准连接。        |
|请求消息     |  可为请求手动批准的连接指定消息。 此消息可用于标识特定的请求。        |
|连接状态   |   一个只读属性，指定专用终结点是否处于活动状态。 只能使用处于已批准状态的专用终结点发送流量。 更多可用状态： <br>-**已批准**：连接已自动或手动批准，随时可供使用。</br><br>-**等待中**：连接是手动创建的，正等待由专用链接资源所有者批准。</br><br>-**已拒绝**：连接已被专用链接资源所有者拒绝。</br><br>-**已断开连接**：连接已被专用链接资源所有者删除。 专用终结点已变为参考性终结点，应将其删除以清理资源。 </br>|

下面是有关专用终结点的一些重要详细信息： 
- 专用终结点使同一 VNet、区域性对等互连的 Vnet、全局性对等互连的 Vnet 中的以及本地用户能够使用 [VPN](https://azure.microsoft.com/services/vpn-gateway/) 或 [Express 路由](https://azure.microsoft.com/services/expressroute/)和由专用链接提供支持的服务在用户之间实现互连。
 
- 网络连接只能由连接到专用终结点的客户端启动。 服务提供商未提供路由配置，因此无法创建与服务使用者的连接。 只能在单个方向建立连接。

- 创建专用终结点时，系统会创建一个在资源的生命周期内有效的只读网络接口。 系统会为该接口分配子网中映射到专用链接资源的动态专用 IP 地址。 专用 IP 地址的值在专用终结点的整个生命周期中保持不变。
 
- 专用终结点必须与虚拟网络部署在同一区域和订阅中。 
 
- 专用链接资源可部署在与虚拟网络和专用终结点不同的区域中。
 
- 可以使用同一个专用链接资源创建多个专用终结点。 对于使用常见 DNS 服务器配置的单个网络，建议的做法是对给定的专用链接资源使用单个专用终结点，以避免出现重复条目或 DNS 解析冲突。 
 
- 可以在同一虚拟网络中的相同或不同子网上创建多个专用终结点。 在一个订阅中可以创建的专用终结点数量有限制。 有关详细信息，请参阅  [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。

- 来自专用链接资源的订阅也必须注册到 Microsoft。 Network 资源提供程序。 有关详细信息，请参阅 [Azure 资源提供程序](../azure-resource-manager/management/resource-providers-and-types.md)。

 
## <a name="private-link-resource"></a>专用链接资源 
专用链接资源是给定专用终结点的目标。 下表列出了可用的专用终结点资源： 
 
| 专用链接资源名称 | 资源类型 | 子资源 |
| ---------------------------| ------------- | ------------- |
| **Azure 应用配置** | Microsoft.Appconfiguration/configurationStores | configurationStores |
| **Azure 自动化** | Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
| **Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | SQL、MongoDB、Cassandra、Gremlin、表 |
| **Azure Batch** | Microsoft.Batch/batchAccounts | Batch 帐户 |
| **用于 Redis 的 Azure 缓存** | Microsoft.Cache/Redis | redisCache |
| Azure Cache for Redis Enterprise | Microsoft.Cache/redisEnterprise | redisEnterprise |
| **认知服务** | Microsoft.CognitiveServices/accounts | account |
| **Azure 托管磁盘** | Microsoft.Compute/diskAccesses | 托管磁盘 |
| **Azure 容器注册表** | Microsoft.ContainerRegistry/registries | 注册表 |
| **Azure Kubernetes 服务 - Kubernetes API** | Microsoft.ContainerService/managedClusters | 管理 |
| **Azure 数据工厂** | Microsoft.DataFactory/factories | 数据工厂 |
| **Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers | mariadbServer |
| **Azure Database for MySQL** | Microsoft.DBforMySQL/servers | mysqlServer |
| Azure Database for PostgreSQL - 单一服务器 | Microsoft.DBforPostgreSQL/servers | postgresqlServer |
| **Azure IoT 中心** | Microsoft.Devices/IotHubs | iotHub |
| Microsoft Digital Twins | Microsoft.DigitalTwins/digitalTwinsInstances | digitaltwinsinstance |
| **Azure 事件网格** | Microsoft.EventGrid/domains | 域 |
| **Azure 事件网格** | Microsoft.EventGrid/topics  | 事件网格主题 |
| **Azure 事件中心** | Microsoft.EventHub/namespaces | 命名空间 |
| **适用于 FHIR 的 Azure API** | Microsoft.HealthcareApis/services | 服务 |
| Azure Keyvault HSM | Microsoft.Keyvault/managedHSMs | HSM |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | 保管库 |
| **Azure 机器学习** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | project |
| **应用程序网关** | Microsoft.Network/applicationgateways | 应用程序网关 |
| **专用链接服务**（你自己的服务） |  Microsoft.Network/privateLinkServices | empty |
| **Power BI** | Microsoft.PowerBI/privateLinkServicesForPowerBI | Power BI |
| **Azure Purview** | Microsoft.Purview/accounts | account |
| **Azure 备份** | Microsoft.RecoveryServices/vaults | 保管库 |
| **Azure 中继** | Microsoft.Relay/namespaces | 命名空间 |
| **Microsoft Search** | Microsoft.Search/searchServices | 搜索服务 |
| **Azure 服务总线** | Microsoft.ServiceBus/namespaces | 命名空间 |
| **SignalR** | Microsoft.SignalRService/SignalR | signalr |
| **SignalR** | Microsoft.SignalRService/webPubSub | webpubsub |
| **Azure SQL 数据库** | Microsoft.Sql/servers | Sql Server (sqlServer) |
| **Azure 存储** | Microsoft.Storage/storageAccounts | Blob（blob、blob_secondary）<BR> 表（table、table_secondary）<BR> 队列（queue、queue_secondary）<BR> 文件（file、file_secondary）<BR> Web（web、web_secondary） |
| **Azure 文件同步** | Microsoft.StorageSync/storageSyncServices | 文件同步服务 |
| **Azure Synapse** | Microsoft.Synapse/privateLinkHubs | synapse |
| **Azure Synapse Analytics** | Microsoft.Synapse/workspaces | Sql、SqlOnDemand、Dev | 
| **Azure 应用服务** | Microsoft.Web/hostingEnvironments | 宿主环境 |
| **Azure 应用服务** | Microsoft.Web/sites | sites |
| **Azure 应用服务** | Microsoft.Web/staticSites | staticSite |

 
## <a name="network-security-of-private-endpoints"></a>专用终结点的网络安全性 
使用 Azure 服务的专用终结点时，流量将受到特定专用链接资源的保护。 平台会执行访问控制，以验证网络连接是否仅抵达指定的专用链接资源。 若要在同一 Azure 服务中访问更多资源，需要使用额外的专用终结点。 
 
可以完全锁定工作负荷，使其无法访问公共终结点来连接受支持的 Azure 服务。 此控制提供内置的渗透防护，可阻止对同一 Azure 服务上托管的其他资源进行访问，从而为资源提供了额外的网络安全层。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用批准工作流访问专用链接资源 
可使用以下连接批准方法连接到专用链接资源：
- 当你拥有特定的专用链接资源或对其拥权限时 **自动** 批准。 所需的权限基于采用以下格式的专用链接资源类型：Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionsApproval/action
- 没有所需的权限并想要请求访问权限时 **手动** 发出请求。 将发起批准工作流。 将会以“挂起”状态创建专用终结点和后续的专用终结点连接。 专用链接资源所有者负责审批该连接。 获得批准后，将启用专用终结点来正常发送流量，如以下批准工作流图中所示。  

![工作流批准](media/private-endpoint-overview/private-link-paas-workflow.png)
 
专用链接资源所有者可以对专用终结点连接执行以下操作： 

- 评审所有专用终结点连接详细信息。 
- 批准专用终结点连接。 将启用相应的专用终结点，以将流量发送到专用链接资源。 
- 拒绝专用终结点连接。 相应的专用终结点将会更新以反映状态。
- 删除处于任何状态的专用终结点连接。 将使用已断开连接状态更新相应的专用终结点以反映操作，专用终结点所有者此时只能删除资源。 
 
> [!NOTE]
> 只有处于已批准状态的专用终结点才能将流量发送到给定的专用链接资源。 

### <a name="connecting-using-alias"></a>使用别名进行连接
别名是当服务所有者在标准负载均衡器后面创建专用链接服务时，生成的唯一名字对象。 服务所有者可与其使用者脱机共享此别名。 使用者可以使用资源 URI 或别名请求连接到专用链接服务。 若要使用别名进行连接，必须使用手动连接批准方法创建专用终结点。 若要使用手动连接批准方法，请在专用终结点创建流期间将手动请求参数设置为 true。 有关详细信息，请查看 [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) 和 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)。 

## <a name="dns-configuration"></a>DNS 配置 
使用完全限定的域名 (FQDN) 作为连接字符串的一部分连接到专用链接资源时，正确配置 DNS 设置以解析为给定的专用 IP 地址很重要。 现有的 Azure 服务可能已有在通过公共终结点进行连接时要使用的 DNS 配置。 必须覆盖此配置，才能使用专用终结点进行连接。 
 
与专用终结点关联的网络接口包含配置 DNS 所需的完整的一组信息，包括为专用链接资源指定的 FQDN 和专用 IP 地址。 

如需详细了解为专用终结点配置 DNS 的完整建议，请参阅[专用终结点 DNS 配置](private-endpoint-dns.md)。
 
## <a name="limitations"></a>限制
 
下表列出了使用专用终结点时的已知限制： 

| 限制 | 说明 |缓解措施 |
| --------- | --------- | --------- |
| 使用用户定义的路由发往专用终结点的流量可能是非对称的。 | 从专用终结点返回的流量会绕过 NVA 并尝试返回到源 VM。 | 对于通过 UDR 发往专用终结点的所有流量，建议通过在 NVA 的 SNAT 流量来确保对称路由。  |

> [!IMPORTANT]
> NSG 和 UDR 对专用终结点的支持处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



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
