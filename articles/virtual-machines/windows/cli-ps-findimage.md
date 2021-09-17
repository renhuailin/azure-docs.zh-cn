---
title: 使用 PowerShell 查找并使用市场购买计划信息
description: 使用 Azure PowerShell 查找映像 URN 和购买计划参数，如发布者、产品/服务、SKU 和市场 VM 映像的版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: f13c13ec30972c6129f794f831d3f937ca98c9dc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698336"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>使用 Azure PowerShell 查找并使用 Azure 市场 VM 映像

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集


本文介绍如何使用 Azure PowerShell 在 Azure 市场中查找 VM 映像。 然后，可以在创建 VM 时指定市场映像和计划信息。

还可以使用 [Azure 市场](https://azuremarketplace.microsoft.com/)或 [Azure CLI](../linux/cli-ps-findimage.md) 浏览可用的映像和产品/服务。 

## <a name="terminology"></a>术语

Azure 中的市场映像具有以下属性：

* **发布者**：创建映像的组织。 示例：Canonical、MicrosoftWindowsServer
* **产品/服务**：发布者创建的一组相关映像的名称。 示例：UbuntuServer、WindowsServer
* **SKU**：产品/服务的实例，例如分发的主要版本。 示例：18.04-LTS、2019-Datacenter
* **版本**：映像 SKU 的版本号。 

可以单独传递这些值，或将这些值作为映像 URN 传递，并将它们用冒号 (:) 隔开。 例如：发布者:产品/服务:SKU:版本   。 可将 URN 中的版本号替换为 `latest`，以使用最新版本的映像。 

如果映像发布者提供附加许可条款和购买条款，则你必须接受这些条款才能使用映像。  有关详细信息，请参阅[接受购买计划条款](#accept-purchase-plan-terms)。

## <a name="list-images"></a>列出映像

可以使用 PowerShell 缩小映像列表范围。 替换变量的值以满足你的需求。

1. 使用 [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) 列出映像发布者。
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. 对于给定的发布者，请使用 [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) 列出其产品/服务。
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. 对于给定的发布者和产品/服务，请使用 [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) 列出可用的 SKU。
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. 对于 SKU，请使用 [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) 列出映像的版本。

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    如果要使用最新的映像，而不是特定的较旧版本，也可以使用 `latest`。


现在可以将所选发布者、产品/服务、SKU 和版本合并到 URN 中（由“:”分隔的值）。 使用 [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet 创建 VM 时，使用 `-Image` 参数传递此 URN。 还可以将 URN 中的版本号替换为 `latest`，以获取最新版本的映像。

如果使用资源管理器模板部署 VM，请在 `imageReference` 属性中单独设置映像参数。 请参阅[模板参考](/azure/templates/microsoft.compute/virtualmachines)。


## <a name="view-purchase-plan-properties"></a>查看购买计划属性

Azure 市场中的某些 VM 映像具有附加许可条款和购买条款，你必须接受这些条款，然后才能以编程方式部署这些映像。 对于每个订阅，都需要接受映像的条款。

若要查看映像的购买计划信息，请运行 `Get-AzVMImage` cmdlet。 如果输出中的 `PurchasePlan` 属性不是 `null`，则映像有条款，在以编程方式部署前需要接受该条款。  

例如，*Windows Server 2016 Datacenter* 映像没有附加条款，因此，`PurchasePlan` 信息为 `null`：

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

输出将类似于以下内容：

```output
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

以下示例显示了适用于 *Data Science Virtual Machine - Windows 2016* 映像的类似命令，该映像具有以下 `PurchasePlan` 属性：`name`、`product` 和 `publisher`。 某些映像还具有 `promotion code` 属性。 若要部署此映像，请参阅以下部分，以接受条款并启用编程式部署。

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

输出将类似于以下内容：

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

若要查看许可条款，请使用 [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) cmdlet 并传入购买计划参数。 输出会提供指向市场映像条款的链接，并显示是否以前已接受条款。 请务必使用字母全部小写的参数值。

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

输出将类似于以下内容：

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>接受购买计划条款

使用 [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) cmdlet 接受或拒绝条款。 对于映像的每个订阅，只需接受条款一次。 请务必使用字母全部小写的参数值。 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```


## <a name="create-a-new-vm-from-a-marketplace-image"></a>从市场映像创建新 VM

如果已有要使用的映像的相关信息，可以将该信息传递到 [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) cmdlet，以将映像信息添加到 VM 配置。 请参阅后续部分，了解如何搜索和列出市场中可用的映像。

某些付费映像还要求使用 [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) 提供购买计划信息。 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

然后将 VM 配置与其他配置对象一起传递给 `New-AzVM` cmdlet。 有关将 VM 配置与 PowerShell 配合使用的详细示例，请参阅此[脚本](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)。

如果收到有关接受映像条款的消息，请参阅前面的[接受购买计划条款](#accept-purchase-plan-terms)部分。

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>使用购买计划信息从 VHD 创建新的 VM

如果你有使用 Azure 市场映像创建的现有 VHD，则在从该 VHD 创建新的 VM 时，可能需要提供购买计划信息。

如果你仍有原始 VM 或从同一映像创建的其他 VM，则可以使用 Get-AzVM 从其中获取计划名称、发布者和产品信息。 此示例获取 myResourceGroup 资源组中名为 myVM 的 VM，然后显示购买计划信息 。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

如果在删除原始 VM 之前未获得计划信息，则可提交[支持请求](https://ms.portal.azure.com/#create/Microsoft.Support)。 他们将需要 VM 名称、订阅 ID 和删除操作的时间戳。

若要使用 VHD 创建 VM，请参阅[从专用 VHD 创建 VM](create-vm-specialized.md) 一文，并添加一行，使用 [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) 将计划信息添加到 VM 配置，如下所示：

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>后续步骤

若要使用基本映像信息通过 `New-AzVM` cmdlet 快速创建虚拟机，请参阅[使用 PowerShell 创建 Windows 虚拟机](quick-create-powershell.md)。

有关使用 Azure 市场映像在共享映像库中创建自定义映像的更多信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](../marketplace-images.md)。
