---
title: 升级公共 IP 地址
titleSuffix: Azure Virtual Network
description: 将公共 IP 地址从 "基本" 升级到 "标准"。
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 6cabc340c0be347165a3e506703a6277f7eb1cea
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503927"
---
# <a name="upgrade-public-ip-addresses"></a>升级公共 IP 地址

使用 SKU （基本或标准）创建 Azure 公共 IP 地址，该地址可确定其功能的各个方面 (包括分配方法、跨可用性区域的使用情况以及可以与) 关联的资源。 

本文介绍了以下方案：
* 如何使用门户、PowerShell 或 CLI 将基本 SKU 公共 IP 升级到标准 SKU 公共 IP () 
* 如何将经典 Azure 保留 IP 迁移到 Azure 资源管理器基本 SKU 公共 IP

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>从基本 SKU 升级到标准 SKU 的公共 IP 地址

若要升级公共 IP，它不得与任何资源相关联 (参阅 [此页](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) ，以了解有关如何取消关联公共 ip) 的详细信息。

>[!IMPORTANT]
>从基本到标准 SKU 升级的公共 Ip 将继续没有 [可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)。  这意味着，不能将它们与区域冗余的 Azure 资源关联，也不能与提供此项的区域中的预指定区域关联。

---
# <a name="basic-to-standard---powershell"></a>[**基本到标准-PowerShell**](#tab/option-upgrade-powershell)

下面的示例假定之前创建基本 SKU 公共 IP，并使用在 **myResourceGroup** 中具有基本公共 IP **myBasicPublicIP** 的 [此页面](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic)上提供的示例。

若要升级 IP，只需使用 PowerShell 执行以下命令。  注意如果已静态分配 IP 地址，则可以跳过此部分。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**基本到标准-CLI**](#tab/option-upgrade-cli)

下面的示例假定之前创建基本 SKU 公共 IP，并使用在 **myResourceGroup** 中具有基本公共 IP **myBasicPublicIP** 的 [此页面](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic)上提供的示例。

若要升级 IP，只需使用 Azure CLI 执行以下命令。  注意如果已静态分配 IP 地址，则可以跳过此部分。

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>升级 (将经典保留 IP 迁移) 为静态公共 IP

若要利用 Azure 资源管理器中的新功能，可以将现有的公共静态 IP 地址（称为保留 Ip）从经典模型迁移到新式 Azure 资源管理器模型。  迁移的公共 IP 将是基本的 SKU 类型。


---

# <a name="reserved-to-basic---powershell"></a>[**保留到基本-PowerShell**](#tab/option-migrate-powershell)

以下示例假设先前在 **myResourceGroup** 中创建了经典 Azure 保留 IP **myReservedIP** 。 迁移的另一个先决条件是确保 Azure 资源管理器订阅已注册迁移。 本 [页面](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-ps)的步骤3和4详细介绍了这一情况。

若要迁移保留 IP，请使用 PowerShell 执行以下命令。  注意如果 IP 地址不与任何服务相关联 (下面有一个名为 **myService**) 的服务，则可以跳过该步骤。

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
前面的命令显示阻止迁移的任何警告和错误。 如果验证成功，则可以继续执行以下步骤来准备和提交迁移：
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Azure 资源管理器中的新资源组是使用以上示例中的已迁移保留 IP (名称创建的，它将是 **MyReservedIP 迁移的** 资源组) 。

# <a name="reserved-to-basic---cli"></a>[**保留到基本-CLI**](#tab/option-migrate-cli)

以下示例假设先前在 **myResourceGroup** 中创建了经典 Azure 保留 IP **myReservedIP** 。 迁移的另一个先决条件是确保 Azure 资源管理器订阅已注册迁移。 本 [页面](https://docs.microsoft.com/azure/virtual-machines/linux/migration-classic-resource-manager-cli)的步骤3和4详细介绍了这一情况。

若要迁移保留 IP，请使用 Azure CLI 执行以下命令。  注意如果 IP 地址不与任何服务相关联 (下面有一个名为 **myService** 的服务和部署 **myDeployment**) ，则可以跳过该步骤。

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
前面的命令显示阻止迁移的任何警告和错误。 如果验证成功，则可以继续执行以下步骤来准备和提交迁移：
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Azure 资源管理器中的新资源组是使用以上示例中的已迁移保留 IP (名称创建的，它将是 **MyReservedIP 迁移的** 资源组) 。

---

## <a name="limitations"></a>限制

* 为了升级基本的公共 IP，它无法与任何 Azure 资源关联。  请查看 [此页](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) ，以了解有关如何解除公共 ip 关联的详细信息。  同样，为了迁移保留 IP，它不能与任何云服务关联。  请查看 [此页](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm) ，以了解有关如何解除保留 ip 关联的详细信息。  
* 从基本 SKU 升级到标准 SKU 的公共 Ip 将继续没有 [可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) ，因此不能与区域冗余或区域性的 Azure 资源关联。  请注意，这仅适用于提供可用性区域的区域。
* 不能从标准版降级到基本版。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的 [公共 ip 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) ，包括 SKU 类型和 [公共 ip 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)之间的差异。
- 了解如何 [从基本版升级到标准版负载均衡](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard)器。
- 了解 [经典 Azure 保留 ip](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) ，并 [将经典资源迁移到 Azure 资源管理器](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)。
