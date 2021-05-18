---
title: 从 Windows VHD 的通用映像为 Azure Stack Edge Pro GPU 设备创建 VM 映像
description: 介绍如何从 Windows VHD 或 VHDX 开始从通用映像创建 VM 映像。 使用此通用映像创建与 Azure Stack Edge Pro GPU 设备上部署的 VM 配合使用的 VM 映像。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962248"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>使用 Windows VHD 中的通用映像为 Azure Stack Edge Pro 设备创建 VM 映像

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

若要在 Azure Stack Edge Pro 设备上部署 VM，你需要能够创建可用于创建 VM 的自定义 VM 映像。 本文介绍了准备 Windows VHD 或 VHDX 以创建通用映像所需的步骤。 然后，使用该通用映像来为 Azure Stack Edge Pro 设备创建 VM 映像。 

## <a name="about-preparing-windows-vhd"></a>关于准备 Windows VHD

可以使用 Windows VHD 或 VHDX 来创建通用映像或专用映像 。 下表汇总了通用映像与专用映像之间的主要差异 。


|映像类型  |通用  |专用  |
|---------|---------|---------|
|目标     |在任何系统上部署         | 面向特定系统        |
|启动后设置     | 需要在首次启动 VM 时设置。          | 无需设置。 <br> 平台打开该 VM。        |
|配置     |需要主机名、管理员用户和其他特定于 VM 的设置。         |已完全预配。         |
|使用时间     |从同一个映像创建多个新 VM。         |迁移特定的计算机，或从以前的备份还原 VM。         |


本文介绍从通用映像进行部署所需的步骤。 若要从专用映像进行部署，请参阅为设备[使用专用 Windows VHD](azure-stack-edge-placeholder.md)。

> [!IMPORTANT]
> 此过程不涉及使用自定义配置和设置配置源 VHD 的情况。 例如，若要对包含自定义防火墙规则或代理设置的 VHD 进行通用化，可能需要执行其他操作。 若要详细了解这些其他操作，请参阅[准备要上传到 Azure 的 Windows VHD - Azure 虚拟机](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。


## <a name="vm-image-workflow"></a>VM 映像工作流

准备 Windows VHD 以用作通用映像的大致工作流包括以下步骤：

1. 将源 VHD 或 VHDX 转换为固定大小的 VHD。
1. 使用固定 VHD 在 Hyper-V 中创建 VM。
1. 连接到 Hyper-V VM。
1. 使用 sysprep 实用工具来通用化 VHD。 
1. 将通用映像复制到 Blob 存储。
1. 使用通用映像在设备上部署 VM。 有关详细信息，请参阅如何[通过 Azure 门户部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) 或[通过 PowerShell 部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。


## <a name="prerequisites"></a>先决条件

在准备 Windows VHD 以用作 Azure Stack Edge 上的通用映像之前，请先确保：

- VHD 或 VHDX 包含受支持的 Windows 版本。 请参阅 Azure Stack Edge Pro 的[受支持的来宾操作系统]()。 
- 对安装了 Hyper-V 管理器的 Windows 客户端具有访问权限。 
- 在 VHD 准备完成后，你可以访问 Azure Blob 存储帐户来存储该 VHD。

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>从 VHD 准备通用 Windows 映像

## <a name="convert-to-a-fixed-vhd"></a>转换为固定 VHD 

对于你的设备，需要使用固定大小的 VHD 来创建 VM 映像。 需要将源 Windows VHD 或 VHDX 转换为固定 VHD。 执行以下步骤:

1. 在客户端系统上打开 Hyper-V 管理器。 转到“编辑磁盘”。

    ![打开 Hyper-V 管理器](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. 在“开始之前”页面上，选择“下一步 >” 。

1. 在“定位虚拟硬盘”页上，浏览到要转换的源 Windows VHD 或 VHDX 的位置。 选择“下一步 >”。

    ![找到虚拟硬盘页](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. 在“选择操作”页上，选择“转换”，然后选择“下一步 >”  。

    ![选择操作页](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. 在“选择磁盘格式”页上，选择“VHD 格式”，然后选择“下一步 >”  。

   ![选择磁盘格式页](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. 在“选择磁盘类型”页上，选择“固定大小”，然后选择“下一步 >”  。

   ![选择磁盘类型页](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. 在“配置磁盘”页上，浏览到该位置，并指定固定大小的 VHD 磁盘的名称。 选择“下一步 >”。

   ![配置磁盘页](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. 查看摘要并选择“完成”。 VHD 或 VHDX 转换会需要几分钟时间。 转换时间取决于源磁盘的大小。 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
在本文所有后续步骤中，将会使用此固定 VHD。


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>从固定 VHD 创建 Hyper-V VM

1. 在“Hyper-V 管理器”的范围窗格中，右键单击系统节点，以打开上下文菜单，然后选择“新建” > “虚拟机”  。

    ![在范围窗格中选择新虚拟机](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. 在“新建虚拟机向导”的“准备工作”页上，选择“下一步” 。

1. 在“指定名称和位置”页上，提供虚拟机的“名称”和“位置”  。 选择“**下一页**”。

    ![指定 VM 的名称和位置](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. 在“指定代系”页上，选择“第 1 代”作为 .vhd 设备映像类型，然后选择“下一步”  。    

    ![指定代系](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. 分配所需内存和网络配置。

1. 在“连接虚拟硬盘”页上，选择“使用现有虚拟硬盘”，指定前面创建的 Windows 固定 VHD 的位置，然后选择“下一步”  。

    ![“连接虚拟硬盘”页](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. 查看“摘要”，然后选择“完成”，以创建该虚拟机 。

创建虚拟机需要几分钟时间。
     

## <a name="connect-to-the-hyper-v-vm"></a>连接到 Hyper-V VM

VM 会出现在客户端系统上虚拟机的列表中。 


1. 选择该 VM，然后右键单击并选择“启动”。 

    ![选择 VM 并启动它](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. 该 VM 会显示为“正在运行”。 选择该 VM，然后右键单击并选择“连接”。

    ![连接到 VM](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

在连接到该 VM 后，请完成计算机设置向导，然后登录到该 VM。


## <a name="generalize-the-vhd"></a>通用化 VHD  

使用 sysprep 实用工具来通用化 VHD。 

1. 在 VM 中，打开命令提示符。
1. 运行以下命令来通用化该 VHD。 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    有关详细信息，请参阅 [Sysprep（系统准备）概述](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。
1.  在命令完成后，VM 将会关闭。 请勿重启 VM。

## <a name="upload-the-vhd-to-azure-blob-storage"></a>将 VHD 上传到 Azure Blob 存储

现在，可以使用该 VHD 在 Azure Stack Edge 上创建通用映像。 

1. 将该 VHD 上传到 Azure Blob 存储。 请参阅[使用 Azure 存储资源管理器上传 VHD](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md) 中的详细说明。
1. 在上传完成后，可以使用上传的映像来创建 VM 映像和 VM。 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>后续步骤

根据部署的性质，可以选择以下过程之一。

- [通过 Azure 门户从通用映像部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [通过 Azure PowerShell 从通用映像部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
