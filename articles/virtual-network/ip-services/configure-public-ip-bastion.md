---
title: 管理 Azure Bastion 的公共 IP 地址
titleSuffix: Azure Virtual Network
description: 了解在 Azure Bastion 中使用公共 IP 地址的方式，以及如何更改配置。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 22fa5e1843cdff45a5bea9fb61393a0579278964
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367507"
---
# <a name="manage-a-public-ip-address-with-azure-bastion"></a>管理 Azure Bastion 的公共 IP 地址

公共 IP 地址可在两个 SKU 中使用：标准和基本。 所选的 SKU 确定了 IP 地址的功能。 SKU 确定了 IP 地址可以关联到的资源。

部署 Azure Bastion 的目的是提供与虚拟网络中虚拟机的安全管理连接。 使用 Azure Bastion 服务能够通过 RDP 和 SSH 安全无缝地连接到虚拟网络中的 VM。 无需在 VM 上公开公共 IP，Azure Bastion 就能启用连接。 连接是直接通过 Azure 门户建立的，而无需额外安装客户端/代理或软件。 Azure Bastion 支持标准 SKU 公共 IP 地址。

Azure Bastion 主机需要在其配置中使用公共 IP 地址。

本文将介绍如何使用订阅中的现有公共 IP 创建 Azure Bastion 主机。 创建公共 IP 地址后，Azure Bastion 不支持更改该 IP 地址。  Azure Bastion 不支持公共 IP 前缀。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 订阅中的一个标准 SKU 公共 IP 地址。 该 IP 地址不能与任何资源相关联。 有关如何创建标准 SKU 公共 IP 地址的详细信息，请参阅[创建公共 IP - Azure 门户](../../virtual-network/create-public-ip-portal.md)。
    - 对于本文中的示例，请将新的公共 IP 地址命名为 myStandardPublicIP。

## <a name="create-azure-bastion-using-existing-ip"></a>使用现有 IP 创建 Azure Bastion

在本部分，你将创建一个 Azure Bastion 主机。 你将选择在先决条件部分创建的 IP 地址作为堡垒主机的公共 IP。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户顶部的搜索框中，输入“堡垒”。

3. 在搜索结果中选择“堡垒”。

4. 选择“+ 新建”。 

5. 在“创建堡垒”中，输入或选择以下信息。

    | 设置 | 值 | 
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅 |
    | 资源组 | 选择“新建”。 </br> 在“名称”中输入“myResourceGroup” 。 </br> 选择“确定”。 |
    | **实例详细信息** |  |
    | 名称 | 输入“myBastionHost”。 |
    | 区域 | 选择“(US) 美国西部 2”。 |
    | **配置虚拟网络** |   |
    | 虚拟网络 | 选择“新建”。 </br> 在“名称”中输入“myVNet” 。 </br> 保留默认地址空间“10.4.0.0/16”。 </br> 保留默认子网“10.4.0.0/24”。 </br> 在“默认”子网下的文本框中，输入“AzureBastionSubnet” 。 </br> 为“地址范围”中，输入“10.4.1.0/27”。 </br> 选择“确定”。 |
    | 子网 | 选择“AzureBastionSubnet”。 |
    | **公共 IP 地址** |   |
    | 公共 IP 地址 | 选择“使用现有项”。 |
    | 选择公共 IP 地址 | 选择“myStandardPublicIP”。 |

6. 选择“查看 + 创建”选项卡，或选择蓝色的“查看 + 创建”按钮 。

7. 选择“创建”。

> [!NOTE]
> 有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion？](../../bastion/bastion-overview.md)

## <a name="change-or-remove-public-ip-address"></a>更改或删除公共 IP 地址

创建公共 IP 地址后，Azure Bastion 不支持更改该 IP 地址。

## <a name="more-information"></a>详细信息

* 通过 Azure Bastion 连接时，不要求虚拟机上具有单独的公共 IP。 流量首先路由到 Bastion 的公共 IP。 然后，Bastion 将 RDP 或 SSH 连接路由到与虚拟机关联的专用 IP 地址。 

## <a name="caveats"></a>注意事项

* Azure Bastion 目前不支持公共 IPv6 地址。  

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何创建 Azure Bastion 和使用现有公共 IP。 

- 有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion？](../../bastion/bastion-overview.md)
- 若要详细了解 Azure 中的公共 IP 地址，请参阅[公共 IP 地址](../../virtual-network/public-ip-addresses.md)。
