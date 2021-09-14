---
title: 使用 Azure API 管理连接到虚拟网络
description: 了解如何在 Azure API 管理中创建与虚拟网络的连接并通过该连接访问 Web 服务。
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 08/10/2021
ms.author: apimpm
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 6eb89c069cb8ce1017b84f5c886231ae767a25a8
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537362"
---
# <a name="connect-to-a-virtual-network-using-azure-api-management"></a>使用 Azure API 管理连接到虚拟网络

可将 Azure API 管理部署在 Azure 虚拟网络 (VNET) 内部，以访问该网络中的后端服务。 有关 VNET 连接选项、要求和注意事项，请参阅[将虚拟网络与 Azure API 管理配合使用](virtual-network-concepts.md)。

本文介绍如何在外部模式下为 API 管理实例设置 VNET 连接，在此模式下，可以从公共 Internet 访问开发人员门户、API 网关和其他 API 管理终结点。 对于特定于内部模式的配置（在此模式下，只能在 VNET 内部访问这些终结点），请参阅[使用 Azure API 管理连接到内部虚拟网络](./api-management-using-with-internal-vnet.md)。

:::image type="content" source="media/api-management-using-with-vnet/api-management-vnet-external.png" alt-text="连接到外部 VNET":::

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>先决条件

某些先决条件因托管 API 管理实例的[计算平台](compute-infrastructure.md)的版本（`stv2` 或 `stv1`）而异。

> [!TIP]
> 使用门户创建或更新 API 管理实例的网络配置时，该实例将托管在 `stv2` 计算平台上。

### <a name="stv2"></a>[stv2](#tab/stv2)

+ **API 管理实例。** 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

* 虚拟网络和子网位于 API 管理实例所在的区域和订阅中。 子网可以包含其他 Azure 资源。

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

### <a name="stv1"></a>[stv1](#tab/stv1)

+ **API 管理实例。** 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

* 虚拟网络和子网位于 API 管理实例所在的区域和订阅中。

    子网必须专用于 API 管理实例。 若尝试将 Azure API 管理实例部署到包含其他资源的资源管理器 VNET 子网，则部署将失败。

---

## <a name="enable-vnet-connection"></a>启用 VNET 连接

### <a name="enable-vnet-connectivity-using-the-azure-portal-stv2-compute-platform"></a>使用 Azure 门户启用 VNET 连接（`stv2` 计算平台）

1. 转到 [Azure 门户](https://portal.azure.com)，查找 API 管理实例。 搜索并选择“API 管理服务”。
1. 选择你的 API 管理实例。

1. 选择“虚拟网络”。
1. 选择“外部”访问类型。
    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="在 Azure 门户中选择“VNET”。":::

1. 在预配了 API 管理服务的位置（区域）列表中： 
    1. 选择“位置”  。
    1. 选择“虚拟网络”、“子网”和“IP 地址”  。 
    * 系统将在 VNET 列表中填充你所配置区域内设置的 Azure 订阅中的可用资源管理器虚拟网络。

        :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="门户中的 VNET 设置。":::

1. 选择“应用”。 API 管理实例的“虚拟网络”页将使用新的 VNET 和子网选项进行更新。

1. 继续为 API 管理实例的其余位置配置 VNET 设置。

7. 在顶部导航栏中，选择“保存”，然后选择“应用网络配置”。

    更新 API 管理实例可能需要 15 到 45 分钟。

### <a name="enable-connectivity-using-a-resource-manager-template"></a>使用资源管理器模板启用连接

使用以下模板部署 API 管理实例并连接到 VNET。 模板因托管 API 管理实例的[计算平台](compute-infrastructure.md)的版本（`stv2` 或 `stv1`）而异。

### <a name="stv2"></a>[stv2](#tab/stv2)

#### <a name="api-version-2021-01-01-preview"></a>API 版本 2021-01-01-preview 

* Azure 资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-external-vnet-publicip)

     [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="stv1"></a>[stv1](#tab/stv1)

#### <a name="api-version-2020-12-01"></a>API 版本 2020-12-01

* Azure 资源管理器[模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-external-vnet)

     [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-external-vnet%2Fazuredeploy.json)

### <a name="enable-connectivity-using-azure-powershell-cmdlets"></a>使用 Azure PowerShell cmdlet 启用连接 

在 VNET 中[创建](/powershell/module/az.apimanagement/new-azapimanagement)或[更新](/powershell/module/az.apimanagement/update-azapimanagementregion) API 管理实例。

---

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a>连接到托管在虚拟网络中的 Web 服务
将 API 管理服务连接到 VNET 后，即可像访问公共服务一样访问 VNET 中的后端服务。 创建或编辑 API 时，请在“Web 服务 URL”字段键入 Web 服务的本地 IP 地址或主机名（若为 VNET 配置 DNS 服务器）。

:::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-add-api.png" alt-text="从 VNET 添加 API":::

## <a name="network-configuration"></a>网络配置
有关其他网络配置设置，请查看以下部分。 

这些设置解决了在 VNET 中部署 API 管理服务时可能发生的常见错误配置问题。

### <a name="custom-dns-server-setup"></a>自定义 DNS 服务器设置 
在外部 VNET 模式下，Azure 默认会管理 DNS。API 管理服务依赖于多个 Azure 服务。 当 API 管理托管在包含自定义 DNS 服务器的 VNET 中时，API 管理需要解析这些 Azure 服务的主机名。  
* 有关自定义 DNS 设置的指导，请参阅 [Azure 虚拟网络中资源的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。  
* 有关参考信息，请参阅[所需端口](#required-ports)和网络要求。

> [!IMPORTANT]
> 如果计划使用自定义 DNS 服务器配置 VNET，则应先进行设置，再将 API 管理服务部署到 VNET 中。 否则，每次通过运行[应用网络配置操作](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates)更改 DNS 服务器时，都需要更新 API 管理服务。

### <a name="required-ports"></a>所需端口  

可以使用[网络安全组][NetworkSecurityGroups]控制其中部署了 API 管理的子网的入站和出站流量。 如果以下任一端口不可用，则 API 管理可能无法正常工作且不可访问。 

在 VNET 中托管 API 管理服务实例时，将使用下表中的端口。 某些要求因托管 API 管理实例的[计算平台](compute-infrastructure.md)的版本（`stv2` 或 `stv1`）而异。

>[!IMPORTANT]
> “目的”列中的粗体项是成功部署 API 管理服务的必需项。 不过，阻止其他端口将导致使用和监视运行中服务以及提供所承诺 SLA 的能力下降 。

#### <a name="stv2"></a>[stv2](#tab/stv2)

| 源/目标端口 | 方向          | 传输协议 |   [服务标记](../virtual-network/network-security-groups-overview.md#service-tags) <br> 源/目标   | 目的 (\*)                                                 | VNET 类型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | 入站            | TCP                | INTERNET/VIRTUAL_NETWORK            | 客户端与 API 管理的通信                      | 外部             |
| * / 3443                     | 入站            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 门户和 PowerShell 的管理终结点         | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | **与 Azure 存储的依赖关系**                             | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) 和 Azure 密钥保管库依赖关系                  | 外部和内部  |
| * / 1433                     | 出站           | TCP                | VIRTUAL_NETWORK / SQL                 | **访问 Azure SQL 终结点**                           | 外部和内部  |
| * / 443                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                | 对 Azure 密钥保管库的访问权限                         | 外部和内部  |
| * / 5671, 5672, 443          | 出站           | TCP                | VIRTUAL_NETWORK / Event Hub            | [事件中心策略日志](api-management-howto-log-event-hubs.md)和监视代理的依赖项 | 外部和内部  |
| * / 445                      | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | 与适用于 [GIT](api-management-configuration-repository-git.md) 的 Azure 文件共享的依赖关系                      | 外部和内部  |
| * / 443, 12000                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 运行状况和监视扩展         | 外部和内部  |
| * / 1886、443                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 发布[诊断日志和指标](api-management-howto-use-azure-monitor.md)、[资源运行状况](../service-health/resource-health-overview.md)和 [Application Insights](api-management-howto-app-insights.md)                   | 外部和内部  |
| * / 25、587、25028                       | 出站           | TCP                | VIRTUAL_NETWORK/INTERNET            | 连接到 SMTP 中继以发送电子邮件                    | 外部和内部  |
| * / 6381 - 6383              | 入站和出站 | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 访问 Redis 服务以获取计算机之间的[缓存](api-management-caching-policies.md)策略         | 外部和内部  |
| * / 4290              | 入站和出站 | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 同步用于计算机之间的[速率限制](api-management-access-restriction-policies.md#LimitCallRateByKey)策略的计数器         | 外部和内部  |
| * / 6390                       | 入站            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure 基础结构负载均衡器                          | 外部和内部  |

#### <a name="stv1"></a>[stv1](#tab/stv1)

| 源/目标端口 | 方向          | 传输协议 |   [服务标记](../virtual-network/network-security-groups-overview.md#service-tags) <br> 源/目标   | 目的 (\*)                                                 | VNET 类型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | 入站            | TCP                | INTERNET/VIRTUAL_NETWORK            | 客户端与 API 管理的通信                      | 外部             |
| * / 3443                     | 入站            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 门户和 PowerShell 的管理终结点         | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | **与 Azure 存储的依赖关系**                             | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) 依赖关系                  | 外部和内部  |
| * / 1433                     | 出站           | TCP                | VIRTUAL_NETWORK / SQL                 | **访问 Azure SQL 终结点**                           | 外部和内部  |
| * / 5671, 5672, 443          | 出站           | TCP                | VIRTUAL_NETWORK / Event Hub            | [事件中心策略日志](api-management-howto-log-event-hubs.md)和监视代理的依赖项 | 外部和内部  |
| * / 445                      | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | 与适用于 [GIT](api-management-configuration-repository-git.md) 的 Azure 文件共享的依赖关系                      | 外部和内部  |
| * / 443, 12000                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 运行状况和监视扩展         | 外部和内部  |
| * / 1886、443                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 发布[诊断日志和指标](api-management-howto-use-azure-monitor.md)、[资源运行状况](../service-health/resource-health-overview.md)和 [Application Insights](api-management-howto-app-insights.md)                   | 外部和内部  |
| * / 25、587、25028                       | 出站           | TCP                | VIRTUAL_NETWORK/INTERNET            | 连接到 SMTP 中继以发送电子邮件                    | 外部和内部  |
| * / 6381 - 6383              | 入站和出站 | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 访问 Redis 服务以获取计算机之间的[缓存](api-management-caching-policies.md)策略         | 外部和内部  |
| * / 4290              | 入站和出站 | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 同步用于计算机之间的[速率限制](api-management-access-restriction-policies.md#LimitCallRateByKey)策略的计数器         | 外部和内部  |
| * / *                         | 入站            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure 基础结构负载均衡器                          | 外部和内部  |

---

### <a name="tls-functionality"></a>TLS 功能  
  若要启用 TLS/SSL 证书链生成及验证程序，API 管理服务需要与 `ocsp.msocsp.com`、`mscrl.microsoft.com` 和 `crl.microsoft.com` 建立出站网络连接。 但若上传到 API 管理的任何证书包含指向 CA 根的完整链，则不需要此依赖项。

### <a name="dns-access"></a>DNS 访问
  与 DNS 服务器通信时，`port 53` 需要进行出站访问。 如果 VPN 网关的另一端存在自定义 DNS 服务器，则该 DNS 服务器必须可从承载 API 管理的子网访问。

### <a name="metrics-and-health-monitoring"></a>指标和运行状况监视 

与 Azure 监视终结点建立出站网络连接时，这些终结点将在下列域下解析，并根据 AzureMonitor 服务标记进行表示，以与网络安全组配合使用。

|     Azure 环境 | 终结点                                                                                                                                                                                                                                                                                                                                           |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
| Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
| Azure 中国世纪互联     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |
  
### <a name="regional-service-tags"></a>区域服务标记

允许与存储、SQL 和事件中心服务标记建立出站连接的 NSG 规则可以使用与包含 API 管理实例的区域对应的这些标记的区域版本（例如，美国西部区域中 API 管理实例对应 Storage.WestUS）。 在多区域部署中，每个区域中的 NSG 应该允许流量进入该区域和主要区域的服务标记。

> [!IMPORTANT]
> 通过允许出站连接至美国西部区域的 blob 存储，即可在 VNET 中发布 API 管理实例的[开发人员门户](api-management-howto-developer-portal.md)。 例如，在 NSG 规则中使用 Storage.WestUS 服务标记。 当前若要发布任何 API 管理实例的开发人员门户，都需要连接至美国西部区域的 Blob 存储。

### <a name="smtp-relay"></a>SMTP 中继  
  
允许在主机 `smtpi-co1.msn.com`、`smtpi-ch1.msn.com`、`smtpi-db3.msn.com`、`smtpi-sin.msn.com` 和 `ies.global.microsoft.com` 下解析的 SMTP 中继的出站网络连接

> [!NOTE]
> 只有 API 管理中提供的 SMTP 中继可用于从你的实例发送电子邮件。

### <a name="developer-portal-captcha"></a>开发人员门户验证码 
允许在主机 `client.hip.live.com` 和 `partner.hip.live.com` 下解析的开发人员门户 CAPTCHA 的出站网络连接。

### <a name="azure-portal-diagnostics"></a>Azure 门户诊断  
  从 VNET 内部使用 API 管理扩展时，需要对 `port 443` 上的 `dc.services.visualstudio.com` 进行出站访问，以启用 Azure 门户的诊断日志流。 此访问有助于排查你在使用扩展时可能遭遇的问题。

### <a name="azure-load-balancer"></a>Azure 负载均衡器  
  无需允许来自 `Developer` SKU 的 `AZURE_LOAD_BALANCER` 服务标记的入站请求，因为其后只部署了一个计算单位。 但是，当缩放至更高的 SKU（如 `Premium`）时，来自 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 的入站将变得至关重要，因为负载均衡器发生运行状况探测故障将会导致部署失败。

### <a name="application-insights"></a>Application Insights  
  如果已针对 API 管理启用 [Azure Application Insights](api-management-howto-app-insights.md) 监视功能，则允许从 VNET 到[遥测终结点](../azure-monitor/app/ip-addresses.md#outgoing-ports)的出站连接。

### <a name="force-tunneling-traffic-to-on-premises-firewall-using-expressroute-or-network-virtual-appliance"></a>使用 ExpressRoute 或网络虚拟设备强制通过隧道将流量传输到本地防火墙  
  你通常可以通过配置及定义自己的默认路由 (0.0.0.0/0)，以使来自 API 管理委托子网的所有流量强制穿过本地防火墙或流入网络虚拟设备。 此流量流会中断与 Azure API 管理的连接，因为出站流量会在本地遭到阻止，或者已经过网络地址转换处理，变为一组无法再与各种 Azure 终结点配合工作的无法识别的地址。 不过，你可以通过多种方法解决此问题： 

  * 在部署以下服务的 API 管理所在的子网上启用[服务终结点][ServiceEndpoints]：
      * Azure SQL
      * Azure 存储
      * Azure 事件中心
      * Azure 密钥保管库（v2 平台） 
  
     通过直接从 API 管理子网为这些服务启用终结点，可以使用 Microsoft Azure 主干网络为服务流量提供最佳路由。 若在使用服务终结点时采用强制隧道 API 管理，则上述 Azure 服务流量不会强制通过隧道传输。 其他 API 管理服务依赖项流量将强制通过隧道传输且不能丢失； 一旦丢失，API 管理服务将无法正常工作。

  * 所有从 Internet 传输至 API 管理服务之管理终结点的控制平面流量均通过由 API 管理托管的一组特定入站 IP 进行路由。 当系统强制通过隧道传输流量时，响应将不会以对称方式映射回这些入站源 IP。 若要克服此限制，请将以下用户定义路由 ([UDR][UDRs]) 的目标设置为“Internet”，以将流量引回 Azure。 如需查找有关控制平面流量入站 IP 的设置信息，请参阅[控制平面 IP 地址](#control-plane-ip-addresses)中的记录。

  * 对于强制通过隧道传输流量的其他 API 管理服务依赖项，应解析主机名并访问相应终结点。 其中包括:
      - 指标和运行状况监视
      - Azure 门户诊断
      - SMTP 中继
      - 开发人员门户验证码

## <a name="routing"></a>路由

+ 负载均衡的公共 IP 地址 (VIP) 将会保留，以用于访问 VNET 外部的所有服务终结点和资源。
  + 如需查找负载均衡的公共 IP 地址，请前往 Azure 门户的“概述/概要”边栏选项卡。
+ 子网 IP 范围内的 IP 地址 (DIP) 用于访问 VNET 中的资源。

> [!NOTE]
> 在以下情况下，API 管理实例的 VIP 地址将会更改：
> * VNET 处于启用或禁用状态。 
> * API 管理从“外部”转为“内部”虚拟网络模式，反之亦然 。
> * 在实例所在的某个位置启用、更新或禁用[区域冗余](zone-redundancy.md)设置（仅适用于高级 SKU）。

## <a name="control-plane-ip-addresses"></a>控制平面 IP 地址

以下 IP 地址是按 Azure 环境划分的。 允许入站请求时，必须允许标记有“全局”(Global) 的 IP 地址以及特定于区域的 IP 地址 。

| **Azure 环境**|   **区域**|  IP 地址|
|-----------------|-------------------------|---------------|
| Azure Public| 美国中南部（全球）| 104.214.19.224|
| Azure Public| 美国中北部（全球）| 52.162.110.80|
| Azure Public| 澳大利亚中部| 20.37.52.67|
| Azure Public| 澳大利亚中部 2| 20.39.99.81|
| Azure Public| 澳大利亚东部| 20.40.125.155|
| Azure Public| 澳大利亚东南部| 20.40.160.107|
| Azure Public| 巴西南部| 191.233.24.179|
| Azure Public| 巴西东南部| 191.232.18.181|
| Azure Public| 加拿大中部| 52.139.20.34|
| Azure Public| 加拿大东部| 52.139.80.117|
| Azure Public| 印度中部| 13.71.49.1|
| Azure Public| 美国中部| 13.86.102.66|
| Azure Public| 美国中部 EUAP| 52.253.159.160|
| Azure Public| 东亚| 52.139.152.27|
| Azure Public| 美国东部| 52.224.186.99|
| Azure Public| 美国东部 2| 20.44.72.3|
| Azure Public| 美国东部 2 EUAP| 52.253.229.253|
| Azure Public| 法国中部| 40.66.60.111|
| Azure Public| 法国南部| 20.39.80.2|
| Azure Public| 德国北部| 51.116.0.0|
| Azure Public| 德国中西部| 51.116.96.0|
| Azure Public| 日本东部| 52.140.238.179|
| Azure Public| 日本西部| 40.81.185.8|
| Azure Public| Jio 印度中部| 20.192.234.160|
| Azure Public| Jio 印度西部| 20.193.202.160|
| Azure Public| 韩国中部| 40.82.157.167|
| Azure Public| 韩国南部| 40.80.232.185|
| Azure Public| 美国中北部| 40.81.47.216|
| Azure Public| 北欧| 52.142.95.35|
| Azure Public| 挪威东部| 51.120.2.185|
| Azure Public| 挪威西部| 51.120.130.134|
| Azure Public| 南非北部| 102.133.130.197|
| Azure Public| 南非西部| 102.133.0.79|
| Azure Public| 美国中南部| 20.188.77.119|
| Azure Public| 印度南部| 20.44.33.246|
| Azure Public| 东南亚| 40.90.185.46|
| Azure Public| 瑞士北部| 51.107.0.91|
| Azure Public| 瑞士西部| 51.107.96.8|
| Azure Public| 阿联酋中部| 20.37.81.41|
| Azure Public| 阿拉伯联合酋长国北部| 20.46.144.85|
| Azure Public| 英国南部| 51.145.56.125|
| Azure Public| 英国西部| 51.137.136.0|
| Azure Public| 美国中西部| 52.253.135.58|
| Azure Public| 西欧| 51.145.179.78|
| Azure Public| 印度西部| 40.81.89.24|
| Azure Public| 美国西部| 13.64.39.16|
| Azure Public| 美国西部 2| 51.143.127.203|
| Azure Public| 美国西部 3| 20.150.167.160|
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

## <a name="troubleshooting"></a>疑难解答
* 在子网中初次部署 API 管理服务失败 
  * 将虚拟机部署到相同子网。 
  * 连接到虚拟机，并验证与 Azure 订阅中以下其中一项资源的连接：
    * Azure 存储 Blob
    * Azure SQL 数据库
    * Azure 存储表
    * Azure 密钥保管库（适用于 [`stv2` 平台](compute-infrastructure.md)上托管的 API 管理实例）

  > [!IMPORTANT]
  > 验证连接后，先删除子网中的所有资源，然后将 API 管理部署到子网中（当 API 管理托管在 `stv1` 平台上时需要执行此操作）。

* 验证网络连接状态  
  * 在将 API 管理部署到子网后，应使用门户来检查实例与依赖项（如 Azure 存储）之间的连接性。 
  * 在门户的左侧菜单中的“部署和基础结构”下，选择“网络连接状态” 。

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="在门户中验证网络连接状态":::

  | 筛选器 | 说明 |
  | ----- | ----- |
  | **必需** | 选择“必需”，以查看 API 管理与必需 Azure 服务之间的连接性。 如果失败，则表示该实例无法执行核心操作来管理 API。 |
  | **可选** | 选择“可选”，以查看 API 管理与可选服务之间的连接性。 如果连接失败，仅表示特定功能（如 SMTP）将无法正常工作。 连接失败可能会导致使用和监视 API 管理实例以及提供所承诺 SLA 的能力下降。 |

  若要解决连接问题，请查看[网络配置设置](#network-configuration)并修复所需的网络设置。

* **增量更新**  
  更改网络时，请参阅 [NetworkStatus API](/rest/api/apimanagement/2020-12-01/network-status)，以验证 API 服务是否仍具有关键资源的访问权限。 连接状态应每 15 分钟更新一次。

* 资源导航链接  
  托管在 [`stv1` 计算平台](compute-infrastructure.md)上的 APIM 实例在部署到某个资源管理器 VNET 子网后，将通过创建资源导航链接来保留该子网。 如果子网已包含来自其他提供程序的资源，则部署将 **失败**。 同样，当删除 API 管理服务或将其移至其他子网时，资源导航链接也将会删除。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [使用 VPN 网关将虚拟网络连接到后端](../vpn-gateway/design.md#s2smulti)
* [从不同的部署模型连接虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [使用请求跟踪调试 API](api-management-howto-api-inspector.md)
* [虚拟网络常见问题解答](../virtual-network/virtual-networks-faq.md)
* [服务标记](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
