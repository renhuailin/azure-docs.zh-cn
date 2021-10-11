---
title: 快速入门：使用资源管理器模板创建公共 IP
titleSuffix: Azure Virtual Network
description: 了解如何使用资源管理器模板创建公共 IP
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: ef14611c6d5d981c61a69693b40cc8f3b8fd5185
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368787"
---
# <a name="quickstart-create-a-public-ip-address-using-a-resource-manager-template"></a>快速入门：使用资源管理器模板创建公共 IP 地址

本文演示如何在资源管理器模板内创建公共 IP 地址资源。

若要详细了解此公共 IP 可关联到的资源以及基本和标准 SKU 之间的区别，请查看[公共 IP 地址](public-ip-addresses.md)。 

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure 订阅中的资源组。
* 公共 IP 部分的 Azure 资源管理器模板。

## <a name="create-standard-sku-public-ip-with-zones"></a>创建不含区域的标准 SKU 公共 IP

在本部分，你将创建一个包含区域的公共 IP。 公共 IP 地址可以是区域冗余，也可以是区域性的。

### <a name="zone-redundant"></a>区域冗余

本部分的代码可创建名为“myStandardPublicIP”的标准区域冗余公共 IPv4 地址。

若要创建 IPv6 地址，请将 `publicIPAddressVersion` 参数设置为“IPv6” 。

要添加的模板节：

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "1",
                "2",
                "3"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```
> [!IMPORTANT]
> 对于 2020-08-01 之前的 API 版本，使用上面没有为标准 SKU 指定区域参数的代码创建区域冗余 IP 地址。 
>

>[!NOTE]
>上述区域选项仅是具有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

### <a name="zonal"></a>局部区域

本部分的代码可创建名为“myStandardPublicIP-zonal”的标准区域公共 IPv4 地址。 

若要在区域 2 创建标准区域公共 IP 地址，“区域”属性要包含“2”。

要添加的模板节：

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP-zonal",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "2"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```

>[!NOTE]
>上述区域选项仅是具有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

## <a name="create-standard-public-ip-without-zones"></a>创建不带区域的标准公共 IP

在本部分，你将创建一个非区域性的 IP 地址。 

本部分的代码创建标准非区域性的公共 IPv4 地址，命名为 myStandardPublicIP。 代码部分适用于含或不含[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的所有区域。

若要创建 IPv6 地址，请将 publicIPAddressVersion 参数修改为 IPv6 。

要添加的模板节：

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP-nozone",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```
> [!IMPORTANT]
> 对于早于 2020-08-01 的 API 版本，不为标准 SKU 指定区域参数将创建区域冗余的 IP 地址。 
>


## <a name="create-a-basic-public-ip"></a>创建基本公共 IP

在本部分，你将创建基本公共 IP。 基本公共 IP 不支持可用性区域。 

本部分代码创建一个名为 myBasicPublicIP 的基本公共 IPv4 地址。

若要创建 IPv6 地址，请将 publicIPAddressVersion 参数修改为 IPv6 。 

要添加的模板节：

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myBasicPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Basic"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": 'IPv4'
      }
```

如果可以接受 IP 地址随时间而变这种情况，可通过将“AllocationMethod”更改为“Dynamic”来选择 publicIPAllocationMethod IP 分配 。 

>[!NOTE]
> 基本 IPv6 地址必须始终为“Dynamic”。

## <a name="routing-preference-and-tier"></a>路由首选项和层

标准 SKU 静态公共 IPv4 地址支持路由首选项或全局层功能。

### <a name="routing-preference"></a>路由首选项

默认情况下，公共 IP 地址的路由首选项设置为“Microsoft 网络”，该网络通过 Microsoft 的全球广区网络向用户传送流量。  

相比传输 ISP 网络，选择“Internet”可最大限度减少 Microsoft 网络漫游，从而按成本优化费率传送流量。  

有关路由首选项的详细信息，请参阅[什么是路由首选项（预览）？](routing-preference-overview.md)。

若要对标准区域冗余公共 IPv4 地址使用 Internet 路由首选项，模板部分应类似于：

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardZRPublicIP-RP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "1",
                "2",
                "3"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4",
            "ipTags": [
          {
           "ipTagType": "RoutingPreference",
            "tag": "Internet"
           }
         ]
      }
    }
```

### <a name="tier"></a>层

公共 IP 地址与单个区域相关联。 全局层可跨多个区域扩展 IP 地址。 跨区域负载均衡器前端需要全局层。  

有关详细信息，请参阅[跨区域负载均衡器](../../load-balancer/cross-region-overview.md)。

若要使用标准全局公共 IPv4 地址，模板部分应类似于：

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP-Global",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard",
            "tier": "Global"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```

## <a name="additional-information"></a>其他信息 

有关本文中列出的公共 IP 属性的详细信息，请参阅 [管理公共 IP 地址](virtual-network-public-ip-address.md#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](../../virtual-network/associate-public-ip-address-vm.md#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
