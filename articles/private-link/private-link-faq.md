---
title: Azure 专用链接常见问题解答 (FAQ)
description: 了解 Azure 专用链接。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496467"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure 专用链接常见问题解答 (FAQ)

## <a name="private-link"></a>专用链接

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>什么是 Azure 专用终结点和 Azure 专用链接服务？

- **[Azure 专用终结点](private-endpoint-overview.md)** ：Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 可以使用专用终结点连接到支持专用链接的 Azure PaaS 服务，或者连接到你自己的专用链接服务。
- **[Azure 专用链接服务](private-link-service-overview.md)** ：Azure 专用链接服务是服务提供商创建的服务。 目前，专用链接服务可以附加到标准负载均衡器的前端 IP 配置。 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>使用专用链接时流量是如何发送的？
流量是使用 Microsoft 主干以私密方式发送的。 它不会遍历 Internet。 Azure 专用链接不存储客户数据。
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>服务终结点和专用终结点之间的区别是什么？
- 专用终结点授予对特定资源的网络访问权，这些资源位于提供细粒度分段的给定服务之后。 流量可以从本地到达服务资源，而无需使用公共终结点。
- 服务终结点仍然是公共可路由的 IP 地址。  专用终结点是配置了专用终结点的虚拟网络的地址空间中的专用 IP。

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>专用链接服务与专用终结点之间的关系是什么？
多个专用链接资源类型支持通过专用终结点进行访问。 资源包括 Azure PaaS 服务和你自己的专用链接服务。 这是一种一对多关系。 

一个专用链接服务可以接收来自多个专用终结点的连接。 一个专用终结点连接到一个专用链接服务。    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>是否需要禁用专用链接的网络策略
是的。 专用终结点和专用链接服务都需要禁用网络策略才能正常工作。 它们的属性彼此独立。

## <a name="private-endpoint"></a>专用终结点 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>是否可在同一 VNet 中创建多个专用终结点？ 它们可以连接到不同的服务吗？ 
是的。 可以在同一 VNet 或子网中创建多个专用终结点。 它们可以连接到不同的服务。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>专用终结点是否需要专用子网？ 
不知道。 专用终结点不需要专用子网。 可以从部署了你的服务的 VNet 的任何子网中选择专用终结点 IP。  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>专用终结点是否可以跨 Azure Active Directory 租户连接到专用链接服务？ 
是的。 专用终结点可以跨 Azure Active Directory 租户连接到专用链接服务或 Azure PaaS。 跨租户连接的专用终结点需要手动请求批准。 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>专用终结点是否可以跨 Azure 区域连接到 Azure PaaS 资源？
是的。 专用终结点可以跨 Azure 区域连接到 Azure PaaS 资源。

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>能否修改专用终结点网络接口 (NIC)？
创建专用终结点时，将分配一个只读 NIC。 这无法修改，并且将在专用终结点的生命周期中保留。

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>在发生区域性故障时，如何在使用专用终结点的同时实现可用性？

专用终结点是具有 99.99% SLA 的高度可用资源 [[Azure 专用链接的 SLA]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)。 但是，由于它们是区域资源，任何 Azure 区域中断都可能会影响可用性。 若要在发生区域性故障时实现可用性，可以在不同的区域中部署多个连接到同一目标资源的 PE。 这样一来，如果一个区域出现故障，你仍可以通过不同区域中的 PE 来路由恢复方案的流量，以访问目标资源。 有关如何在目标服务端处理区域性故障的信息，请查看有关故障转移和恢复的服务文档。 专用链接流量遵循目标终结点的 Azure DNS 解析。 


## <a name="private-link-service"></a>专用链接服务
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>创建专用链接服务的先决条件是什么？ 
服务后端应当位于虚拟网络中，并位于标准负载均衡器后面。
 
### <a name="how-can-i-scale-my-private-link-service"></a>如何缩放专用链接服务？ 
可以通过几种不同的方式缩放专用链接服务： 
- 向标准负载均衡器后面的池添加后端 VM 
- 向专用链接服务添加 IP。 每个专用链接服务最多允许 8 个 IP。  
- 向标准负载均衡器添加新的专用链接服务。 每个负载均衡器最多允许 8 个专用链接服务。   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>专用链接服务中使用的 NAT（网络地址转换）IP 配置是什么？ 如何在可用端口和连接方面进行缩放？ 

NAT IP 配置通过在目标端（服务提供商端）的专用链接流量上提供源 NAT，确保源（使用者端）和目标（服务提供商）地址空间之间不存在 IP 冲突。 NAT IP 地址将显示为服务收到的所有数据包的源 IP，并显示为服务发送的所有数据包的目标 IP。  可以从服务提供商虚拟网络中的任何子网选择 NAT IP。 

每个 NAT IP 为标准负载均衡器后的每个 VM 提供 64k TCP 连接（64k 端口）。 为了扩展和添加更多连接，可以添加新的 NAT IP，或在标准负载均衡器后面添加更多 VM。 这样做会扩展端口可用性，并允许更多连接。 连接将分布在 NAT IP 和标准负载均衡器后面的 VM 上。

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>是否可以将服务连接到多个专用终结点？
是的。 一个专用链接服务可以接收来自多个专用终结点的连接。 但是，一个专用终结点只能连接到一个专用链接服务。  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>应该如何控制我的专用链接服务的公开？
你可以使用专用链接服务上的可见性配置来控制公开。 可见性支持三种设置：

- **无** - 只有具有 Azure RBAC 访问权限的订阅才能找到服务。 
- **限制** - 只有已批准且具有 Azure RBAC 访问权限的订阅才能找到服务。 
- **全部** - 所有人都可以找到服务。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>是否可以使用基本负载均衡器创建专用链接服务？ 
不知道。 不支持通过基本负载均衡器的专用链接服务。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>专用链接服务是否需要专用子网？ 
不知道。 专用链接服务不需要专用子网。 可以选择部署了你的服务的 VNet 中的任何子网。   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>我是使用 Azure 专用链接的服务提供商。 我是否需要确保所有客户都具有唯一的 IP 空间且不会与我的 IP 空间重叠？ 
不知道。 Azure 专用链接会为你提供此功能。 你无需具有与客户的地址空间不重叠的地址空间。 

##  <a name="next-steps"></a>后续步骤

- 了解 [Azure 专用链接](private-link-overview.md)
