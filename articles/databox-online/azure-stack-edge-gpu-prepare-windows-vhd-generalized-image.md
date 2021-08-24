---
title: 从 Windows VHD 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM
description: 介绍如何从 Windows VHD 或 VHDX 开始创建通用 VM 映像。 使用此通用 VM 映像在 Azure Stack Edge Pro GPU 设备上部署虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/18/2021
ms.author: alkohli
ms.openlocfilehash: e60f7e7195ef81b3bf17a85c5662824df945ef91
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968362"
---
# <a name="prepare-generalized-image-from-windows-vhd-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>从 Windows VHD 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

若要在 Azure Stack Edge Pro GPU 设备上部署 VM，你需要能够创建可用于创建 VM 的自定义 VM 映像。 本文介绍如何从 Windows VHD 或 VHDX 准备一个可用于在 Windows Stack Edge Pro GPU 设备上部署虚拟机的通用映像。

若要使用 ISO 准备通用 VM 映像，请参阅[从 ISO 准备通用映像，以在 Azure Stack Edge PRO GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)。

## <a name="about-vm-images"></a>关于 VM 映像

可以使用 Windows VHD 或 VHDX 创建专用映像或通用映像 。 下表汇总了专用映像与通用映像之间的主要差异 。

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>工作流

从现有虚拟机的 VHD 或 VHDX 着手准备一个用作通用映像的 Windows VHD 时，其工作流大致包含以下步骤：

1. 从 Windows VHD 准备源 VM：
   1. 将源 VHD 或 VHDX 转换为固定大小的 VHD。
   1. 使用该 VHD 创建新的虚拟机。<!--Can this procedure be generalized and moved to an include file?-->
1. 启动 VM，并安装 Windows 操作系统。
1. 使用 sysprep 实用工具来通用化 VHD。
1. 将通用映像复制到 Blob 存储。

## <a name="prerequisites"></a>先决条件

在准备一个要在 Azure Stack Edge Pro GPU 设备上用作通用映像的 Windows VHD 之前，请先确保：

- VHD 或 VHDX 包含受支持的 Windows 版本。 
- 对安装了 Hyper-V 管理器的 Windows 客户端具有访问权限。 
- 在 VHD 准备完成后，你可以访问 Azure Blob 存储帐户来存储该 VHD。

## <a name="prepare-source-vm-from-windows-vhd"></a>从 Windows VHD 准备源 VM

如果 VM 源是 Windows VHD 或 VHDX，则首先需要将 Windows VHD 转换为固定大小的 VHD。 需使用固定大小的 VHD 创建新的虚拟机。

> [!IMPORTANT]
> 这些过程不涉及使用自定义配置和设置来配置源 VHD 的情况。 例如，若要对包含自定义防火墙规则或代理设置的 VHD 进行通用化，可能需要执行其他操作。 若要详细了解这些其他操作，请参阅[准备要上传到 Azure 的 Windows VHD - Azure 虚拟机](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。

#### <a name="convert-source-vhd-to-a-fixed-size-vhd"></a>将源 VHD 转换为固定大小的 VHD

对于你的设备，需要使用固定大小的 VHD 来创建 VM 映像。 需要将源 Windows VHD 或 VHDX 转换为固定 VHD。 

执行以下步骤:

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
在本文的所有后续步骤中，需使用此固定大小的 VHD。

#### <a name="create-hyper-v-vm-from-the-fixed-size-vhd"></a>从固定大小的 VHD 创建 Hyper-V VM

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

VM 会出现在客户端系统上虚拟机的列表中。

## <a name="start-vm-and-install-operating-system"></a>启动 VM 并安装操作系统

为了完成生成虚拟机的操作，需要启动该虚拟机并逐步完成操作系统安装过程。

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

在连接到该 VM 后，请完成计算机设置向导，然后登录到该 VM。<!--It's not clear what they are doing here. Where does the Machine setup wizard come in?-->

## <a name="generalize-the-vhd"></a>通用化 VHD

使用 sysprep 实用工具来通用化 VHD。 

[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]

现在，可以使用你的 VHD 创建要在在 Azure Stack Edge Pro GPU 上使用的通用映像。

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>将通用 VHD 上传到 Azure Blob 存储

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->

## <a name="next-steps"></a>后续步骤

根据部署的性质，可以选择以下过程之一。

- [通过 Azure 门户从通用映像部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [从 ISO 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [准备专用映像并使用该映像部署 VM](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
