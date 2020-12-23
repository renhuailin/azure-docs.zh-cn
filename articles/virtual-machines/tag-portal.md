---
title: 如何使用 Azure 门户标记 VM
description: 了解如何使用 Azure 门户为虚拟机添加标签。
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594923"
---
# <a name="tagging-a-vm-using-the-portal"></a>使用门户标记 VM

本文介绍如何使用门户将标记添加到 VM。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 50 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。


1. 导航到门户中的 VM。
1. 在 " **Essentials** " 中，选择 **"单击此处添加标记"** 。

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="VM 页的 &quot;概要&quot; 部分的屏幕截图。":::

1. 为 " **名称** " 和 " **值** " 添加值，然后选择 " **保存** "。

    :::image type="content" source="media/tag/key-value.png" alt-text="用于添加作为标记的键值对的页面屏幕截图。":::



后续步骤

- 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)和 [使用标记来组织 Azure 资源](../azure-resource-manager/management/tag-resources.md)。
- 要了解标记如何帮助你管理 Azure 资源的使用，请参阅 [Understanding your Azure Bill](../cost-management-billing/understand/review-individual-bill.md)（了解 Azure 帐单）和 [Gain insights into your Microsoft Azure resource consumption](../cost-management-billing/manage/usage-rate-card-overview.md)（深入了解 Microsoft Azure 资源消耗）。