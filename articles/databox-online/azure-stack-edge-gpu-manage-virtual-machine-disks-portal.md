---
title: 使用 Azure 门户管理 Azure Stack Edge Pro GPU、Pro R 和 Mini R 中的 VM 磁盘
description: 了解如何管理磁盘，包括通过 Azure 门户添加、拆离和删除在 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 中部署的 VM 的数据磁盘并调整其大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 8b9be47fb87c626bb5e6f2f20054f6cf249a57d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732944"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>使用 Azure 门户管理 Azure Stack Edge Pro GPU 中 VM 上的磁盘

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

可使用 Azure 门户在 Azure Stack Edge Pro 设备中部署的虚拟机 (VM) 上预配磁盘。 磁盘是通过本地 Azure 资源管理器在设备上预配的，并且会占用设备容量。 可以通过 Azure 门户完成添加、拆离和删除磁盘等操作，而 Azure 门户则会调用本地 Azure 资源管理器来预配存储。 

本文介绍如何通过 Azure 门户在现有 VM 中添加、拆离或删除数据磁盘，以及调整 VM 本身的大小。

        
## <a name="about-disks-on-vms"></a>关于 VM 上的磁盘

VM 可以具有 OS 磁盘和数据磁盘。 设备上部署的每个虚拟机都附加了一个操作系统磁盘。 该 OS 磁盘有一个预先安装的 OS，这是在创建 VM 时选择的。 此磁盘包含启动卷。

> [!NOTE]
> 无法更改在设备上部署的 VM 的 OS 磁盘大小。 OS 磁盘大小取决于所选 VM 的大小。

另一方面，数据磁盘是附加到设备上运行的 VM 的托管磁盘。 数据磁盘用于存储应用程序数据。 数据磁盘通常是 SCSI 驱动器。 VM 的大小决定了可以附加到 VM 的数据磁盘数量。 默认情况下，高级存储用于托管磁盘。

设备上部署的 VM 有时可能包含临时磁盘。 临时磁盘为应用程序和进程提供短期存储，仅用于存储页面或交换文件等数据。 在维护事件期间或重新部署 VM 时，临时磁盘上的数据可能会丢失。 在以标准方式成功重启 VM 期间，临时磁盘上的数据将保留。 


## <a name="prerequisites"></a>先决条件

在开始通过 Azure 门户管理设备上运行的 VM 中的磁盘之前，请确保：


1. 可以访问激活的 Azure Stack Edge Pro GPU 设备。 还启用了设备上的计算网络接口。 此操作会在 VM 上的该网络接口上创建一个虚拟交换机。 
    1. 在设备的本地 UI 中，转到“计算”。 选择将用于创建虚拟交换机的网络接口。

        > [!IMPORTANT] 
        > 只能为计算配置一个端口。

    1. 在网络接口上启用计算。 Azure Stack Edge Pro GPU 创建并管理与该网络接口相对应的虚拟交换机。

1. 在设备上至少部署了一个 VM。 若要创建此 VM，请参阅[通过 Azure 门户在 Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) 中的说明。


## <a name="add-a-data-disk"></a>添加数据磁盘

按照以下步骤将磁盘添加到设备上部署的虚拟机。

1. 转到要向其添加数据磁盘的虚拟机，然后在虚拟机“详细信息”中选择“磁盘” 。
    
    ![Azure Stack Edge 虚拟机的“详细信息”选项卡上的“磁盘”设置的屏幕截图。 突出显示了“磁盘”标签。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. 在“磁盘”边栏选项卡的“数据磁盘”下，选择“创建并附加新磁盘”  。

    ![虚拟机的“概述”视图中“磁盘”边栏选项卡的屏幕截图。 突出显示了“数据磁盘”下的“创建并附加新磁盘”选项。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. 在“创建新磁盘”边栏选项卡中，输入以下参数：

    
    |字段  |说明  |
    |---------|---------|
    |名称     | 资源组中的唯一名称。 创建数据磁盘后无法更改其名称。     |
    |Edge 资源组 |输入要在其中存储新磁盘的 Edge 资源组。|
    |大小| 数据磁盘的大小（以 GiB 为单位）。 数据磁盘的最大大小取决于所选的 VM 大小。 预配磁盘时，还应考虑设备上的实际空间，以及正在运行且占用容量的其他 VM 工作负载。  |         

    ![虚拟机的“创建新磁盘”边栏选项卡的屏幕截图。 突出显示了“确定”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    选择“确定”并继续。

1. 在“磁盘”显示页中，将显示与新磁盘相对应的条目。 接受默认设置或为磁盘分配有效的逻辑单元号 (LUN)，然后选择“保存”。 LUN 是 SCSI 磁盘的唯一标识符。 有关详细信息，请参阅[什么是 LUN？](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun)。

    ![添加新磁盘后虚拟机的“磁盘”显示页的屏幕截图。 突出显示了新磁盘的磁盘数量和“保存”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. 此时会显示一条通知，指出正在创建磁盘。 成功创建磁盘后，将更新虚拟机。 

    ![显示“正在创建磁盘”通知的屏幕截图。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. 导航回虚拟机的“详细信息”页。 磁盘列表将更新，以显示新创建的数据磁盘。

    ![虚拟机的“详细信息”选项卡的屏幕截图。 “磁盘”区域中突出显示了新创建的虚拟机磁盘。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>更改数据磁盘

按照以下步骤更改与设备上部署的虚拟机关联的磁盘。

1. 转到其中包含要更改的数据磁盘的虚拟机，然后在虚拟机“详细信息”中选择“磁盘” 。

1. 在数据磁盘列表中，选择要更改的磁盘。 在所选磁盘的最右侧，选择编辑图标（铅笔）。  

    ![虚拟机上的“磁盘”的屏幕截图。 突出显示了“数据磁盘”部分和用于编辑数据磁盘的铅笔图标。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. 在“更改磁盘”边栏选项卡中，只能更改磁盘的大小。 创建磁盘后，无法更改其名称。 更改磁盘的大小并保存更改。

    ![虚拟机的“更改磁盘”边栏选项卡的屏幕截图。 突出显示了“大小”选项和“确定”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > 只能扩展数据磁盘。 无法缩小磁盘。

1. 在“磁盘”显示页上，磁盘列表将刷新，以显示更新的磁盘。


## <a name="attach-an-existing-disk"></a>附加现有磁盘

按照以下步骤将现有磁盘连接到设备上部署的虚拟机。

1. 转到要向其附加现有磁盘的虚拟机，然后在虚拟机“详细信息”中选择“磁盘” 。

1. 在“磁盘”边栏选项卡的“数据磁盘”下，选择“附加现有磁盘”  。

    ![Azure Stack Edge 虚拟机的“磁盘”边栏选项卡的屏幕截图。 突出显示了“附加现有磁盘”选项。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. 接受默认 LUN 或分配有效的 LUN。 从下拉列表中选择现有数据磁盘。 选择“保存”。

    ![添加现有磁盘后 Azure Stack Edge 虚拟机的“磁盘”边栏选项卡的屏幕截图。 突出显示了磁盘的 LUN 编号和名称以及“保存”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    选择“保存”并继续。

1. 你将看到一条通知，指出正在更新虚拟机。 更新 VM 后，导航回虚拟机的“详细信息”页。 刷新页面，查看数据磁盘列表中新附加的磁盘。

    ![显示虚拟机“详细信息”窗格的“数据磁盘”部分的屏幕截图。 突出显示了数据磁盘条目。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>分离数据磁盘

按照以下步骤拆离或删除与设备上部署的虚拟机关联的数据磁盘。

> [!NOTE]
> - 你可以在 VM 运行时删除数据磁盘。 从 VM 中拆离磁盘之前，请确保没有任何设备正在使用该磁盘。
> - 如果拆离磁盘，它将不会自动删除。 按照下面[删除磁盘](#delete-a-data-disk)中的步骤操作。

1. 转到要从中拆离数据磁盘的虚拟机，然后在虚拟机“详细信息”中选择“磁盘” 。

    ![显示虚拟机“详细信息”窗格的“数据磁盘”部分的屏幕截图。 突出显示了“数据磁盘”下的磁盘条目。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. 在磁盘列表中，选择要拆离的磁盘。 在所选磁盘的最右侧，选择拆离图标（“X”）。 此操作将拆离所选磁盘。 选择“保存”。

    ![虚拟机的“磁盘”显示页的屏幕截图。 突出显示了可拆离的未附加磁盘的 X 图标以及“保存”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. 拆离磁盘后，将更新虚拟机。 刷新此页，以查看更新的数据磁盘列表。

    ![拆离数据磁盘后，虚拟机的已刷新“磁盘”显示页的屏幕截图。 突出显示了数据磁盘列表。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="delete-a-data-disk"></a>删除数据磁盘

按照以下步骤删除未附加到设备上部署的 VM 的数据磁盘。

> [!NOTE]
> 在删除数据磁盘之前，如果正在使用磁盘，则必须[将数据磁盘与 VM 拆离](#detach-a-data-disk)。

1. 转到设备上的“虚拟机”，然后转到“资源”窗格 。 选择“磁盘”。
 
    ![显示虚拟机中“资源”边栏选项卡上的“磁盘”选项卡的屏幕截图。 突出显示了“资源”标签和“磁盘”选项卡。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-1.png)

1. 在磁盘列表中，选择要删除的磁盘。 在所选磁盘的最右侧，选择删除图标（垃圾桶）。

    ![显示虚拟机中“资源”边栏选项卡上的“磁盘”选项卡的屏幕截图。 突出显示了带有垃圾桶图标（表示可删除）的数据磁盘。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-2.png)

    如果未看到删除图标，可以在“附加的 VM”列中选择 VM 名称，然后[将磁盘与 VM 拆离](#detach-a-data-disk)。

1. 你将看到一条消息，询问你是否确认要删除该磁盘。 此操作无法撤销。 选择 **“是”** 。

    ![显示询问你是否确认要删除数据磁盘的通知的屏幕截图。](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-3.png)

    删除完成后，会从列表中删除磁盘。


## <a name="next-steps"></a>后续步骤

若要了解如何在 Azure Stack Edge Pro 设备上部署虚拟机，请参阅[通过 Azure 门户部署虚拟机](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。
