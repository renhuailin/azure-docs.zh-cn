---
title: Azure 门户 - 限制对托管磁盘的导入/导出访问
description: 使用 Azure 门户为托管磁盘启用专用链接。 这允许在虚拟网络中安全地导出和导入磁盘。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b023f7f068d1dc52c073519cc7e7f308e5d86ad0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696452"
---
# <a name="restrict-importexport-access-for-managed-disks-using-azure-private-link"></a>使用 Azure 专用链接限制对托管磁盘的导入/导出访问

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

可以使用[专用终结点](../private-link/private-endpoint-overview.md)来限制托管磁盘的导出和导入，并通过[专用链接](../private-link/private-link-overview.md)从 Azure 虚拟网络上的客户端更安全地访问数据。 专用终结点将虚拟网络地址空间中的 IP 地址用于托管磁盘。 虚拟网络上客户端与托管磁盘之间的网络流量仅穿过虚拟网络以及 Microsoft 主干网络上的专用链接，因此不会从公共 Internet 公开。

若要使用专用链接导出和导入托管磁盘，首先要创建磁盘访问资源，并通过创建专用终结点将其链接到同一订阅中的虚拟网络。 然后，将磁盘或快照与磁盘访问实例相关联。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="create-a-disk-access-resource"></a>创建磁盘访问资源

1. 登录到 Azure 门户并使用[此链接](https://aka.ms/disksprivatelinks)导航到“磁盘访问”。

    > [!IMPORTANT]
    > 必须使用[提供的链接](https://aka.ms/disksprivatelinks)导航到“磁盘访问”窗格。 如果不使用链接，则当前在公共门户中不可见。

1. 选择“+ 创建”创建新的磁盘访问资源。
1. 在“创建磁盘访问”窗格中，选择订阅和资源组。 在“实例详细信息”中，输入名称并选择区域。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="磁盘访问创建窗格的屏幕截图。填写所需名称，选择区域，选择资源组，然后继续":::

1. 选择“查看 + 创建”。
1. 创建资源后，直接导航到该资源。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="门户中“转到资源”按钮的屏幕截图":::

## <a name="create-a-private-endpoint"></a>创建专用终结点

接下来，你需要创建专用终结点并将其配置为用于磁盘访问。

1. 从磁盘访问资源中，在“设置”下，选择“专用终结点连接接” 。
1. 选择 “+ 专用终结点”。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="磁盘访问资源的概述窗格屏幕截图。突出显示了专用终结点连接。":::

1. 在“创建专用终结点”窗格中，选择资源组。
1. 提供名称并选择创建磁盘访问资源时所在的同一区域。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="专用终结点创建工作流（第一个窗格）的屏幕截图。如果未选择适当的区域，则稍后可能会遇到问题。":::

1. 在完成时选择“下一步:资源”。
1. 在“资源”窗格中，选择“连接到我的目录中的 Azure 资源” 。
1. 对于“资源类型”，请选择“Microsoft.Compute/diskAccesses” 。
1. 为“资源”选择先前创建的磁盘访问资源。
1. 将“目标子资源”保留为“磁盘” 。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="创建专用终结点的工作流（第二个窗格）的屏幕截图。突出显示了所有值（资源类型、资源、目标子资源）":::

1. 选择“下一步: 配置”。
1. 选择要限制磁盘导入和导出的虚拟网络。 这可防止将磁盘导入和导出到其他虚拟网络。

    > [!NOTE]
    > 如果为所选子网启用了网络安全组，则仅在此子网上对专用终结点禁用它。 此子网中的其他资源仍会强制使用网络安全组。

1. 选择相应的子网。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="创建专用终结点的工作流（第三个窗格）的屏幕截图。强调了虚拟网络和子网。":::

1. 选择“查看 + 创建”。

## <a name="enable-private-endpoint-on-your-disk"></a>启用磁盘上的专用终结点

1. 导航到要配置的磁盘。
1. 在“设置”下选择“网络” 。
1. 选择“专用终结点(通过磁盘访问)”并选择之前创建的磁盘访问权限。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="托管磁盘网络窗格的屏幕截图。突出显示了专用终结点选择以及所选的磁盘访问。保存此选择会将磁盘配置为使用这种访问方式。":::

1. 选择“保存”。

现已配置了可用于导入和导出托管磁盘的专用链接。

## <a name="next-steps"></a>后续步骤

- 将 VHD 上传到 Azure 或将托管磁盘复制到其他区域 - [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) 或 [Azure PowerShell 模块](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- 下载 VHD - [Windows](windows/download-vhd.md) 或 [Linux](linux/download-vhd.md)
- [专用链接和托管磁盘常见问题解答](/azure/virtual-machines/faq-for-disks#private-links-for-securely-exporting-and-importing-managed-disks)
- [使用 PowerShell 将托管快照作为 VHD 导出/复制到不同区域中的存储帐户](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)