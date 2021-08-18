---
title: 利用经过批准的基础映像创建 Azure 虚拟机 (VM) 产品/服务
description: 了解如何利用经过批准的基础映像创建虚拟机 (VM) 产品/服务（Azure 市场）。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 46b29bb6a7294d0f6741be4cac7028fe7e42ec95
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430262"
---
# <a name="create-a-virtual-machine-using-an-approved-base"></a>使用经过批准的基础映像创建虚拟机

本文介绍如何使用 Azure 来创建一个包含预配置的经过认可的操作系统的虚拟机 (VM)。 如果此内容与你的解决方案不兼容，则可以使用经过批准的操作系统来[创建并配置本地 VM](azure-vm-create-using-own-image.md)。

> [!NOTE]
> 在开始此过程之前，请先查看 Azure VM 产品/服务的[技术要求](marketplace-virtual-machines.md#technical-requirements)，包括虚拟硬盘 (VHD) 要求。

## <a name="select-an-approved-base-image"></a>选择经过批准的基础映像

请选择下面的 Windows 或 Linux 映像之一作为基础映像。

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、[2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview)、[2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure 提供一系列已批准的 Linux 发行版。 有关最新列表，请参阅 [Azure 认可的 Linux 分发版](../virtual-machines/linux/endorsed-distros.md)。

## <a name="create-vm-on-the-azure-portal"></a>在 Azure 门户中创建 VM

1. 登录 [Azure 门户](https://ms.portal.azure.com/)。
2. 选择“虚拟机”。
3. 选择“+ 添加”以打开“创建虚拟机”屏幕 。
4. 从下拉列表中选择映像，或选择“浏览所有公共和专用映像”，以搜索或浏览所有可用的虚拟机映像。
5. 若要创建 Gen 2 VM，请转到“高级”选项卡并选择“Gen 2”选项  。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="选择 Gen 1 或 Gen 2。":::

6. 选择要部署的 VM 的大小。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="为所选映像选择建议的 VM 大小。":::

7. 提供创建 VM 所需的其他详细信息。
8. 选择“查看 + 创建”可查看选择。 在出现“已通过验证”消息时，选择“创建” 。

Azure 随即开始预配所指定的虚拟机。 请通过选择左侧菜单中的“虚拟机”选项卡来跟踪预配进度。 在创建之后，虚拟机的状态会更改为“正在运行”。

## <a name="configure-the-vm"></a>配置 VM

本部分介绍如何对 Azure VM 进行大小调整、更新和通用化。 必须执行这些步骤才能准备好要在 Azure 市场部署的 VM。

### <a name="connect-to-your-vm"></a>连接到 VM

请参阅以下文档，了解如何连接到 [Windows](../virtual-machines/windows/connect-logon.md) 或 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM。

### <a name="install-the-most-current-updates"></a>安装最新的更新

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>执行附加的安全检查

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>后续步骤

- 建议的后续步骤：[测试 VM 映像](azure-vm-image-test.md)，以确保它满足 Azure 市场发布要求。 此为可选项。
- 如果你不想测试 VM 映像，请登录到[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165935)来发布映像。
- 如果在创建新的基于 Azure 的 VHD 时遇到困难，请参阅 [Azure 市场的 VM 常见问题解答](azure-vm-create-faq.yml)。
