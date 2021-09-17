---
title: Azure 服务标记概述
titlesuffix: Azure Virtual Network
description: 了解服务标记。 服务标记有助于尽量降低创建安全规则时的复杂性。
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/23/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 11f8c7ddd7e06e9cc4c27173d5bb8f9dd14cbd42
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768470"
---
# <a name="virtual-network-service-tags"></a>虚拟网络服务标记
<a name="network-service-tags"></a>

服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量减少频繁更新网络安全规则所需的复杂操作。

可以在[网络安全组](./network-security-groups-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制。 创建安全规则时，请使用服务标记代替特定 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量 。 

> [!NOTE] 
> 从 2021 年 3 月起，你还可以使用服务标记来代替[用户定义的路由](./virtual-networks-udr-overview.md)中的显式 IP 范围。 此功能目前提供公共预览版。 

可使用服务标记来实现网络隔离，保护 Azure 资源免受常规 Internet 侵害，同时访问具有公共终结点的 Azure 服务。 可创建入站/出站网络安全组规则，以拒绝进出 Internet 的流量并允许进出 AzureCloud 或特定 Azure 服务的其他[可用服务标记](#available-service-tags)的流量 。

![使用服务标记对 Azure 服务进行网络隔离](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>可用服务标记
下表列出了可在[网络安全组](./network-security-groups-overview.md#security-rules)规则中使用的所有服务标记。

列指示标记是否：

- 适用于涵盖入站或出站流量的规则。
- 支持[区域](https://azure.microsoft.com/regions)范围。
- 可在 [Azure 防火墙](../firewall/service-tags.md)规则中使用。

默认情况下，服务标记反映了整个云的范围。 某些服务标记还通过将相应的 IP 范围限于指定的区域，带给你更精细的控制。 例如，服务标记“Storage”表示整个云的 Azure 存储，而“Storage.WestUS”则将范围缩小到来自美国西部区域的存储 IP 地址范围 。 下表指出每个服务标记是否支持此类区域范围。 请注意，为每个标记列出的方向为建议项。 例如，AzureCloud 标记可用于允许入站流量。 但在大多数情况下，我们不建议这样做，因为这意味着允许来自所有 Azure IP 的流量，包括其他 Azure 客户使用的流量。 

| 标记 | 目的 | 可以使用入站还是出站连接？ | 可以支持区域范围？ | 是否可与 Azure 防火墙一起使用？ |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | 操作组。 | 入站 | 否 | 否 |
| **ApiManagement** | 专用于 Azure API 管理的部署的管理流量。 <br/><br/>*注意：* 此标记表示每个区域的控制平面的 Azure API 管理服务终结点。 这使得客户可以对在 API 管理服务中配置的 API、操作、策略和 NamedValues 执行管理操作。  | 入站 | 是 | 是 |
| **ApplicationInsightsAvailability** | Application Insights 可用性。 | 入站 | 否 | 否 |
| **AppConfiguration** | 应用配置。 | 出站 | 否 | 否 |
| **AppService**    | Azure 应用服务。 建议将此标记用于 Web 应用和函数应用的出站安全规则。  | 出站 | 是 | 是 |
| **AppServiceManagement** | 应用服务环境专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory。 | 出站 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory 域服务专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **AzureAdvancedThreatProtection** | Azure 高级威胁防护。 | 出站 | 否 | 否 |
| **AzureArcInfrastructure** | 已启用 Azure Arc 的服务器、已启用 Azure Arc 的 Kubernetes 和来宾配置流量。<br/><br/>注意：此标记依赖于 AzureActiveDirectory、AzureTrafficManager 和 AzureResourceManager 标记  。 此标记当前不可通过 Azure 门户进行配置。| 出站 | 否 | 是 |
| **AzureAttestation** | Azure 证明。<br/><br/>注意：此标签当前无法通过 Azure 门户进行配置 | 出站 | 否 | 是 | 
| **AzureBackup** |Azure 备份。<br/><br/>*注意：* 此标记依赖于 **存储** 和 **AzureActiveDirectory** 标记。 | 出站 | 否 | 是 |
| **AzureBotService** | Azure 机器人服务。 | 出站 | 否 | 否 |
| **AzureCloud** | 所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 出站 | 是 | 是 |
| **AzureCognitiveSearch** | Azure 认知搜索。 <br/><br/>可以使用此标记或此标记涵盖的 IP 地址授予索引器对数据源的安全访问权限。 有关更多详细信息，请参阅[索引器连接文档](../search/search-indexer-troubleshooting.md#connection-errors)。 <br/><br/> *注意*：此服务标记的 IP 范围列表中不包含搜索服务的 IP，该 IP **也需要添加** 到数据源的 IP 防火墙中。 | 入站 | 否 | 否 |
| **AzureConnectors** | 此标记表示用于托管连接器的 IP 地址，这些托管连接器对 Azure 逻辑应用服务进行入站 Webhook 回调，并对其各自的服务（例如 Azure 存储或 Azure 事件中心）进行出站调用。 | 入站/出站 | 是 | 是 |
| **AzureContainerRegistry** | Azure 容器注册表。 | 出站 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos DB。 | 出站 | 是 | 是 |
| **AzureDatabricks** | Azure Databricks。 | 推送、请求和匿名 | 否 | 否 |
| **AzureDataExplorerManagement** | Azure 数据资源管理器管理。 | 入站 | 否 | 否 |
| **AzureDataLake** | Azure Data Lake Storage Gen1。 | 出站 | 否 | 是 |
| **AzureDevSpaces** | Azure Dev Spaces。 | 出站 | 否 | 否 |
| **AzureDevOps** | Azure Dev Ops。<br/><br/>注意：此标签当前无法通过 Azure 门户进行配置| 入站 | 否 | 是 |
| **AzureDigitalTwins** | Azure 数字孪生。<br/><br/>注意：此标记或此标记所涵盖的 IP 地址可用于限制对事件路由配置的终结点的访问。 此标记当前不可通过 Azure 门户进行配置 | 入站 | 否 | 是 |
| **AzureEventGrid** | Azure 事件网格。 | 推送、请求和匿名 | 否 | 否 |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door。 | 推送、请求和匿名 | 否 | 否 |
| **AzureInformationProtection** | Azure 信息保护。<br/><br/>*注意：* 此标记依赖于 AzureActiveDirectory、AzureFrontDoor.Frontend 和 AzureFrontDoor.FirstParty 标记  。 | 出站 | 否 | 否 |
| **AzureIoTHub** | Azure IoT 中心。 | 出站 | 是 | 否 |
| **AzureKeyVault** | Azure Key Vault。<br/><br/>*注意：* 此标记依赖于 **AzureActiveDirectory** 标记。 | 出站 | 是 | 是 |
| **AzureLoadBalancer** | Azure 基础结构负载均衡器。 此标记将转换为[主机的虚拟 IP 地址](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16)，Azure 的运行状况探测源于该 IP。 这只包括探测流量，而不包括到后端资源的实际流量。 如果不使用 Azure 负载均衡器，则可替代此规则。 | 推送、请求和匿名 | 否 | 否 |
| **AzureMachineLearning** | Azure 机器学习。 | 推送、请求和匿名 | 否 | 是 |
| **AzureMonitor** | Log Analytics、Application Insights、AzMon 和自定义指标（GiG 终结点）。<br/><br/>注意：如果是日志分析，亦必须有“存储”标记。 如果使用了 Linux 代理，亦必须有“GuestAndHybridManagement”。 | 出站 | 否 | 是 |
| **AzureOpenDatasets** | Azure 开放数据集。<br/><br/>*注意：* 此标记依赖于 AzureFrontDoor.Frontend 和 Storage 标记 。 | 出站 | 否 | 否 |
| **AzurePlatformDNS** | 基本基础结构（默认）DNS 服务。<br/><br>可以使用此标记来禁用默认 DNS。 使用此标记时要格外小心。 建议你阅读 [Azure 平台注意事项](./network-security-groups-overview.md#azure-platform-considerations)。 我们还建议你在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **AzurePlatformIMDS** | Azure 实例元数据服务 (IMDS)，它是一个基本基础结构服务。<br/><br/>可以使用此标记来禁用默认 IMDS。 使用此标记时要格外小心。 建议你阅读 [Azure 平台注意事项](./network-security-groups-overview.md#azure-platform-considerations)。 我们还建议你在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **AzurePlatformLKM** | Windows 授权或密钥管理服务。<br/><br/>可以使用此标记来禁用授权默认值。 使用此标记时要格外小心。 建议你阅读 [Azure 平台注意事项](./network-security-groups-overview.md#azure-platform-considerations)。  我们还建议你在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **AzureResourceManager** | Azure 资源管理器。 | 出站 | 否 | 否 |
| **AzureSignalR** | Azure SignalR。 | 出站 | 否 | 否 |
| **AzureSiteRecovery** | Azure Site Recovery。<br/><br/>*注意：* 此标记依赖于 **AzureActiveDirectory**、**AzureKeyVault**、**EventHub**、**GuestAndHybridManagement** 和 **Storage** 标记。 | 出站 | 否 | 否 |
| **AzureTrafficManager** | Azure 流量管理器探测 IP 地址。<br/><br/>有关流量管理器探测 IP 地址的详细信息，请参阅 [Azure 流量管理器常见问题解答](../traffic-manager/traffic-manager-faqs.md)。 | 入站 | 否 | 是 |  
| **AzureUpdateDelivery** | 用于访问 Windows 更新。 <br/><br/>*注意：* 此标记提供对 Windows 更新元数据服务的访问。 若要成功下载更新，还必须启用 AzureFrontDoor.FirstParty 服务标记并使用定义的协议和端口配置出站安全规则，如下所示： <ul><li>AzureUpdateDelivery：TCP，端口 443</li><li>AzureFrontDoor.FirstParty：TCP，端口 80</li></ul>此标记当前不可通过 Azure 门户进行配置| 出站 | 否 | 否 |  
| **BatchNodeManagement** | Azure Batch 专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **CognitiveServicesManagement** | Azure 认知服务的流量的地址范围。 | 推送、请求和匿名 | 否 | 否 |
| **DataFactory**  | Azure 数据工厂 | 推送、请求和匿名 | 否 | 否 |
| **DataFactoryManagement** | Azure 数据工厂的管理流量。 | 出站 | 否 | 否 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 的营销电子邮件服务的地址范围。 | 出站 | 是 | 否 |
| EOPExternalPublishedIPs | 此标记表示用于安全与合规中心 Powershell 的 IP 地址。 请参阅[使用 EXO V2 模块连接到安全与合规中心 PowerShell，了解更多详情](/powershell/exchange/connect-to-scc-powershell)。 <br/><br/> 注意：此标签当前无法通过 Azure 门户进行配置。 | 推送、请求和匿名 | 否 | 是 |
| **EventHub** | Azure 事件中心。 | 出站 | 是 | 是 |
| **GatewayManager** | Azure VPN 网关和应用程序网关专用部署的管理流量。 | 入站 | 否 | 否 |
| **GuestAndHybridManagement** | Azure 自动化和来宾配置。 | 出站 | 否 | 是 |
| **HDInsight** | Azure HDInsight。 | 入站 | 是 | 否 |
| **Internet** | 虚拟网络外部的 IP 地址空间，可以通过公共 Internet 进行访问。<br/><br/>此地址范围包括 [Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。 | 推送、请求和匿名 | 否 | 否 |
| **LogicApps** | 逻辑应用。 | 推送、请求和匿名 | 否 | 否 |
| **LogicAppsManagement** | 逻辑应用的管理流量。 | 入站 | 否 | 否 |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security。 | 出站 | 否 | 否 |
| **MicrosoftContainerRegistry** | 用于 Microsoft 容器映像的容器注册表。 <br/><br/>*注意：* 此标记依赖于 AzureFrontDoor.FirstParty 标记。 | 出站 | 是 | 是 |
| **PowerBI** | PowerBi。 注意：此标签当前无法通过 Azure 门户进行配置。 | 推送、请求和匿名 | 否 | 否|
| **PowerQueryOnline** | Power Query Online。 | 推送、请求和匿名 | 否 | 否 |
| **服务总线** | 使用高级服务层级的 Azure 服务总线流量。 | 出站 | 是 | 是 |
| **ServiceFabric** | Azure Service Fabric。<br/><br/>*注意：* 此标记表示每个区域的控制平面的 Service Fabric 服务终结点。 通过它，客户可从其 VNET 针对 Service Fabric 群集执行管理操作（终结点，例如 https:// westus.servicefabric.azure.com） | 推送、请求和匿名 | 否 | 否 |
| **Sql** | Azure SQL 数据库、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Database for MariaDB 和 Azure Synapse Analytics。<br/><br/>*注意：* 此标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。 此标记不适用于 SQL 托管实例。 | 出站 | 是 | 是 |
| **SqlManagement** | SQL 专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **存储** | Azure 存储。 <br/><br/>*注意：* 此标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。 | 出站 | 是 | 是 |
| **StorageSyncService** | 存储同步服务。 | 推送、请求和匿名 | 否 | 否 |
| **WindowsAdminCenter** | 允许 Windows Admin Center 后端服务与 Windows Admin Center 的客户安装通信。 注意：此标签当前无法通过 Azure 门户进行配置。 | 出站 | 否 | 是 |
| **WindowsVirtualDesktop** | Windows 虚拟桌面。 | 推送、请求和匿名 | 否 | 是 |
| **VirtualNetwork** | 虚拟网络地址空间（为虚拟网络定义的所有 IP 地址范围）、所有连接的本地地址空间、[对等互连](virtual-network-peering-overview.md)的虚拟网络、已连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虚拟网络、[主机的虚拟 IP 地址](./network-security-groups-overview.md#azure-platform-considerations)以及在[用户定义的路由](virtual-networks-udr-overview.md)上使用的地址前缀。 此标记还可能包含默认路由。 | 推送、请求和匿名 | 否 | 否 |

>[!NOTE]
>在经典部署模型中（Azure 资源管理器之前），支持上表中列出的标记的子集。 这些标记的拼写不同：
>
>| 经典拼写 | 等效的资源管理器标记 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服务的服务标记表示来自所使用的特定云的地址前缀。 例如，与 Azure 公有云上的 Sql 标记值对应的基础 IP 范围将不同于 Azure 中国云上的基础范围。

> [!NOTE]
> 如果为某个服务（例如 Azure 存储或 Azure SQL 数据库）实现了[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)，Azure 会将[路由](virtual-networks-udr-overview.md#optional-default-routes)添加到该服务的虚拟网络子网。 路由中的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。

## <a name="service-tags-on-premises"></a>本地服务标记  
可获取服务标记和范围的当前信息，将其包含在本地防火墙配置中。 此信息是对应于每个服务标记的 IP 范围的最新列表（截止目前）。 可以通过编程方式或通过 JSON 文件下载获取信息，如以下各节所述。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服务标记发现 API（公共预览版）
可以编程方式检索最新的服务标记列表和 IP 地址范围详细信息：

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure CLI](/cli/azure/network#az_network_list_service_tags)

> [!NOTE]
> 在 API 结果中传播新服务标记数据需要最长 4 周时间。 发生这种情况时，响应元数据中的更改号将递增。 如果指定了不同的位置值，结果中可能会存在临时差异。 使用结果创建 NSG 规则时，应将位置参数设置为与 NSG 区域匹配。 

> [!NOTE]
> API 数据将表示可以与 NSG 规则一起使用的那些标记，它们是可下载的 JSON 文件中当前包含的部分标记。 在公共预览版中，我们不保证在两次更新之间数据保持不变。 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下载的 JSON 文件发现服务标记 
可以下载包含最新服务标记列表和 IP 地址范围详细信息的 JSON 文件。 这些列表每周更新和发布。 每个云的位置如下：

- [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure 美国政府版](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 中国](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 德国](https://www.microsoft.com/download/details.aspx?id=57064)   

这些文件中的 IP 地址范围采用 CIDR 表示法。 

> [!NOTE]
>XML 文件中已发布关于 [Azure 公用](https://www.microsoft.com/download/details.aspx?id=41653)、[Azure 中国](https://www.microsoft.com/download/details.aspx?id=42064)和 [Azure 德国](https://www.microsoft.com/download/details.aspx?id=54770)的部分此类信息。 这些 XML 下载内容将于 2020 年 6 月 30 日被弃用，该日期之后不再可用。 应按照前面几节中所述，使用发现 API 或 JSON 文件下载进行迁移。

### <a name="tips"></a>提示 
- 可记下 JSON 文件中变大的 changeNumber 值，检测相邻两次发布中的更新内容。 每个子部分（例如 Storage.WestUS）都有自己的 changeNumber，该值在出现更改时递增。 任何子部分更改时，文件的 changeNumber 的顶层都将递增。
- 要通过示例了解如何分析服务标记信息（例如获取美国西部区域存储的所有地址范围），请参阅[服务标记发现 API PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag) 文档。
- 当新的 IP 地址添加到服务标签中时，它们至少一周内不会在 Azure 中使用。 可以利用这段时间更新任何可能需要跟踪与服务标记相关的 IP 地址的系统。

## <a name="next-steps"></a>后续步骤
- 了解如何[创建网络安全组](tutorial-filter-network-traffic.md)。
