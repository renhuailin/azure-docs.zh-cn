---
title: 添加缩放单元以进行主机缩放
titleSuffix: Azure Bastion
description: 了解如何向 Azure Bastion 添加额外的实例（缩放单元）。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: d5087994a72b52ce091dac98987b8e0bc60e287e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224741"
---
# <a name="configure-host-scaling-preview"></a>配置主机缩放（预览版）

本文帮助你向 Azure Bastion 添加额外的缩放单元（实例），以容纳其他并发客户端连接。 在预览期间，仅可在 Azure 门户中配置此设置。 有关主机缩放的详细信息，请参阅[配置设置](configuration-settings.md#instance)。 

## <a name="configuration-steps"></a>配置步骤

1. 登录 [Azure 门户](https://ms.portal.azure.com)。
1. 在 Azure 门户中，导航到你的 Bastion 主机。
1. 主机缩放实例计数需要标准层。 在“配置”页上，验证“层”中为“标准”层  。 如果为“基本”层，请从下拉列表中选择“标准”。 

   :::image type="content" source="./media/configure-host-scaling/select-sku.png" alt-text="“选择层”的屏幕截图。" lightbox="./media/configure-host-scaling/select-sku.png":::
1. 要配置缩放，请调整实例计数。 每个实例都是一个缩放单元。

   :::image type="content" source="./media/configure-host-scaling/instance-count.png" alt-text="“实例计数”滑块的屏幕截图。" lightbox="./media/configure-host-scaling/instance-count.png":::
1. 单击“应用”以应用更改。

## <a name="next-steps"></a>后续步骤

* 有关其他信息，请参阅 [Bastion 常见问题解答](bastion-faq.md)。
