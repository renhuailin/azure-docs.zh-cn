---
title: 从 Azure 下载 Windows VHD
description: 使用 Azure 门户下载 Windows VHD。
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: de96ea4b32b9a2fc6baefd4cd6b14d31dea98165
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690804"
---
# <a name="download-a-windows-vhd-from-azure"></a>从 Azure 下载 Windows VHD

适用于：:heavy_check_mark: Windows VM 

本文介绍如何使用 Azure 门户从 Azure 下载 Windows 虚拟硬盘 (VHD) 文件。

## <a name="optional-generalize-the-vm"></a>可选：通用化 VM

如果要使用 VHD 作为[映像](tutorial-custom-images.md)来创建其他 VM，则应使用 [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) 来通用化操作系统。 否则，必须为要创建的每个 VM 复制磁盘。

若要使用 VHD 作为映像创建其他 VM，请通用化该 VM。

1. 登录到 [Azure 门户](https://portal.azure.com/)（如果未登录）。
2. [连接到 VM](connect-logon.md)。 
3. 在 VM 上，以管理员身份打开“命令提示符”窗口。
4. 将目录切换到 *%windir%\system32\sysprep*，然后运行 sysprep.exe。
5. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”。
6. 在“关闭选项”中选择“关闭”，然后单击“确定”。 

如果不想通用化当前 VM，仍可首先[创建 OS 磁盘的快照](#alternative-snapshot-the-vm-disk)、从快照创建新的 VM，然后对副本进行通用化，从而创建通用映像。

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 附加到正在运行的 VM，则不能从 Azure 下载。 如果要使 VM 保持运行，可[创建快照，然后下载快照](#alternative-snapshot-the-vm-disk)。

1. 在 Azure 门户的“中心”菜单上，单击“虚拟机”。
1. 从列表中选择 VM。
1. 在 VM 的边栏选项卡上，单击“停止”。

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
> 仅建议具有单个操作系统磁盘的 VM 使用此方法。 在下载快照之前或为操作系统磁盘和每个数据磁盘创建快照之前，应停止具有一个或多个数据磁盘的 VM。

## <a name="generate-download-url"></a>生成下载 URL

若要下载 VHD 文件，需要生成[共享访问签名 (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL。 生成 URL 时，将为 URL 分配到期时间。

1. 在 VM 的页面上，单击左侧菜单中的“磁盘”。
1. 选择 VM 的操作系统磁盘。
1. 在磁盘的页面上，从左侧菜单中选择“磁盘导出”。
1. URL 默认的过期时间为 3600 秒（1 小时）。 对于 Windows OS 磁盘或大型数据磁盘，可能需要增加此时间。 通常，36000 秒（10 小时）足够了。
1. 单击“生成 URL”。

> [!NOTE]
> 从默认值增加到期时间，以便提供足够时间来下载 Windows Server 操作系统的大型 VHD 文件。 下载大型 VHD 可能需要几个小时，具体取决于你的连接和 VM 大小。 
> 
> 

## <a name="download-vhd"></a>下载 VHD

1. 在生成的 URL 下，单击“下载 VHD 文件”。
1. 可能需要单击浏览器中的“保存”以开始下载。 VHD 文件的默认名称为 *abcd*。

## <a name="next-steps"></a>后续步骤

- 了解如何[将 VHD 文件上传到 Azure](upload-generalized-managed.md)。 
- [从存储帐户中的非托管磁盘创建托管磁盘](attach-disk-ps.md)。
- [使用 PowerShell 管理 Azure 磁盘](tutorial-manage-data-disk.md)。
