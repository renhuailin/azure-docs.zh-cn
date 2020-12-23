---
title: 添加 NSX-T 网段
description: 用于为 Azure VMware 解决方案添加 NSX-T 网段的步骤。
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335039"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. 在 NSX-T Manager 中，选择“网络” > “段”，然后选择“添加段”  。 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="显示如何添加新段的屏幕截图":::

1. 选择“添加段”并输入段名称。

1. 选择 Tier1 网关 (TNTxx-T1) 作为“连接的网关”，并将“类型”保留为“灵活” 。

1. 选择预配置的覆盖“传输区域”(TNTxx-OVERLAY-TZ)，然后选择“设置子网” 。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="设置网段名称、连接的网关和类型以及传输区域，然后选择“设置子网”。":::

1. 输入网关的 IP 地址，然后选择“添加”。 

   >[!IMPORTANT]
   >IP 地址需要位于不重叠的 RFC1918 地址块上，这样可确保连接到新段上的 VM。

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="设置新网段的网关 IP 地址，然后选择“添加”。":::

1. 依次选择“应用”、“保存” 。

1. 选择“否”以拒绝继续配置段的选项。 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="通过选择“否”，拒绝进一步配置新创建的网段。":::

1. 确认新网段是否存在。 此示例中，ls01 是新网段。

   1. 在 NSX-T Manager 中，选择“网络” > “段” 。 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="确认 NSX-T 中存在新网段。":::

   1. 在 vCenter 中，选择“网络”>“SDDC 数据中心”。

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="确认 vCenter 中存在新网段。":::