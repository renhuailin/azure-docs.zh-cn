---
title: 在 vnet 中运行 Azure Spring Cloud 的客户责任
description: 本文介绍了在 vnet 中运行 Azure Spring Cloud 的客户责任。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java
ms.openlocfilehash: fbcf185aa48e7fc4333b128f21b4664106043064
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256424"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>在 VNET 中运行 Azure Spring Cloud 的客户责任

本文档包括有关在虚拟网络中使用 Azure Spring Cloud 时的规范。

在 Azure Spring Cloud 部署到虚拟网络后，它对虚拟网络外部的服务有出站依赖关系。 出于管理和运营目的，Azure Spring Cloud 必须访问特定的端口和完全限定的域名 (FQDN)。 与 Azure Spring Cloud 管理平面进行通信时，或者下载并安装核心 Kubernetes 群集组件和安全更新时，都需要使用这些终结点。

默认情况下，Azure Spring Cloud 具有不受限制的出站（出口）Internet 访问权限。 此级别的网络访问权限允许你运行的应用程序根据需要访问外部资源。 如果你希望限制出口流量，则有限数量的端口和地址必须可供访问，以便执行维护任务。 保护出站地址的最简单解决方案是使用可基于域名控制出站流量的防火墙设备。 例如，Azure 防火墙可以根据目标的 FQDN 限制出站 HTTP 和 HTTPS 流量。 还可配置首选的防火墙和安全规则，以允许所需的端口和地址。

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud 资源要求

下面列出了 Azure Spring Cloud 服务的资源要求。 作为一般要求，你不应该修改由 Azure Spring Cloud 创建的资源组，也不应该修改基础网络资源。

- 不要修改由 Azure Spring Cloud 创建并拥有的资源组。
  - 默认情况下，这些资源组被命名为 ap-svc-rt_[SERVICE-INSTANCE-NAME] _[REGION]* 和 ap_[SERVICE-INSTANCE-NAME]_[REGION]*。
  - 请勿阻止 Azure Spring Cloud 更新这些资源组中的资源。
- 不要修改 Azure Spring Cloud 使用的子网。
- 不要在同一子网中创建多个 Azure Spring Cloud 服务实例。
- 使用防火墙来控制流量时，不要阻止发往运行、维护服务实例以及为其提供支持的 Azure Spring Cloud 组件的以下出口流量。

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud 网络要求

| 目标终结点                                         | 端口             | 用途                                       | 注意                                                         |
| ------------------------------------------------------------ | ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| *:1194 *或* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194         | 基础 Kubernetes 群集管理。 |                                                              |
| *:443 *或* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443          | Azure Spring Cloud 服务管理。    | 在“networkProfile”部分下的资源有效负载中，可以了解服务实例“requiredTraffics”的信息。 |
| *:9000 *或* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000         | 基础 Kubernetes 群集管理。 |                                                              |
| *:123 *或* ntp.ubuntu.com:123                                | UDP:123          | Linux 节点上的 NTP 时间同步。  |                                                              |
| *.azure.io:443 *或* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443          | Azure 容器注册表。                 | 可以通过[在虚拟网络中启用 Azure 容器注册表服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)来进行替换。 |
| *.core.windows.net:443 和 *.core.windows.net:445 或 [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Storage:443 和 Storage:445 | TCP:443、TCP:445 | Azure 文件                        | 可以通过[在虚拟网络中启用 Azure 存储服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)来进行替换。 |
| *.servicebus.windows.net:443 或 [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443          | Azure 事件中心。                          | 可以通过[在虚拟网络中启用 Azure 事件中心服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)来进行替换。 |


## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud FQDN 要求/应用程序规则

Azure 防火墙提供 FQDN 标记 AzureKubernetesService 来简化以下配置：

| 目标 FQDN                  | 端口      | 用途                                                          |
| --------------------------------- | --------- | ------------------------------------------------------------ |
| *.azmk8s.io                       | HTTPS:443 | 基础 Kubernetes 群集管理。                    |
| <i>mcr.microsoft.com</i>          | HTTPS:443 | Microsoft 容器注册表 (MCR)。                          |
| *.cdn.mscr.io                     | HTTPS:443 | 由 Azure CDN 提供支持的 MCR 存储。                         |
| *.data.mcr.microsoft.com          | HTTPS:443 | 由 Azure CDN 提供支持的 MCR 存储。                         |
| <i>management.azure.com</i>       | HTTPS:443 | 基础 Kubernetes 群集管理。                    |
| *login.microsoftonline.com<i></i> | HTTPS:443 | Azure Active Directory 身份验证。                       |
| <i>*login.microsoft.com</i>       | HTTPS:443 | Azure Active Directory 身份验证。                       |
| <i>packages.microsoft.com</i>     | HTTPS:443 | Microsoft 包存储库。                               |
| <i>acs-mirror.azureedge.net</i>   | HTTPS:443 | 安装所需二进制文件（如 kubenet 和 Azure CNI）时所需的存储库。 |
| *mscrl.microsoft.com*             | HTTPS:80  | 所需的 Microsoft 证书链路径。                  |
| *crl.microsoft.com*               | HTTPS:80  | 所需的 Microsoft 证书链路径。                  |
| *crl3.digicert.com*               | HTTPS:80  | 第三方 SSL 证书链路径。                       |

## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>适用于第三方应用程序性能管理的 Azure Spring Cloud 可选 FQDN

Azure 防火墙提供 FQDN 标记 AzureKubernetesService 来简化以下配置：

| 目标 FQDN            | 端口       | 用途                                                          |
| --------------------------- | ---------- | ------------------------------------------------------------ |
| collector*.newrelic.com     | TCP:443/80 | 美国区域的 New Relic APM 代理所需的网络，另请参阅 [APM 代理网络](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)。 |
| collector*.eu01.nr-data.net | TCP:443/80 | 欧洲区域的 New Relic APM 代理所需的网络，另请参阅 [APM 代理网络](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)。 |
| *.live.dynatrace.com        | TCP:443    | Dynatrace APM 代理所需的网络。                    |
| *.live.ruxit.com            | TCP:443    | Dynatrace APM 代理所需的网络。                    |

## <a name="see-also"></a>另请参阅

* [在专用网络中访问应用程序](access-app-virtual-network.md)
* [使用应用程序网关和 Azure 防火墙公开应用](expose-apps-gateway-azure-firewall.md)
