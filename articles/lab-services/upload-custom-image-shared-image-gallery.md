---
title: Azure 实验室服务 - 将 Windows 自定义映像引入共享映像库
description: 介绍如何将 Windows 自定义映像引入共享映像库。
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 87ae45819a19390b1776cc81e45b85b4ba1e2aee
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967785"
---
# <a name="bring-a-windows-custom-image-to-shared-image-gallery"></a>将 Windows 自定义映像引入共享映像库

可以使用共享映像库引入自己的 Windows 自定义映像，并使用这些映像在 Azure 实验室服务中创建实验室。  本文演示如何从以下位置引入自定义映像：

* [物理实验室环境](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-a-physical-lab-environment)。
* [Azure 虚拟机](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-an-azure-virtual-machine)。

此任务通常由学校的 IT 部门执行。

## <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>从物理实验室环境引入 Windows 自定义映像

本部分中的步骤说明如何导入从物理实验室环境启动的自定义映像。  使用此方法，在物理环境中创建一个 VHD 并将其导入到共享映像库，以便能够在实验室服务中使用。  下面是此方法的部分主要优势：

* 可以灵活地创建[通用或专用](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)映像以用于实验室。  否则，如果使用[实验室的模板 VM 导出映像](how-to-use-shared-image-gallery.md)，映像始终为专用映像。
* 可以上传使用其他工具（例如 [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction)）创建的映像，以便不必使用实验室的模板 VM 即可手动设置映像。

本部分中的步骤要求你有权在学校的 Azure 订阅中创建[托管磁盘](../virtual-machines/managed-disks-overview.md)。

> [!IMPORTANT]
> 将映像从物理实验室环境移动到实验室服务时，应重构每个映像，以便仅包含实验室类所需的软件。  有关详细信息，请参阅[从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)博客文章。

可通过许多方法在物理实验室环境中创建 VHD。 以下步骤演示如何使用 Hyper-V 管理器从 Windows Hyper-V 虚拟机 (VM) 创建 VHD。

1. 从物理实验室环境中已基于映像创建的 Hyper-V VM 开始。  有关详细信息，请参阅[如何在 Hyper-V 中创建虚拟机](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)一文。
    1. VM 必须创建为第 1 代 VM。
    1. VM 的虚拟磁盘必须是固定大小的 VHD。  磁盘大小不能超过 128 GB。 创建 VM 时，输入磁盘大小，如下图所示。
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="连接虚拟硬盘":::

    > [!IMPORTANT]
    > 实验室服务不支持磁盘大小大于 128 GB 的映像。 
   
1. 按照以下步骤连接到 Hyper-V VM 并[为 Azure 做好准备](../virtual-machines/windows/prepare-for-upload-vhd-image.md)：
    1. [设置 Azure 的 Windows 配置](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)。
    1. [检查确保 VM 连接所需的 Windows 服务](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)。
    1. [更新远程桌面注册表设置](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)。
    1. [配置 Windows 防火墙规则](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)。
    1. [安装 Windows 更新](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。
    1. [安装 Azure VM 代理和其他配置（如下所示）](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
        
    可以将专用或通用映像上传到共享映像库，并使用它们来创建实验室。  上述步骤将创建一个专用映像。 如果需要改为创建通用映像，则还需要[运行 SysPrep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)。  

    > [!IMPORTANT]
    > 如果要保留计算机特定的信息和用户配置文件，应创建专用映像。  有关通用映像与专用映像之间的差异的详细信息，请参阅[通用和专用映像](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)。

1. 由于 Hyper-V 默认创建 VHDX 文件，因此需要将其转换为 VHD 文件。
    1. 导航到“Hyper-V 管理器” -> “操作” -> “编辑磁盘”。
    1. 接下来，将磁盘从 VHDX 转换为 VHD。  
     - 如果扩展磁盘大小，请确保不超过 128 GB。        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="选择操作":::   

    有关详细信息，请阅读演示如何[将虚拟磁盘转换为固定大小的 VHD](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd) 的文章。

1. 将 VHD 上传到 Azure 以创建托管磁盘。
    1. 可以使用存储资源管理器，或在命令行中使用 AzCopy，如[将 VHD 上传到 Azure 或将托管磁盘复制到其他区域](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)中所示。        

    1. 上传 VHD 后，现在应具有可在 Azure 门户看到的托管磁盘。 
    
    > [!WARNING]
    > 如果计算机进入睡眠或锁定状态，上传过程可能会中断和失败。 
    
    > [!IMPORTANT] 
    > 使用 Azure 门户的托管磁盘的“大小和性能”选项卡可以更改磁盘大小。 如前所述，大小不能超过 128 GB。

1. 创建托管磁盘的快照。
    使用 PowerShell、Azure 门户或存储资源管理器均可完成此任务，如[使用门户或 PowerShell 创建快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)中所示。

1. 在共享映像库中，创建映像定义和版本：
    1. [创建映像定义](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)。  
     - 对于“VM 代系”，选择“第 1 代”。
     - 选择要为操作系统状态创建专用还是通用映像。
     
    若要详细了解可以为映像定义指定的值，请参阅[映像定义](../virtual-machines/shared-image-galleries.md#image-definitions)。 
    
    > [!NOTE] 
    > 还可以选择使用现有映像定义，并创建自定义映像的新版本。
    
1. [创建映像版本](../virtual-machines/windows/shared-images-portal.md#create-an-image-version)。
    - “版本号”属性采用以下格式：MajorVersion.MinorVersion.Patch。   使用实验室服务创建实验室并选择自定义映像时，将自动使用最新版本的映像。  依次根据版本最高的 MajorVersion、MinorVersion 和 Patch 选择最新版本。
    - 对于“源”，从下拉列表中选择“磁盘和/或快照”。
    - 对于“OS 磁盘”属性，选择在先前步骤中创建的快照。
    
    有关可为映像定义指定的值的详细信息，请参阅[映像定义](../virtual-machines/shared-image-galleries.md#image-versions)。 
   
1. 在实验室服务中[创建实验室](tutorial-setup-classroom-lab.md)，并从共享映像库中选择自定义映像。

    如果在原始 Hyper-V VM 上安装操作系统后扩展了磁盘，则还需要在 Windows 中扩展 C 盘，才能使用未分配的磁盘空间：      
    - 登录到实验室的模板 VM，然后按照[扩展基本卷](/windows-server/storage/disk-management/extend-a-basic-volume)中所述的类似步骤执行操作。 

## <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>从 Azure 虚拟机引入 Windows 自定义映像
另一种方法是使用 [Azure 虚拟机](../virtual-machines/windows/overview.md)设置 Windows 映像。  使用 Azure 虚拟机 (VM) 可以灵活地创建专用或通用映像以用于实验室。  与[从 VHD 创建映像](upload-custom-image-shared-image-gallery.md#bring-a-windows-custom-image-from-a-physical-lab-environment)相比，从 Azure VM 准备映像的过程更简单，因为映像已准备好在 Azure 中运行。

你需要有权在学校的 Azure 订阅中创建 Azure VM 才能完成以下步骤：

1. 使用 [Azure 门户](../virtual-machines/windows/quick-create-portal.md)、[PowerShell](../virtual-machines/windows/quick-create-powershell.md)、[Azure CLI](../virtual-machines/windows/quick-create-cli.md) 或从 [ARM 模板](../virtual-machines/windows/quick-create-template.md)创建 Azure VM。
    
    - 指定磁盘设置时，请确保磁盘的大小不超过 128 GB。
    
1. 安装软件并对 Azure VM 的映像进行任何必要的配置更改。

1. 如果需要创建通用映像，请运行 [SysPrep](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)。  有关详细信息，请参阅[通用和专用映像](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)。

1. 在共享映像库中，[创建映像定义](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)或选择现有映像定义。
     - 对于“VM 代系”，选择“第 1 代”。
     - 选择要为操作系统状态创建专用还是通用映像。
    
1. [创建映像版本](../virtual-machines/windows/shared-images-portal.md#create-an-image-version)。
    - “版本号”属性采用以下格式：MajorVersion.MinorVersion.Patch。   
    - 对于“源”，从下拉列表中选择“磁盘和/或快照”。
    - 对于“OS 磁盘”属性，选择在先前步骤中创建的 Azure VM 磁盘。

1. 在实验室服务中[创建实验室](tutorial-setup-classroom-lab.md)，并从共享映像库中选择自定义映像。

还可以使用 PowerShell 将自定义映像从 Azure VM 导入到共享映像库。  有关详细信息，请参阅以下脚本和随附的自述文件：
    
- [将映像引入共享映像库脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="next-steps"></a>后续步骤

* [共享映像库概述](../virtual-machines/shared-image-galleries.md)
* [附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)
* [如何使用共享映像库](how-to-use-shared-image-gallery.md)