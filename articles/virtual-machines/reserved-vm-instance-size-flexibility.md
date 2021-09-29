---
title: 虚拟机大小灵活性 - Azure 虚拟机预留实例
description: 了解购买虚拟机预留实例时哪些大小系列有预留折扣。
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.openlocfilehash: 8d76bdbf17fbc03b9c99a2c08c56de1faf2ffea9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215427"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>虚拟机预留实例的虚拟机大小灵活性

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

购买虚拟机预留实例时，可以选择优化实例大小灵活性或容量优先级。 有关设置或更改虚拟机预留实例的优化设置的详细信息，请参阅[更改虚拟机预留实例的优化设置](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。

使用已优化实例大小灵活性的虚拟机预留实例时，所购买的预留可以应用到同一实例大小灵活性组中的虚拟机 (VM) 大小。 例如，如果购买在 DSv2 系列中列出的某个 VM 大小（例如 Standard_DS3_v2）的预留，则预留折扣可以应用到同一实例大小灵活性组中列出的其他大小：

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

但是，该预留折扣不适用于在其他实例大小灵活性组中列出的 VM 大小，例如高内存 DSv2 系列中的 SKU：Standard_DS11_v2、Standard_DS12_v2 等。

在实例大小灵活性组中，预留折扣适用于多少 VM 取决于在购买预留时选取的 VM 大小。 也取决于运行的 VM 的大小。 比率列将比较该实例大小灵活性组中每个 VM 大小的相对占用情况。 请使用比率值来计算如何将预留折扣应用到运行的 VM。

## <a name="examples"></a>示例

以下示例使用 DSv2 系列表中的大小和比率。

你购买一个大小为 Standard_DS4_v2 的虚拟机预留实例，其中的比率或相对于该系列中其他大小的占用量为 8。

- 方案 1：运行 8 个比率为 1 的 Standard_DS1_v2 大小的 VM。 预留折扣适用于这所有 8 个 VM。
- 方案 2：运行 2 个 Standard_DS2_v2 大小的 VM，每个的比率为 2。 另外，运行一个比率为 4 的 Standard_DS3_v2 大小的 VM。 总占用量为 2+2+4=8。 因此，预留折扣适用于这所有 3 个 VM。
- 方案 3：运行 1 个比率为 16 的 Standard_DS5_v2 大小的 VM。 预留折扣适用于该 VM 的半数计算费用。
- 方案 4：运行 1 个比率为 16 的 Standard_DS5_v2 大小的 VM，并额外购买一个比例为 8 的 Standard_DS4_v2 大小的预留项。 这两个预留项合并，并将折扣应用到整个 VM。

以下部分介绍了在购买优化的可以灵活调整实例大小的虚拟机预留实例时，同一大小系列组中有哪些大小。

## <a name="instance-size-flexibility-ratio-for-vms"></a>虚拟机的实例大小灵活性比率 

下面的 CSV 包含实例大小灵活性组、ArmSkuName 和比率。  

[实例大小灵活性比率](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Azure 保持更新链接和架构，以便你可以通过编程方式使用该文件。

## <a name="view-vm-size-recommendations"></a>查看虚拟机大小建议

Azure 会在你购买时显示虚假机大小推荐。 若要查看最小大小建议，请选择“按最小大小分组”。

:::image type="content" source="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" alt-text="显示建议数量的屏幕截图。" lightbox="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" :::

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[什么是 Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)。
