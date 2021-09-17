---
title: 教程 - 在私有云中缩放群集
description: 在本教程中，使用 Azure 门户缩放 Azure VMware 解决方案私有云。
ms.topic: tutorial
ms.date: 08/03/2021
ms.openlocfilehash: 47bc7d12a025104df48d5a55af4abf342759696e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638798"
---
# <a name="tutorial-scale-clusters-in-a-private-cloud"></a>教程：在私有云中缩放群集

若要充分利用 Azure VMware 解决方案私有云体验，请缩放群集和主机，使之反映你对计划内工作负载的需求。 可以根据应用程序工作负载的需求，缩放私有云中的群集和主机。  应该逐一解决特定服务的性能和可用性限制。 

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

在本教程中，你将使用 Azure 门户执行以下操作：

> [!div class="checklist"]
> * 向现有私有云添加群集
> * 向现有群集添加主机

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个现有私有云。 如果未创建私有云，请按照[创建私有云教程](tutorial-create-private-cloud.md)来进行创建。 

## <a name="add-a-new-cluster"></a>添加新群集

1. 在 Azure VMware 解决方案私有云中，在“管理”下，选择“群集” > “添加群集”。

   :::image type="content" source="media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="屏幕截图：如何将群集添加到 Azure VMware 解决方案私有云。" border="true":::

1. 使用滑块选择主机数，然后选择“保存”。

   :::image type="content" source="media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="屏幕截图：如何配置新群集。" border="true":::

   随即开始部署新群集。

## <a name="scale-a-cluster"></a>缩放群集 

1. 在 Azure VMware 解决方案私有云中，在“管理”下，选择“群集” 。

1. 选择要缩放的群集，选择“更多”(...)，然后选择“编辑” 。

   :::image type="content" source="media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="屏幕截图：在何处编辑现有群集。" border="true":::

1. 使用滑块选择主机数，然后选择“保存”。

   将开始向群集添加主机。

## <a name="next-steps"></a>后续步骤

如果需要另一个 Azure VMware 解决方案私有云，请[创建另一个私有云](tutorial-create-private-cloud.md)，遵循相同的网络先决条件、群集和主机限制。

<!-- LINKS - external-->

<!-- LINKS - internal -->
