---
title: 如何通过 Azure 门户在 Azure Stack Edge Pro 上管理 VM 网络接口
description: 了解如何通过 Azure 门户管理 Azure Stack Edge Pro GPU 上部署的 VM 上的网络接口。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 3e709b04b4eac60e6cc0ba3e53eb77583162dfef
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078871"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>使用 Azure 门户管理 Azure Stack Edge Pro GPU 上 VM 上的网络接口

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

你可以使用 Azure 门户、模板、Azure PowerShell cmdlet 以及通过 Azure CLI/Python 脚本在 Azure Stack Edge 设备上创建和管理虚拟机 (VM)。 本文介绍如何使用 Azure 门户管理在 Azure Stack Edge 设备上运行的 VM 上的网络接口。 

创建 VM 时，需要指定一个要创建的虚拟网络接口。 创建虚拟机后，可能要向其添加一个或多个网络接口。 还可更改现有网络接口的默认网络接口设置。

本文介绍如何将网络接口添加到现有 VM、更改 IP 类型（静态与动态）等现有设置以及最终删除或拆离现有接口。 

        
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

1. VM 应处于“已停止”状态。 要停止 VM，请转到“虚拟机”>“概述”中，选择要停止的 VM。 在“VM 属性”页上，选择“停止”，然后在提示确认时选择“是” 。 在添加、编辑或删除网络接口之前，必须停止 VM。

    ![从“VM 属性”页中停止 VM](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>添加网络接口

按照以下步骤将网络接口添加到设备上部署的虚拟机。 

1. 转到已停止的虚拟机，然后转到“VM 属性”页。 选择“网络”。
    
    ![选择“VM 属性”页上的“网络”](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. 在“网络”边栏选项卡中，从命令栏中选择“+ 添加网络接口” 。

    ![选择“添加网络接口”](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. 在“添加网络接口”边栏选项卡中，输入以下参数：

    
    |Column1  |Column2  |
    |---------|---------|
    |名称     | 资源组中的唯一名称。 创建网络接口后，无法更改其名称。 若要轻松管理多个网络接口，请使用[命名约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)中提供的建议。     |
    |虚拟网络| 在网络接口上启用计算时，与设备上创建的虚拟交换机关联的虚拟网络。 只有一个与设备关联的虚拟网络。         |         
    |子网     | 所选虚拟网络中的子网。 此字段将自动填充与启用了计算的网络接口关联的子网。         |       
    |IP 分配   | 网络接口的静态或动态 IP。 静态 IP 应该是指定子网范围内可用的空闲 IP。 如果环境中存在 DHCP 服务器，请选择动态。        | 

    ![“添加网络接口”边栏选项卡](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. 此时会显示一条通知，指出正在创建网络接口。

    ![正在创建网络接口时的通知](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  成功创建网络接口后，网络接口的列表会刷新以显示新创建的接口。

    ![已更新的网络接口列表](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>编辑网络接口

按照以下步骤编辑与设备上部署的虚拟机关联的网络接口。

1. 转到已停止的虚拟机，然后转到“VM 属性”页。 选择“网络”。

1. 在网络接口列表中，选择要编辑的接口。 在所选网络接口的最右侧，选择编辑图标（铅笔）。  

    ![选择要编辑的网络接口](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. 在“编辑网络接口”边栏选项卡中，只能更改网络接口的 IP 分配。 创建网络接口后，无法更改其名称、虚拟网络和与其关联的子网。 将“IP 分配”更改为静态，并保存所做的更改。

    ![更改网络接口的 IP 分配](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. 刷新网络接口列表，以显示更新的网络接口。


## <a name="detach-a-network-interface"></a>拆离网络接口

按照以下步骤拆离或删除与设备上部署的虚拟机关联的网络接口。

1. 转到已停止的虚拟机，然后转到“VM 属性”页。 选择“网络”。

1. 在网络接口列表中，选择要编辑的接口。 在所选网络接口的最右侧，选择拆离图标（拔掉）。  

    ![选择要拆离的网络接口](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. 完全拆离接口后，会刷新网络接口列表以显示剩余的接口。

## <a name="next-steps"></a>后续步骤

若要了解如何在 Azure Stack Edge Pro 设备上部署虚拟机，请参阅[通过 Azure 门户部署虚拟机](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。
