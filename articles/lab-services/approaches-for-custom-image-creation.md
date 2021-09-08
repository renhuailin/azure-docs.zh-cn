---
title: Azure 实验室服务：为实验室创建自定义映像的建议方法
description: 介绍为实验室创建自定义映像的各种方法。
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 621456c910b5137ed14a77d8d2cfeb9664910fa9
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450632"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>创建自定义映像的建议方法
本文介绍了以下用于创建自定义映像的建议方法：

-   从[实验室的模板虚拟机 (VM)](how-to-create-manage-template.md) 创建和保存自定义映像。
-   使用以下方法从实验室上下文的外部引入自定义映像：
    - [Azure VM](https://azure.microsoft.com/services/virtual-machines/)。
    - 物理实验室环境中的 VHD。

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>保存实验室的模板 VM 中的自定义映像

使用实验室的模板 VM 创建和保存自定义映像是创建映像的最简单方法，因为可使用 Azure 实验室服务门户来实现。 这样，IT 部门和教师都可以使用实验室的模板 VM 创建自定义映像。

例如，可以先从某个 Azure 市场映像开始，然后安装班级所需的软件应用程序和工具。 完成映像设置后，可以将其保存在[连接的共享映像库](how-to-attach-detach-shared-image-gallery.md)中，以便你和其他教师可以使用该映像创建新的实验室。

使用此方法时，有几个要点需要注意：

- 实验室服务在导出模板 VM 中的映像时，会自动保存一个专用映像。 在大多数情况下，专用映像非常适合用于创建新的实验室，因为映像会保留特定于计算机的信息和用户配置文件。 使用专用映像有助于确保在使用映像创建新实验室时所安装的软件运行相同的功能。 如果需要创建通用映像，必须使用本文中建议的其他方法之一来创建自定义映像。

    可以基于 Azure 实验室服务中的通用映像和专用映像创建实验室。 有关差异的详细信息，请参阅[通用映像和专用映像](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)。

- 对于更高级的映像设置方案，你可能会发现，使用 Azure VM 或来自物理实验室环境的 VHD 在实验室外创建映像会很有用。 有关详细信息，请阅读后面各节。

### <a name="use-a-labs-template-vm-to-save-a-custom-image"></a>使用实验室的模板 VM 保存自定义映像 

可以使用实验室的模板 VM 创建 Windows 或 Linux 自定义映像。 有关详细信息，请参阅[将映像保存到共享映像库](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)。

## <a name="bring-a-custom-image-from-an-azure-vm"></a>从 Azure VM 引入自定义映像

另一种方法是使用 Azure VM 设置自定义映像。 完成映像设置后，可以将其保存到共享映像库，以便你和你的同事可以使用该映像创建新的实验室。

使用 Azure VM 可提供更大的灵活性：
- 可以创建[通用或专用](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)映像。 否则，如果使用[实验室的模板 VM ](how-to-use-shared-image-gallery.md)导出映像，映像始终为专用映像。
- 可以访问 Azure VM 的更高级功能，这些功能对于设置映像可能很有用。 例如，可以使用[扩展](../virtual-machines/extensions/overview.md)来完成部署后的配置和自动化。 此外，还可以访问 VM 的[启动诊断](../virtual-machines/boot-diagnostics.md)和[串行控制台](/troubleshoot/azure/virtual-machines/serial-console-overview)。

使用 Azure VM 设置映像会更加复杂。 因此，通常是 IT 部门负责在 Azure VM 上创建自定义映像。

### <a name="use-an-azure-vm-to-set-up-a-custom-image"></a>使用 Azure VM 设置自定义映像

以下是从 Azure VM 引入自定义映像的步骤概述：

1. 使用 Windows 和 Linux 市场映像创建 [Azure VM](https://azure.microsoft.com/services/virtual-machines/)。
1. 连接到 Azure VM 并安装其他软件。 还可以创建实验室所需的其他自定义。
1. 完成映像设置后，[将 VM 的映像保存到共享映像库](../virtual-machines/image-version.md)。 在此步骤中，还需要创建映像的定义和版本。
1. 将自定义映像保存到库中后，可以使用你的映像创建新的实验室。 


步骤会有所不同，这取决于创建的是自定义 Windows 映像还是自定义 Linux 映像。 有关详细步骤，请阅读以下文章：

-   [从 Azure VM 引入自定义 Windows 映像](how-to-bring-custom-windows-image-azure-vm.md)
-   [从 Azure VM 引入自定义 Linux 映像](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>从物理实验室环境中的 VHD 中引入自定义映像

要考虑的第三种方法是，将自定义映像从物理实验室环境中的 VHD 引入到共享映像库。 将映像引入共享映像库后，你和其他教师就可以使用该映像创建新的实验室。

以下是可能需要使用此方法的几个原因：

- 可以创建[通用或专用](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)映像以用于实验室。 否则，如果使用[实验室的模板 VM](how-to-use-shared-image-gallery.md) 导出映像，映像始终为专用映像。
- 可以访问本地环境中存在的资源。 例如，你可能在本地环境中有大型安装文件，将这些文件复制到实验室的模板 VM 会耗费太多时间。
- 可以使用其他工具（例如 [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction)）上传创建的映像，不必使用实验室的模板 VM 手动设置映像。

从 VHD 中引入自定义映像是最先进的方法，因为必须确保正确设置映像，使其在 Azure 中正常工作。 因此，通常是 IT 部门负责从 VHD 创建自定义映像。

### <a name="bring-a-custom-image-from-a-vhd"></a>从 VHD 引入自定义映像

以下是从 VHD 引入自定义映像的步骤概述：

1. 使用本地计算机上的 [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/) 创建 Windows 或 Linux VHD。
1. 连接到 Hyper-V VM 并安装其他软件。 还可以创建实验室所需的其他自定义。
1. 完成映像设置后，在 Azure 中上传 VHD 来创建[托管磁盘](../virtual-machines/managed-disks-overview.md)。
1. 在托管磁盘中，创建共享映像库中[映像的定义](../virtual-machines/shared-image-galleries.md#image-definitions)和版本。
1. 将自定义映像保存到库中后，可以使用该映像创建新的实验室。 

步骤会有所不同，这取决于创建的是自定义 Windows 映像还是自定义 Linux 映像。 有关详细步骤，请阅读以下文章：

-   [从 VHD 引入自定义 Windows 映像](upload-custom-image-shared-image-gallery.md)
-   [从 VHD 引入自定义 Linux 映像](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>后续步骤

* [共享映像库概述](../virtual-machines/shared-image-galleries.md)
* [附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)
* [使用共享映像库](how-to-use-shared-image-gallery.md)
