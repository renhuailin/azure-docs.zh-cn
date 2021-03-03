---
title: 为虚拟机配置两个路由首选项 Azure 门户
description: 了解如何同时启用这两个路由首选项
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679133"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>为虚拟机配置两个路由首选项选项

本文介绍如何为虚拟机 (VM)  (Internet 和 Microsoft 全局网络) 配置 [路由首](./routing-preference-overview.md) 选项选项。 这是使用两个虚拟网络接口实现的：一个网络接口具有通过 Microsoft 全局网络路由的公共 IP，另一个网络接口使用通过 ISP 网络路由的公共 IP。

## <a name="prerequisites"></a>先决条件

按照使用 Azure 门户 [创建具有静态公共 ip 地址 Azure 门户的虚拟机](./virtual-network-deploy-static-pip-arm-portal.md) 中所述的说明，使用公共 ip 地址创建一个虚拟机。 公共 IP 的默认路由首选项选择是通过 **Microsoft 全球网络**。 拥有创建公共 IP 的虚拟机后，请将另一个公共 IP 添加到 VM，以便通过传输 ISP 网络路由流量，并将其路由首选项配置为 **Internet**。

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>使用路由首选项选择 Internet 创建公共 IP 地址
1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在“路由首选项”选项中，选择“Internet”。

      ![创建公共 IP 地址](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > 公共 IP 地址是使用 IPv4 或 IPv6 地址创建的。 但是，路由首选项目前仅支持 IPV4。

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>创建网络接口并关联公共 IP

1. 使用 Azure 门户和默认设置创建 [网络接口](./routing-preference-overview.md) 。 
1. [将公共 IP 关联到](./associate-public-ip-address-vm.md) 在上一部分中创建的网络接口。 IP 地址可能需要在几秒钟后才会显示。 查看分配给 IP 配置的公共 IP 地址，如下所示：

    ![关联公共 IP](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>将网络接口附加到 VM

1. [将上一节中创建的网络接口附加到虚拟机](./virtual-network-network-interface-vm.md)。
2. 你现在可以查看与虚拟机关联的两个虚拟网络接口，一个虚拟网络接口具有通过 Microsoft 全局网络路由的公共 IP，另一个通过 ISP 网络路由，如下所示：  ![ 将网络接口附加到 VM](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>后续步骤
- 详细了解[具有路由首选项的公共 IP](routing-preference-overview.md)。
- [为 VM 配置路由首选项](tutorial-routing-preference-virtual-machine-portal.md)。
- [使用 PowerShell 配置公共 IP 地址的路由首选项](routing-preference-powershell.md)。
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。