---
title: '在 Azure Marketplace 中创建 Azure 虚拟机产品/服务 (VM) '
description: 了解如何从批准的基准创建 (VM) 产品/服务的虚拟机。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 74cab14cd162ba608a9c5d0168485e9055661f55
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590778"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>如何使用已批准的基准创建虚拟机

本文介绍如何使用 Azure 创建 (VM) 的虚拟机，该虚拟机包含预配置的经认可的操作系统。 如果这与你的解决方案不兼容，则可以使用已批准的操作系统 [创建和配置本地 VM](azure-vm-create-using-own-image.md) ，并根据 [准备 WINDOWS VHD 或 VHDX 以便上传到 Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)中所述，对其进行配置和准备以便上传。

> [!NOTE]
> 在开始此过程之前，请查看 Azure VM 产品/服务的 [技术要求](marketplace-virtual-machines.md#technical-requirements) ，包括虚拟硬盘 (VHD) 要求。

## <a name="select-an-approved-base-image"></a>选择已批准的基本映像

选择以下 Windows 或 Linux 映像之一作为基础。

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、 [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview)、 [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure 提供一系列已批准的 Linux 发行版。 有关最新列表，请参阅 [Azure 认可的 Linux 分发版](../virtual-machines/linux/endorsed-distros.md)。

## <a name="create-vm-on-the-azure-portal"></a>在 Azure 门户上创建 VM

1. 登录 [Azure 门户](https://ms.portal.azure.com/)。
2. 选择“虚拟机”。
3. 选择 " **+ 添加** " 以打开 " **创建虚拟机** " 屏幕。
4. 从下拉列表中选择图像，或选择 " **浏览所有公用和专用映像** "，搜索或浏览所有可用的虚拟机映像。
5. 如果要创建 **第1代** VM，请转到 " **磁盘** " 选项卡，将 " **使用托管磁盘** " 选项设置为 " **否** "，以便能够在指定的存储帐户中获取 VM 的 VHD

   :::image type="content" source="media/create-vm/create-virtual-machine-sizes-2.png" alt-text="选择非托管磁盘。":::
   
6. 若要创建 **第2代** VM，请在 " **高级** " 选项卡中选择 " **第2代** " 选项。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="选择 &quot;第1代&quot; 或 &quot;第2代&quot;。":::

7. 选择要部署的 VM 的大小。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="为所选映像选择建议的 VM 大小。":::

7. 提供创建 VM 所需的其他详细信息。
8. 选择“查看 + 创建”可查看选择。 显示 " **验证通过** " 消息后，选择 "  **创建**"。

Azure 随即开始预配所指定的虚拟机。 选择左侧菜单中的 " **虚拟机** " 选项卡跟踪其进度。 创建后，虚拟机的状态将更改为 " **正在运行**"。


## <a name="configure-the-vm"></a>配置 VM

本部分介绍如何对 Azure VM 进行大小调整、更新和通用化。 必须执行这些步骤才能准备好要在 Azure 市场部署的 VM。

### <a name="connect-to-your-vm"></a>连接到 VM

请参阅以下文档以连接到 [Windows](../virtual-machines/windows/connect-logon.md) 或 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM。

### <a name="install-the-most-current-updates"></a>安装最新的更新

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>执行附加的安全检查

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>后续步骤

- 建议下一步： [测试 VM 映像](azure-vm-image-test.md) 以确保它满足 Azure Marketplace 发布要求。 该地址为可选。
- 如果不测试 VM 映像，请继续 [生成 SAS URI](azure-vm-get-sas-uri.md)。
- 如果在创建新的基于 Azure 的 VHD 时遇到困难，请参阅 [Azure Marketplace 的 VM 常见问题解答](azure-vm-create-faq.md)。