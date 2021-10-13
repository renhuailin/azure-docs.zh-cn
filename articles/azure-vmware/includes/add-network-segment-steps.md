---
title: 添加 NSX-T 网段
description: 在 NSX-T Manager 中为 Azure VMware 解决方案添加 NSX-T 网段的步骤。
ms.topic: include
ms.date: 03/13/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1e6d50e1ca05ea15c4d6690965c112565dae76f5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638523"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->

1. 在 NSX-T Manager 中，选择“网络” > “段”，然后选择“添加段”  。 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="显示如何在 NSX-T Manager 中添加新段的屏幕截图。":::

1. 输入段的名称。

1. 选择第 1 层网关 (TNTxx-T1) 作为“连接的网关”，并将“类型”保留为“灵活” 。

1. 选择预配置的覆盖“传输区域”(TNTxx-OVERLAY-TZ)，然后选择“设置子网” 。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="屏幕截图，显示用于添加新 NSX-T 网段的“段”详细信息。":::

1. 输入网关 IP 地址，然后选择“添加”。 

   >[!IMPORTANT]
   >IP 地址需要位于不重叠的 RFC1918 地址块上，这样可确保连接到新段上的 VM。

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="显示新段的网关 IP 地址的屏幕截图。":::

1. 依次选择“应用”、“保存” 。

1. 选择“否”以拒绝继续配置段的选项。 


