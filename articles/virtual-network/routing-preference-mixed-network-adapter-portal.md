---
title: 为虚拟机配置两个路由首选项选项 - Azure 门户
description: 了解如何同时启用两个路由首选项
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679133"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>为虚拟机配置两个路由首选项选项

本文介绍如何为虚拟机 (VM) 配置两个[路由首选项](./routing-preference-overview.md)选项（Internet 和 Microsoft 全局网络）。 这通过使用两个虚拟网络接口来实现：一个网络接口具有通过 Microsoft 全局网络路由的公共 IP，另一个网络接口具有通过 ISP 网络路由的公共 IP。

## <a name="prerequisites"></a>先决条件

按照[使用 Azure 门户创建具有静态公共 IP 地址的虚拟机](./virtual-network-deploy-static-pip-arm-portal.md)中的说明使用 Azure 门户创建具有公共 IP 地址的虚拟机。 公共 IP 的默认路由首选项通过 Microsoft 全局网络路由来实现。 创建具有公共 IP 的虚拟机后，向该 VM 添加另一个公共 IP，该公共 IP 通过传输 ISP 网络路由流量，并且路由首选项配置为 Internet。

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>创建具有路由首选项 Internet 的公共 IP 地址
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。 
3. 在搜索框中键入“公共 IP 地址”。
4. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
5. 在“路由首选项”选项中，选择“Internet”。

      ![创建公共 IP 地址](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > 公共 IP 地址是使用 IPv4 或 IPv6 地址创建的。 但是，路由首选项目前仅支持 IPV4。

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>创建网络接口并关联公共 IP

1. 使用 Azure 门户和默认设置创建[网络接口](./routing-preference-overview.md)。 
1. [将公共 IP 关联到在上一部分中创建的网络接口](./associate-public-ip-address-vm.md)。 IP 地址可能需要在几秒钟后才会显示。 查看分配给 IP 配置的公共 IP 地址，如下图所示：

    ![关联公共 IP](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>将网络接口附加到 VM

1. [将在上一部分中创建的网络接口附加到虚拟机](./virtual-network-network-interface-vm.md)。
2. 现在可以查看与虚拟机关联的两个虚拟网络接口，一个接口具有通过 Microsoft 全局网络路由的公共 IP，另一个接口具有通过 ISP 网络路由的公共 IP，如![将网络接口附加到 VM](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png)中所示 

## <a name="next-steps"></a>后续步骤
- 详细了解[具有路由首选项的公共 IP](routing-preference-overview.md)。
- [为 VM 配置路由首选项](tutorial-routing-preference-virtual-machine-portal.md)。
- [使用 PowerShell 配置公共 IP 地址的路由首选项](routing-preference-powershell.md)。
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。