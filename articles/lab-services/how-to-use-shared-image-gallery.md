---
title: 在 Azure 实验室服务中使用共享映像库 | Microsoft Docs
description: 了解如何将实验室帐户配置为使用共享映像库，以便用户可以与其他用户共享映像，其他用户可以使用该映像在实验室中创建模板 VM。
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 8a0cd273d2444281fd2c43823aaa822b5e476ed0
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450541"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中使用共享映像库

映像包含 VM 上安装的操作系统、软件应用程序、文件和设置。  设置新实验室时，可以使用两种类型的映像：
-   Microsoft 预装的、在 Azure 内使用的市场映像。  这些映像已安装Windows 或 Linux，并且可能还包括软件应用程序。  例如，[Data Science Virtual Machine 映像](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)包括已安装的深度学习框架和工具。
-   由机构 IT 部门以及\或其他教师创建的自定义映像。  可以同时创建 Windows 和 Linux 自定义映像，并灵活地根据你的独特需求安装 Microsoft 和第三方应用程序。  还可以添加文件、更改应用程序设置等。

本文介绍教师/实验室管理员如何创建自定义映像并将其从模板虚拟机保存到[共享映像库](../virtual-machines/shared-image-galleries.md)，以便其他人可以使用它来创建新实验室。

> [!IMPORTANT]
> 使用共享映像库时，Azure 实验室服务仅支持 OS 磁盘空间小于 128 GB 的映像。 在创建实验室期间，具有超过 128 GB 的磁盘空间或多个磁盘的映像将不会显示在虚拟机映像列表中。

## <a name="scenarios"></a>方案
此功能支持以下两个场景： 

- 实验室帐户管理员将共享映像库附加到实验室帐户，并将映像上传到实验室上下文外部的共享映像库。 然后，实验室创建者可以使用共享映像库中的映像创建实验室。 
- 实验室帐户管理员将共享映像库附加到实验室帐户。 实验室创建者（讲师）将其实验室的自定义映像保存到共享映像库。 然后，其他实验室创建者可以从共享映像库中选择此映像，为其实验室创建模板。 

    将映像保存到共享映像库时，Azure 实验室服务会将已保存的映像复制到同一[地域](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他区域。 它确保映像可用于在同一地域中的其他区域中创建的实验室。 将映像保存到共享映像库会产生额外费用，其中包括所有复制映像的成本。 此成本不同于 Azure 实验室服务的使用成本。 有关共享映像库定价的详细信息，请参阅[共享映像库 – 计费](../virtual-machines/shared-image-galleries.md#billing)。
    
## <a name="prerequisites"></a>先决条件
- 创建[共享映像库](../virtual-machines/create-gallery.md)。
- 你已将共享映像库附加到实验室帐户。 有关分步说明，请参阅[如何附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)。

## <a name="save-an-image-to-the-shared-image-gallery"></a>将映像保存到共享映像库
附加共享映像库后，实验室帐户管理员或教师可以将映像保存到共享映像库，以便其他教师可以重复使用。 

1. 在实验室的“模板”页上，选择工具栏上的“导出到共享映像库”。

    ![“保存映像”按钮](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在“导出到共享映像库”对话框中，输入“映像名称”，然后选择“导出”。 

    ![“导出到共享映像库”对话框](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)

3. 可以在“模板”页上查看此操作的进度。 此操作可能需要一段时间。 

    ![正在进行导出](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 如果导出操作成功，则会看到以下消息：

    ![导出已完成](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    将映像保存到共享映像库后，可以在创建其他实验室时从库中使用该映像。 还可以将映像上传到实验室上下文之外的共享映像库。 有关详细信息，请参阅：


    - [共享映像库概述](../virtual-machines/shared-image-galleries.md)
    - [创建自定义映像的建议方法](approaches-for-custom-image-creation.md)


    > [!IMPORTANT]
    > 在 Azure 实验室服务中[将实验室的模板映像保存](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)到共享映像库时，会将该映像作为专用映像上传到库。 [专用映像](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)保留计算机特定的信息和用户配置文件。 你仍可以直接将通用映像上传到 Azure 实验室服务外部的库。    

## <a name="use-a-custom-image-from-the-shared-image-gallery"></a>使用共享映像库中的自定义映像
教师可以在设置新实验室时创建的模板 VM 的共享映像库中选择可用的自定义映像。

![使用库中的虚拟机映像](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> 可以基于 Azure 实验室服务中的通用映像和专用映像创建模板 VM 。

### <a name="resave-a-custom-image-to-shared-image-gallery"></a>将自定义映像重新保存到共享映像库

从共享映像库中的自定义映像创建实验室后，可以使用模板 VM 对映像进行更改，然后将映像重新导出到共享映像库。  重新导出时，可以选择创建新映像或更新原始映像。 

 ![“重新导出到共享映像库”对话框](./media/how-to-use-shared-image-gallery/reexport-to-shared-image-gallery-dialog.png) 

如果选择“创建新映像”，将创建新的[映像定义](../virtual-machines/shared-image-galleries.md#image-definitions)。  这样，无需更改共享映像库中已存在的原始自定义映像，即可保存全新的自定义映像。

如果选择“更新现有映像”，则原始自定义映像的定义将更新为新[版本](../virtual-machines/shared-image-galleries.md#image-versions)。  下次使用自定义映像创建实验室时，实验室服务会自动使用最新版本。

## <a name="next-steps"></a>后续步骤
若要了解如何通过将共享映像库附加和拆离到实验室帐户来设置共享映像库，请参阅[如何附加和拆离共享映像库](how-to-attach-detach-shared-image-gallery.md)。

若要探索在非实验室情景中将自定义映像引入共享映像库的其他选择，请参阅[建议的自定义映像创建方法](approaches-for-custom-image-creation.md)。

若要详细了解共享映像库的常规概念，请参阅[共享映像库](../virtual-machines/shared-image-galleries.md)。