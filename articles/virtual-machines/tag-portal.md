---
title: 如何使用 Azure 门户标记 VM
description: 了解如何使用 Azure 门户标记虚拟机。
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 543317b2e1dcc0c55ac970212612f5266e5f84aa
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695359"
---
# <a name="tagging-a-vm-using-the-portal"></a>使用门户标记 VM

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文介绍如何使用门户将标记添加到 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。


1. 在门户中导航到你的 VM。
1. 在“概要”中，选择“单击此处以添加标记”。

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="VM 页的“概要”部分的屏幕截图。":::

1. 为“名称”和“值”添加值，然后选择“保存”。

    :::image type="content" source="media/tag/key-value.png" alt-text="用于将键值对添加为标记的页的屏幕截图。":::

### <a name="next-steps"></a>后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。
- 要了解标记如何帮助你管理 Azure 资源的使用，请参阅[了解 Azure 帐单](../cost-management-billing/understand/review-individual-bill.md)。
