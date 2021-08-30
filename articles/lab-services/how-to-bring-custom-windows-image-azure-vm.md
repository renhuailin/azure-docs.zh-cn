---
title: Azure 实验室服务 - 如何从 Azure 虚拟机中引入 Windows 自定义映像
description: 描述如何从 Azure 虚拟机中引入 Windows 自定义映像。
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: ab79f4acb0b61a46e4a8f1cebe243e6df43702bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778099"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>从 Azure 虚拟机引入 Windows 自定义映像

本文中的步骤显示了如何导入从[Azure 虚拟机 (VM)](https://azure.microsoft.com/services/virtual-machines/) 开始的自定义图像。  通过此方法，你可以在 Azure VM 上设置图像，并将图像导入共享图像库，以便在实验室服务中使用。  在使用此方法创建自定义图像之前，请阅读文章[创建自定义图像的建议方法](approaches-for-custom-image-creation.md)以决定适用于你方案的最佳方法。

## <a name="prerequisites"></a>先决条件

你需要有权在学校的 Azure 订阅中创建 Azure VM 才能完本文中的步骤。

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>在 Azure VM 上准备自定义映像

1. 使用 [Azure 门户](../virtual-machines/windows/quick-create-portal.md)、[PowerShell](../virtual-machines/windows/quick-create-powershell.md)、[Azure CLI](../virtual-machines/windows/quick-create-cli.md) 或从 [ARM 模板](../virtual-machines/windows/quick-create-template.md)创建 Azure VM。
    
    - 指定磁盘设置时，请确保磁盘的大小不超过 128 GB。
    
1. 安装软件并对 Azure VM 的映像进行任何必要的配置更改。

1. 也可以通用化映像。 如果需要创建通用映像，请运行 [SysPrep](../virtual-machines/generalize.md#windows)。  否则，如果创建的是专用映像，则可以跳到下一步。

    如果要保留计算机特定的信息和用户配置文件，应创建专用映像。  有关通用映像与专用映像之间的差异的详细信息，请参阅[通用和专用映像](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)。

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>将自定义映像导入共享映像库

1. 在共享映像库中，[创建映像定义](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)或选择现有映像定义。
     - 对于“VM 代系”，选择“第 1 代”。
     - 选择要为操作系统状态创建专用还是通用映像。

    若要详细了解可以为映像定义指定的值，请参阅[映像定义](../virtual-machines/shared-image-galleries.md#image-definitions)。 
    
    还可以选择使用现有映像定义，并创建自定义映像的新版本。
    
1. [创建映像版本](../virtual-machines/windows/shared-images-portal.md#create-an-image-version)。
    - “版本号”属性采用以下格式：MajorVersion.MinorVersion.Patch。   
    - 对于“源”，从下拉列表中选择“磁盘和/或快照”。
    - 对于“OS 磁盘”属性，选择在先前步骤中创建的 Azure VM 磁盘。

    还可以使用 PowerShell 将自定义映像从 Azure VM 导入到共享映像库。  有关详细信息，请参阅以下脚本和随附的自述文件：
    - [将映像引入共享映像库脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/)

## <a name="create-a-lab"></a>创建实验室

1. 在实验室服务中[创建实验室](tutorial-setup-classroom-lab.md)，并从共享映像库中选择自定义映像。

## <a name="next-steps"></a>后续步骤

* [共享映像库概述](../virtual-machines/shared-image-galleries.md)
* [附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)
* [如何使用共享映像库](how-to-use-shared-image-gallery.md)