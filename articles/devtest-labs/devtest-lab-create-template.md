---
title: 基于 VHD 文件创建 Azure 开发测试实验室自定义映像
description: 介绍如何使用 Azure 门户基于 VHD 文件在 Azure 开发测试实验室中创建自定义映像
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 821a429b19218d8bb813be7519dd124cd025b3bf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587889"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>从 VHD 文件创建自定义映像

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>分步说明

以下步骤将引导使用 Azure 门户基于 VHD 文件创建自定义映像：

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“所有服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表，选择所需的实验室。  

1. 在实验室的主窗格中，选择“配置和策略”。 

1. 在“配置和策略”窗格中，选择“自定义映像”。

1. 在“自定义映像”窗格中，选择“+添加”。

    ![添加自定义映像](./media/devtest-lab-create-template/add-custom-image.png)

1. 输入自定义映像的名称。 在创建 VM 时，此名称显示在基础映像的列表中。

1. 键入自定义映像的说明。 说明显示在基础映像的列表中。

1. 对于“OS 类型”：选择“Windows”或“Linux”。

    - 如果选择 **Windows**，请通过复选框指定 *sysprep* 是否已在计算机上运行。 
    - 如果选择 **Linux**，请通过复选框指定 *deprovision* 是否已在计算机上运行。 

1. 从下拉菜单中选择“VHD”。 此 VHD 将用于创建新的自定义映像。 如有必要，请选择“使用 PowerShell 上传 VHD”。

1. 如果用于创建自定义映像的映像不是已授权映像（由 Microsoft 发布），还可以输入计划名称、计划套餐和计划发布服务器。

   - **计划名称：** 输入从中创建此自定义映像的市场映像 (SKU) 的名称 
   - **计划产品/服务：** 输入从中创建此自定义映像的市场映像的产品（服务） 
   - **计划发布服务器：** 输入从中创建此自定义映像的市场映像的发布服务器

   > [!NOTE]
   > 如果要用于创建自定义映像的映像 **不** 是已授权映像，则这些字段为空并且在你选择时可以填写。 如果该映像 **是** 已授权映像，则可以使用计划信息自动填充这些字段。 如果在这种情况下尝试更改这些字段，将显示一条警告消息。
   >
   >

1. 选择“确定”以创建自定义映像。

几分钟之后，将创建自定义映像并将其存储在实验室的存储帐户中。 当实验室用户想要创建新 VM 时，可在基础映像的列表中找到该映像。

![自定义映像可在基础映像的列表中找到](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章

- [自定义映像或公式？](/azure/devtest-labs/devtest-lab-faq#blog-post)
- [复制 Azure 开发测试实验室间的自定义映像](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>后续步骤

- [将 VM 添加到实验室](./devtest-lab-add-vm.md)