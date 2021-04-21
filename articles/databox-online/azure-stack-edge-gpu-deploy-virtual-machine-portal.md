---
title: 如何通过 Azure 门户在 Azure Stack Edge Pro 上部署 VM
description: 了解如何通过 Azure 门户在 Azure Stack Edge Pro 上创建和管理 VM。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 139b543160b679ba063a0633f9091e7bc0ef1fc1
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074768"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>通过 Azure 门户在 Azure Stack Edge Pro GPU 设备上部署 VM

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

你可以使用 Azure 门户、模板、Azure PowerShell cmdlet 以及通过 Azure CLI/Python 脚本在 Azure Stack Edge 设备上创建和管理虚拟机 (VM)。 本文介绍如何使用 Azure 门户在 Azure Stack Edge 设备上创建和管理 VM。 

本文适用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备。 

> [!IMPORTANT] 
> 我们建议你为管理云设备上部署的 VM 的用户启用多重身份验证。
        
## <a name="vm-deployment-workflow"></a>VM 部署工作流

下面是此部署工作流的基本摘要信息：

1. 在 Azure Stack Edge 设备上启用计算网络接口。 这会在指定的网络接口上创建一个虚拟交换机。
1. 通过 Azure 门户启用虚拟机云管理。
1. 使用存储资源管理器将 VHD 上传到 Azure 存储帐户。 
1. 使用上传的 VHD 将 VHD 下载到设备上，并从 VHD 创建 VM 映像。 
1. 使用在前面的步骤中创建的服务：
    1. 所创建的 VM 映像。
    1. 与启用了计算的网络接口关联的 VSwitch。
    1. 与 VSwitch 关联的子网。

    并创建或指定以下内联资源：
    1. VM 名称，为 VM 选择支持的 VM 大小和登录凭据。 
    1. 创建新的数据磁盘或附加现有的数据磁盘。
    1. 为 VM 配置静态或动态 IP。 如果提供静态 IP，请在为计算启用的网络接口的子网范围中选择一个可用 IP。

    使用上述资源创建虚拟机。


## <a name="prerequisites"></a>先决条件

在开始通过 Azure 门户在设备上创建和管理 VM 之前，请确保：

1. 已完成 Azure Stack Edge Pro 设备上的网络设置，如[步骤 1：配置 Azure Stack Edge Pro 设备](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)中所述。

    1. 已启用计算网络接口。 此网络接口 IP 用于为 VM 部署创建虚拟交换机。 在设备的本地 UI 中，转到“计算”。 选择将用于创建虚拟交换机的网络接口。

        > [!IMPORTANT] 
        > 只能为计算配置一个端口。

    1. 在网络接口上启用计算。 Azure Stack Edge Pro 创建并管理与该网络接口相对应的虚拟交换机。

1. 你有权访问将用于为要创建的虚拟机创建 VM 映像的 Windows 或 Linux VHD。

## <a name="deploy-a-vm"></a>部署 VM

按照以下步骤在 Azure Stack Edge 设备上创建虚拟机。

### <a name="add-a-vm-image"></a>添加 VM 映像

1. 将 VHD 上传到 Azure 存储帐户。 按照[使用 Azure 存储资源管理器上传 VHD](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md) 中的步骤操作。

1. 在 Azure 门户中，转到 Azure Stack Edge 设备的 Azure Stack Edge 资源。 转到“Edge 计算”>“虚拟机”。

    ![添加 VM 映像 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. 选择“虚拟机”，转到“概述”页 。 “启用”虚拟机云管理。
    ![添加 VM 映像 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. 第一步是添加 VM 映像。 在之前的步骤中，你已将 VHD 上传到存储帐户。 你将使用此 VHD 来创建 VM 映像。

    选择“添加映像”，从存储帐户下载 VHD，并将其添加到设备中。 下载过程需要几分钟时间，具体取决于 VHD 的大小和用于下载的 Internet 带宽。 

    ![添加 VM 映像 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. 在“添加映像”边栏选项卡中，输入以下参数。 选择 **添加** 。


    |参数  |说明  |
    |---------|---------|
    |从存储 blob 下载    |浏览到你上传 VHD 的存储账户中存储 blob 的位置。         |
    |下载到    | 自动设置为要在其中部署虚拟机的当前设备。        |
    |将映像另存为      | 要从上传到存储帐户的 VHD 创建的 VM 映像的名称。        |
    |OS 类型     |选择 Windows 或 Linux 作为要用于创建 VM 映像的 VHD 的操作系统。         |
   

    ![添加 VM 映像 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. 随即将下载 VHD 并创建 VM 映像。 映像创建过程需要几分钟时间才能完成。 你会看到成功完成 VM 映像的通知。

    ![添加 VM 映像 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. 成功创建 VM 映像后，系统会将其添加到“映像”边栏选项卡中的映像列表。
    ![添加 VM 映像 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    “部署”边栏选项卡将更新，以指示部署的状态。

    ![添加 VM 映像 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    新添加的映像也将显示在“概述”页中。
    ![添加 VM 映像 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>添加 VM

创建 VM 映像后，请按照以下步骤创建 VM。

1. 在“概述”页中，选择“添加虚拟机” 。

    ![添加 VM 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. 在“基本信息”选项卡中，输入以下参数。


    |参数 |说明  |
    |---------|---------|
    |虚拟机名称     |         |
    |Edge 资源组     | 为与 VM 关联的所有资源创建新的资源组。        |
    |映像     | 从设备上提供的 VM 映像中进行选择。        |
    |大小     | 从[支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md)中进行选择。        |
    |用户名     | 使用管理员的默认用户名“azureuser”登录到 VM。        |
    |身份验证类型    | 从 SSH 公钥或用户定义的密码中进行选择。       |
    |Password     | 输入用于登录到虚拟机的密码。 密码必须至少 12 个字符，且符合定义的[复杂性要求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。        |
    |确认密码    | 再次输入密码。        |


    ![添加 VM 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    在完成时选择“下一步:**磁盘”** 。

1. 在“磁盘”选项卡上，将磁盘附加到 VM。 
    
    1. 你可以选择“创建并附加新磁盘”或“附加现有磁盘” 。

        ![添加 VM 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. 选择“创建并附加新磁盘”。 在“创建新磁盘”边栏选项卡中，提供磁盘名称和大小 (GiB)。

        ![添加 VM 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  重复上述步骤，添加更多磁盘。 创建磁盘后，这些磁盘会显示在“磁盘”选项卡中。请选择“下一步: 网络”。

1. 在“网络”选项卡中，你将为 VM 配置网络连接。

    
    |参数  |说明 |
    |---------|---------|
    |虚拟网络    | 如果在网络接口上启用了计算，请从下拉列表中选择在 Azure Stack Edge 设备上创建的虚拟交换机。    |
    |子网     | 此字段将自动填充与启用了计算的网络接口关联的子网。         |
    |IP 地址     | 为 VM 提供静态或动态 IP。 静态 IP 应该是指定子网范围内可用的空闲 IP。        |

    ![添加 VM 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    在完成时选择“下一步:查看 + 创建”。

1. 在“高级”选项卡中，可以指定自定义数据或 cloud-init 来自定义你的 VM。 

    在 VM 首次启动时，可以使用 cloud-init 自定义 VM。 使用 cloud-init 来安装包和写入文件，或者配置用户和安全性。 当 cloud-init 在初始启动过程中运行时，无需额外的步骤即可应用配置。 有关 cloud-init 的详细信息，请参阅 [cloud-init 概述](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview)。

    ![添加 VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-1.png)    

1. 在“查看 + 创建”选项卡中，查看 VM 的规范，然后选择“创建” 。

    ![添加 VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. 随即开始创建 VM，此过程可能最多需要 20 分钟。 你可以转到“部署”来监视 VM 的创建过程。

    ![添加 VM 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. 成功创建 VM 后，“概述”页将更新，以显示新的 VM。

    ![添加 VM 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. 选择新创建的 VM，以转到“虚拟机”。

    ![添加 VM 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    选择 VM，查看详细信息。 

    ![添加 VM 12](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>连接到 VM

根据所创建的是 Windows 还是 Linux VM，连接步骤可能会有所不同。 通过 Azure 门户无法连接到设备上部署的 VM。 需要执行以下步骤才可连接到 Linux 或 Windows VM。

### <a name="connect-to-linux-vm"></a>连接到 Linux VM

按照以下步骤连接到 Linux VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>连接到 Windows VM

按照以下步骤连接到 Windows VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>后续步骤

若要了解如何管理 Azure Stack Edge Pro 设备，请参阅[使用本地 WEB UI 管理 Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)。