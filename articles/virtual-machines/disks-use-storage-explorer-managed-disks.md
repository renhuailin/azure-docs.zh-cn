---
title: 使用 Azure 存储资源管理器管理 Azure 托管磁盘
description: 了解如何使用 Azure 存储资源管理器跨区域上传、下载和迁移 Azure 托管磁盘并创建托管磁盘的快照。
author: roygara
ms.author: rogarana
ms.date: 09/07/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 84b2ea53ebb0b6102edf5bc501e0e1b9b6f21726
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124781637"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>使用 Azure 存储资源管理器管理 Azure 托管磁盘

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Azure 存储资源管理器包含一组丰富的功能，使你能够：

- 上传、下载和复制托管磁盘。
- 从操作系统或数据磁盘虚拟硬盘创建快照。
- 将数据从本地迁移到 Azure。
- 跨 Azure 区域迁移数据。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

- Azure 订阅。
- 至少一个 Azure 托管磁盘。
- 最新版本的 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

如果存储资源管理器未连接到 Azure，则不能用于管理资源。 按照本部分中的步骤，将存储资源管理器连接到你的 Azure 帐户。 之后便可以使用它来管理磁盘。

1. 打开 Azure 存储资源管理器并选择工具栏中的“连接”图标。

    [![Azure 存储资源管理器屏幕截图，其中显示了“连接”图标的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-lrg.png#lightbox)

1. 在“连接到 Azure 存储”对话框中，选择“订阅”。 

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“订阅”选项的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-lrg.png#lightbox)

1. 选择适当的环境，然后选择“下一步”。 还可以选择“管理自定义环境”来配置和添加自定义环境。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“Azure 环境”选项的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-lrg.png#lightbox)

1. 在“登录”对话框中，输入 Azure 凭据。

    ![“登录”对话框的屏幕截图。](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 从列表中选择订阅，然后选择“打开资源管理器”。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“打开资源管理器”选项的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-lrg.png#lightbox)

## <a name="upload-an-on-premises-vhd"></a>上传本地 VHD

可以将本地虚拟硬盘 (VHD) 文件上传到 Azure，并用来创建映像。 按照此部分中的步骤上传源文件。

1. 在“资源管理器”窗格中，展开“磁盘”并选择要将磁盘上传到的资源组。 

    [![Azure 存储资源管理器的屏幕截图，突出显示了用于上传磁盘的磁盘节点的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. 在资源组详细信息窗格中，选择“上传”。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“上传”选项的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-lrg.png#lightbox)

1. 在“上传 VHD”对话框中，指定你的 VHD 源文件、磁盘名称、操作系统类型、要将磁盘上传到的区域和帐户类型。 如果区域支持可用性区域，则可以自行选择某个区域。 选择“创建”开始上传磁盘。 

    [![Azure 存储资源管理器的“上传 VHD”对话框的屏幕截图。](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-lrg.png#lightbox)

1. 现在，上传状态会显示在“活动”中。 

    [![Azure 存储资源管理器的屏幕截图，其中突出显示包含上传状态消息的“"活动”窗格的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-lrg.png#lightbox)

如果上传已完成，但在“活动”窗格中看不到磁盘，请选择“刷新”。 

## <a name="download-a-managed-disk"></a>下载托管磁盘

按照本部分中的步骤，将托管磁盘下载到本地 VHD。 磁盘的状态必须是“未附加”才能被下载。

1. 在“资源管理器”窗格中，展开“磁盘”并选择要从中下载磁盘的资源组。 

    [![Azure 存储资源管理器的屏幕截图，突出显示了用于下载磁盘的磁盘节点的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. 在“资源组详细信息”窗格中，选择要下载的磁盘。
1. 选择“下载”，然后选择磁盘的保存位置。 

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“下载”选项的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-lrg.png#lightbox)

1. 选择“保存”开始下载。 下载状态会显示在“活动”中。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示包含下载状态消息的“活动”窗格的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-lrg.png#lightbox)

## <a name="copy-a-managed-disk"></a>复制托管磁盘

使用存储资源管理器可以在区域内部或跨区域复制托管磁盘。 若要复制磁盘：

1. 在“资源管理器”窗格中，展开“磁盘”下拉列表，然后选择包含要复制的磁盘的资源组。 

    [![Azure 存储资源管理器的屏幕截图，突出显示了用于复制磁盘的磁盘节点的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. 在“资源组详细信息”窗格中，选择要复制的磁盘，然后选择“复制”。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“复制”按钮的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-lrg.png#lightbox)

1. 在“资源管理器”窗格中，展开“磁盘”并选择要粘贴磁盘的资源组。 

    [![Azure 存储资源管理器的屏幕截图，突出显示了用于粘贴磁盘的磁盘节点的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-lrg.png#lightbox)

1. 在资源组详细信息窗格中选择“粘贴”。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“粘贴”按钮的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-lrg.png#lightbox)

1. 在“粘贴磁盘”对话框中，填写值。 还可以指定受支持地区中的可用性区域。

    [![Azure 存储资源管理器的粘贴磁盘表单的屏幕截图。](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-lrg.png#lightbox)

1. 选择“粘贴”以开始磁盘复制。 状态显示在“活动”中。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示包含复制和粘贴状态消息的“活动”窗格的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-lrg.png#lightbox)

## <a name="create-a-snapshot"></a>创建快照

1. 在“资源管理器”窗格中，展开“磁盘”，然后选择包含要生成快照的磁盘的资源组。 

    [![Azure 存储资源管理器的屏幕截图，突出显示了用于创建磁盘快照的磁盘节点的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. 在“资源组详细信息”窗格中，选择要生成快照的磁盘，然后选择“创建快照”。

    [![Azure 存储资源管理器的屏幕截图，其中突出显示了“创建快照”按钮的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-lrg.png#lightbox)

1. 在“创建快照”中，指定快照的名称以及您将在其中创建它的资源组。 选择“创建”  。

    [![Azure 存储资源管理器的“创建快照”对话框的屏幕截图。](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-lrg.png#lightbox)

1. 创建快照后，可以在“活动”中选择“在门户中打开”以在 Azure 门户中查看快照。 

    [![Azure 存储资源管理器的屏幕截图，其中突出显示包含快照状态消息的“活动”窗格中链接的位置。](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-lrg.png#lightbox)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户从 VHD 创建虚拟机](/azure/virtual-machines/windows/create-vm-specialized-portal)
- [使用 Azure 门户将托管数据磁盘附加到 Windows 虚拟机](/azure/virtual-machines/windows/attach-managed-disk-portal)
