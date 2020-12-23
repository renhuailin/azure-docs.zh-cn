---
title: Azure Cache for Redis 网络隔离选项
description: 在本文中，你将了解如何根据需要确定最佳网络隔离解决方案。 我们将深入了解 Azure 专用链接、Azure 虚拟网络 (VNet) 注入和 Azure 防火墙规则及其优点和局限性。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621512"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure Cache for Redis 网络隔离选项 
在本文中，你将了解如何根据需要确定最佳网络隔离解决方案。 我们将深入了解 Azure 专用链接、Azure 虚拟网络 (VNet) 注入和 Azure 防火墙规则及其优点和局限性。  

## <a name="azure-private-link-public-preview"></a>Azure 专用链接（公共预览版） 
Azure 专用链接提供从虚拟网络到 Azure PaaS 服务的专用连接。 它简化了网络体系结构，并通过消除数据在公共 Internet 上的暴露来保护 Azure 中终结点之间的连接。 

### <a name="advantages"></a>优点
* 在基本、标准和高级 Azure Cache for Redis 实例上受支持。 
* 通过使用 [Azure 专用链接](../private-link/private-link-overview.md)，你可以通过专用终结点从虚拟网络连接到 Azure Cache 实例，该终结点在虚拟网络中的子网内分配有一个专用 IP 地址。 通过此方法，可以从 VNet 内部和公共位置访问缓存实例。  
* 创建专用终结点后，可以通过 `publicNetworkAccess` 标志限制对公用网络的访问。 默认情况下，此标志设置为 `Disabled` ，这将只允许专用链接访问。 您可以 `Enabled` 使用 PATCH 请求将值设置为或 `Disabled` 。 有关详细信息，请参阅[使用 Azure 专用链接的 Azure Cache for Redis（预览版）](cache-private-link.md)。 
* 所有外部缓存依赖项都不会影响 VNet 的 NSG 规则。

### <a name="limitations"></a>限制 
* 对专用终结点禁用网络安全组 (NSG)。 但是，如果子网上还有其他资源，则 NSG 强制将应用于这些资源。
* 目前尚不支持异地复制、防火墙规则、门户控制台支持、每个群集缓存多个终结点、防火墙的持久性连接和 VNet 注入的缓存。 
* 若要连接到群集缓存，需要将 `publicNetworkAccess` 设置为 `Disabled` 并且只能有一个专用终结点连接。

> [!NOTE]
> 将专用终结点添加到缓存实例时，由于 DNS，所有 Redis 流量都将移至专用终结点。
> 请确保之前调整了以前的防火墙规则。  
>
>

## <a name="azure-virtual-network-injection"></a>Azure 虚拟网络注入 
VNet 是 Azure 中专用网络的基本构建块。 借助 VNet，许多 Azure 资源可以安全地与彼此、Internet 和本地网络通信。 VNet 类似于可以在自己的数据中心进行操作的传统网络，但具有 Azure 基础结构、规模、可用性和隔离的优点。 

### <a name="advantages"></a>优点
* 为 Azure Cache for Redis 实例配置了 VNet 后，该实例不可公开寻址，而只能从 VNet 中的虚拟机和应用程序进行访问。  
* 将 VNet 与受限制的 NSG 策略相结合有助于降低数据泄露的风险。 
* VNet 部署可为 Azure Cache for Redis、子网、访问控制策略和其他功能提供增强的安全性和隔离，从而进一步限制访问。 
* 支持异地复制。 

### <a name="limitations"></a>限制
* VNet 注入的缓存仅适用于高级 Azure Cache for Redis。 
* 使用 VNet 注入的缓存时，需要打开 VNet 以缓存 CRL/PKI、AKV、Azure 存储、Azure Monitor 等依赖项。  


## <a name="azure-firewall-rules"></a>Azure 防火墙规则
[Azure 防火墙](../firewall/overview.md)是托管的、基于云的网络安全服务，可保护 Azure VNet 资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 可以跨订阅和虚拟网络集中创建、实施和记录应用程序与网络连接策略。  

### <a name="advantages"></a>优点
* 配置防火墙规则时，仅指定 IP 地址范围内的客户端连接可以连接到缓存。 即使配置了防火墙规则，仍始终允许来自 Azure Redis 缓存监视系统的连接。 还允许你定义的 NSG 规则。  

### <a name="limitations"></a>限制
* 防火墙规则可以与 VNet 注入的缓存一起使用，但目前不能与专用终结点一起使用。 


## <a name="next-steps"></a>后续步骤
* 了解如何[为高级 Azure Cache for Redis 实例配置 VNet 注入的缓存](cache-how-to-premium-vnet.md)。  
* 了解如何[为所有 Azure Cache for Redis 层配置防火墙规则](cache-configure.md#firewall)。 
* 了解如何[为所有 Azure Cache for Redis 层配置专用终结点](cache-private-link.md)。