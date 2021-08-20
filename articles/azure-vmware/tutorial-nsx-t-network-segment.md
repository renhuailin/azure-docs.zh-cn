---
title: 教程 - 在 Azure VMware 解决方案中添加一个网段
description: 了解如何添加用于 vCenter 中虚拟机 (VM) 的网段。
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 04681c5d55536e3ee1cc0b12d2ebc94f19b3873f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464419"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>教程：在 Azure VMware 解决方案中添加一个网段 

部署 Azure VMware 解决方案后，可以通过 NSX-T Manager 或 Azure 门户配置 NSX-T 网段。  配置后，这些网段将显示在 Azure VMware 解决方案、NSX-T Manger 和 vCenter 中。  默认情况下，NSX-T 预先配置了主动/主动模式的 NSX-T 第 0 层网关，以及主动/待机模式的默认 NSX-T 第 1 层网关 。  通过这些网关可以将段连接（逻辑交换机）并提供东-西和北-南连接。 

>[!TIP]
>Azure 门户针对不熟悉 NSX-T Manager 的用户提供了 VMware 管理员日常所需 NSX-T 操作的简化视图。 


在本教程中，你将了解：

> [!div class="checklist"]
> * 使用 NSX-T Manager 或 Azure 门户添加网段
> * 验证新网段 

## <a name="prerequisites"></a>先决条件

有权访问 vCenter 和 NSX-T Manager 界面的 Azure VMware 解决方案私有云。 有关详细信息，请参阅[配置网络](tutorial-configure-networking.md)教程。

## <a name="use-nsx-t-manager-to-add-network-segment"></a>使用 NSX-T Manager 添加网段 

在 vCenter 中创建的虚拟机 (VM) 将被放到在 NSX-T 中创建的网段，并且在 vCenter 中可见。

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="use-azure-portal-to-add-an-nsx-t-segment"></a>使用 Azure 门户添加 NSX-T 网段

[!INCLUDE [create-nsxt-segment-azure-portal-steps](includes/create-nsxt-segment-azure-portal-steps.md)]


## <a name="verify-the-new-network-segment"></a>验证新网段

验证新网段是否存在。 此示例中，ls01 是新网段。

1. 在 NSX-T Manager 中，选择“网络” > “段” 。 

    :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="该屏幕截图显示新网段存在于 NSX-T 中。":::

1. 在 vCenter 中，选择“网络” > “SDDC-Datacenter” 。

    :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="该屏幕截图显示新网段存在于 vCenter 中。":::

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建用于 vCenter 中 VM 的 NSX-T 网段。 

现在可以执行以下操作： 

- [配置和管理 Azure VMware 解决方案的 DHCP](configure-dhcp-azure-vmware-solution.md)
- [创建内容库以在 Azure VMware 解决方案中部署 VM](deploy-vm-content-library.md) 
- [将本地环境与私有云对等互连](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
