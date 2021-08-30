---
title: 升级公共 IP 地址：Azure PowerShell
description: 本文介绍如何使用 Azure PowerShell 升级基本 SKU 公共 IP 地址。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 396122ce5200f6b0ed8fe95eb402c9d60947d5fc
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438985"
---
# <a name="upgrade-a-public-ip-address-using-azure-powershell"></a>使用 Azure PowerShell 升级公共 IP 地址

Azure 公共 IP 地址是使用基本或标准 SKU 创建的。 SKU 决定了地址功能，包括分配方法、功能支持和可以关联的资源。 

本文将介绍如何使用 Azure PowerShell 将静态基本 SKU 公共 IP 地址升级为标准 SKU。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 订阅中的静态基本 SKU 公共 IP 地址。 有关详细信息，请参阅[创建公共 IP 地址 - Azure 门户](create-public-ip-portal.md#create-a-basic-sku-public-ip-address)。
* 本地安装的 Azure PowerShell 或 Azure Cloud Shell

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="upgrade-public-ip-address"></a>升级公共 IP 地址

在本部分，你将使用 Azure CLI 将静态基本 SKU 公共 IP 升级到标准SKU。

```azurepowershell-interactive
### Place the public IP address into a variable. ###
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
$pubIP = Get-AzPublicIpAddress @ip

### Set the SKU to standard. ###
$pubIP.Sku.Name = 'Standard'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

```
> [!NOTE]
> 要升级的基本公共 IP 必须具有静态分配类型。 如果尝试升级动态分配的 IP 地址，会收到一条警告，指示无法升级 IP。

> [!WARNING]
> 将基本公共 IP 升级为标准 SKU 的过程不可逆。 从基本 SKU 升级到标准 SKU 的公共 IP 仍然没有保证的[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)。

## <a name="verify-upgrade"></a>验证升级

在本部分中，将验证公共 IP 地址现在是否为标准 SKU。

```azurepowershell-interactive
### Place the public IP address into a variable. ###
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
$pubIP = Get-AzPublicIpAddress @ip

### Display setting. ####
$pubIP.Sku.Name
```
该命令应显示“标准”。

## <a name="next-steps"></a>后续步骤

本文中，已经将基本 SKU 公共 IP 地址 升级为标准 SKU。

有关 Azure 中公共 IP 地址的详细信息，请参阅：

- [Azure 中的公共 IP 地址](public-ip-addresses.md)
- [创建公共 IP - Azure 门户](create-public-ip-portal.md)

