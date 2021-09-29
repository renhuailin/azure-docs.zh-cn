---
title: Azure 中的 Windows VM 概述
description: Azure 中的 Windows 虚拟机概述。
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 51a8bde53cf2e75501eadd185e0754ec6f0f673c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803708"
---
# <a name="windows-virtual-machines-in-azure"></a>Azure 中的 Windows 虚拟机

适用于：:heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

Azure 虚拟机 (VM) 是 Azure 提供的多种[可缩放按需分配计算资源](/azure/architecture/guide/technology-choices/compute-decision-tree)之一。 通常情况下，如果需要以更大的力度（相对于其他控制选项）控制计算环境，则应选择 VM。 本文介绍创建 VM 之前的注意事项，以及 VM 的创建方法和管理方式。

使用 Azure VM 可以灵活进行虚拟化，而无需购买和维护运行 VM 的物理硬件。 不过，仍然需要通过执行任务来维护 VM，例如，配置、修补和安装在 VM 上运行的软件。

可通过多种方式使用 Azure 虚拟机。 一些示例如下：

* **开发和测试** – 在 Azure VM 上，可以快速轻松地创建具有特定配置的计算机来满足编写代码和应用程序测试的需要。
* **云中的应用程序** – 由于应用程序的需求会不断变化，在 Azure 中的 VM 上运行应用程序可能会节省成本。 使用 VM 时，需要支付额外的费用；不需要 VM 时，则需要关闭它们。
* **扩展的数据中心** – Azure 虚拟网络中的虚拟机可以轻松连接到组织的网络。

应用程序使用的 VM 数可根据你的需要纵向扩展和横向扩展为任意数目。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>在创建 VM 之前需要考虑哪些因素？
在 Azure 中构建应用程序基础结构时，始终要考虑多种[设计注意事项](/azure/architecture/reference-architectures/n-tier/windows-vm)。 在开始之前，必须考虑到 VM 的以下重要方面：

* 应用程序资源的名称
* 资源的存储位置
* VM 的大小
* 可以创建的 VM 数目上限
* VM 运行的操作系统
* VM 在启动后的配置
* VM 所需的相关资源

### <a name="locations"></a>位置
在 Azure 中创建的所有资源分布在世界各地的多个[地理区域](https://azure.microsoft.com/regions/)。 创建 VM 时，区域通常称为“位置”。 位置指定 VM 虚拟硬盘的存储位置。

下表显示了获取可用位置列表的一些方法。

| 方法 | 说明 |
| --- | --- |
| Azure 门户 |创建 VM 时，可从列表中选择位置。 |
| Azure PowerShell |使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 命令。 |
| REST API |使用[列出位置](/rest/api/resources/subscriptions/listlocations)操作。 |
| Azure CLI |使用 [az account list-locations](/cli/azure/account) 操作。 |

## <a name="availability"></a>可用性
Azure 宣布了行业领先的单实例虚拟机服务级别协议：可用性达到 99.9%（前提是为所有磁盘使用高级存储部署 VM）。  为了使部署符合标准 99.95% 的 VM 服务级别协议，仍需要在可用性集中部署两个或更多个运行工作负荷的 VM。 可用性集可确保 VM 分布在 Azure 数据中心内的多个容错域，并使用不同的维护时段部署到主机。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。


## <a name="vm-size"></a>VM 大小
VM 的[大小](../sizes.md)由所要运行的工作负荷决定。 然后，选择的大小决定了处理能力、内存和存储容量等因素。 Azure 提供各种大小来支持多种类型的用途。

Azure 根据 VM 的大小和操作系统按[小时价格](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)计费。 对于不足一小时的部分，Azure 仅根据使用的分钟数计费。 存储将另行定价和收费。

## <a name="vm-limits"></a>VM 限制
订阅附带默认的[配额限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)，在为项目部署大量 VM 时，这些限制可能会造成影响。 每个订阅的当前限制是每区域 20 个 VM。 可以[开具支持票证](../../azure-portal/supportability/regional-quota-requests.md)来请求提高限制

### <a name="operating-system-disks-and-images"></a>操作系统磁盘和映像
虚拟机使用[虚拟硬盘 (VHD)](../managed-disks-overview.md) 来存储其操作系统 (OS) 和数据。 VHD 还可用于存储映像，可以选择某个映像来安装 OS。 

Azure 提供许多[市场映像](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1)来配合各种版本和类型的 Windows Server 操作系统使用。 市场映像由映像发布者、产品、SKU 和版本（通常指定为最新版本）标识。 仅支持 64 位操作系统。 有关受支持的来宾操作系统、角色和功能的详细信息，请参阅 [Microsoft 服务器软件对 Microsoft Azure 虚拟机的支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

下表显示了查找映像信息的一些方法。

| 方法 | 说明 |
| --- | --- |
| Azure 门户 |选择要使用的映像时，系统会自动指定值。 |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API |[列出映像发布者](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[列出映像产品](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[列出映像 SKU](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](/cli/azure/vm/image) --location *location*<BR>[az vm image list-offers](/cli/azure/vm/image) --location *location* --publisher *publisherName*<BR>[az vm image list-skus](/cli/azure/vm) --location *location* --publisher *publisherName* --offer *offerName*|

可以选择[上传并使用自己的映像](upload-generalized-managed.md)，在这种情况下，无需使用发布者名称、产品和 SKU。

### <a name="extensions"></a>扩展
VM [扩展](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json)通过部署后的配置和自动化任务来增加 VM 的功能。

可以使用扩展完成以下常见任务：

* **运行自定义脚本** – 预配 VM 时，[自定义脚本扩展](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json)可以通过运行脚本，帮助在 VM 上配置工作负荷。
* **部署和管理配置** – 可以借助 [PowerShell Desired State Configuration (DSC) 扩展](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)在 VM 上设置用于管理配置和环境的 DSC。
* **收集诊断数据** – 可以借助 [Azure 诊断扩展](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)将 VM 配置为收集诊断数据，然后，可以使用这些数据监视应用程序的运行状况。

### <a name="related-resources"></a>相关资源
VM 使用下表中的资源，创建 VM 时，这些资源必须存在，否则要予以创建。

| 资源 | 必选 | 说明 |
| --- | --- | --- |
| [资源组](../../azure-resource-manager/management/overview.md) |是 |VM 必须包含在资源组中。 |
| [存储帐户](../../storage/common/storage-account-create.md) |是 |VM 需要使用存储帐户来存储其虚拟硬盘。 |
| [虚拟网络](../../virtual-network/virtual-networks-overview.md) |是 |VM 必须是虚拟网络的成员。 |
| [公共 IP 地址](../../virtual-network/public-ip-addresses.md) |否 |可以向 VM 分配一个公共 IP 地址，以便远程访问它。 |
| [网络接口](../../virtual-network/virtual-network-network-interface.md) |是 |VM 需要使用网络接口在网络中通信。 |
| [数据磁盘](attach-managed-disk-portal.md) |否 |VM 可以包含数据磁盘，以便扩展存储功能。 |


## <a name="data-residency"></a>数据驻留

在 Azure 中，将客户数据存储到一个区域的功能目前仅适用于亚太地区的东南亚区域（新加坡）和巴西地区的巴西南部区域（圣保罗州）。 对于其他所有区域，客户数据存储在以下地域。 有关详细信息，请参阅[信任中心](https://azure.microsoft.com/global-infrastructure/data-residency/)。


## <a name="next-steps"></a>后续步骤

创建第一个 VM！

- [门户](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure CLI](quick-create-cli.md)