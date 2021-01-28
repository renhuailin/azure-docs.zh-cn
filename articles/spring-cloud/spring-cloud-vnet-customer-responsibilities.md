---
title: 在 vnet 中运行 Azure 春季 Cloud 的客户责任
description: 本文介绍了在 vnet 中运行 Azure 春季 Cloud 的客户责任。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5ecf9e49887eb584269f724d5199cbfb014351e0
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986847"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>在 VNET 中运行 Azure 春季云中的客户责任
本文档包含有关在虚拟网络中使用 Azure 春季云的规格。

在虚拟网络中部署 Azure 春季云后，它将对虚拟网络外部的服务进行出站依赖。 出于管理和操作目的，Azure 春季云必须访问特定端口和完全限定的域名 (Fqdn) 。 需要这些终结点才能与 Azure 春季云管理平面通信，并下载并安装 core Kubernetes 群集组件和安全更新。

默认情况下，Azure 春季云具有不受限制的出站 (出口) internet 访问。 此级别的网络访问允许运行的应用程序根据需要访问外部资源。 如果你想要限制传出流量，则必须能够访问有限数量的端口和地址，才能执行维护任务。 保护出站地址的最简单解决方案是使用防火墙设备，该设备可以基于域名控制出站流量。 例如，Azure 防火墙可以根据目标的 FQDN 限制出站 HTTP 和 HTTPS 流量。 还可配置首选的防火墙和安全规则，以允许所需的端口和地址。

## <a name="azure-spring-cloud-resource-requirements"></a>Azure 春季云资源需求 

下面列出了 Azure 春季云服务的资源要求。 一般要求不应修改由 Azure 春季云和基础网络资源创建的资源组。
- 不要修改 Azure 春季云中创建和拥有的资源组。
  - 默认情况下，这些资源组命名为 "ap-svc-rt_ [服务实例名称]_[region] * 和 ap_[服务实例名称] _ [region] *"。
- 不要修改 Azure 春季 Cloud 使用的子网。
- 不要在同一子网中创建多个 Azure 春季云服务实例。
- 使用防火墙控制流量时， *不要* 将以下出口流量阻止到运行、维护和支持服务实例的 Azure 春季云组件。

## <a name="azure-spring-cloud-network-requirements"></a>Azure 春季云网络要求

  | 目标终结点 | 端口 | 用途 | 备注 |
  |------|------|------|
  | *： 1194 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud：1194 | UDP：1194 | 底层 Kubernetes 群集管理。 | |
  | *： 443 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud：443 | TCP：443 | Azure 春季云服务管理。 | 在 "networkProfile" 部分下的资源负载中，可以了解服务实例 "requiredTraffics" 的信息。 |
  | *： 9000 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud：9000 | TCP：9000 | 底层 Kubernetes 群集管理。 |
  | *： 123 *或* ntp.ubuntu.com:123 | UDP:123 | Linux 节点上的 NTP 时间同步。 | |
  | *. azure.io:443 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureContainerRegistry：443 | TCP：443 | Azure 容器注册表。 | 可以通过 [在虚拟网络中](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)启用 *Azure 容器注册表* 服务终结点来替换。 |
  | *. core.windows.net:443 和 *. core.windows.net:445 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -Storage：443和 storage：445 | TCP：443，TCP：445 | Azure 文件存储 | 可以替换为虚拟网络中的 *Azure 存储*[服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)。 |
  | *. servicebus.windows.net:443 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ：443 | TCP：443 | Azure 事件中心。 | 可以替换为虚拟网络中的 *Azure 事件中心*[服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)。 |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Azure 春季 Cloud FQDN 要求/应用程序规则

Azure 防火墙提供完全限定的域名 (FQDN) 标记 **AzureKubernetesService** 以简化以下配置。

  | 目标 FQDN | 端口 | 用途 |
  |------|------|------|
  | *. azmk8s.io | HTTPS:443 | 底层 Kubernetes 群集管理。 |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft 容器注册表 (MCR) 。 |
  | *.cdn.mscr.io | HTTPS:443 | Azure CDN 支持的 MCR 存储。 |
  | *.data.mcr.microsoft.com | HTTPS:443 | Azure CDN 支持的 MCR 存储。 |
  | <i>management.azure.com</i> | HTTPS:443 | 底层 Kubernetes 群集管理。 |
  | <i>login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory 身份验证。 |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Microsoft 包存储库。 |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | 安装所需的二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |
  | *mscrl.microsoft.com* | HTTPS：80 | 必需的 Microsoft 证书链路径。 |
  | *crl.microsoft.com* | HTTPS：80 | 必需的 Microsoft 证书链路径。 |
  | *crl3.digicert.com* | HTTPS：80 | 第三方 SSL 证书链路径。 |

## <a name="see-also"></a>另请参阅
* [在专用网络中访问应用程序](spring-cloud-access-app-virtual-network.md)
* [使用应用程序网关和 Azure 防火墙公开应用程序](spring-cloud-expose-apps-gateway-azure-firewall.md) 