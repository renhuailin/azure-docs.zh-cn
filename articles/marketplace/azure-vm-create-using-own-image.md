---
title: 使用自己的映像在 Azure Marketplace 上创建 Azure 虚拟机产品/服务
description: 了解如何使用自己的映像将虚拟机产品/服务发布到 Azure Marketplace。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283684"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>如何使用自己的映像创建虚拟机

本文介绍了如何创建和部署用户提供的虚拟机 (VM) 映像。

> [!NOTE]
> 在开始此过程之前，请查看 Azure VM 产品/服务的 [技术要求](marketplace-virtual-machines.md#technical-requirements) ，包括虚拟硬盘 (VHD) 要求。

若要改为使用已批准的基本映像，请按照 [从已批准的基准创建 VM 映像](azure-vm-create-using-approved-base.md)中的说明进行操作。

## <a name="configure-the-vm"></a>配置 VM

本部分介绍如何对 Azure VM 进行大小调整、更新和通用化。 必须执行这些步骤才能准备好要在 Azure 市场部署的 VM。

### <a name="size-the-vhds"></a>调整 Vhd 大小

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>安装最新的更新

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>执行附加的安全检查

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>将 VHD 上传到 Azure

如 [准备要上传到 Azure 的 WINDOWS vhd 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 中所述，配置和准备要上传的 VM，或者 [创建并上载 Linux VHD](../virtual-machines/linux/create-upload-generic.md)。

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>如果使用映像构建服务) ，则从映像 (提取 VHD

如果使用的是映像构建服务（如 [Packer](https://www.packer.io/)），则可能需要从映像中提取 VHD。 没有直接的方法可执行此操作。 你将需要创建一个 VM，并从 VM 磁盘中提取 VHD。

### <a name="create-the-vm-on-the-azure-portal"></a>在 Azure 门户上创建 VM

按照以下步骤在 [Azure 门户](https://ms.portal.azure.com/)上创建基本 VM 映像。

1. 登录 [Azure 门户](https://ms.portal.azure.com/)。
2. 选择“虚拟机”。 
3. 选择 " **+ 添加** " 以打开 " **创建虚拟机** " 屏幕。
4. 从下拉列表中选择图像，或选择 " **浏览所有公用和专用映像** "，搜索或浏览所有可用的虚拟机映像。
5. 若要创建 **第2代** VM，请在 " **高级** " 选项卡中选择 " **第2代** " 选项。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="选择 &quot;第1代&quot; 或 &quot;第2代&quot;。":::

6. 选择要部署的 VM 的大小。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="选择 &quot;第1代&quot; 或 &quot;第2代&quot;。" **正在运行**"。

### <a name="connect-to-your-vm"></a>连接到 VM

请参阅以下文档以连接到 [Windows](../virtual-machines/windows/connect-logon.md) 或 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM。

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>后续步骤

- 建议下一步： [测试 VM 映像](azure-vm-image-test.md) 以确保它满足 Azure Marketplace 发布要求。 这是可选的。
- 如果不测试 VM 映像，请继续 [生成 SAS URI](azure-vm-get-sas-uri.md)。
- 如果在创建新的基于 Azure 的 VHD 时遇到困难，请参阅 [Azure Marketplace 的 VM 常见问题解答](azure-vm-create-faq.md)。
