---
title: Azure 网络体系结构文档
description: 了解 Azure 网络服务提供的参考体系结构文档。
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 9b608312d66e6a3e7455c4577ea4644b33e4e82e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079742"
---
# <a name="azure-networking-architecture-documentation"></a>Azure 网络体系结构文档

本文提供体系结构指南的相关信息，这些指南可帮助你探索 Azure 中可用于构建应用程序的不同网络服务。

## <a name="networking-overview"></a>网络概述

下表包括一些文章，这些文章从网络角度概述性介绍了 Azure 中的虚拟数据中心和中心辐射型拓扑。

|标题 |说明  |
|---------|---------|
|[虚拟数据中心](/azure/architecture/vdc/networking-virtual-datacenter)   | 提供 Azure 中虚拟数据中心的网络透视图。       |
|[中心辐射型拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |概述性介绍 Azure 中的中心辐射型网络拓扑，以及有关订阅限制和多中心的信息。          |

## <a name="connect-to-azure-resources"></a>连接到 Azure 资源

下表包含有关 Azure 网络服务的文章，这些服务提供 Azure 资源之间的连接、本地网络到 Azure 资源的连接以及 Azure 中分支与分支的连接。

|标题 |说明  |
|---------|---------|
|[向对等虚拟网络添加 IP 地址空间](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | 提供可帮助向对等虚拟网络添加 IP 空间的脚本。        |
|[使用 Azure 网络适配器连接独立服务器](/azure/architecture/hybrid/azure-network-adapter)   | 演示如何使用通过 Windows Admin Center 部署的 Azure 网络适配器将本地独立服务器连接到 Microsoft Azure 虚拟网络。        |
|[在虚拟网络对等互连和 VPN 网关之间选择](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | 比较两种连接 Azure 中虚拟网络的方式：虚拟网络对等互连和 VPN 网关。        |
|[将本地网络连接到 Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | 比较用于将本地网络连接到 Azure 虚拟网络 (VNet) 的选项。 每个选项都有可用的更详细的参考体系结构。        |
|[带有 Azure 虚拟 WAN 的 SD-WAN 连接体系结构](../../virtual-wan/sd-wan-connectivity-architecture.md)|介绍将专用的软件定义的 WAN (SD-WAN) 与 Azure 虚拟 WAN 互连的不同连接选项。|

## <a name="deploy-highly-available-applications"></a>部署具有高可用性的应用程序

下表包含的文章介绍如何结合使用 Azure 网络服务来部署应用程序以实现高可用性。

|标题 |说明  |
|---------|---------|
|[多区域 N 层应用程序](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)  | 介绍一个使用流量管理器将传入请求路由到主要区域的多区域 N 层应用程序，如果主要区域变得不可用，流量管理器将故障转移到次要区域。      |
| [Azure 上的多租户 SaaS](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   使用结合应用 Front Door 和应用程序网关的多租户解决方案。  Front Door 有助于跨区域对流量进行负载均衡，应用程序网关将应用程序内部的流量进行负载均衡并路由到满足客户端业务需求的各种服务。  |
| [可实现高可用性和灾难恢复的多层 Web 应用程序](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      部署为实现高可用性和灾难恢复而构建的可复原的多层应用程序。 如果主要区域变得不可用，则流量管理器将故障转移到次要区域。  |
|[IaaS：包含关系数据库的 Web 应用程序](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   介绍如何使用跨多个区域分布的资源来提供高可用性体系结构，以托管基础结构即服务 (IaaS) Web 应用程序和 SQL Server 数据库。     |
|[使用低成本无服务器 Azure 服务实时共享位置](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   使用 Azure Front Door 为应用程序提供与部署到单个区域相比更高的可用性。 如果区域性故障影响主要区域，可以使用 Front Door 来故障转移到次要区域。      |
|[高度可用的网络虚拟设备](/azure/architecture/reference-architectures/dmz/nva-ha)     | 展示了如何在 Azure 中部署一组网络虚拟设备 (NVA) 以实现高可用性。        |

## <a name="secure-your-network-resources"></a>保护你的网络资源

下表提供介绍如何使用 Azure 网络服务保护网络资源的文章。

|标题 |说明  |
|---------|---------|
|[网络安全最佳实践](../../security/fundamentals/network-best-practices.md) |介绍一系列 Azure 最佳做法以增强网络安全。         |
[Azure 防火墙体系结构指南](/azure/architecture/example-scenario/firewalls/) | 提供一种使用第三方虚拟设备在 Azure 中设计高度可用的防火墙的结构化方法。        |
|[实现安全的混合网络](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | 介绍在本地网络和 Azure 虚拟网络之间实现 DMZ （也称为外围网络）的体系结构。 所有入站和出站流量均通过 Azure 防火墙。        |
|[通过网格级分段来保护和管理工作负载](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | 从网络角度介绍用于在 Azure 中组织工作负载的三种常见模式。   其中的每种模式都提供不同类型的隔离和连接。      |
|[面向虚拟网络的防火墙和应用程序网关](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | 介绍 Azure 防火墙和 Azure 应用程序网关等 Azure 虚拟网络安全服务，每项服务的使用时机，以及结合使用两者的网络设计选项。      |

## <a name="next-steps"></a>后续步骤

了解 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)。
