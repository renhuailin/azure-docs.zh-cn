---
title: 通过 Azure 门户调整 Azure Stack Edge Pro GPU、Pro R、Mini R 上 VM 的大小
description: 了解如何通过 Azure 门户调整 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、Azure Stack Edge Mini R 上运行的虚拟机 (VM) 的大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 5e41edf0cf080d06a0829e31e7105a13a0035f93
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736744"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>使用 Azure 门户来调整 Azure Stack Edge Pro GPU 上 VM 的大小

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何调整在 Azure Stack Edge Pro GPU 设备上部署的虚拟机 (VM) 的大小。

       
## <a name="about-vm-sizing"></a>关于 VM 调整大小

VM 大小决定 VM 可用计算资源（如 CPU、GPU 和内存）的数量。 应使用适用于应用程序工作负载的 VM 大小创建虚拟机。 

即使所有计算机都在同一硬件上运行，不同大小的计算机的磁盘访问限制也不同。 这可以帮助你管理 VM 上的总体磁盘访问。 如果工作负载增加，也可重设现有虚拟机的大小。

有关详细信息，请参阅[设备上受支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md)。


## <a name="prerequisites"></a>先决条件

在通过 Azure 门户重设设备上运行的 VM 的大小时，请确保：

1. 在设备上至少部署了一个 VM。 若要创建此 VM，请参阅[通过 Azure 门户在 Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) 中的说明。

1. VM 应处于“已停止”状态。 要停止 VM，请转到“虚拟机”>“概述”中，选择要停止的 VM。 在“概述”页上，选择“停止”，然后在提示确认时选择“是” 。 重设 VM 大小之前，必须停止 VM。

    ![从虚拟机概述页停止 VM 的屏幕的屏幕截图。 突出显示了“是”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>调整 VM 的大小

按照以下步骤重设设备上部署的虚拟机。 

1. 转到已停止的虚拟机，然后选择虚拟机“详细信息”中的“VM 大小(更改)” 。
    
    ![虚拟机的“详细信息”选项卡的屏幕截图。 突出显示了“详细信息”选项卡和“VM 大小”选项。](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. 在“更改 VM 大小”边栏选项卡的命令栏中选择“VM 大小”，然后选择“更改”  。

    ![“更改 VM 大小”屏幕的屏幕截图。 突出显示了 VM 大小和“更改”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. 随即会显示一条通知，指示正在更新虚拟机。 成功更新虚拟机后，“概述”页将刷新并显示重设大小后的 VM。

    ![VM“概述”页的屏幕截图。 突出显示了已调整大小的 VM 的“VM 大小”值。](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>后续步骤

若要了解如何在 Azure Stack Edge Pro 设备上部署虚拟机，请参阅[通过 Azure 门户部署虚拟机](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。
