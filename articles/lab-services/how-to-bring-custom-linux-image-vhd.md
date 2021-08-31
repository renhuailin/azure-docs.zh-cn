---
title: Azure 实验室服务 - 如何从物理实验室环境引入 Linux 自定义映像
description: 描述如何从物理实验室环境中引入 Linux 自定义映像。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 68d0cde7e22b4175725241f402d9205c68b8ccd1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778590"
---
# <a name="bring-a-linux-custom-image-from-your-physical-lab-environment"></a>从物理实验室环境中引入 Linux 自定义映像

本文中的步骤说明如何导入从物理实验室环境启动的 Linux 自定义映像。  使用此方法，在物理环境中创建一个 VHD 并将其导入到共享映像库，以便能够在实验室服务中使用。  在使用此方法创建自定义映像之前，请阅读[创建自定义映像的建议方法](approaches-for-custom-image-creation.md)一文，以决定适用于你的方案的最佳方法。

Azure 认可多种[分发和版本](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions)。  从 VHD 中引入自定义 Linux 映像的步骤因每个分发而异。  每个分发都是不同的，因为每个分发都具有必须设置为在 Azure 上运行的唯一必备组件。

本文将介绍从 VHD 中引入自定义 Ubuntu 16.04\18.04\20.04 映像的步骤。  有关使用 VHD 为其他分发创建自定义映像的信息，请参阅 [Linux 分发的通用步骤](../virtual-machines/linux/create-upload-generic.md)一文。

## <a name="prerequisites"></a>先决条件

你需要有权在学校的 Azure 订阅中创建 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)才能完成本文中的步骤。

将映像从物理实验室环境移动到实验室服务时，应重构每个映像，以便仅包含实验室类所需的软件。  有关详细信息，请参阅[从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)博客文章。

## <a name="prepare-a-custom-image-using-hyper-v-manager"></a>使用 Hyper-V 管理器准备自定义映像

以下步骤介绍如何使用 Windows Hyper-V 管理器从 Hyper-V 虚拟机 (VM) 创建 Ubuntu 16.04\18.04\20.04 映像。

1.  将官方 [Linux Ubuntu 服务器](https://ubuntu.com/server/docs)映像下载到 Windows 主机，你将使用该主机在 Hyper-V VM 上设置自定义映像。

    我们建议使用未安装 [GNOME](https://www.gnome.org/) GUI 桌面的 Ubuntu 映像。  GNOME 当前与映像在 Azure 实验室服务中正常运行所需的 Azure Linux 代理冲突。  例如，我们建议你使用 Ubuntu 服务器映像并安装其他 GUI 桌面，如 XFCE 或 MATE。

    Ubuntu 还发布了预生成的 [Azure VHD 供下载](https://cloud-images.ubuntu.com/)。  但是，这些 VHD 专用于在 Linux 主机和虚拟机监控程序（如 KVM）中创建自定义映像。  这些 VHD 要求你先设置默认的用户密码，该步骤仅可使用 Linux 工具（如 qemu）完成，但这些 VHD 不适用于 Windows。  因此，当你使用 Windows Hyper-V 创建自定义映像时，你将无法连接到这些 VHD 来进行映像自定义。  有关预生成的 Azure VHD 的详细信息，请参阅 [Ubuntu 文档](https://help.ubuntu.com/community/UEC/Images?_ga=2.114783623.1858181609.1624392241-1226151842.1623682781#QEMU_invocation)。

1.  从物理实验室环境中已基于映像创建的 Hyper-V VM 开始。  有关设置以下设置的详细信息，请阅读[如何在 Hyper-V 中创建虚拟机](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)一文：
    -   VM 必须创建为第 1 代 VM。
    -   使用“默认交换机”网络配置选项允许 VM 连接到 Internet。
    -   在“连接虚拟硬盘”设置中，磁盘的“大小”不得超过 128 GB，如下图所示。
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="连接虚拟硬盘":::

    - 在“安装选项”设置中，选择之前从 Ubuntu 下载的 .iso 文件。

    实验室服务不支持磁盘大小大于 128 GB 的映像。

1.  按照本文中的步骤连接到 Hyper-V VM 并为 Azure 做好准备：
    -   [创建和上传 Ubuntu VHD 的手动步骤](../virtual-machines/linux/create-upload-ubuntu.md#manual-steps)

        为 Azure 准备 Linux 映像的步骤因分发而异。  有关每个分发的详细信息和具体步骤，请参阅[分发和版本](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions)一文。

    按照上述步骤进行操作时，需要重点介绍几个要点：
    -   运行 deprovision+user 命令时，这些步骤将创建[通用](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)映像。  它无法保证映像中的所有敏感信息均已清除，或该映像适合再分发。
    -   最后一步是将 VHDX 文件转换为 VHD 文件。  下面是说明如何通过 Hyper-V 管理器执行此操作的等效步骤：
        
        1.  导航到“Hyper-V 管理器” -> “操作” -> “编辑磁盘”。
        1.  接下来，将磁盘从 VHDX 转换为 VHD。
        1.  对于“磁盘类型”，选择“固定大小”。 
            - 如果此时还选择扩展磁盘大小，请确保不超过 128 GB。        
            :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="选择操作":::

为了帮助调整 VHD 大小并将其转换为 VHDX，还可使用以下 PowerShell cmdlet：
- [Resize-VHD](/powershell/module/hyper-v/resize-vhd?view=windowsserver2019-ps)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>将自定义映像上传到共享映像库

1. 将 VHD 上传到 Azure 以创建托管磁盘。
    1. 可以使用存储资源管理器，或在命令行中使用 AzCopy，如[将 VHD 上传到 Azure 或将托管磁盘复制到其他区域](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)中所示。        

    1. 上传 VHD 后，现在应具有可在 Azure 门户看到的托管磁盘。 
    
    如果计算机进入睡眠或锁定状态，上传过程可能会中断和失败。  此外，请确保在 AzCopy 完成后，撤销 SAS 对磁盘的访问权限。  否则，当你尝试从磁盘创建映像时，将看到一个错误：状态为“活动上传”的磁盘“磁盘名称”不支持操作“创建映像”。错误代码: OperationNotAllowed
    
    使用 Azure 门户的托管磁盘的“大小和性能”选项卡可以更改磁盘大小。 如前所述，大小不能超过 128 GB。

1. 在共享映像库中，创建映像定义和版本：
    1. [创建映像定义](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)。  
        - 对于“VM 代系”，选择“第 1 代”。
        - 选择“Linux”作为“操作系统” 。
        - 选择“通用”作为“操作系统状态” 。
     
    若要详细了解可以为映像定义指定的值，请参阅[映像定义](../virtual-machines/shared-image-galleries.md#image-definitions)。 
    
    还可以选择使用现有映像定义，并创建自定义映像的新版本。
    
1. [创建映像版本](../virtual-machines/windows/shared-images-portal.md#create-an-image-version)。
    - “版本号”属性采用以下格式：MajorVersion.MinorVersion.Patch。   使用实验室服务创建实验室并选择自定义映像时，将自动使用最新版本的映像。  依次根据版本最高的 MajorVersion、MinorVersion 和 Patch 选择最新版本。
    - 对于“源”，从下拉列表中选择“磁盘和/或快照”。
    - 对于“OS 磁盘”属性，选择在先前步骤中创建的磁盘。
    
    有关可为映像定义指定的值的详细信息，请参阅[映像定义](../virtual-machines/shared-image-galleries.md#image-versions)。

## <a name="create-a-lab"></a>创建实验室
   
1. 在实验室服务中[创建实验室](tutorial-setup-classroom-lab.md)，并从共享映像库中选择自定义映像。

    如果在原始 Hyper-V VM 上安装操作系统后扩展了磁盘，则还需要在 Linux 文件系统中扩展分区，才能使用未分配的磁盘空间：
    -   登录到实验室的模板 VM，并执行与[扩展磁盘分区和文件系统](../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem)一文中所示内容类似的步骤。  
    
    OS 磁盘通常位于 /dev/sad2 分区上。  若要查看 OS 磁盘分区的当前大小，请使用以下命令：df -h。
    
## <a name="next-steps"></a>后续步骤

* [共享映像库概述](../virtual-machines/shared-image-galleries.md)
* [附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)
* [如何使用共享映像库](how-to-use-shared-image-gallery.md)