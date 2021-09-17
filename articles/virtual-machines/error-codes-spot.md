---
title: Azure 现成虚拟机和规模集实例的错误代码
description: 了解在使用 Azure 现成虚拟机和规模集实例时可能出现的错误代码。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: f035d7813dfcf84d21e5913fa4626188e125db9a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691083"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Azure 现成虚拟机和规模集的错误消息

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

下面是使用 Azure 现成虚拟机和规模集时可能会出现的一些错误代码。


| 密钥 | 消息 | 说明 |
|-----|---------|-------------|
| SkuNotAvailable | 资源“\<resource\>”的请求层当前在订阅“\<subscriptionID\>”的位置“\<location\>”不可用。 请尝试使用另一层或部署到其他位置。 | 此位置没有足够的 Azure 现成虚拟机容量用于创建 VM 或规模集实例。 |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  只能在 Azure 现成虚拟机上设置逐出策略。 | 此 VM 不是 Azure 现成虚拟机，因此无法设置逐出策略。 |
| AzureSpotVMNotSupportedInAvailabilitySet  |  可用性集不支持 Azure 现成虚拟机。 | 需要选择使用 Azure 现成虚拟机或使用可用性集中的 VM，而不能同时选择两者。 |
| AzureSpotFeatureNotEnabledForSubscription  |  未在订阅中启用 Azure 现成虚拟机功能。 | 请使用支持 Azure 现成虚拟机的订阅。 |
| VMPriorityCannotBeApplied  |  指定的优先级值“{0}”无法应用于虚拟机“{1}”，因为在创建虚拟机时未指定优先级。 | 请在创建 VM 时指定优先级。 |
| SpotPriceGreaterThanProvidedMaxPrice  |  无法执行操作“{0}”，因为提供的最大价格“{1} 美元”低于 Azure 现成虚拟机大小“{3}”的当前现成 VM 价格“{2} 美元”。 | 请选择更高的最大价格。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 或 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 的定价信息。|
| MaxPriceValueInvalid  |  最大价格值无效。 仅支持使用 -1 或大于零的小数作为最大价格值。 最大价格值 -1 表示出于价格原因而不逐出 Azure 现成虚拟机。 | 请输入有效的最大价格。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 或 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 的定价。 |
| MaxPriceChangeNotAllowedForAllocatedVMs | 当前分配了 VM“{0}”时不允许更改最大价格。 请解除分配，然后重试。 | 请停止/解除分配 VM，以便可以更改最大价格。 |
| MaxPriceChangeNotAllowed | 不允许更改最大价格。 | 不能更改此 VM 的最大价格。 |
| AzureSpotIsNotSupportedForThisAPIVersion  |  此 API 版本不支持 Azure 现成虚拟机。 | API 版本需是 2019-03-01。 |
| AzureSpotIsNotSupportedForThisVMSize  |  此 VM 大小 {0} 不支持 Azure 现成虚拟机。 | 选择其他 VM 大小。 有关详细信息，请参阅 [Azure 现成虚拟机](./spot-vms.md)。 |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  只有 Azure 现成虚拟机支持最大价格。 | 有关详细信息，请参阅 [Azure 现成虚拟机](./spot-vms.md)。 |
| MoveResourcesWithAzureSpotVMNotSupported  |  “移动资源”请求包含 Azure 现成虚拟机。 不支持。 检查虚拟机 ID 的错误详细信息。 | 不能移动 Azure 现成虚拟机。 |
| MoveResourcesWithAzureSpotVmssNotSupported  |  “移动资源”请求包含 Azure 现成虚拟机规模集。 不支持。 检查虚拟机规模集 ID 的错误详细信息。 | 不能移动 Azure 现成虚拟机规模集实例。 |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | 采用 VM 业务流程模式的虚拟机规模集不支持 Azure 现成虚拟机。 | 将业务流程模式设置为虚拟机规模集，以便能够使用 Azure 现成虚拟机实例。 |


**后续步骤** 有关详细信息，请参阅 [现成虚拟机](./spot-vms.md)。