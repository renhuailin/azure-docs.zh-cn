---
title: Azure 门户 - 使用专用链接限制对托管磁盘的导入/导出访问
description: 使用 Azure 门户为托管磁盘启用专用链接。 允许在虚拟网络中安全地导出和导入磁盘。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/15/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af8b44cbd229fc2bdf28d2885356e219b3a8198a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288304"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>使用 Azure 门户限制通过专用链接对托管磁盘的导入/导出访问

对托管磁盘的专用链接支持使你能够限制托管磁盘的导出和导入，使其仅发生在 Azure 虚拟网络中。 可以为未连接的托管磁盘和快照生成有时间限制的共享访问签名 (SAS) URI，用于将数据导出到其他区域以进行区域扩展、灾难恢复，以及读取数据以进行取证分析。 还可以使用 SAS URI 将 VHD 从本地直接上传到空磁盘。 虚拟网络上的客户端与托管磁盘之间的网络流量仅穿过虚拟网络以及 Microsoft 主干网络上的专用链接，因此不会对公共 Internet 公开。

可以创建磁盘访问资源，并通过创建专用终结点将其链接到同一订阅中的虚拟网络。 必须将磁盘或快照与磁盘访问关联，才能通过专用链接导出和导入数据。 此外，还需要将磁盘或快照的 NetworkAccessPolicy 属性设置为 `AllowPrivate`。 

可以将 NetworkAccessPolicy 属性设置为 `DenyAll`，以防止任何人生成磁盘或快照的 SAS URI。 NetworkAccessPolicy 属性的默认值为 `AllowAll`。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>创建磁盘访问资源

1. 登录到 Azure 门户并使用[此链接](https://aka.ms/disksprivatelinks)导航到“磁盘访问”。

    > [!IMPORTANT]
    > 必须使用[提供的链接](https://aka.ms/disksprivatelinks)导航到“磁盘访问”边栏选项卡。 如果不使用链接，则当前在公共门户中不可见。

1. 选择“+ 添加”创建新的磁盘访问资源。
1. 在创建边栏选项卡上，选择订阅、资源组、输入名称并选择区域。
1. 选择“查看 + 创建”。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="磁盘访问创建边栏选项卡的屏幕截图。填写所需的名称，选择一个区域，选择一个资源组，然后继续操作":::

创建资源后，直接导航到该资源。

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="门户中“转到资源”按钮的屏幕截图":::

## <a name="create-a-private-endpoint"></a>创建专用终结点

现在，你已有了磁盘访问资源，可以使用它来处理对磁盘导出/导入操作的访问，这是通过专用终结点完成的。 因此，你需要创建专用终结点并将其配置为用于磁盘访问。

1. 从磁盘访问资源中，选择“专用终结点连接接”。
1. 选择 “+ 专用终结点”。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="磁盘访问资源的概览边栏选项卡的屏幕截图。突出显示了专用终结点连接。":::

1. 选择资源组
1. 填写名称并选择创建磁盘访问资源时所在的同一区域。
1. 在完成时选择“下一步:“资源”>

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="专用终结点创建工作流的屏幕截图，第一个边栏选项卡。如果不选择合适的区域，以后可能会遇到问题。":::

1. 在“资源”窗格中，选择“连接到我的目录中的 Azure 资源” 。
1. 对于“资源类型”，请选择“Microsoft.Compute/diskAccesses” 
1. 为“资源”选择先前创建的磁盘访问资源
1. 将“目标子资源”保留为“磁盘” 
1. **选择“下一步:** 配置 >”。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="创建专用终结点的工作流（第二个窗格）的屏幕截图。突出显示了所有值（资源类型、资源、目标子资源）":::

1. 选择要仅限其为磁盘导出位置的虚拟网络，其他虚拟网络将无法导出磁盘。

    > [!NOTE]
    > 如果为所选子网启用了网络安全组 (NGS)，则仅在此子网上对专用终结点禁用它。 子网中的其他资源仍将强制使用 NSG。

1. 选择相应的子网
1. 选择“查看 + 创建”。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="创建专用终结点的工作流（第三个窗格）的屏幕截图。强调了虚拟网络和子网。":::

## <a name="enable-private-endpoint-on-your-disk"></a>启用磁盘上的专用终结点

1. 导航到要配置的磁盘
1. 选择“网络”
1. 选择“专用终结点(通过磁盘访问)”并选择之前创建的磁盘访问权限。
1. 选择“保存”。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="托管磁盘网络窗格的屏幕截图。突出显示了专用终结点选择以及所选的磁盘访问。保存此选择会将磁盘配置为使用这种访问方式。":::

现在，已经完成了在导入/导出托管磁盘时可以使用的专用链接的配置。

## <a name="next-steps"></a>后续步骤

- 将 VHD 上传到 Azure 或将托管磁盘复制到其他区域 - [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) 或 [Azure PowerShell 模块](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- 下载 VHD - [Windows](windows/download-vhd.md) 或 [Linux](linux/download-vhd.md)
- [专用链接常见问题解答](/azure/virtual-machines/faq-for-disks#private-links-for-securely-exporting-and-importing-managed-disks)
- [使用 PowerShell 将托管快照作为 VHD 导出/复制到不同区域中的存储帐户](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)