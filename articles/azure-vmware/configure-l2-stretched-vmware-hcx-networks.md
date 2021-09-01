---
title: 在 L2 延伸 VMware HCX 网络上配置 DHCP
description: 了解如何将 DHCP 请求从 Azure VMware 解决方案 VM 发送到非 NSX-T DHCP 服务器。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 05/28/2021
ms.openlocfilehash: c59df75e70bf8913575b70b80048b14ae42d62a2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322743"
---
# <a name="configure-dhcp-on-l2-stretched-vmware-hcx-networks"></a>在 L2 延伸 VMware HCX 网络上配置 DHCP

当 DHCP 服务器位于本地数据中心时，DHCP 对于 VMware HCX L2 延伸网络中的虚拟机不起作用。 这是因为默认情况下，NSX 会阻止所有 DHCP 请求遍历 L2 延伸网络。 因此，若要将 DHCP 请求从 Azure VMware 解决方案 VM 发送到非 NSX-T DHCP 服务器，需要在 L2 延伸 VMware HCX 网络上配置 DHCP。

1. （可选）如果需要查找 L2 扩展的段名称：

   1. 登录到你的本地 vCenter，然后在“主页”下选择“HCX”。

   1. 在“服务”下选择“网络扩展” 。

   1. 选择支持 Azure VMware 解决方案的 DHCP 请求传送到本地的网络扩展。

   1. 记下目标网络名称。

      :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere 客户端中网络扩展的屏幕截图。" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. 在 NSX-T Manager 中，选择“网络” > “段” > “段配置文件”  。

1. 选择“添加段配置文件”，然后选择“网段安全”。

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="如何在 NSX-T 中添加网段配置文件的屏幕截图。" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. 提供名称和标记，然后将“BPDU 筛选器”开关设置为“开”，并将所有 DHCP 开关都设置为“关”。

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="显示已打开“BPDU 筛选器”开关且已关闭 DHCP 开关的屏幕截图。" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::
    
   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="“网段安全”字段的屏幕截图。" lightbox="media/manage-dhcp/edit-segment-security.png":::
