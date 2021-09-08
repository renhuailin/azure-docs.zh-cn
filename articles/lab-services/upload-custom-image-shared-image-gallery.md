---
title: Azure 实验室服务 - 如何从物理实验室环境引入 Windows 自定义映像
description: 介绍如何从物理实验室环境引入 Windows 自定义映像。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: b838517a82ea0d6fb9437ff52c2b7a16098ee3ad
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451414"
---
# <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>从物理实验室环境引入 Windows 自定义映像

本文中的步骤说明了如何导入从物理实验室环境启动的自定义映像。  利用此方法，从物理环境创建 VHD 并将该 VHD 导入到共享映像库中，这样就能够在实验室服务中使用它。  在使用此方法创建自定义映像之前，请阅读[创建自定义映像的建议方法](approaches-for-custom-image-creation.md)一文，以决定适用于你的方案的最佳方法。

## <a name="prerequisites"></a>先决条件

你需要有权在学校的 Azure 订阅中创建 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)才能完成本文中的步骤。

将映像从物理实验室环境移动到实验室服务时，应重构每个映像，以便仅包含实验室类所需的软件。  有关详细信息，请参阅[从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)博客文章。

## <a name="prepare-a-custom-image-using-hyper-v-manager"></a>使用 Hyper-V 管理器准备自定义映像

以下步骤介绍如何使用 Hyper-V 管理器从 Windows Hyper-V 虚拟机 (VM) 创建 Windows 映像：

1. 从物理实验室环境中已基于映像创建的 Hyper-V VM 开始。  有关详细信息，请参阅[如何在 Hyper-V 中创建虚拟机](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)一文。
    -   VM 必须创建为第 1 代 VM。
    -   使用“默认交换机”网络配置选项以允许 VM 连接到 Internet。
    -   VM 的虚拟磁盘必须是固定大小的 VHD。  磁盘大小不能超过 128 GB。 创建 VM 时，输入磁盘大小，如下图所示。
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="连接虚拟硬盘":::

    实验室服务不支持磁盘大小大于 128 GB 的映像。 
   
1. 按照以下步骤连接到 Hyper-V VM 并[为 Azure 做好准备](../virtual-machines/windows/prepare-for-upload-vhd-image.md)：
    1. [设置 Azure 的 Windows 配置](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)。
    1. [检查确保 VM 连接所需的 Windows 服务](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)。
    1. [更新远程桌面注册表设置](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)。
    1. [配置 Windows 防火墙规则](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)。
    1. [安装 Windows 更新](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。
    1. [安装 Azure VM 代理和其他配置（如下所示）](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
        
    可以将专用或通用映像上传到某个共享映像库，然后使用它们来创建实验室。  上述步骤将创建一个专用映像。 如果需要改为创建通用映像，则还需要[运行 SysPrep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)。  

    如果要保留计算机特定的信息和用户配置文件，应创建专用映像。  有关通用映像与专用映像之间的差异的详细信息，请参阅[通用和专用映像](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)。

1. 由于 Hyper-V 默认创建 VHDX 文件，因此需要将其转换为 VHD 文件。
    1. 导航到“Hyper-V 管理器” -> “操作” -> “编辑磁盘”。
    1. 接下来，将磁盘从 VHDX 转换为 VHD。  
     - 如果扩展磁盘大小，请确保不超过 128 GB。        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="选择操作":::   

    有关详细信息，请阅读演示如何[将虚拟磁盘转换为固定大小的 VHD](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd) 的文章。

为了重设 VHD 的大小并将其转换为 VHDX，还可以使用以下 PowerShell cmdlet：
- [Resize-VHD](/powershell/module/hyper-v/resize-vhd?view=windowsserver2019-ps)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>将自定义映像上传到共享映像库

1. 将 VHD 上传到 Azure 以创建托管磁盘。
    1. 可以使用存储资源管理器，或在命令行中使用 AzCopy，如[将 VHD 上传到 Azure 或将托管磁盘复制到其他区域](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)中所示。        

    1. 上传 VHD 后，现在应具有可在 Azure 门户看到的托管磁盘。 
    
    如果计算机进入睡眠或锁定状态，上传过程可能会中断和失败。  此外，请确保在 AzCopy 完成后撤销 SAS 对磁盘的访问权限。  否则，你在尝试从磁盘创建映像时会看到错误：状态为“活动上传”的磁盘“磁盘名称”不支持“创建映像”操作。错误代码：OperationNotAllowed
    
    使用 Azure 门户的托管磁盘的“大小和性能”选项卡可以更改磁盘大小。 如前所述，大小不能超过 128 GB。

1. 在共享映像库中，创建映像定义和版本：
    1. [创建映像定义](../virtual-machines/image-version.md)。  
     - 对于“VM 代系”，选择“第 1 代”。
     - 选择要为操作系统状态创建专用还是通用映像。
     
    若要详细了解可以为映像定义指定的值，请参阅[映像定义](../virtual-machines/shared-image-galleries.md#image-definitions)。 
    
    还可以选择使用现有映像定义，并创建自定义映像的新版本。
    
1. [创建映像版本](../virtual-machines/image-version.md)。
    - “版本号”属性采用以下格式：MajorVersion.MinorVersion.Patch。   使用实验室服务创建实验室并选择自定义映像时，将自动使用最新版本的映像。  依次根据版本最高的 MajorVersion、MinorVersion 和 Patch 选择最新版本。
    - 对于“源”，从下拉列表中选择“磁盘和/或快照”。
    - 对于“OS 磁盘”属性，选择在先前步骤中创建的磁盘。
    
    有关可为映像定义指定的值的详细信息，请参阅[映像定义](../virtual-machines/shared-image-galleries.md#image-versions)。 

## <a name="create-a-lab"></a>创建实验室

1. 在实验室服务中[创建实验室](tutorial-setup-classroom-lab.md)，并从共享映像库中选择自定义映像。

    在原始 Hyper-V VM 上安装操作系统之后，如果扩展了磁盘，则还需要在 Windows 中扩展 C 盘，才能使用未分配的磁盘空间：      
    - 登录到实验室的模板 VM，然后按照[扩展基本卷](/windows-server/storage/disk-management/extend-a-basic-volume)中所述的类似步骤执行操作。

## <a name="next-steps"></a>后续步骤

* [共享映像库概述](../virtual-machines/shared-image-galleries.md)
* [附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)
* [如何使用共享映像库](how-to-use-shared-image-gallery.md)