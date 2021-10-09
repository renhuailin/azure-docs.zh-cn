---
title: 基于 VM 创建 Azure 开发测试实验室自定义映像
description: 介绍如何通过 Azure 门户在 Azure 开发测试实验室中基于预配的 VM 创建自定义映像
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 61d57b458151d2832b32917d39e7ab07953451c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673875"
---
# <a name="create-a-custom-image-from-a-vm"></a>从 VM 创建自定义映像

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>分步说明

可基于预配的 VM 创建自定义映像，随后使用该自定义映像创建相同的 VM。 以下步骤演示了如何从 VM 创建自定义映像：

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“所有服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表，选择所需的实验室。  

1. 在实验室的主窗格中，选择“我的虚拟机”。
 
1. 在“我的虚拟机”窗格中，选择想要从中创建自定义映像的 VM。

1. 在 VM 的管理窗格中的“操作”下面，选择“创建自定义映像” 。

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="创建自定义映像菜单项":::
1. 在“自定义映像”窗格中，输入自定义映像的名称和说明。 创建 VM 时，此信息显示在基项列表中。 自定义映像将包含 OS 磁盘和所有附加到虚拟机的数据磁盘。

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="“创建自定义映像”页":::
1. 选择是否在 VM 上运行 sysprep。 如果未在 VM 上运行 sysprep，请在创建自定义映像时指定是否要在 VM 上运行 sysprep。
1. 创建自定义映像后，选择“确定”。

    几分钟之后，将创建自定义映像并将其存储在实验室的存储帐户中。 当实验室用户想要创建新 VM 时，可在基础映像的列表中找到该映像。

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="自定义映像可在基础映像的列表中找到":::

## <a name="related-blog-posts"></a>相关的博客文章

- [自定义映像或公式？](/azure/devtest-labs/devtest-lab-faq#blog-post)
- [复制 Azure 开发测试实验室间的自定义映像](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>后续步骤

- [将 VM 添加到实验室](devtest-lab-add-vm.md)