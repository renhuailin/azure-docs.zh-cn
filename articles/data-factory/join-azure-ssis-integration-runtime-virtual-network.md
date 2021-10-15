---
title: 将 Azure-SSIS 集成运行时加入虚拟网络
description: 了解如何将 Azure-SSIS Integration Runtime 加入 Azure 虚拟网络。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 023b6898b5ba7d7d5b457f1e0f9c9051d74a1126
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399767"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>将 Azure-SSIS 集成运行时加入虚拟网络

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 数据工厂中使用 SQL Server Integration Services (SSIS) 时，对于以下情况，应将 Azure SSIS 集成运行时 (IR) 加入 Azure 虚拟网络：

- 希望在不配置或管理用作代理的自承载 IR 的情况下，从 Azure-SSIS IR 中运行的 SSIS 包连接到本地数据存储。 

- 想要在具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库中或具有专用终结点的 SQL 托管实例中托管 SSIS 目录数据库 (SSISDB)。 

- 想要从 Azure-SSIS IR 中运行的 SSIS 包连接到配置了虚拟网络服务终结点的 Azure 资源。

- 想要从 Azure-SSIS IR 中运行的 SSIS 包连接到配置了 IP 防火墙规则的数据存储/资源。

使用数据工厂可将 Azure-SSIS IR 加入通过经典部署模型或 Azure 资源管理器部署模型创建的虚拟网络。

> [!IMPORTANT]
> 经典虚拟网络即将弃用，请改用 Azure 资源管理器虚拟网络。  如果已使用经典虚拟网络，请尽快切换到 Azure 资源管理器虚拟网络。

[配置 Azure SQL Server Integration Services (SSIS) 集成运行时 (IR) 以加入虚拟网络](tutorial-deploy-ssis-virtual-network.md)教程提供了通过 Azure 门户完成的最少量步骤。 本文对该教程的内容做了补充，介绍如何执行以下所有可选任务：

- 如果使用的是虚拟网络（经典）。
- 如果使用自己的 Azure-SSIS IR 公共 IP 地址。
- 如果使用自己的域名系统 (DNS) 服务器。
- 如果在子网中使用网络安全组 (NSG)。
- 如果使用 Azure ExpressRoute 或用户定义的路由 (UDR)。
- 如果使用自定义的 Azure-SSIS IR。
- 如果使用 Azure PowerShell 预配。

## <a name="access-to-on-premises-data-stores"></a>访问本地数据存储

如果 SSIS 包访问本地数据存储，则你可以将 Azure-SSIS IR 加入已连接到本地网络的虚拟网络。 或者，可以将自承载 IR 配置为 Azure-SSIS IR 的代理并对其进行管理。 有关详细信息，请参阅[配置自承载 IR 作为 Azure-SSIS IR 的代理](./self-hosted-integration-runtime-proxy-ssis.md)。 

将 Azure-SSIS IR 加入到虚拟网络时，请记住以下要点： 

- 如果没有任何虚拟网络连接到本地网络，请先创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)。 然后，配置从该虚拟网络到本地网络的站点到站点 [VPN 网关连接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 连接。 

- 如果已有一个 Azure 资源管理器虚拟网络连接到 Azure-SSIS 集成运行时所在同一位置中的本地网络，可将 IR 加入该虚拟网络。 

- 如果已有一个经典虚拟网络连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)。 然后，配置[经典到 Azure 资源管理器虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)连接。 
 
- 如果已有一个 Azure 资源管理器虚拟网络连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以先创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)。 然后，配置 [Azure 资源管理器到 Azure 资源管理器虚拟网络](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)连接。 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>在 SQL 数据库中承载 SSIS 目录

如果将 SSIS 目录承载在具有虚拟网络服务终结点的 Azure SQL 数据库中，请确保将 Azure-SSIS IR 加入到同一虚拟网络和子网中。

如果你在具有专用终结点的 SQL 托管实例中托管 SSIS 目录，请确保将 Azure-SSIS IR 加入同一虚拟网络，但让其位于与托管实例不同的子网中。 若要将 Azure-SSIS IR 加入与 SQL 托管实例不同的虚拟网络，我们建议建立虚拟网络对等互连（限于相同的区域）或者虚拟网络之间的连接。 有关详细信息，请参阅[将应用程序连接到 Azure SQL 托管实例](../azure-sql/managed-instance/connect-application-instance.md)。

## <a name="access-to-azure-services"></a>对 Azure 服务的访问权限

如果 SSIS 包访问支持[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的 Azure 资源，并且你想要从 Azure-SSIS IR 保护对这些资源的访问，则可以将 Azure-SSIS IR 加入到配置了虚拟网络服务终结点的虚拟网络子网，然后将虚拟网络规则添加到相关的 Azure 资源，以允许来自同一子网的访问。

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>访问 IP 防火墙规则保护的数据源

如果 SSIS 包访问仅允许特定静态公共 IP 地址的数据存储/资源，并且你想要从 Azure-SSIS IR 保护对这些资源的访问，则可以将[公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md)与 Azure-SSIS IR 相关联，同时将其加入虚拟网络，然后将 IP 防火墙规则添加到相关的 Azure 资源，以允许来自这些 IP 地址的访问。 可通过两种替代方法来实现此目的： 

- 创建 Azure-SSIS IR 时，可以自带公共 IP 地址，并通过 [Azure 数据工厂工作室 UI](join-azure-ssis-integration-runtime-virtual-network-ui.md) 或 [Azure PowerShell SDK](join-azure-ssis-integration-runtime-virtual-network-powershell.md) 指定。 只有 Azure-SSIS IR 的出站 Internet 连接将使用你提供的公共 IP 地址，子网中的其他设备将不使用它们。
- 你还可以为 Azure-SSIS IR 将加入的子网设置[虚拟网络 NAT](../virtual-network/nat-gateway/nat-overview.md)，并且该子网中的所有出站连接都将使用你指定的公共 IP 地址。

在所有情况下，都只能通过 Azure 资源管理器部署模型部署虚拟网络。

## <a name="next-steps"></a>后续步骤

- [Azure-SSIS 集成运行时虚拟网络配置详细信息](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [使用 Azure 数据工厂工作室 UI 将 Azure-SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [使用 Azure PowerShell 将 Azure-SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

有关 Azure-SSIS IR 的详细信息，请参阅以下文章： 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关 IR（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](./tutorial-deploy-ssis-packages-azure.md) 中的分步说明创建一个。 此教程提供有关创建 Azure-SSIS IR 的分步说明。 它使用 Azure SQL 数据库来托管 SSIS 目录。 
- [创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。 此文对本教程的内容做了扩充。 其中说明了如何使用具有虚拟网络服务终结点的 Azure SQL 数据库或虚拟网络中的 SQL 托管实例来托管 SSIS 目录。 此外，它介绍了如何将 Azure-SSIS IR 加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何获取有关 Azure-SSIS IR 的信息。 其中提供了返回的信息的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加节点来扩展 Azure-SSIS IR。
