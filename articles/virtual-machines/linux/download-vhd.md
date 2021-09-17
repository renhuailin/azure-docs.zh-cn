---
title: 从 Azure 下载 Linux VHD
description: 使用 Azure CLI 和 Azure 门户下载 Linux VHD。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 4492eb9a346d89789f1527bf80b46a08cc74fe92
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696086"
---
# <a name="download-a-linux-vhd-from-azure"></a>从 Azure 下载 Linux VHD

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

本文介绍如何使用 Azure 门户从 Azure 下载 Linux 虚拟硬盘 (VHD) 文件。 

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 附加到正在运行的 VM，则不能从 Azure 下载。 如果要使 VM 保持运行，可以[创建快照，然后下载快照](#alternative-snapshot-the-vm-disk)。

若要停止 VM，请执行以下操作：

1.  登录 [Azure 门户](https://portal.azure.com/)。
2.  在左侧菜单中，选择“虚拟机”。
3.  从列表中选择 VM。
4.  在 VM 的页面上，选择“停止”。

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="显示用于停止 VM 的菜单按钮。":::

### <a name="alternative-snapshot-the-vm-disk"></a>替代方法：创建 VM 磁盘快照

创建要下载的磁盘快照。

1. 在[门户](https://portal.azure.com)中选择 VM。
2. 在左侧菜单中选择“磁盘”，然后选择要创建快照的磁盘。 系统将显示磁盘的详细信息。  
3. 从页面的顶部菜单选择“创建快照”。 “创建快照”页将打开。
4. 在“名称”中键入快照的名称。 
5. 对于“快照类型”，选择“完全”或“增量”  。
6. 完成操作后，选择“查看 + 创建”。

快照将很快创建，然后可用于下载或从中创建另一个 VM。

> [!NOTE]
> 如果不先停止 VM，快照将不会清理。 快照的状态就好像在创建快照时 VM 已重启或崩溃一样。  尽管这通常是安全的，但如果当时正在运行的应用程序会因崩溃受到影响，则可能会导致问题。
>  
> 仅建议具有单个操作系统磁盘的 VM 使用此方法。 在下载快照之前，或者在为操作系统磁盘和每个数据磁盘创建快照之前，应停止具有一个或多个数据磁盘的 VM。

## <a name="generate-sas-url"></a>生成 SAS URL

若要下载 VHD 文件，需要生成[共享访问签名 (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL。 生成 URL 时，将为 URL 分配到期时间。

1. 在 VM 页的菜单上，选择“磁盘”。
2. 为 VM 选择操作系统磁盘，然后选择“磁盘导出”。
1. 如果需要，请更新“URL 到期时间(秒)”的值，留出足够的时间来完成下载。 默认值为 3600 秒（1 小时）。
3. 选择“生成 URL”。
 
      
## <a name="download-vhd"></a>下载 VHD

1.  在生成的 URL 下，选择“下载 VHD 文件”。

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="显示用于下载 VHD 的按钮。":::

2.  可能需要选择浏览器中的“保存”以开始下载。 VHD 文件的默认名称为 *abcd*。

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure CLI 从自定义磁盘上传并创建 Linux VM](upload-vhd.md)。 
- [使用 Azure CLI 管理 Azure 磁盘](tutorial-manage-disks.md)。
