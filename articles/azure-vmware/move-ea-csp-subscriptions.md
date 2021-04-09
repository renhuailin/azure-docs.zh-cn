---
title: 移动 EA 和 CSP Azure VMware 解决方案订阅
description: 了解如何将私有云从一个订阅移到另一个订阅。 可能出于各种原因（例如计费）而进行移动。
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103554985"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>移动 EA 和 CSP Azure VMware 解决方案订阅

本文介绍如何将私有云从一个订阅移到另一个订阅。 可能出于各种原因（例如计费）而进行移动。 

>[!IMPORTANT]
>你应该至少对源订阅和目标订阅都具有参与者权限。 VNet 和 VNet 网关无法从一个订阅移到另一个订阅。 此外，移动订阅对管理和工作负荷（如 vCenter、NSX 和工作负荷虚拟机）没有影响。

1. 登录到 Azure 门户并选择要移动的私有云。

1. 选择“订阅(更改)”链接。

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="此屏幕截图显示了私有云详细信息。":::

1. 提供“目标”的订阅详细信息，然后选择“下一步”。

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="目标资源的屏幕截图。" lightbox="media/move-resources-subscription-target.png":::

1. 确认要移动的选定资源的有效性，然后选择“下一步”。 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="此屏幕截图显示了要移动的资源。" lightbox="media/confirm-move-resources-subscription-target.png":::

1. 选中表明你了解关联的工具和脚本在更新为使用新资源 ID 之前将无法使用的复选框。 然后选择“移动”。

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="此屏幕截图显示了要移动的选定资源的摘要。" lightbox="media/review-move-resources-subscription-target.png":::

   资源移动完成后，会显示一条通知。 新订阅会显示在私有云的“概述”中。

   :::image type="content" source="media/moved-subscription-target.png" alt-text="此屏幕截图显示了新订阅。" lightbox="media/moved-subscription-target.png":::

