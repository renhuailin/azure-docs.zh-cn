---
title: 使用 NAT 网关管理公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解如何配合使用公共 IP 地址和 Azure 虚拟网络 NAT 网关以及如何更改配置。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 2fd41f3459469474c100657747c4873ee99dc057
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367534"
---
# <a name="manage-a-public-ip-address-with-a-nat-gateway"></a>使用 NAT 网关管理公共 IP 地址

Azure NAT 网关资源支持来自虚拟网络子网的出站 Internet 连接。 NAT 网关虚拟网络子网中部署的资源必须是标准 SKU。 不支持将 NAT 网关部署到包含基本 SKU 资源的虚拟网络子网。 

NAT 网关支持来自使用 NAT 网关的资源的源网络地址转换 (SNAT) 连接。 NAT 网关支持标准 SKU 公共 IP 地址和公共 IP 前缀。 支持任意组合，但给定的 IP 数不能超过 16 个。 添加 IP 地址或 IP 前缀会扩展来自使用 NAT 网关的资源的 SNAT 连接。 

在本文中，你将了解如何使用订阅中的现有公共 IP 创建 NAT 网关。 

你将了解如何更改与 NAT 网关关联的当前公共 IP。 

最后，你会将 IP 配置从公共 IP 地址更改为 IP 前缀。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 订阅中的两个标准 SKU 公共 IP 地址。 IP 地址不能与任何资源相关联。 有关如何创建标准 SKU 公共 IP 地址的详细信息，请参阅[创建公共 IP - Azure 门户](../../virtual-network/create-public-ip-portal.md)。
    - 对于本文中的示例，请将新的公共 IP 地址命名为 myStandardPublicIP-1 和 myStandardPublicIP-2 。
- 订阅中的公共 IP 前缀。 有关如何创建公共 IP 前缀的详细信息，请参阅[使用 Azure 门户创建公共 IP 前缀](../../virtual-network/create-public-ip-prefix-portal.md)。
    - 就本文中的示例而言，请将新的公共 IP 前缀命名为 myPublicIPPrefixNAT。

## <a name="create-nat-gateway-existing-public-ip"></a>创建 NAT 网关现有公共 IP

在本部分中，你将创建一个 NAT 网关资源。 你将选择在先决条件中创建的 IP 地址作为 NAT 网关的公共 IP。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“NAT 网关”。

3. 在搜索结果中，选择“NAT 网关”。

4. 选择“+ 新建”。 

5. 在“创建网络地址转换(NAT)网关”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。 </br> 输入“myResourceGroupNAT”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 名称 | 输入 myNATGateway。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | 可用性区域 | 保留默认值“无”。 |
    | 空闲超时 | 保留默认值“4”。 |

6. 选择“出站 IP”选项卡，或选择“下一步: 出站 IP” 。

7. 在“出站 IP”选项卡中，为“公共 IP 地址”选择 myStandardPublicIP-1  。

6. 选择“查看 + 创建”选项卡，或选择蓝色的“查看 + 创建”按钮 。 

7. 选择“创建”。

> [!NOTE]
> 这是 NAT 网关的简单部署。 有关高级配置和设置，请参阅[教程：使用 Azure 门户创建 NAT 网关](../nat-gateway/tutorial-create-nat-gateway-portal.md)
>
> 有关 Azure 虚拟网络 NAT 的详细信息，请参阅[什么是 Azure 虚拟网络 NAT？](../nat-gateway/nat-overview.md)

## <a name="change-or-remove-public-ip-address"></a>更改或删除公共 IP 地址

在本部分中，你将登录到 Azure 门户并更改 NAT 网关的 IP 地址。 

若要更改 IP，你需要将之前创建的新公共 IP 地址与 NAT 网关相关联。 NAT 网关必须至少分配有一个 IP 地址。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“NAT 网关”。

3. 在搜索结果中，选择“NAT 网关”。

4. 在“NAT 网关”中，选择 myNATgateway 或想要更改的 NAT 网关 。

5. 在 myNATgateway 的设置中，选择“出站 IP” 。

6. 选择“出站 IP”中“公共 IP 地址”旁边的“更改”  。

7. 你可以选择替换当前 IP 地址或添加现有地址。 在“公共 IP 地址”旁边的“管理公共 IP 地址和前缀”中，选择 myStandardPublicIP-2  。

8. 选择“确定”。

9. 验证 myStandardPublicIP-2 是否已添加到公共 IP 地址。 如果需要，可以通过选择垃圾桶来删除已分配的 IP 地址。

10. 选择“保存”  。

## <a name="add-public-ip-prefix"></a>添加公共 IP 前缀

公共 IP 前缀扩展了来自 NAT 网关的出站连接的 SNAT 的扩展性。 公共 IP 前缀可避免 SNAT 端口耗尽。 每个 IP 提供 64,000 个可使用的临时端口。

> [!NOTE] 
> 向 NAT 网关分配公共 IP 前缀时，将使用整个范围。 

在本部分中，你将更改出站 IP 配置以使用你先前创建的公共 IP 前缀。

> [!NOTE]
> 你可以选择删除与 NAT 网关关联的单个 IP 地址并重复使用，或将其保留为与 NAT 网关关联以增加出站 SNAT 端口。 NAT 网关在出站 IP 配置中支持公共 IP 和前缀的组合。 如果创建了包含 16 个地址的公共 IP 前缀，请在添加前缀之前删除单个公共 IP 并保存配置。 分配的 IP 数不能超过 16 个。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“NAT 网关”。

3. 在搜索结果中，选择“NAT 网关”。

4. 在“NAT 网关”中，选择 myNATgateway 或想要更改的 NAT 网关 。

5. 在 myNATgateway 的设置中，选择“出站 IP” 。

6. 选择“出站 IP”中“公共 IP 前缀”旁边的“更改”  。

7. 选择 myPublicIPPrefixNAT 或你的前缀。

8. 选择“确定”。

9. 验证 myPublicIPPrefixNAT 是否已添加到公共 IP 前缀。

10. 选择“保存”  。

## <a name="more-information"></a>详细信息

* 在具有 NAT 网关的虚拟网络中部署虚拟机时，所有发往 NAT 网关的入口流量都通过 NAT 网关流出。 当使用具有标准公共负载均衡器的 NAT 网关时，所有发往 NAT 网关公共 IP 地址的入口流量都将通过 NAT 网关流出。 

    > [!NOTE] 
    > 负载均衡规则或出站规则中的任何出站配置将由 NAT 网关取代。 负载均衡器后端池的成员还会将 NAT 网关用于出站连接。 有关详细信息，请参阅[使用 NAT 网关资源设计虚拟网络](../nat-gateway/nat-gateway-resource.md)

* NAT 网关和公共 IP 地址可以分配一个 TCP 超时值，用于指定在听到保持连接之前将连接保持为打开状态的时长。  如果将公共 IP 分配给 NAT 网关，则将优先使用该 IP 的超时值。  有关详细信息，请参阅[使用 NAT 网关资源设计虚拟网络](../nat-gateway/nat-gateway-resource.md#timers)

## <a name="caveats"></a>注意事项

* 目前 NAT 网关不支持公共 IPv6 地址和公共 IPv6 前缀。 NAT 网关不能部署在具有 IPv6 前缀的虚拟网络子网中。 有关详细信息，请参阅[排查 Azure 虚拟网络 NAT 连接问题](../nat-gateway/troubleshoot-nat.md)
## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何创建负载 NAT 网关和使用现有公共 IP。 你替换了 NAT 网关出站 IP 配置中的 IP 地址。 最后，你将出站 IP 配置更改为了使用公共 IP 前缀。

- 有关 Azure 虚拟网络 NAT 的详细信息，请参阅[什么是 Azure 虚拟网络 NAT？](../nat-gateway/nat-overview.md)
- 若要详细了解 Azure 中的公共 IP 地址，请参阅[公共 IP 地址](../../virtual-network/public-ip-addresses.md)。