---
title: Azure 防火墙 DNS 设置
description: 可以为 Azure 防火墙配置 DNS 服务器和 DNS 代理设置。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: 197d48a2f5368111ec194a18f86aedf5ad78e1b2
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565613"
---
# <a name="azure-firewall-dns-settings"></a>Azure 防火墙 DNS 设置

可以为 Azure 防火墙配置自定义 DNS 服务器并启用 DNS 代理。 部署防火墙时配置这些设置，或稍后从 " **DNS 设置** " 页配置这些设置。

## <a name="dns-servers"></a>DNS 服务器

DNS 服务器维护域名并将它解析为 IP 地址。 默认情况下，Azure 防火墙将 Azure DNS 用于名称解析。 通过“DNS 服务器”设置，你可以配置自己的 DNS 服务器来用于 Azure 防火墙名称解析。 你可以配置一台或多台服务器。

> [!NOTE]
> 对于使用 Azure 防火墙管理器管理的 Azure 防火墙的实例，会在关联的 Azure 防火墙策略中配置 DNS 设置。

### <a name="configure-custom-dns-servers---azure-portal"></a>配置自定义 DNS 服务器-Azure 门户

1. 在 Azure 防火墙的“设置”下，选择“DNS 设置” 。
2. 在 " **DNS 服务器** " 下，可以键入或添加之前已在虚拟网络中指定的现有 DNS 服务器。
3. 选择“保存”。

防火墙现在会将 DNS 流量定向到指定的 DNS 服务器进行名称解析。

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="显示 D N S 服务器的设置的屏幕截图。":::

### <a name="configure-custom-dns-servers---azure-cli"></a>配置自定义 DNS 服务器-Azure CLI

以下示例使用 Azure CLI 通过自定义 DNS 服务器更新 Azure 防火墙。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> 命令 `az network firewall` 要求安装 Azure CLI 扩展 `azure-firewall` 。 你可以使用命令来安装它 `az extension add --name azure-firewall` 。 

### <a name="configure-custom-dns-servers---azure-powershell"></a>配置自定义 DNS 服务器-Azure PowerShell

以下示例使用 Azure PowerShell 更新具有自定义 DNS 服务器的 Azure 防火墙。

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS 代理

可以对 Azure 防火墙进行配置来充当 DNS 代理。 DNS 代理是从客户端虚拟机向 DNS 服务器发出的 DNS 请求的中介。 如果配置自定义 DNS 服务器，请启用 DNS 代理以避免 DNS 解析不匹配，并启用 FQDN (完全限定的域名) 在网络规则中进行筛选。

如果未启用 DNS 代理，来自客户端的 DNS 请求可能会在不同的时间发送到 DNS 服务器，或者返回与防火墙不同的响应。 DNS 代理将 Azure 防火墙放置在客户端请求的路径中以避免不一致。

当 Azure 防火墙是 DNS 代理时，可以使用两种缓存函数类型：

- **正缓存** ： DNS 解析成功。 防火墙使用 TTL (时间) 数据包或对象的实时运行。 

- **负缓存** ： DNS 解析不会导致响应或不解决问题。 防火墙将此信息缓存一小时。

DNS 代理在网络规则中存储来自 Fqdn 的所有已解析的 IP 地址。 最佳做法是使用解析为一个 IP 地址的 Fqdn。  

### <a name="dns-proxy-configuration"></a>DNS 代理配置

DNS 代理配置需要三个步骤：
1. 在 Azure 防火墙 DNS 设置中启用 DNS 代理。
2. （可选）配置自定义 DNS 服务器或使用提供的默认值。
3. 将 Azure 防火墙专用 IP 地址配置为虚拟网络 DNS 服务器设置中的自定义 DNS 地址。 此设置可确保将 DNS 流量定向到 Azure 防火墙。

#### <a name="configure-dns-proxy---azure-portal"></a>配置 DNS 代理-Azure 门户

若要配置 DNS 代理，必须将你的虚拟网络 DNS 服务器设置配置为使用防火墙专用 IP 地址。 然后在 Azure 防火墙 **DNS 设置** 中启用 DNS 代理。

##### <a name="configure-virtual-network-dns-servers"></a>配置虚拟网络 DNS 服务器 

1. 选择将通过 Azure 防火墙实例路由 DNS 流量的虚拟网络。
2. 在“设置”下，选择“DNS 服务器”。 
3. 在“DNS 服务器”下，选择“自定义”。
4. 输入防火墙的专用 IP 地址。
5. 选择“保存” 。
6. 重启已连接到虚拟网络的 Vm，以便为它们分配新的 DNS 服务器设置。 Vm 将继续使用其当前 DNS 设置，直到重新启动它们。

##### <a name="enable-dns-proxy"></a>启用 DNS 代理

1. 选择 Azure 防火墙实例。
2. 在“设置”下，选择“DNS 设置” 。
3. 默认情况下，“DNS 代理”已禁用。 启用此设置后，防火墙会在端口53上侦听，并将 DNS 请求转发到配置的 DNS 服务器。
4. 查看“DNS 服务器”配置以确保设置适用于你的环境。
5. 选择“保存”。

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="显示 D N S 代理设置的屏幕截图。":::

#### <a name="configure-dns-proxy---azure-cli"></a>配置 DNS 代理-Azure CLI

你可以使用 Azure CLI 在 Azure 防火墙中配置 DNS 代理设置。 你还可以使用它来更新虚拟网络，以使用 Azure 防火墙作为 DNS 服务器。

##### <a name="configure-virtual-network-dns-servers"></a>配置虚拟网络 DNS 服务器

下面的示例将虚拟网络配置为使用 Azure 防火墙作为 DNS 服务器。
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>启用 DNS 代理

以下示例在 Azure 防火墙中启用 DNS 代理功能。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>配置 DNS 代理-Azure PowerShell

你可以使用 Azure PowerShell 在 Azure 防火墙中配置 DNS 代理设置。 你还可以使用它来更新虚拟网络，以使用 Azure 防火墙作为 DNS 服务器。

##### <a name="configure-virtual-network-dns-servers"></a>配置虚拟网络 DNS 服务器

下面的示例将虚拟网络配置为使用 Azure 防火墙作为 DNS 服务器。

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>启用 DNS 代理

以下示例在 Azure 防火墙中启用 DNS 代理功能。

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>后续步骤

[网络规则中的 FQDN 筛选](fqdn-filtering-network-rules.md)
