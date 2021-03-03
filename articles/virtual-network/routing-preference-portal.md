---
title: 配置公共 IP 地址的路由首选项 - Azure 门户
description: 了解如何创建具有 Internet 流量路由首选项的公共 IP
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
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672932"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户配置公共 IP 地址的路由首选项

本文介绍如何通过 ISP 网络为公共 IP 地址配置 [路由首](./routing-preference-overview.md) 选项 (**Internet** 选项) 。 创建公共 IP 地址后，可以将它与以下 Azure 资源关联，以用于 Internet 的入站和出站流量：

* 虚拟机
* 虚拟机规模集
* Azure Kubernetes 服务 (AKS)
* 面向 Internet 的负载均衡器
* 应用程序网关
* Azure 防火墙

默认情况下，对于所有 Azure 服务，公共 IP 地址的路由首选项设置为“Microsoft 全球网络”，并且可以与任何 Azure 服务相关联。

如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>创建具有路由首选项的公共 IP 地址
1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源”。
3. 在搜索框中键入“公共 IP 地址”。
3. 在搜索结果中时，选择“公共 IP 地址”。 接下来，在“公共 IP 地址”页中，选择“创建”。
1. 对于 SKU，选择 " **标准**"。
1. 对于 **路由首选项**，请选择 **Internet**。

      ![创建公共 IP 地址](./media/routing-preference-portal/public-ip-new.png)
1. 在 " **IPV4 IP 地址配置** " 部分中，输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择 " **新建**"，输入 *RoutingPreferenceResourceGroup*，然后选择 **"确定"**。 |
    | 位置 | 选择“美国东部”。|
    | 可用性区域 | 保留默认值- **区域冗余**。 |
1. 选择“创建”。

    > [!NOTE]
    > 公共 IP 地址是使用 IPv4 或 IPv6 地址创建的。 但是，路由首选项目前仅支持 IPV4。

可以将上面创建的公共 IP 地址与 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机相关联。 使用“教程”页上的“CLI”部分：[将公共 IP 地址与虚拟机关联](associate-public-ip-address-vm.md#azure-cli)以将公共 IP 关联至 VM。 也可将上面创建的公共 IP 地址与 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)相关联，只需将其分配给负载均衡器前端配置即可。 此公共 IP 地址充当负载均衡型虚拟 IP 地址 (VIP)。

## <a name="next-steps"></a>后续步骤
- 详细了解[具有路由首选项的公共 IP](routing-preference-overview.md)。
- [为 VM 配置路由首选项](tutorial-routing-preference-virtual-machine-portal.md)。
- [使用 PowerShell 配置公共 IP 地址的路由首选项](routing-preference-powershell.md)。
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。