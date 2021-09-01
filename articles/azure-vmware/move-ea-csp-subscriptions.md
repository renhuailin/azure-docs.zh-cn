---
title: 将 Azure VMware 解决方案订阅移动到另一个订阅
description: 本文介绍如何将 Azure VMware 解决方案订阅移动到另一个订阅。 可能出于各种原因（例如计费）而移动资源。
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 04/26/2021
ms.openlocfilehash: b4541bf1cf13a8b4c090365c31c2403685a69c7f
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322228"
---
# <a name="move-azure-vmware-solution-subscription-to-another-subscription"></a>将 Azure VMware 解决方案订阅移动到另一个订阅

本文介绍如何将 Azure VMware 解决方案订阅移动到另一个订阅。 可能出于各种原因（例如计费）而移动订阅。

## <a name="prerequisites"></a>先决条件
你应至少对源订阅和目标订阅都具有参与者权限 。 

>[!IMPORTANT]
>VNet 和 VNet 网关无法从一个订阅移到另一个订阅。 此外，移动订阅对管理和工作负荷（如 vCenter、NSX 和工作负荷虚拟机）没有影响。

## <a name="prepare-and-move"></a>准备并移动 

1. 在 Azure 门户中，选择要移动的私有云。

   :::image type="content" source="media/move-subscriptions/source-subscription-id.png" alt-text="显示所选私有云的概述详细信息的屏幕截图。":::

1. 在命令提示符下，对组件和工作负载进行 ping 操作，验证它们是否正在从同一订阅进行 ping 操作。  

   :::image type="content" source="media/move-subscriptions/verify-components-workloads.png" alt-text="显示 ping 命令和 ping 结果的屏幕截图。":::

1. 选择“订阅(更改)”链接。

   :::image type="content" source="media/move-subscriptions/private-cloud-overview-subscription-id.png" alt-text="此屏幕截图显示了私有云详细信息。"::: 

1. 提供“目标”的订阅详细信息，然后选择“下一步”。

   :::image type="content" source="media/move-subscriptions/move-resources-subscription-target.png" alt-text="目标资源的屏幕截图。":::

1. 确认要移动的选定资源的有效性。 在验证过程中，你将看到状态为“待验证”。 

   :::image type="content" source="media/move-subscriptions/pending-move-resources-subscription-target.png" alt-text="此屏幕截图显示了要移动的资源。":::

1. 验证成功后，选择“下一步”开始迁移私有云。

   :::image type="content" source="media/move-subscriptions/move-resources-succeeded.png" alt-text="显示“已成功”验证状态的屏幕截图。":::

1. 选中表明你了解关联的工具和脚本在更新为使用新资源 ID 之前将无法使用的复选框。 然后选择“移动”。

   :::image type="content" source="media/move-subscriptions/review-move-resources-subscription-target.png" alt-text="显示要移动的选定资源的摘要的屏幕截图。":::

## <a name="verify-the-move"></a>验证移动情况

资源移动完成后，会显示一条通知。 

:::image type="content" source="media/move-subscriptions/notification-move-resources-subscription-target.png" alt-text="资源移动完成后的通知的屏幕截图。":::

新订阅会显示在私有云的“概述”中。

:::image type="content" source="media/move-subscriptions/moved-subscription-target.png" alt-text="此屏幕截图显示了新订阅。":::

## <a name="next-steps"></a>后续步骤
了解有关以下方面的详细信息：

- [跨区域移动 Azure VMware 解决方案](move-azure-vmware-solution-across-regions.md)
- [网络资源移动指南](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [针对虚拟机的移动指南](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [针对应用服务资源的移动指南](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)
