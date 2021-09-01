---
title: 从 ISO 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM
description: 介绍如何从 ISO 开始创建通用 Windows VM 映像。 使用此通用映像在 Azure Stack Edge Pro GPU 设备上部署虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 22f9c86342428d3d011eb787745f74ac6ea026f8
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970259"
---
# <a name="prepare-generalized-image-from-iso-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>从 ISO 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

要在 Azure Stack Edge Pro GPU 设备上部署 VM，你需要能够创建可用于创建 VM 的自定义虚拟机 (VM) 映像。 本文介绍如何使用 ISO 安装媒体准备 Windows VM 映像，然后将该映像通用化，以便可以使用它在 Azure Stack Edge Pro GPU 设备上部署多个新 VM。

要准备从 Windows VHD 或 VHDX 创建的通用映像，请参阅[从 Windows VHD 准备通用映像以在 Azure Stack Edge Pro GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)。

## <a name="about-vm-images"></a>关于 VM 映像

可以使用 Windows VHD 或 VHDX 创建专用映像或通用映像 。 下表汇总了专用映像与通用映像之间的主要差异 。

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>工作流 

使用 ISO 创建通用 Windows VHD 的高级工作流是：

1. 使用 ISO 映像准备源 VM：
   1. 在 Hyper-V 管理器中创建新的空白固定大小的 VHD。
   1. 使用该 VHD 创建新的虚拟机。
   1. 在新 VM 的 DVD 驱动器上装载 ISO 映像。
1. 启动 VM，并安装 Windows 操作系统。
1. 使用 sysprep 实用工具来通用化 VHD。
1. 将通用映像复制到 Azure Blob 存储。

## <a name="prerequisites"></a>先决条件

在可以使用 ISO 映像创建通用 Windows VHD 之前，确保：

- 你有一个 ISO 映像，适用于你想要转换成通用 VHD 的受支持 Windows 版本。 可从 [Microsoft 评估中心](https://www.microsoft.com/en-us/evalcenter/)下载 Windows ISO 映像。

- 对安装了 Hyper-V 管理器的 Windows 客户端具有访问权限。

- 在 VHD 准备完成后，你可以访问 Azure Blob 存储帐户来存储该 VHD。

## <a name="prepare-source-vm-using-an-iso"></a>使用 ISO 准备源 VM

使用 ISO 映像在 VM 映像上安装操作系统时，首先需要在 Hyper-V 管理器中创建一个空白的固定大小的 VHD。 然后使用该 VHD 创建虚拟机。 再将 ISO 映像附加到 VM。

#### <a name="create-new-vhd-in-hyper-v-manager"></a>在 Hyper-V 管理器中创建新的 VHD

第一步是在 Hyper-V 管理器中创建新的第 1 代 VHD，该 VHD 将作为新虚拟机的源 VHD。

要创建 VHD，请执行以下步骤：

1. 在客户端系统上打开 Hyper-V 管理器。 在“操作”菜单上，选择“新建”，然后选择“硬盘”。  

   ![选择“新建”，然后选择“硬盘”](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

1. 在“选择磁盘格式”下，选择“VHD”。  然后，选择“下一步 >”。 

   ![选择 VHD 作为磁盘格式](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

2. 在“选择磁盘类型”下，选择“固定大小”。  然后，选择“下一步 >”。

   ![选择“固定大小”作为磁盘类型](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-03.png)

3. 在“指定名称和位置”下，输入新 VHD 的名称和位置。 然后，选择“下一步 >”。

   ![输入 VHD 的名称和位置](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-04.png)

4. 在“配置磁盘”下，选择“创建新的空白虚拟硬盘”，并输入要创建的磁盘大小（对于 Windows Server，一般为 20 GB 和更高）。  然后，选择“下一步 >”。

   ![用于创建新的空白虚拟硬盘和指定大小的设置](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-05.png)

5. 在“摘要”下，查看你的选择，然后选择“完成”以创建新的 VHD。  此过程需要 5 分钟或更长时间，具体取决于创建的 VHD 的大小。

   ![VHD 设置摘要](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-06.png)

#### <a name="create-hyper-v-vm-from-vhd"></a>从 VHD 创建 Hyper-V VM

现在，你将使用刚刚创建的 VHD 创建新的虚拟机。

要创建新的虚拟机，请执行以下步骤：

1. 在 Windows 客户端上打开 Hyper-V 管理器。

2. 在“操作”窗格中，选择“新建”，然后选择“虚拟机”。  

   ![从右侧菜单中选择“新建”，然后选择“虚拟机”。](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-07.png)

3. 在“新建虚拟机向导”中，指定 VM 的名称和位置。

   ![新建虚拟机向导，指定名称和位置](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-08.png)

4. 在“指定代数”下，选择“第 1 代”。  然后，选择“下一步 >”。

   ![新建虚拟机向导，选择要创建的虚拟机的代数](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-09.png)

5. 在“分配内存”下，将所需内存分配给虚拟机。 然后，选择“下一步 >”。

   ![新建虚拟机向导，分配内存](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-10.png)

6. 在“配置网络”下，输入网络配置。 然后，选择“下一步 >”。

   ![新建虚拟机向导，配置网络](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-11.png)

7. 在“连接虚拟硬盘”下，选择“使用现有虚拟硬盘”，并浏览到在上一过程中创建的固定 VHD。  然后，选择“下一步 >”。 

   ![新建虚拟机向导，选择现有的虚拟硬盘作为源](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-12.png)

8. 查看“摘要”，然后选择“完成”，以创建该虚拟机。 

#### <a name="mount-iso-image-on-dvd-drive-of-vm"></a>在 VM 的 DVD 驱动器上装载 ISO 映像

创建新的虚拟机后，请按照以下步骤在虚拟机的 DVD 驱动器上装载 ISO 映像：

1. 在 Hyper-V 管理器中，选择刚创建的 VM，然后选择“设置”。
 
   ![在 Hyper-V 管理器中，打开虚拟机设置](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-13.png)

2. 在“BIOS”下，确保“CD”位于“启动顺序”列表的顶部。  

   ![在 BIOS 设置中，“启动顺序”下的第一项应为 CD](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-14.png)


3. 在“DVD 驱动器”下，选择“映像文件”，然后浏览到 ISO 映像。   

   ![在“DVD 驱动器设置”中，为 VHD 选择映像文件](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-15.png)

4. 选择“确定”以保存 VM 设置。

## <a name="start-vm-and-complete-os-installation"></a>启动 VM，并完成操作系统安装

为了完成生成虚拟机的操作，需要启动该虚拟机并逐步完成操作系统安装过程。

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

> [!NOTE]
> 如果在虚拟机上安装了 Windows Server 2019 Standard 操作系统，则需要在[通用化 VHD](#generalize-the-vhd) 之前将 BIOS 设置更改为 IDE。  

## <a name="generalize-the-vhd"></a>通用化 VHD

使用 sysprep 实用工具来通用化 VHD。

1. 如果要通用化 Windows Server 2019 Standard VM，那么在通用化 VHD 之前，请将 IDE 设置为虚拟机的第一个 BIOS 设置。

    1. 在“Hyper-V 管理器”中，选择“VM”，然后选择“设置”。
 
       ![屏幕截图：显示如何为 Hyper-V 管理器中选定的 VM 打开设置](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

     1. 在“BIOS”下，确保“IDE”位于“启动顺序”列表的顶部。   然后，选择“确定”以保存设置。

        ![屏幕截图：显示在 Hyper-V 管理器中 VM 的 BIOS 设置中，IDE 位于启动顺序的顶部](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

1. 在 VM 中，打开命令提示符。

1. 运行以下命令来通用化该 VHD。 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    有关详细信息，请参阅 [Sysprep（系统准备）概述](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

1.  在命令完成后，VM 将会关闭。 请勿重启 VM。

<!--[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]-->

现在，你可以使用 VHD 创建要在 Azure Stack Edge Pro GPU 上使用的通用映像。

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>将通用 VHD 上传到 Azure Blob 存储

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户从通用映像部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [从 Windows VHD 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [准备专用映像并使用该映像部署 VM](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
