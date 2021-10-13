---
title: 使用 Azure VMware 解决方案添加 NSX-T 网段
description: 在 Azure 门户中为 Azure VMware 解决方案添加 NSX-T 网段的步骤。
ms.topic: include
ms.date: 07/16/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 812266ef6c7ed937c21c1c78c2c9b2aa5ff1043d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638213"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->


>[!NOTE]
>如果计划使用 DHCP，则需要先[配置 DHCP 服务器或 DHCP 中继](../configure-dhcp-azure-vmware-solution.md)，然后才能配置 NSX-T 网段。 

1. 在 Azure VMware 解决方案私有云的“工作负荷网络”下，选择“段” > “添加”。 

2. 提供新逻辑段的详细信息，然后选择“确定”。

   :::image type="content" source="../media/networking/add-new-nsxt-segment.png" alt-text="显示如何在 Azure 门户中添加新的 NSX-T 段的屏幕截图。":::

   - “段名称”- vCenter 中可见的段名称。

   - “子网网关”- 带有子网掩码的段的子网网关 IP 地址。 VM 附加到逻辑段，连接到此段的所有 VM 都属于同一子网。  而且，附加到此逻辑段的所有 VM 必须携带来自同一段的 IP 地址。

   - “DHCP”（可选） - 逻辑段的 DHCP 范围。 必须配置 [DHCP 服务器或 DHCP 中继](../configure-dhcp-azure-vmware-solution.md)，以使用段的 DHCP。    

   >[!NOTE]
   >默认情况下，“已连接网关”被选中，且为只读。  其显示第 1 层网关和段信息类型。 
   >
   >- “T1” - NSX-T Manager 中第 1 层网关的名称。 私有云配有主动/主动模式的 NSX-T 第 0 层网关和主动/待机模式的默认 NSX-T 第 1 层网关。  通过 Azure VMware 解决方案控制台创建的段只连接到默认的第 1 层网关，这些段的工作负荷获得东-西和北-南连接。 只能通过 NSX-T Manager 创建更多第 1 层网关。 从 NSX-T Manager 控制台创建的第 1 层网关在 Azure VMware 解决方案控制台中不可见。 
   >
   >- “类型” - Azure VMware 解决方案支持的覆盖段。

此段现在可在 Azure VMware 解决方案、NSX-T Manger 和 vCenter 中看到。
