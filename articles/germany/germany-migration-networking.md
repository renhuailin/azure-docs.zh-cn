---
title: 将 Azure 网络资源从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 网络资源从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1e8cb8dd4d167b1671edb8367beb5514104938ac
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "117029192"
---
# <a name="migrate-network-resources-to-global-azure"></a>将网络资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

大多数网络服务并不支持从 Azure 德国迁移到全球 Azure。 但是，可以使用站点到站点 VPN 连接这两个云环境中的网络。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在云之间设置站点到站点 VPN 的步骤类似于在本地网络与 Azure 之间部署站点到站点 VPN 的步骤。 在这两个云中定义一个网关，然后告知 VPN 要如何相互通信。 [在 Azure 门户中创建站点到站点连接](../vpn-gateway/tutorial-site-to-site-portal.md)介绍了部署站点到站点 VPN 所要完成的步骤。 下面是这些步骤的摘要：

1. 定义虚拟网络。
1. 定义地址空间。
1. 定义子网。
1. 定义网关子网。
1. 定义虚拟网络的网关。
1. 定义本地网络（本地 VPN 设备）的网关。
1. 配置本地 VPN 设备。
1. 构建连接。

若要在全球 Azure 与 Azure 德国之间连接虚拟网络，请执行以下操作：

1. 在全球 Azure 中完成上述过程的步骤 1-5。
1. 在 Azure 德国中完成步骤 1-5。
1. 在全球 Azure 中完成步骤 6：
   - 在 Azure 德国中输入 VPN 网关的公共 IP 地址。
1. 在 Azure 德国中完成步骤 6：
   - 在全球 Azure 中输入 VPN 网关的公共 IP 地址。
1. 跳过步骤 7。
1. 完成步骤 8。

## <a name="virtual-networks"></a>虚拟网络

目前不支持将虚拟网络从 Azure 德国迁移到全球 Azure。 建议在目标区域中创建新的虚拟网络，并将资源迁移到这些虚拟网络。

参考信息：

- 通过完成 [Azure 虚拟网络教程](../virtual-network/index.yml)学习更多新知识。
- 查看[虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
- 了解如何[规划虚拟网络](../virtual-network/virtual-network-vnet-plan-design-arm.md)。

## <a name="network-security-groups"></a>网络安全组

目前不支持将网络安全组从 Azure 德国迁移到全球 Azure。 建议在目标区域中创建新的网络安全组，并将网络安全组规则应用于新的应用程序环境。

通过门户或运行以下 PowerShell 命令获取任何网络安全组的当前配置：

```powershell
$nsg=Get-AzNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```

参考信息：

- 学习更多[有关网络安全组的新知识](../virtual-network/network-security-groups-overview.md#network-security-groups)。
- 查看[网络安全性概述](../virtual-network/network-security-groups-overview.md)
- 了解如何[管理网络安全组](../virtual-network/manage-network-security-group.md)。

## <a name="expressroute"></a>ExpressRoute

目前不支持将 Azure ExpressRoute 实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建新的 ExpressRoute 线路和新的 ExpressRoute 网关。

参考信息：

- 通过完成 [ExpressRoute 教程](../expressroute/index.yml)学习更多新知识。
- 了解如何[创建新的 ExpressRoute 网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
- 了解 [ExpressRoute 位置和服务提供商](../expressroute/expressroute-locations.md)。
- 了解 [ExpressRoute 的虚拟网络网关](../expressroute/expressroute-about-virtual-network-gateways.md)。

## <a name="vpn-gateway"></a>VPN 网关

目前不支持将 Azure VPN 网关实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建和配置 VPN 网关的新实例。

可以使用门户或 PowerShell 收集有关当前 VPN 网关配置的信息。 在 PowerShell 中，使用一组以 `Get-AzVirtualNetworkGateway*` 开头的 cmdlet。

确保更新本地配置。 此外，在更新 Azure 网络环境后，删除旧 IP 地址范围的所有现有规则。

参考信息：

- 通过完成 [VPN 网关教程](../vpn-gateway/index.yml)学习更多新知识。
- 了解如何[创建站点到站点连接](../vpn-gateway/tutorial-site-to-site-portal.md)。
- 查看 [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell cmdlet。
- 阅读博客文章[创建站点到站点连接](/archive/blogs/ralfwi/connecting-clouds)。
 
## <a name="application-gateway"></a>应用程序网关

目前不支持将 Azure 应用程序网关实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建和配置新网关。

可以使用门户或 PowerShell 收集有关当前网关配置的信息。 在 PowerShell 中，使用一组以 `Get-AzApplicationGateway*` 开头的 cmdlet。

参考信息：

- 通过完成[应用程序网关教程](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)学习更多新知识。
- 了解如何[创建应用程序网关](../application-gateway/quick-create-portal.md)。
- 查看 [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell cmdlet。

## <a name="dns"></a>DNS

若要将 Azure DNS 配置从 Azure 德国迁移到全球 Azure，请导出 DNS 区域文件，然后将其导入到新订阅下。 目前，只能使用 Azure CLI 导出区域文件。

在 Azure 德国中登录到源订阅后，将 Azure CLI 配置为使用 Azure 资源管理器模式。 运行以下命令导出区域：

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

示例：

```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```

此命令调用 Azure DNS 服务来导出 `myresourcegroup` 资源组中的区域 `contoso.com`。 输出作为 BIND 兼容的区域文件存储在当前文件夹下的 contoso.com.txt 中。

导出完成后，请删除区域文件中的 NS 记录。 将为新的区域和订阅创建新的 NS 记录。

现在，请登录到目标环境，创建新的资源组（或选择现有的资源组），然后导入区域文件：

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

导入区域后，必须运行以下命令来验证区域：

```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```

验证完成后，请联系域注册机构并重新委托 NS 记录。 若要获取 NS 记录信息，请运行以下命令：

```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```

参考信息：

- 通过完成 [Azure DNS 教程](../dns/index.yml)学习更多新知识。
- 查看 [Azure DNS 概述](../dns/dns-overview.md)。
- 详细了解 [Azure DNS 导入和导出](../dns/dns-import-export.md)。

## <a name="network-watcher"></a>网络观察程序

目前不支持将 Azure 网络观察程序实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建和配置新的网络观察程序实例。 然后，比较新旧环境之间的结果。

参考信息：

- 通过完成[网络观察程序教程](../network-watcher/index.yml)学习更多新知识。
- 查看[网络观察程序概述](../network-watcher/network-watcher-monitoring-overview.md)。
- 详细了解[网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)。
- 了解[连接监视器](../network-watcher/connection-monitor.md)。

## <a name="traffic-manager"></a>流量管理器

Azure 流量管理器可帮助你顺利完成迁移。 但是，不能将在 Azure 德国创建的流量管理器配置文件迁移到全球 Azure。 （在迁移过程中，你会将流量管理器终结点迁移到目标环境，因此无论如何都需要更新流量管理器配置文件。）

当流量管理器仍在源环境中运行时，你可以使用它在目标环境中定义其他终结点。 当流量管理器在新环境中运行时，你仍然可以定义尚未在源环境中迁移的终结点。 此方案称为[蓝绿方案](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)。 此方案涉及以下步骤：

1. 在全球 Azure 中创建新的流量管理器配置文件。
1. 在 Azure 德国中定义终结点。
1. 将 DNS CNAME 记录更改为新的流量管理器配置文件。
1. 关闭旧的流量管理器配置文件。
1. 迁移和配置终结点。 对于 Azure 德国的每个终结点：
   1. 将终结点迁移到全球 Azure。
   1. 更改流量管理器配置文件以使用新的终结点。

参考信息：

- 通过完成[流量管理器教程](../traffic-manager/index.yml)学习更多新知识。
- 查看[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。
- 了解如何[创建流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)。

## <a name="load-balancer"></a>负载均衡器

目前不支持将 Azure 负载均衡器实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建并配置新的负载均衡器。

参考信息：

- 通过完成[负载均衡器教程](../load-balancer/index.yml)学习更多新知识。
- 查看[负载均衡器概述](../load-balancer/load-balancer-overview.md)。
- 了解如何[创建新的负载均衡器](../load-balancer/quickstart-load-balancer-standard-public-portal.md)。

## <a name="next-steps"></a>后续步骤

了解用于迁移以下服务类别中的资源的工具、方法和建议：

- [计算](./germany-migration-compute.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)
