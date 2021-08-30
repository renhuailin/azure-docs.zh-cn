---
title: 使用负载均衡器管理公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解如何结合使用公共 IP 地址和 Azure 负载均衡器，以及如何更改配置。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 88e67711b71ad80b48a7d5c19377847d0dadd296
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725945"
---
# <a name="manage-a-public-ip-address-with-a-load-balancer"></a>使用负载均衡器管理公共 IP 地址

公共负载均衡器是第 4 层解决方案，用于将 TCP 和 UDP 流量分发到后端池。 基本和标准 SKU 可用于负载均衡器。 

这些 SKU 对应于公共 IP 地址的基本和标准 SKU。

与负载均衡器关联的公共 IP 充当面向 Internet 的前端 IP 配置。 前端用于访问后端池中的资源。 前端 IP 可用于将后端池成员的出口流量发送到 Internet。 

基本 SKU Azure 负载均衡器的可用性选项和功能集有限。 对于生产负载，建议部署标准 SKU 负载均衡器和 IP 地址的组合。 标准 SKU IP 地址支持受支持区域中的可用性区域。 

本文介绍如何使用订阅中的现有公共 IP 地址创建负载均衡器。 

你将了解如何更改与负载均衡器关联的当前公共 IP。 

你将了解如何将出站后端池的前端配置更改为公共 IP 前缀。  

最后，本文将回顾结合使用负载均衡器以及公共 IP 和公共 IP 前缀的独特方面。 

> [!NOTE]
> 本文中的示例使用标准 SKU 负载均衡器和公共 IP。 对于基本 SKU 负载均衡器，除了在创建负载均衡器和公共 IP 资源时选择 SKU 以外，这些过程是相同的。 基本负载均衡器不支持出站规则或公共 IP 前缀。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 订阅中的两个标准 SKU 公共 IP 地址。 IP 地址不能与任何资源相关联。 有关如何创建标准 SKU 公共 IP 地址的详细信息，请参阅[创建公共 IP - Azure 门户](create-public-ip-portal.md)。
    - 对于本文中的示例，请将新的公共 IP 地址命名为 myStandardPublicIP-1 和 myStandardPublicIP-2 。
- 订阅中的公共 IP 前缀。 有关如何创建公共 IP 前缀的详细信息，请参阅[使用 Azure 门户创建公共 IP 前缀](create-public-ip-prefix-portal.md)。
    - 对于本文中的示例，请将新的公共 IP 前缀命名为“myPublicIPPrefixOutbound”。

## <a name="create-load-balancer-existing-public-ip"></a>创建负载均衡器现有的公共 IP

在本部分，你将创建标准 SKU 负载均衡器。 你将选择在先决条件中创建的 IP 地址作为负载均衡器的前端 IP。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。

3. 在搜索结果中，选择“负载均衡器”。

4. 选择“+ 新建”。 

5. 在“创建负载均衡器”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“myResourceGroupIP”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 名称 | 输入 **myLoadBalancer**。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | 类型 | 保留默认值“公共”。 |
    | SKU | 保留默认值“标准”。 |
    | 层 | 保留默认值“区域”。 |
    | **公共 IP 地址** |   |
    | 公共 IP 地址 | 选择“使用现有项”。 |
    | 选择公共 IP 地址 | 选择“myStandardPublicIP-1”。 |
    | 添加一个公共 IPv6 地址 | 保留默认值“否”。 |

6. 选择“查看 + 创建”选项卡，或选择蓝色的“查看 + 创建”按钮 。 

7. 选择“创建”。

> [!NOTE]
> 这是一个简单的负载均衡器部署。 有关高级配置和设置，请参阅[快速入门：使用 Azure 门户创建公共负载均衡器以对 VM 进行负载均衡](../load-balancer/quickstart-load-balancer-standard-public-portal.md)
>
> 有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md)

## <a name="change-or-remove-public-ip-address"></a>更改或删除公共 IP 地址

在本部分中，你将登录到 Azure 门户并更改负载均衡器的 IP 地址。 

Azure 负载均衡器必须具有与前端关联的 IP 地址。 可以使用单独的公共 IP 地址作为入口和出口流量的前端。 

若要更改 IP，需将之前创建的新公共 IP 地址与负载均衡器前端相关联。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。

3. 在搜索结果中，选择“负载均衡器”。

4. 在“负载均衡器”中，选择要更改的“myLoadBalancer”或负载均衡器 。

5. 在“myLoadBalancer”的设置中，选择“前端 IP 配置” 。

6. 在“前端 IP 配置”中，选择“LoadBalancerFrontend”或负载均衡器前端 。

7. 在负载均衡器前端配置中，选择“公共 IP 地址”中的“myStandardPublicIP-2” 。

8. 选择“保存”  。

9. 验证负载均衡器前端是否显示名为“myStandardPublicIP-2”的新 IP 地址。

    > [!NOTE]
    > 这些过程适用于跨区域负载均衡器。 有关跨区域负载均衡器的详细信息，请参阅[跨区域负载均衡器](../load-balancer/cross-region-overview.md)。


## <a name="add-public-ip-prefix"></a>添加公共 IP 前缀

标准负载均衡器支持源网络地址转换 (SNAT) 的出站规则。 SNAT 允许将后端池成员的出口流量发送到 Internet。 公共 IP 前缀允许多个 IP 地址用于出站连接，从而扩展了 SNAT 的可扩展性。 

多个 IP 可避免 SNAT 端口耗尽。 每个前端 IP 提供负载均衡器可使用的 64000 个临时端口。 有关详细信息，请参阅[出站规则](../load-balancer/outbound-rules.md)。

在本部分中，你将更改用于出站连接的前端配置，以使用公共 IP 前缀。

> [!IMPORTANT]
> 若要完成本部分，必须有已部署出站前端配置和出站规则的负载均衡器。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“负载均衡器”。

3. 在搜索结果中，选择“负载均衡器”。

4. 在“负载均衡器”中，选择要更改的“myLoadBalancer”或负载均衡器 。

5. 在“myLoadBalancer”的设置中，选择“前端 IP 配置” 。

6. 在“前端 IP 配置”中，选择“LoadBalancerFrontendOutbound”或负载均衡器出站前端 。

7. 对于“IP 类型”，请选择“公共 IP 前缀” 。

8. 在“公共 IP 前缀”中，选择之前创建的公共 IP 前缀“myPublicIPPrefixOutbound” 。

9. 选择“保存”  。

10. 在“前端 IP 配置”中，确认 IP 前缀已添加到出站前端配置。

## <a name="more-information"></a>详细信息

* 跨区域负载均衡器是一种特殊类型的标准公共负载均衡器，可以跨多个区域。 跨区域负载均衡器的前端只能与标准 SKU 公共 IP 的全局层选项一起使用。 发送到跨区域负载均衡器前端 IP 的流量分布在区域公共负载均衡器之间。 区域前端 IP 包含在跨区域负载均衡器的后端池中。 有关详细信息，请参阅[跨区域负载均衡器](../load-balancer/cross-region-overview.md)。

* 默认情况下，公共负载均衡器不允许对同一后端端口使用多个负载均衡规则。 如果需要对同一后端端口进行多规则配置，则为负载均衡规则启用浮动 IP 选项。 此设置将覆盖发送到后端池的流量的目标 IP 地址。 如果没有启用浮动 IP，目标将是后端池专用 IP。 启用浮动 IP 后，目标 IP 将是负载均衡器前端公共 IP。 后端实例必须在其网络配置中配置此公共 IP 才能正确接收此流量。 在实例中配置了前端 IP 地址的环回接口。 有关详细信息，请参阅 [Azure 负载均衡器浮动 IP 配置](../load-balancer/load-balancer-floating-ip.md)

* 使用负载均衡器设置时，通常还可以为后端池的成员分配实例级公共 IP。 如果使用此体系结构，则直接将流量发送到这些 IP 会绕过负载均衡器。 

* 标准公共负载均衡器和公共 IP 地址都可以分配一个 TCP 超时值，用于指定在听到保持连接之前将连接保持为打开状态的时长。 如果分配公共 IP 作为负载均衡器前端，则 IP 上的超时值优先。 请注意，此设置仅适用于与负载均衡器的入站连接。 有关详细信息，请参阅[负载均衡器 TCP 重置](../load-balancer/load-balancer-tcp-reset.md)

## <a name="caveats"></a>注意事项

* 标准公共负载均衡器可以使用 IPv6 地址作为其前端公共 IP 或公共 IP 前缀。  每个部署都必须是包含 IPv4 和 IPv6 前端的双堆栈。 NAT64 转换不可用。 有关详细信息，请参阅[在 Azure 中部署 IPv6 双堆栈应用程序 - PowerShell](./virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)

* 当多个前端分配给一个公共负载均衡器时，没有方法可以将特定后端实例的流分配到特定 IP 上的出口。  有关详细信息，请参阅 [Azure 负载均衡器的多个前端](../load-balancer/load-balancer-multivip-overview.md)
## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何创建负载均衡器和使用现有公共 IP。 

替换了负载均衡器前端配置中的 IP 地址。 

最后，你将出站前端配置更改为了使用公共 IP 前缀。

- 有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md)
- 若要详细了解 Azure 中的公共 IP 地址，请参阅[公共 IP 地址](public-ip-addresses.md)。
