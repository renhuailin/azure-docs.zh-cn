---
title: 使用专用终结点
titleSuffix: Azure SignalR Service
description: 用于从虚拟网络安全访问 Azure SignalR 服务的专用终结点概述。
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 92e93c3746308d2d6c1a489efc6b5c866b0ad2d9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682624"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>为 Azure SignalR 服务使用专用终结点

你可以使用 Azure SignalR 服务的 [专用终结点](../private-link/private-endpoint-overview.md) ，以允许虚拟网络 (VNet) 上的客户端通过 [专用链接](../private-link/private-link-overview.md)安全地访问数据。 专用终结点为 Azure SignalR 服务使用 VNet 地址空间中的 IP 地址。 VNet 和 Azure SignalR 服务上的客户端之间的网络流量通过 Microsoft 主干网络上的专用链接进行传输，避免暴露给公共 Internet。

通过为 Azure SignalR 服务使用专用终结点，你可以：

- 使用网络访问控制来阻止 Azure SignalR 服务的公共终结点上的所有连接，从而保护 Azure SignalR 服务。
- 阻止数据从 VNet 渗出，从而提高虚拟网络 (VNet) 的安全性。
- 使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoutes](../expressroute/expressroute-locations.md) 通过专用对等互连从连接到 VNet 的本地网络安全地连接到 Azure SignalR 服务。

## <a name="conceptual-overview"></a>概念概述

![Azure SignalR 服务的专用终结点概述](media/howto-private-endpoints/private-endpoint-overview.png)

专用终结点是用于[虚拟网络](../virtual-network/virtual-networks-overview.md) (VNet) 中的 Azure 服务的特殊网络接口。 为 Azure SignalR 服务创建专用终结点时，它将在 VNet 上的客户端和服务之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点与 Azure SignalR 服务之间的连接使用安全的专用链接。

VNet 中的应用程序可以使用通过其他方式连接时所用的相同连接字符串和授权机制，通过专用终结点无缝地连接到 Azure SignalR 服务。 专用终结点可以与 Azure SignalR 服务支持的所有协议（包括 REST API）一起使用。

在 VNet 中为 Azure SignalR 服务创建专用终结点时，会将一个申请批准的许可请求发送到 Azure SignalR 服务所有者。 如果请求创建专用终结点的用户也是 Azure SignalR 服务的所有者，则此许可请求会自动获得批准。

Azure SignalR 服务所有者可以通过 [Azure 门户](https://portal.azure.com)中 Azure SignalR 服务的“专用终结点”选项卡来管理许可请求和专用同意终结点。

> [!TIP]
> 如果只想限制通过专用终结点对 Azure SignalR 服务的访问，请[配置网络访问控制](howto-network-access-control.md#managing-network-access-control)以拒绝或控制通过公共终结点的访问。

### <a name="connecting-to-private-endpoints"></a>连接到专用终结点

使用专用终结点的 VNet 上的客户端应该为 Azure SignalR 服务使用与连接到公共终结点的客户端相同的连接字符串。 我们依赖于 DNS 解析，通过专用链接自动将连接从 VNet 路由到 Azure SignalR 服务。

> [!IMPORTANT]
> 使用相同的连接字符串通过专用终结点连接到 Azure SignalR 服务，就像在其他情况下使用那样。 请不要使用其 `privatelink` 子域 URL 连接到 Azure SignalR 服务。

默认情况下，我们会创建一个附加到 VNet 的[专用 DNS 区域](../dns/private-dns-overview.md)，并带有专用终结点的必要更新。 但是，如果使用自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。 下面关于 [DNS 更改](#dns-changes-for-private-endpoints)的部分描述了专用终结点所需的更新。

## <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改

创建专用终结点时，Azure SignalR 服务的 DNS CNAME 资源记录将更新为具有前缀 `privatelink` 的子域中的别名。 默认情况下，我们还会创建一个与 `privatelink` 子域对应的[专用 DNS 区域](../dns/private-dns-overview.md)，其中包含专用终结点的 DNS A 资源记录。

使用专用终结点从 VNet 外部解析 Azure SignalR 服务域名时，它会解析为 Azure SignalR 服务的公共终结点。 从托管专用终结点的 VNet 进行解析时，域名解析为专用终结点的 IP 地址。

对于上面所示的示例，Azure SignalR 服务“foobar”的 DNS 资源记录在从托管专用终结点的 VNet 外部解析时将为：

| 名称                                                  | 类型  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | \<Azure SignalR Service public IP address\>           |

如前文所述，可以使用网络访问控制通过公共终结点拒绝或控制 VNet 外部的客户端的访问。

当“foobar”的 DNS 资源记录由托管专用终结点的 VNet 中的客户端解析时，将为：

| 名称                                                  | 类型  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | A     | 10.1.1.5                                              |

对于托管专用终结点的 VNet 上的客户端和 VNet 外部的客户端，此方法允许使用相同的连接字符串访问 Azure SignalR 服务。

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将 Azure SignalR 服务终结点的 FQDN 解析为专用终结点 IP 地址。 应配置 DNS 服务器以将专用链接子域委托到 VNet 的专用 DNS 区域，或者使用专用终结点 IP 地址为 `foobar.privatelink.service.signalr.net` 配置 A 记录。

> [!TIP]
> 使用自定义或本地 DNS 服务器时，应将 DNS 服务器配置为将 `privatelink` 子域中的 Azure SignalR 服务名称解析为专用终结点 IP 地址。 为此，可以将 `privatelink` 子域委托给 VNet 的专用 DNS 区域，或在 DNS 服务器上配置 DNS 区域并添加 DNS A 记录。

Azure SignalR 服务的专用终结点的建议 DNS 区域名称为：`privatelink.service.signalr.net`。

有关配置自己的 DNS 服务器以支持专用终结点的详细信息，请参阅以下文章：

- [Azure 虚拟网络中资源的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [专用终结点的 DNS 配置](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>创建专用终结点

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>在 Azure 门户中创建专用终结点以及新的 Azure SignalR 服务

1. 创建新的 Azure SignalR 服务时，选择“网络”选项卡。选择“专用终结点”作为连接方法。

    ![创建 Azure SignalR 服务 -“网络”选项卡](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. 单击“添加”。 填写新专用终结点的订阅、资源组、位置、名称。 选择虚拟网络和子网。

    ![创建 Azure SignalR 服务 - 添加专用终结点](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. 单击“查看 + 创建”  。

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>在 Azure 门户中为现有 Azure SignalR 服务创建专用终结点

1. 转到“Azure SignalR 服务”。

1. 单击名为“专用终结点连接”的设置菜单。

1. 单击顶部“+ 专用终结点”按钮。

    ![“专用终结点连接”边栏选项卡](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. 填写新专用终结点的订阅、资源组、资源名称和区域。
    
    ![创建专用终结点 - 基本信息](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. 选择目标 Azure SignalR 服务资源。

    ![创建专用终结点 - 资源](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. 选择目标虚拟网络

    ![创建专用终结点 - 配置](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. 单击“查看 + 创建”  。

### <a name="create-a-private-endpoint-using-azure-cli"></a>使用 Azure CLI 创建专用终结点

1. 登录到 Azure CLI
    ```azurecli
    az login
    ```
1. 选择 Azure 订阅
    ```azurecli
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. 创建新的资源组
    ```azurecli
    az group create -n {RG} -l {AZURE REGION}
    ```
1. 将 Microsoft.SignalRService 注册为提供商
    ```azurecli
    az provider register -n Microsoft.SignalRService
    ```
1. 创建新的 Azure SignalR 服务
    ```azurecli
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. 创建虚拟网络
    ```azurecli
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. 添加子网
    ```azurecli
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. 禁用虚拟网络策略
    ```azurecli
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. 添加专用 DNS 区域
    ```azurecli
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. 将专用 DNS 区域链接到虚拟网络
    ```azurecli
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. 创建专用终结点（自动批准）
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. 创建专用终结点（手动请求批准）
    ```azurecli
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. 显示连接状态
    ```azurecli
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知问题

请记住以下关于 Azure SignalR 服务专用终结点的已知问题

### <a name="free-tier"></a>免费层

无法为免费层 Azure SignalR 服务创建任何专用终结点。

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>针对专用终结点所在 VNet 中的客户端的访问约束

具有现有专用终结点的 VNet 中的客户端访问具有专用终结点的其他 Azure SignalR 服务实例时面临约束。 例如，假设 VNet N1 具有 Azure SignalR 服务实例 S1 的专用终结点。 如果 Azure SignalR 服务 S2 在 VNet N2 中具有专用终结点，则 VNet N1 中的客户端也必须使用专用终结点访问 Azure SignalR 服务 S2。 如果 Azure SignalR 服务 S2 没有任何专用终结点，则 VNet N1 中的客户端可以在没有专用终结点的情况下访问该帐户中的 Azure SignalR 服务。

此约束是 Azure SignalR 服务 S2 创建专用终结点时 DNS 发生更改的结果。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>专用终结点所在子网的网络安全组规则

目前，你不能为专用终结点配置[网络安全组](../virtual-network/network-security-groups-overview.md) (NSG) 规则和用户定义的路由。 应用于托管专用终结点的子网的 NSG 规则将应用到专用终结点。 解决此问题的一个有限的解决方法是在源子网上实现专用终结点的访问规则，但这种方法可能需要更高的管理开销。

## <a name="next-steps"></a>后续步骤

- [配置网络访问控制](howto-network-access-control.md)