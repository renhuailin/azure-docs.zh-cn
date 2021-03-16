---
title: SQL Server Always On 可用性组概述
description: 本文介绍 Azure 虚拟机上的 SQL Server Always On 可用性组。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 8bbd56499c9b62248662fc5e8df0d5b3e1b672d4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504161"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM 上的 SQL Server 的 Always On 可用性组
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍 Azure 虚拟机 (VM) 上 SQL Server 的 Always On 可用性组。 

## <a name="overview"></a>概述

Azure 虚拟机上的 Always On 可用性组类似于[本地的 Always On 可用性组](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 但是，由于虚拟机托管在 Azure 中，还存在其他一些注意事项，例如 VM 冗余以及 Azure 网络上的流量路由。 

下图演示了 Azure VM 上的 SQL Server 可用性组：

![可用性组](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM 冗余 

要增加冗余和提高可用性，SQL Server VM 应位于相同的[可用性集](../../../virtual-machines/availability-set-overview.md)或不同的[可用性区域](../../../availability-zones/az-overview.md)中。

通过将一组 VM 放在同一个可用性集，可保护免受由设备故障导致的数据中心内的中断影响（可用性集中的 VM 不会共享资源），或免受更新导致的影响（可用性集中的 VM 不会同时更新）。 可用性区域可保护免受整个数据中心故障的影响，其中每个区域表示一个地区内的一组数据中心。  通过确保资源放在不同的可用性区域，任何数据中心级别的中断都没法导致所有 VM 脱机。

创建 Azure VM 时，必须选择配置可用性集还是配置可用性区域。  一个 Azure VM 不能同时加入这两者。


## <a name="connectivity"></a>连接 

在传统的本地部署中，客户端使用虚拟网络名称 (VNN) 连接到可用性组侦听器，该侦听器将流量路由到可用性组中相应的 SQL Server 副本。 但是，在 Azure 网络上路由流量需满足其他要求。 

通过 Azure VM 上的 SQL Server，配置一个[负载均衡器](availability-group-vnn-azure-load-balancer-configure.md)来将流量路由到可用性组侦听器。如果你在使用 SQL Server 2019 CU8 及更高版本，可配置一个[分布式网络名称 (DNN) 侦听器](availability-group-distributed-network-name-dnn-listener-configure.md)来取代传统的 VNN 可用性组侦听器。 

有关群集连接选项的更多详细信息，请参阅[将 HADR 连接路由到 Azure VM 上的 SQL Server](hadr-cluster-best-practices.md#connectivity)。 

### <a name="vnn-listener"></a>VNN 侦听器 

使用 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)将流量从侦听器路由到 Azure 网络上的传统可用性组虚拟网络名称 (VNN) 侦听器。 

负载均衡器保留 VNN 侦听器的 IP 地址。 如果有多个可用性组，则每个组都需要一个 VNN 侦听器。 一个负载均衡器可以支持多个侦听器。

若要开始，请参阅[配置负载均衡器](availability-group-vnn-azure-load-balancer-configure.md)。 

### <a name="dnn-listener"></a>DNN 侦听器

SQL Server 2019 CU8 引入了对分布式网络名称 (DNN) 侦听器的支持。 DNN 侦听器取代了传统的可用性组侦听程序，消除了使用 Azure 负载均衡器在 Azure 网络上路由流量的需求。 

DNN 侦听器是 Azure 中推荐的 HADR 连接解决方案，它可简化部署、减少维护量和成本，同时减少在出现故障时进行故障转移的时间。 

请使用 DNN 侦听器来替代现有的 VNN 侦听器，或者将它与现有的 VNN 侦听器一起使用，使你的可用性组有两个不同的连接点 - 一个使用 VNN 侦听器名称（如果非默认，则还使用 VNN 侦听器端口），而另一个使用 DNN 侦听器名称和端口。 如果客户想要避免负载均衡器故障转移延迟，但仍使用依赖于 VNN 侦听器的 SQL Server 功能，例如分布式可用性组、服务代理或文件流，则这非常有用。 若要了解详细信息，请参阅 [DNN 侦听器和 SQL Server 功能互操作性](availability-group-dnn-interoperability.md)

若要开始，请参阅[配置DNN 侦听器](availability-group-distributed-network-name-dnn-listener-configure.md)。


## <a name="deployment"></a>部署 

有多个选项来将可用性组部署到 Azure VM 上的 SQL Server，某些选项的自动化程度比其他的高。 

下表提供了可用选项的比较：

| | Azure 门户 | Azure CLI / PowerShell | 快速入门模板 | 手动 |
|---------|---------|---------|---------|---------|
|**SQL Server 版本** |2016+ |2016+|2016+|2012+|
|**SQL Server 版本** |Enterprise |Enterprise |Enterprise |Enterprise、Standard|
|**Windows Server 版本**| 2016+ | 2016+ | 2016+ | All|
|**为你创建群集**|是|是 | 是 |否|
|**为你创建可用性组** |是 |否|否|否|
|**分开创建侦听器和负载均衡器** |否|否|否|是|
|**能否使用此方法创建 DNN 侦听器？**|否|否|否|是|
|**WSFC 仲裁配置**|云见证|云见证|云见证|All|
|**具有多个区域的 DR** |否|否|否|是|
|**多子网支持** |是|是|是|是|
|**支持现有 AD**|是|是|是|是|
|**在同一区域中具有多个地区的 DR**|是|是|是|是|
|**不带 AD 的分布式 AG**|否|否|否|是|
|**不带群集的分布式 AG** |否|否|否|是|

有关详细信息，请参阅 [Azure 门户](availability-group-azure-portal-configure.md)、[Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)、[快速启动模板](availability-group-quickstart-template-configure.md)和[手册](availability-group-manually-configure-prerequisites-tutorial.md)。

## <a name="considerations"></a>注意事项 

在 Azure IaaS VM 来宾故障转移群集上，建议为每个服务器（群集节点）设置一个 NIC，并且只使用一个子网。 Azure 网络具有物理冗余，这使得在 Azure IaaS VM 来宾群集上不需要额外的 NIC 和子网。 虽然群集验证报告将发出警告，指出节点只能在单个网络上访问，但在 Azure IaaS VM 来宾故障转移群集上可以安全地忽略此警告。 

## <a name="next-steps"></a>后续步骤

查看 [HADR 最佳做法](hadr-cluster-best-practices.md)，然后使用 [Azure 门户](availability-group-azure-portal-configure.md)、[Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)、[快速入门模板](availability-group-quickstart-template-configure.md)或通过[手动](availability-group-manually-configure-prerequisites-tutorial.md)操作开始部署可用性组。

或者，可部署[无群集的可用性组](availability-group-clusterless-workgroup-configure.md)或者[多个区域](availability-group-manually-configure-multiple-regions.md)中的可用性组。
