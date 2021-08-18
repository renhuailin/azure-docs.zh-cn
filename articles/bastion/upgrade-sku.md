---
title: 升级 SKU
titleSuffix: Azure Bastion
description: 了解如何将层从基本 SKU 更改为标准 SKU。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 8a2c35f2ef4dafe9024deab1c26116c0ca20e01e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733297"
---
# <a name="upgrade-a-sku-preview"></a>升级 SKU（预览）

本文帮助你从基本层 (SKU) 升级到标准层。 升级后，如果不删除和重新配置 Bastion，就无法恢复到基本 SKU。 在预览期间，仅可在 Azure 门户中配置此设置。 有关主机缩放的详细信息，请参阅[配置设置 - SKU](configuration-settings.md#skus)。 

## <a name="configuration-steps"></a>配置步骤

[!INCLUDE [Azure Bastion preview portal](../../includes/bastion-preview-portal-note.md)]

1. 在 Azure 门户中，导航到你的 Bastion 主机。
1. 在“配置”页上，对于“层”，从下拉列表中选择“标准”  。

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="选择了“标准”的层选择下拉列表屏幕截图。" lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. 单击“应用”以应用更改。

## <a name="next-steps"></a>后续步骤

* 有关其他配置信息，请参阅[配置设置](configuration-settings.md)。
* 阅读 [Bastion 常见问题解答](bastion-faq.md)。
