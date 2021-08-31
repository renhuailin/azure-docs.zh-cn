---
title: 如何通过 Azure 门户在 Azure Stack Edge Pro 上管理 VM 网络接口
description: 了解如何通过 Azure 门户管理 Azure Stack Edge Pro GPU 上部署的 VM 上的网络接口。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 27927f1e5f31243d4d2dcbfd9ea1f4da09b8b544
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736815"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>使用 Azure 门户管理 Azure Stack Edge Pro GPU 上 VM 上的网络接口

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

你可以使用 Azure 门户、模板、Azure PowerShell cmdlet 以及通过 Azure CLI/Python 脚本在 Azure Stack Edge 设备上创建和管理虚拟机 (VM)。 本文介绍如何使用 Azure 门户管理在 Azure Stack Edge 设备上运行的 VM 上的网络接口。

创建 VM 时，需要指定一个要创建的虚拟网络接口。 创建虚拟机后，可能要向其添加一个或多个网络接口。 还可更改现有网络接口的默认网络接口设置。

本文介绍如何将网络接口添加到现有 VM、更改 IP 类型（静态与动态）等现有设置以及拆离或删除现有接口。

        
## <a name="about-network-interfaces-on-vms"></a>关于 VM 上的网络接口

通过网络接口，Azure Stack Edge Pro 设备上运行的虚拟机 (VM) 能够与 Azure 和本地资源进行通信。 启用接口以在设备上实现计算网络时，会在网络接口上创建一个虚拟交换机。 然后，使用此虚拟交换机在设备上部署计算工作负载，例如 VM 或容器化应用程序。 

你的设备仅支持一个虚拟交换机，但可支持多个虚拟网络接口。 VM 上的每个网络接口都分配有一个静态或动态 IP 地址。 将 IP 地址分配到 VM 上的多个网络接口时，会在 VM 上启用某些功能。 例如，VM 可在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。 设备上的 VM 可以充当网络虚拟设备，例如防火墙或负载均衡器。 <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>先决条件

在开始通过 Azure 门户在设备上管理 VM 之前，请确保：

1. 可以访问激活的 Azure Stack Edge Pro GPU 设备。 已启用设备上的计算网络接口。 此操作会在 VM 上的该网络接口上创建一个虚拟交换机。 
    1. 在设备的本地 UI 中，转到“计算”。 选择将用于创建虚拟交换机的网络接口。

        > [!IMPORTANT] 
        > 只能为计算配置一个端口。

    1. 在网络接口上启用计算。 Azure Stack Edge Pro GPU 创建并管理与该网络接口相对应的虚拟交换机。

1. 在设备上至少部署了一个 VM。 若要创建此 VM，请参阅[通过 Azure 门户在 Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) 中的说明。

1. VM 应处于“已停止”状态。 要停止 VM，请转到“虚拟机”中，选择要停止的 VM。 在 VM“详细信息”页上，选择“停止”，然后在提示确认时选择“是”。 在添加、编辑或删除网络接口之前，必须停止 VM。

    ![显示在 Azure Stack Edge 中停止虚拟机的确认提示的屏幕截图。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>添加网络接口

按照以下步骤将网络接口添加到设备上部署的虚拟机。<!--There's no obvious way to add a new NIC to a VM or to an Edge resource group in the portal. To update these procedures, I need to create my own test VM, which I can start and stop, create a new NIC for, detach a NIC from the stopped VM, etc.-->

1. 转到已停止的虚拟机，然后选择“网络”。
    
    ![显示虚拟机的“详细信息”选项卡的屏幕截图。 突出显示“网络”标签，该标签打开 VM 的“网络”边栏选项卡。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. 在“网络”边栏选项卡中，从命令栏中选择“+ 添加网络接口” 。

    ![显示虚拟机的“网络”边栏选项卡的屏幕截图。 突出显示“+ 添加网络接口”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. 在“添加网络接口”边栏选项卡中，输入以下参数：

    |字段    |说明  |
    |---------|-------------|
    |名称     | 边缘资源组内的唯一名称。 创建网络接口后，无法更改其名称。 若要轻松管理多个网络接口，请使用[命名约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)中提供的建议。     |
    |选择边缘资源组 |选择要将网络接口添加到的边缘资源组。|   
    |虚拟网络| 在网络接口上启用计算时，与设备上创建的虚拟交换机关联的虚拟网络。 只有一个与设备关联的虚拟网络。         |
    |子网     | 所选虚拟网络中的子网。 此字段将自动填充与启用了计算的网络接口关联的子网。         |
    |IP 地址分配   | 网络接口的静态或动态 IP。 静态 IP 应该是指定子网范围内可用的空闲 IP。 如果环境中存在 DHCP 服务器，请选择动态。        |

    ![显示虚拟机的“添加网络接口”边栏选项卡的屏幕截图。 突出显示“添加”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. 此时会显示一条通知，指出正在创建网络接口。

    ![显示正在创建网络接口的通知的屏幕截图。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  成功创建网络接口后，网络接口的列表会刷新以显示新创建的接口。

    ![虚拟机的“网络”边栏选项卡的屏幕截图。 突出显示新创建的虚拟机条目。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>编辑网络接口

按照以下步骤编辑与设备上部署的虚拟机关联的网络接口。

1. 转到已停止的虚拟机，然后选择虚拟机“详细信息”中的“网络”。

1. 在网络接口列表中，选择要编辑的接口。 在所选网络接口的最右侧，选择编辑图标（铅笔）。  

    ![显示虚拟机的“网络”边栏选项卡的屏幕截图。 突出显示网络接口的名称以及该项的铅笔图标。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. 在“编辑网络接口”边栏选项卡中，只能更改网络接口的 IP 分配。 创建网络接口后，无法更改其名称、边缘资源组、虚拟网络和与其关联的子网。 将“IP 分配”更改为静态，并保存所做的更改。

    ![虚拟机的“编辑网络接口”边栏选项卡的屏幕截图。 突出显示“IP 地址分配”区域和“保存”按钮。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. 刷新网络接口列表，以显示更新的网络接口。


## <a name="detach-a-network-interface"></a>拆离网络接口

按照以下步骤拆离或删除与设备上部署的虚拟机关联的网络接口。

1. 转到已停止的虚拟机，然后选择虚拟机“详细信息”中的“网络”。

1. 在网络接口列表中，选择要编辑的接口。 在所选网络接口的最右侧，选择拆离图标（拔掉）。  

    ![虚拟机的“网络”边栏选项卡的屏幕截图。 网络接口的名称，其中突出显示其拆离图标。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. 你将看到一条消息，要求你确认是否要拆离网络接口。 选择 **“是”** 。

    ![显示通知的屏幕截图，用于确认是否要从虚拟机拆离网络接口。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-2.png)

    完全拆离接口后，会刷新网络接口列表以显示剩余的接口。


## <a name="delete-a-network-interface"></a>删除网络接口

请按照以下步骤操作，删除未连接到虚拟机的网络接口。

1. 依次转到“虚拟机”和“资源”页。 选择“网络”。
    
    ![显示虚拟机“资源”页上的“网络”选项卡的屏幕截图。 突出显示“资源”窗格标签和“网络”选项卡。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-1.png)

1. 在“网络”边栏选项卡上，根据要删除的网络接口选择删除图标（垃圾桶）。 仅显示未附加到 VM 的网络接口的删除图标。

    ![显示虚拟机资源的“网络”边栏选项卡的屏幕截图。 突出显示未附加的网络接口的删除图标。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-2.png)

1. 你将看到一条消息，要求你确认是否要删除网络接口。 此操作不可逆。 选择 **“是”** 。

    ![通知屏幕截图，要求确认是否要删除所选网络接口。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-3.png)
  
    删除网络接口后，将从列表中删除网络接口。

## <a name="next-steps"></a>后续步骤

若要了解如何在 Azure Stack Edge Pro 设备上部署虚拟机，请参阅[通过 Azure 门户部署虚拟机](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。
