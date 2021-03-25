---
title: Azure 防火墙 SNAT 专用 IP 地址范围
description: 可以为 SNAT 配置 IP 地址范围。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: c425afc314435c38d15d53ab0c38dcd48e35a40b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508922"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 防火墙 SNAT 专用 IP 地址范围

对于发往公共 IP 地址的所有出站流量，Azure 防火墙提供自动 SNAT。 默认情况下，当目标 IP 地址在符合 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) 的专用 IP 地址范围内时，Azure 防火墙不使用网络规则执行 SNAT。 无论目标 IP 地址是什么，始终使用[透明代理](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)来应用应用程序规则。

将流量直接路由到 Internet 时，此逻辑非常有效。 但是，如果已启用[强制隧道](forced-tunneling.md)，则会将 Internet 绑定的流量由 SNAT 转换为 AzureFirewallSubnet 中的某个防火墙专用 IP 地址，从而向本地防火墙隐藏源。

如果组织对专用网络使用公共 IP 地址范围，Azure 防火墙会通过 SNAT 将流量发送到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址。 但是，可以将 Azure 防火墙配置为不 SNAT 公共 IP 地址范围。 例如，若要指定单个 IP 地址，可以按如下所示指定它：`192.168.1.10`。 若要指定 IP 地址范围，可以按如下所示指定它：`192.168.1.0/24`。

- 若要将 Azure 防火墙配置为无论目标 IP 地址为何都不会执行 SNAT，请使用“0.0.0.0/0”作为专用 IP 地址范围 。 通过此配置，Azure 防火墙永远不能将流量直接路由到 Internet。 

- 若要将防火墙配置为无论目标地址为何都始终执行 SNAT，请使用“255.255.255.255/32”作为专用 IP 地址范围 。

> [!IMPORTANT]
> 指定的专用地址范围仅适用于网络规则。 目前，应用程序规则始终为 SNAT。

> [!IMPORTANT]
> 若要指定自己的专用 IP 地址范围，并保留默认的 IANA RFC 1918 地址范围，请确保自定义列表仍包含 IANA RFC 1918 范围。 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>配置 SNAT 专用 IP 地址范围 - Azure PowerShell

可以使用 Azure PowerShell 为防火墙指定专用 IP 地址范围。

### <a name="new-firewall"></a>新建防火墙

对于新建防火墙，Azure PowerShell cmdlet 如下：

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> 使用 `New-AzFirewall` 部署 Azure 防火墙需要一个现有的 VNet 和公共 IP 地址。 有关完整的部署指南，请参阅[使用 Azure PowerShell 部署和配置 Azure 防火墙](deploy-ps.md)。

> [!NOTE]
> 将其他范围添加到 Azure 防火墙中时，IANAPrivateRanges 将扩展到 Azure 防火墙上的当前默认值。 若要在专用范围规范中保留 IANAPrivateRanges 默认值，则必须将其保留在 `PrivateRange` 规范中，如以下示例所示。

有关详细信息，请参阅 [New-AzFirewall](/powershell/module/az.network/new-azfirewall)。

### <a name="existing-firewall"></a>现有防火墙

若要配置现有防火墙，请使用以下 Azure PowerShell cmdlet：

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>配置 SNAT 专用 IP 地址范围 - Azure CLI

可以使用 Azure CLI 为防火墙指定专用 IP 地址范围。

### <a name="new-firewall"></a>新建防火墙

对于新建防火墙，Azure CLI 命令如下：

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> 使用 Azure CLI 命令 `az network firewall create` 部署 Azure 防火墙需要额外的配置步骤来创建公共 IP 地址和 IP 配置。 有关完整的部署指南，请参阅[使用 Azure CLI 部署和配置 Azure 防火墙](deploy-cli.md)。

> [!NOTE]
> 将其他范围添加到 Azure 防火墙中时，IANAPrivateRanges 将扩展到 Azure 防火墙上的当前默认值。 若要在专用范围规范中保留 IANAPrivateRanges 默认值，则必须将其保留在 `PrivateRange` 规范中，如以下示例所示。

### <a name="existing-firewall"></a>现有防火墙

若要配置现有防火墙，可使用以下 Azure CLI 命令：

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>配置 SNAT 专用 IP 地址范围 - ARM 模板

若要在 ARM 模板部署期间配置 SNAT，可将以下内容添加到 `additionalProperties` 属性：

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>配置 SNAT 专用 IP 地址范围 - Azure 门户

可以使用 Azure 门户为防火墙指定专用 IP 地址范围。

1. 选择资源组，然后选择防火墙。
2. 在“概述”页上的“专用 IP 范围”中，选择默认值“IANA RFC 1918”  。

   此时将打开“编辑专用 IP 前缀”页：

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="编辑专用 IP 前缀":::

1. 默认情况下，“IANAPrivateRanges”已配置。
2. 编辑环境的专用 IP 地址范围，然后选择“保存”。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 防火墙强制隧道](forced-tunneling.md)。