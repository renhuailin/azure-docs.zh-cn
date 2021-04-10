---
title: 教程 - 在 Azure VMware 解决方案中添加 NSX-T 网段
description: 了解如何创建用于 vCenter 中虚拟机 (VM) 的 NSX-T 网段。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462111"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>教程：在 Azure VMware 解决方案中添加一个网段 

在 vCenter 中创建的虚拟机 (VM) 将被放到在 NSX-T 中创建的网段，并且在 vCenter 中可见。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 NSX-T Manager 中导航以添加网段
> * 添加新的网段
> * 在 vCenter 中查看新的网段

## <a name="prerequisites"></a>先决条件

有权访问 vCenter 和 NSX-T Manager 界面的 Azure VMware 解决方案私有云。 有关详细信息，请参阅[配置网络](tutorial-configure-networking.md)教程。

## <a name="add-a-network-segment"></a>添加网段

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>后续步骤

本教程中创建了用于 vCenter 中 VM 的 NSX-T 网段。 

你现在可以： 

- [为 Azure VMware 解决方案创建和管理 DHCP](manage-dhcp.md)
- [创建内容库以在 Azure VMware 解决方案中部署 VM](deploy-vm-content-library.md) 
- [将本地环境与私有云对等互连](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
