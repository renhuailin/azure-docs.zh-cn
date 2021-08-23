---
title: 使用 Azure API 管理连接到虚拟网络
description: 了解如何在 Azure API 管理中创建与虚拟网络的连接并通过该连接访问 Web 服务。
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 9de42ef1aa7471f489a02af6e1931c0df0252b7f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746332"
---
# <a name="connect-to-a-virtual-network-using-azure-api-management"></a>使用 Azure API 管理连接到虚拟网络
借助 Azure 虚拟网络 (VNET)，你可以将任何 Azure 资源放置在你能够控制访问权限的非 Internet 可路由网络中。 然后，你可以使用各种 VPN 技术将 VNET 连接到本地网络。 若要了解有关 Azure VNET 的详细信息，请先阅读 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)中的相关信息。

Azure API 管理可以部署到 VNET 内部，以便访问该网络中的后端服务。 你可以将开发人员门户和 API 网关配置为：从 Internet 访问，或者仅在 VNET 内进行访问。 

本文介绍你的 API 管理实例的 VNET 连接选项、设置、限制以及故障排除步骤。 对于特定于内部模式的配置（即仅在 VNET 内访问开发人员门户和 API 网关），请参阅[使用 Azure API 管理连接到内部虚拟网络](./api-management-using-with-internal-vnet.md)。

> [!NOTE]
> API 导入文档 URL 必须托管在可公开访问的 Internet 地址上。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>先决条件

+ **一个有效的 Azure 订阅。** [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **API 管理实例。** 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>启用 VNET 连接

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>使用 Azure 门户启用 VNET 连接

1. 转到 [Azure 门户](https://portal.azure.com)，查找 API 管理实例。 搜索并选择“API 管理服务”。

1. 选择你的 API 管理实例。

1. 选择“虚拟网络”。
1. 配置要在 VNET 中部署的 API 管理实例。

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="在 Azure 门户中选择“VNET”。":::

1. 选择所需的访问类型：

    * 关闭：默认类型。 API 管理未部署到 VNET 中。

    * **外部**：可以通过外部负载均衡器从公共 Internet 访问 API 管理网关和开发人员门户。 网关可以访问 VNET 中的资源。

        ![公共对等互连][api-management-vnet-public]

    * 内部：只能通过内部负载均衡器访问 VNET 内的 API 管理网关和开发人员门户。 网关可以访问 VNET 中的资源。

        ![专用对等互连][api-management-vnet-private]

1. 如果选择了“外部”或“内部”，则会看到一个列表，其中包含预配了 API 管理服务的所有位置（区域） 。 
1. 选择“位置”  。
1. 选取“虚拟网络”、“子网”和“IP 地址”。   
    * 系统将在 VNET 列表中填充你所配置区域内设置的 Azure 订阅中的可用资源管理器虚拟网络。

        :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="门户中的 VNET 设置。":::

        > [!IMPORTANT]
        > * 如果使用 API 2020-12-01 或更早版本在资源管理器 VNET 中部署 Azure API 管理实例：该服务必须位于仅包含 Azure API 管理实例的专用子网中。 若尝试将 Azure API 管理实例部署到包含其他资源的资源管理器 VNET 子网，则部署将失败。
        >
        > * 如果使用 API 2021-01-01 预览版或更高版本在 VNET 中部署 Azure API 管理实例：仅支持资源管理器 VNET，但使用的子网可能包含其他资源。 不必使用专用于 API 管理实例的子网。

1. 选择“应用”。 API 管理实例的“虚拟网络”页将使用新的 VNET 和子网选项进行更新。

1. 继续为 API 管理实例的其余位置配置 VNET 设置。

7. 在顶部导航栏中，选择“保存”，然后选择“应用网络配置”。

    更新 API 管理实例可能需要 15 到 45 分钟。

> [!NOTE]
> 对于使用 API 2020-12-01 及更早版本的客户端，API 管理实例的 VIP 地址将会在以下情形下发生变更：
> * VNET 处于启用或禁用状态。 
> * API 管理从外部虚拟网络移至内部虚拟网络，反之亦然。

> [!IMPORTANT]
> * 如果使用的是 API 2018-01-01 及更早版本：   
> 如果从 VNET 中删除 API 管理或更改 VNET，VNET 将锁定长达 6 个小时。 在这 6 个小时内，你无法删除 VNET 或在其中部署新资源。 
>
> * 如果使用的是 API 2019-01-01 及更高版本：  
> 一旦删除关联的 API 管理服务，即可使用 VNET。

### <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>将 API 管理部署到外部 VNET

你也可以使用以下方法来启用 VNET 连接。

### <a name="api-version-2021-01-01-preview"></a>API 版本 2021-01-01-preview

* Azure 资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet-publicip)

     [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="api-version-2020-12-01"></a>API 版本 2020-12-01

* Azure 资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-external-vnet)

     [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-external-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet - 在 VNET 中[创建](/powershell/module/az.apimanagement/new-azapimanagement)或[更新](/powershell/module/az.apimanagement/update-azapimanagementregion) API 管理实例

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>连接到托管在虚拟网络中的 Web 服务
一旦将 API 管理服务连接到 VNET 后，你即可访问公共服务一样访问其中的后端服务。 创建或编辑 API 时，请在“Web 服务 URL”字段键入 Web 服务的本地 IP 地址或主机名（若为 VNET 配置 DNS 服务器）。

![从 VPN 添加 API][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"></a>常见网络配置问题
在 VNET 中部署 API 管理服务时可能发生的常见配置错误问题包括：

* 自定义 DNS 服务器设置：  
    API 管理服务依赖于多项 Azure 服务。 当 API 管理托管在包含自定义 DNS 服务器的 VNET 中时，API 管理需要解析这些 Azure 服务的主机名。  
    * 有关自定义 DNS 设置的指导，请参阅 [Azure 虚拟网络中资源的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。  
    * 有关引用信息，请参阅[端口表](#required-ports)和网络要求。

    > [!IMPORTANT]
    > 如果计划使用自定义 DNS 服务器配置 VNET，则应先进行设置，再将 API 管理服务部署到 VNET 中。 否则，每次通过运行[应用网络配置操作](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)更改 DNS 服务器时，都需要更新 API 管理服务。

* API 管理所需的端口：  
    你可以使用 [网络安全组][网络安全组] 在子网部署 API 管理，以便控制传入子网的入站和出站流量。 如果以下任一端口不可用，则 API 管理可能无法正常工作且不可访问。 在 VNET 中部署 API 管理时，另一个常见的配置错误问题便是端口遭到阻止。

<a name="required-ports"> </a>在 VNET 中托管 API 管理服务实例时，将使用下表中的端口。

| 源/目标端口 | 方向          | 传输协议 |   [服务标记](../virtual-network/network-security-groups-overview.md#service-tags) <br> 源/目标   | 目的 (\*)                                                 | VNET 类型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | 入站            | TCP                | INTERNET/VIRTUAL_NETWORK            | 客户端与 API 管理的通信                      | 外部             |
| * / 3443                     | 入站            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 门户和 PowerShell 的管理终结点         | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | **与 Azure 存储的依赖关系**                             | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) 和 Azure KeyVault 依赖项                  | 外部和内部  |
| * / 1433                     | 出站           | TCP                | VIRTUAL_NETWORK / SQL                 | **访问 Azure SQL 终结点**                           | 外部和内部  |
| * / 443                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                 | 访问 Azure KeyVault                           | 外部和内部  |
| * / 5671, 5672, 443          | 出站           | TCP                | VIRTUAL_NETWORK / EventHub            | [事件中心策略日志](api-management-howto-log-event-hubs.md)和监视代理的依赖项 | 外部和内部  |
| * / 445                      | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | 与适用于 [GIT](api-management-configuration-repository-git.md) 的 Azure 文件共享的依赖关系                      | 外部和内部  |
| * / 443, 12000                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 运行状况和监视扩展         | 外部和内部  |
| * / 1886、443                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 发布[诊断日志和指标](api-management-howto-use-azure-monitor.md)、[资源运行状况](../service-health/resource-health-overview.md)和 [Application Insights](api-management-howto-app-insights.md)                   | 外部和内部  |
| * / 25、587、25028                       | 出站           | TCP                | VIRTUAL_NETWORK/INTERNET            | 连接到 SMTP 中继以发送电子邮件                    | 外部和内部  |
| * / 6381 - 6383              | 入站和出站 | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 访问 Redis 服务以获取计算机之间的[缓存](api-management-caching-policies.md)策略         | 外部和内部  |
| * / 4290              | 入站和出站 | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 同步用于计算机之间的[速率限制](api-management-access-restriction-policies.md#LimitCallRateByKey)策略的计数器         | 外部和内部  |
| * / *                        | 入站            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure 基础结构负载均衡器                          | 外部和内部  |

>[!IMPORTANT]
> “目的”列中的粗体项是成功部署 API 管理服务的必需项。 不过，阻止其他端口将导致使用和监视运行中服务以及提供所承诺 SLA 的能力下降 。

+ TLS 功能：  
  若要启用 TLS/SSL 证书链生成及验证程序，API 管理服务需要与 `ocsp.msocsp.com`、`mscrl.microsoft.com` 和 `crl.microsoft.com` 建立出站网络连接。 但若上传到 API 管理的任何证书包含指向 CA 根的完整链，则不需要此依赖项。

+ DNS 访问：  
  与 DNS 服务器通信时，`port 53` 需要进行出站访问。 如果 VPN 网关的另一端存在自定义 DNS 服务器，则该 DNS 服务器必须可从承载 API 管理的子网访问。

+ 指标和运行状况监视：  
  与 Azure 监视终结点建立出站网络连接时，这些终结点将在下列域下解析，并根据 AzureMonitor 服务标记进行表示，以与网络安全组配合使用。

    | Azure 环境 | 终结点                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure 中国世纪互联     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  
+ **区域服务标记**：允许与存储、SQL 和事件中心服务标记建立出站连接的 NSG 规则可以使用与包含 API 管理实例的区域对应的这些标记的区域版本（例如，美国西部区域中 API 管理实例对应 Storage.WestUS）。 在多区域部署中，每个区域中的 NSG 应该允许流量进入该区域和主要区域的服务标记。

    > [!IMPORTANT]
    > 通过允许出站连接至美国西部区域的 blob 存储，即可在 VNET 中发布 API 管理实例的[开发人员门户](api-management-howto-developer-portal.md)。 例如，在 NSG 规则中使用 Storage.WestUS 服务标记。 当前若要发布任何 API 管理实例的开发人员门户，都需要连接至美国西部区域的 Blob 存储。

+ SMTP 中继：  
  在主机 `smtpi-co1.msn.com`、`smtpi-ch1.msn.com`、`smtpi-db3.msn.com`、`smtpi-sin.msn.com`、`ies.global.microsoft.com` 下解析的 SMTP 中继的出站网络连接

+ 开发人员门户 CAPTCHA：  
  在主机 `client.hip.live.com` 和 `partner.hip.live.com` 下解析的开发人员门户 CAPTCHA 的出站网络连接。

+ Azure 门户诊断：  
  从 VNET 内部使用 API 管理扩展时，需要对 `port 443` 上的 `dc.services.visualstudio.com` 进行出站访问，以启用 Azure 门户的诊断日志流。 此访问有助于排查你在使用扩展时可能遭遇的问题。

+ Azure 负载均衡器：  
  你无需允许来自 `Developer` SKU 的 `AZURE_LOAD_BALANCER` 服务标记的入站请求，因为其后只部署了一个计算单位。 但是，当缩放至更高的 SKU（如 `Premium`）时，来自 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 的入站将变得至关重要，因为负载均衡器发生运行状况探测故障将会导致部署失败。

+ **Application Insights：**  
  若已针对 API 管理启用 [Azure Application Insights](api-management-howto-app-insights.md) 监视功能，即可出站连接至 VNET 中的[遥测终结点](../azure-monitor/app/ip-addresses.md#outgoing-ports)。

+ 使用 Express Route 或网络虚拟设备强制通过隧道将流量传输至本地防火墙：  
  你通常可以通过配置及定义自己的默认路由 (0.0.0.0/0)，以使来自 API 管理委托子网的所有流量强制穿过本地防火墙或流入网络虚拟设备。 此流量流会中断与 Azure API 管理的连接，因为出站流量会在本地遭到阻止，或者已经过网络地址转换处理，变为一组无法再与各种 Azure 终结点配合工作的无法识别的地址。 不过，你可以通过多种方法解决此问题： 

  * 在部署以下服务的 API 管理所在的子网上启用[服务终结点][ServiceEndpoints]：
      * Azure SQL
      * Azure 存储
      * Azure EventHub
      * Azure ServiceBus 和
      * Azure KeyVault。 
  
    为这些服务直接启用 API 管理委托子网中的终结点，以便使用 Microsoft Azure 主干网络为服务流量提供最佳路由。 若在使用服务终结点时采用强制隧道 API 管理，则上述 Azure 服务流量不会强制通过隧道传输。 其他 API 管理服务依赖项流量将强制通过隧道传输且不能丢失； 一旦丢失，API 管理服务将无法正常工作。

  * 所有从 Internet 传输至 API 管理服务之管理终结点的控制平面流量均通过由 API 管理托管的一组特定入站 IP 进行路由。 当系统强制通过隧道传输流量时，响应将不会以对称方式映射回这些入站源 IP。 若要克服此限制，请将以下用户定义路由 ([UDR][UDRs]) 的目标设置为“Internet”，以将流量引回 Azure。 如需查找有关控制平面流量入站 IP 的设置信息，请参阅[控制平面 IP 地址](#control-plane-ips)中的记录。

  * 对于强制通过隧道传输流量的其他 API 管理服务依赖项，应解析主机名并访问相应终结点。 其中包括:
      - 指标和运行状况监视
      - Azure 门户诊断
      - SMTP 中继
      - 开发人员门户验证码

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>疑难解答
* 在子网中初次部署 API 管理服务时失败： 
  * 将虚拟机部署到相同子网。 
  * 在虚拟机中部署远程桌面，并验证与 Azure 订阅中以下其中一项资源的连接性：
    * Azure 存储 Blob
    * Azure SQL 数据库
    * Azure 存储表

  > [!IMPORTANT]
  > 验证连接性后，先删除子网中的所有资源，然后再将 API 管理部署到子网。

* 验证网络连接状态：  
  * 在将 API 管理部署到子网后，应使用门户来检查实例与依赖项（如 Azure 存储）之间的连接性。 
  * 在门户的左侧菜单中的“部署和基础结构”下，选择“网络连接状态” 。

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="在门户中验证网络连接状态":::

  | 筛选器 | 说明 |
  | ----- | ----- |
  | **必需** | 选择“必需”，以查看 API 管理与必需 Azure 服务之间的连接性。 如果连接失败，则表示该实例无法执行核心操作来管理 API |
  | **可选** | 选择“可选”，以查看 API 管理与可选服务之间的连接性。 如果连接失败，仅表示特定功能（如 SMTP）将无法正常工作。 连接失败可能会导致使用和监视 API 管理实例以及提供所承诺 SLA 的能力下降。 |

  若要解决连接问题，请查看[常见网络配置问题](#network-configuration-issues)并修复所需的网络设置。

* 增量更新：  
  更改网络时，请参阅 [NetworkStatus API](/rest/api/apimanagement/2019-12-01/networkstatus)，以验证 API 服务是否仍具有关键资源的访问权限。 连接状态应每 15 分钟更新一次。

* 资源导航链接：  
  若使用 API 2020-12-01 及更早版本将 API 管理实例部署到资源管理器 VNET 子网，API 管理会通过创建资源导航链接来保留子网。 如果子网已包含来自其他提供程序的资源，则部署将 **失败**。 同样，当删除 API 管理服务或将其移至其他子网时，资源导航链接也将会删除。

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> 子网大小要求
Azure 会保留每个子网中无法使用的一些 IP 地址。 为遵从协议，保留子网的第一个和最后一个 IP 地址。 另外三个地址将用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)。

除 Azure VNET 基础结构使用的 IP 地址外，子网中的每个 API 管理实例还会使用以下地址：
* 每单位 Premium SKU 两个 IP 地址，或 
* Developer SKU，一个 IP 地址。 

每个实例会为外部负载均衡器保留额外的 IP 地址。 当部署到[内部 VNET](./api-management-using-with-internal-vnet.md) 时，实例需要使用相关内部负载均衡器的额外 IP 地址。

根据上述计算原理，可以在其中部署 API 管理的子网的最小大小为 /29，其中提供 3 个可用的 IP 地址。 因此，每增加一个 API 管理缩放单元，即需要增加两个 IP 地址。

## <a name="routing"></a><a name="routing"> </a> 路由
+ 负载均衡的公共 IP 地址 (VIP) 将保留，并用于访问 VNET 外部的所有服务终结点和资源。
  + 如需查找负载均衡的公共 IP 地址，请前往 Azure 门户的“概述/概要”边栏选项卡。
+ 你可以使用子网 IP 范围 (DIP) 中的 IP 地址来访问 VNET 中的资源。

## <a name="limitations"></a><a name="limitations"> </a>限制
* 若使用 API 2020-12-01 及更早版本，包含 API 管理实例的子网将不得包含任何其他 Azure 资源类型。
* 子网和 API 管理服务必须在同一个订阅中。
* 包含 API 管理实例的子网不能在订阅之间移动。
* 对于在内部 VNET 模式下配置的多区域 API 管理部署，用户拥有路由权限，并且负责跨多个区域管理负载均衡。
* 由于平台限制，另一个区域的全局对等互连 VNET 中的资源与处于内部模式的 API 管理服务之间的连接将中断。 有关详细信息，请参阅[一个虚拟网络中的资源无法与对等互连虚拟网络中的 Azure 内部负载均衡器通信](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> 控制平面 IP 地址

IP 地址由 **Azure 环境** 划分。 允许入站请求时，必须允许标记有“全局”(Global) 的 IP 地址以及特定于区域的 IP 地址 。

| **Azure 环境**|   **区域**|  IP 地址|
|-----------------|-------------------------|---------------|
| Azure Public| 美国中南部（全球）| 104.214.19.224|
| Azure Public| 美国中北部（全球）| 52.162.110.80|
| Azure Public| 美国中西部| 52.253.135.58|
| Azure Public| 韩国中部| 40.82.157.167|
| Azure Public| 英国西部| 51.137.136.0|
| Azure Public| 日本西部| 40.81.185.8|
| Azure Public| 美国中北部| 40.81.47.216|
| Azure Public| 英国南部| 51.145.56.125|
| Azure Public| 印度西部| 40.81.89.24|
| Azure Public| 美国东部| 52.224.186.99|
| Azure Public| 西欧| 51.145.179.78|
| Azure Public| 日本东部| 52.140.238.179|
| Azure Public| 法国中部| 40.66.60.111|
| Azure Public| 加拿大东部| 52.139.80.117|
| Azure Public| 阿拉伯联合酋长国北部| 20.46.144.85|
| Azure Public| 巴西南部| 191.233.24.179|
| Azure Public| 巴西东南部| 191.232.18.181|
| Azure Public| 东南亚| 40.90.185.46|
| Azure Public| 南非北部| 102.133.130.197|
| Azure Public| 加拿大中部| 52.139.20.34|
| Azure Public| 韩国南部| 40.80.232.185|
| Azure Public| 印度中部| 13.71.49.1|
| Azure Public| 美国西部| 13.64.39.16|
| Azure Public| 澳大利亚东南部| 20.40.160.107|
| Azure Public| 澳大利亚中部| 20.37.52.67|
| Azure Public| 印度南部| 20.44.33.246|
| Azure Public| 美国中部| 13.86.102.66|
| Azure Public| 澳大利亚东部| 20.40.125.155|
| Azure Public| 美国西部 2| 51.143.127.203|
| Azure Public| 美国西部 3| 20.150.167.160|
| Azure Public| 美国东部 2 EUAP| 52.253.229.253|
| Azure Public| 美国中部 EUAP| 52.253.159.160|
| Azure Public| 美国中南部| 20.188.77.119|
| Azure Public| 美国东部 2| 20.44.72.3|
| Azure Public| 北欧| 52.142.95.35|
| Azure Public| 东亚| 52.139.152.27|
| Azure Public| 法国南部| 20.39.80.2|
| Azure Public| 瑞士西部| 51.107.96.8|
| Azure Public| 澳大利亚中部 2| 20.39.99.81|
| Azure Public| 阿联酋中部| 20.37.81.41|
| Azure Public| 瑞士北部| 51.107.0.91|
| Azure Public| 南非西部| 102.133.0.79|
| Azure Public| 德国中西部| 51.116.96.0|
| Azure Public| 德国北部| 51.116.0.0|
| Azure Public| 挪威东部| 51.120.2.185|
| Azure Public| 挪威西部| 51.120.130.134|
| Azure 中国世纪互联| 中国北部（全球）| 139.217.51.16|
| Azure 中国世纪互联| 中国东部（全球）| 139.217.171.176|
| Azure 中国世纪互联| 中国北部| 40.125.137.220|
| Azure 中国世纪互联| 中国东部| 40.126.120.30|
| Azure 中国世纪互联| 中国北部 2| 40.73.41.178|
| Azure 中国世纪互联| 中国东部 2| 40.73.104.4|
| Azure Government| US Gov 弗吉尼亚州（全球）| 52.127.42.160|
| Azure Government| US Gov 德克萨斯州（全球）| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| US Gov 亚利桑那州| 52.244.32.39|
| Azure Government| US Gov 德克萨斯州| 52.243.154.118|
| Azure Government| USDoD 中部| 52.182.32.132|
| Azure Government| USDoD 东部| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>相关内容
* [使用 Vpn 网关将虚拟网络连接到后端](../vpn-gateway/design.md#s2smulti)
* [通过不同的部署模型连接虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何使用 API 检查器跟踪 Azure API 管理中的调用](api-management-howto-api-inspector.md)
* [虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)
* [服务标记](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
