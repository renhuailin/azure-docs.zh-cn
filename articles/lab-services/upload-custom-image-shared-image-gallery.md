---
title: Azure 实验室服务 - 将自定义映像上传到共享映像库
description: 介绍如何将自定义映像上传到共享映像库。 大学 IT 部门将会发现导入映像特别有用。
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 3e2f38b0cab87eab27181ddef79d0c02bd8c9bdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787157"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>将自定义映像上传到共享映像库

通过共享映像库可导入自己的自定义映像，以便在 Azure 实验室服务中创建实验室。 大学 IT 部门将会发现导入映像特别有用，原因如下： 

* 无需使用实验室模板 VM 来手动创建映像。
* 可以上传使用其他工具（如 SCCM、终结点管理器等）创建的映像。

本文将介绍引入自定义映像以及在 Azure 实验室服务中使用它们需采取的步骤。 

> [!IMPORTANT]
> 将映像从物理实验室环境移到 Az 实验室后，需要适当地重建它们。 不要只是重用来自物理实验室的现有映像。 <br/>有关详细信息，请参阅[从物理实验室移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)博客文章。

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>从物理实验室环境引入自定义映像

以下步骤说明如何导入从物理实验室环境启动的自定义映像。 接下来，在此环境中创建一个 VHD 并将其导入到 Azure 中的共享映像库，以便能够在 Azure 实验室服务中使用。

可通过许多方法在物理实验室环境中创建 VHD。 以下步骤演示了如何在 Windows Hyper-V VM 中创建 VHD：

1. 从物理实验室环境中已基于映像创建的 Hyper-V VM 开始。
    1. VM 必须创建为第 1 代 VM。
    1. VM 必须使用固定磁盘大小。 还可以在此窗口中指定磁盘的大小。 磁盘大小不能超过 128 GB。<br/>    
    Azure 实验室服务不支持磁盘大小 > 128 GB 的映像。 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="连接虚拟硬盘":::   
    1. 像往常一样对 VM 执行映像。
1. [连接到 VM 并为 Azure 做好准备](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。
    1. [设置 Azure 的 Windows 配置](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [检查确保 VM 连接至少所需的 Windows 服务](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [更新远程桌面注册表设置](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [配置 Windows 防火墙规则](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. 安装 Windows 更新
    1. [安装 Azure VM 代理和其他配置（如下所示）](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    上述步骤将创建一个专用映像。 如果要创建通用映像，还需要运行 [SysPrep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)。 <br/>
        如果要维护映像中包括的软件所需的用户目录（可能包含文件、用户帐户信息等），则应创建专用映像。
1. 由于 Hyper-V 默认创建 VHDX 文件，因此需要将其转换为 VHD 文件。
    1. 导航到“Hyper-V 管理器” -> “操作” -> “编辑磁盘”。
    1. 在这里，可以选择将磁盘从 VHDX 转换为 VHD
    1. 尝试扩展磁盘大小时，请确保不要超过 128 GB。        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="选择操作":::   
1. 将 VHD 上传到 Azure 以创建托管磁盘。
    1. 可以使用存储资源管理器，或在命令行中使用 AzCopy，如[将 VHD 上传到 Azure 或将托管磁盘复制到其他区域](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)中所述。        
    如果计算机进入睡眠或锁定状态，上传过程可能会中断和失败。
    1. 完成此步骤后，现在则可以在 Azure 门户中看到一个托管磁盘。 
        可以使用 Azure 门户的“大小\性能”选项卡来选择自己的磁盘大小。 如前所述，大小不得超过 128 GB。
1. 创建托管磁盘的快照。
    使用 PowerShell、Azure 门户或存储资源管理器均可完成此任务，如[使用门户或 PowerShell 创建快照](../virtual-machines/windows/snapshot-copy-managed-disk.md)中所述。
1. 在共享映像库中，创建映像定义和版本：
    1. [创建映像定义](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)。
    1. 此外，还需要在此处指定要创建专用还是通用映像。
1. 在 Azure 实验室服务中创建实验室，并从共享映像库中选择自定义映像。

    如果在原始 Hyper-V VM 上安装操作系统后扩展了磁盘，则还需要在 Windows 中扩展 C 盘，才能使用未分配的磁盘空间。 为此，请在创建实验室后登录到模板 VM，然后按照“[扩展基本卷](/windows-server/storage/disk-management/extend-a-basic-volume)”中所述的类似步骤执行操作。 还可以通过 UI 以及使用 PowerShell 来执行此操作。

## <a name="next-steps"></a>后续步骤

* [共享映像库概述](../virtual-machines/shared-image-galleries.md)
* [如何使用共享映像库](how-to-use-shared-image-gallery.md)