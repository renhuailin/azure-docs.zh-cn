---
title: 将托管数据磁盘附加到 Windows VM - Azure
description: 如何使用 Azure 门户将托管数据磁盘附加到 Windows VM。
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ba6691e343276445bd881d2cbe2f8fcb5d1d314f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666520"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>使用 Azure 门户将托管数据磁盘附加到 Windows VM

本文介绍如何使用 Azure 门户将新的托管数据磁盘附加到 Windows 虚拟机 (VM)。 VM 的大小决定了可以附加的数据磁盘数量。 有关详细信息，请参阅[虚拟机的大小](../sizes.md)。


## <a name="add-a-data-disk"></a>添加数据磁盘

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“虚拟机”。 
1. 从列表中选择虚拟机。
1. 在“虚拟机”窗格中，选择“磁盘” 。
1. 在“磁盘”窗格中，选择“创建并附加新磁盘” 。
1. 在新磁盘的下拉列表中，选择所需的选项，并为磁盘命名。
1. 选择“保存”以创建新的数据磁盘并将其附加到 VM。

## <a name="initialize-a-new-data-disk"></a>初始化新的数据磁盘

1. 连接到 VM。
1. 在正在运行的 VM 中选择 Windows“开始”菜单，然后在搜索框中输入 **diskmgmt.msc**  。 此时会打开“磁盘管理”控制台  。
1. “磁盘管理”识别出新的未初始化磁盘，并显示“初始化磁盘”窗口  。
1. 请确保选择新磁盘，然后选择“确定”对其进行初始化  。
1. 新的磁盘显示为“未分配”  。 右键单击磁盘上任意位置，选择“新建简单卷”。  此时会打开“新建简单卷向导”窗口  。
1. 完成向导中的每一步，保留所有默认值，完成后，选择“完成”  。
1. 关闭“磁盘管理”  。
1. 此时出现一个弹出窗口，通知你需要先格式化新磁盘才能使用新磁盘。 选择“格式化磁盘”  。
1. 在“格式化新磁盘”窗口中，检查设置，然后选择“启动”   。
1. 此时出现一条警告，通知你格式化磁盘会擦除所有数据。 选择“确定”  。
1. 完成格式化后，选择“确定”  。

## <a name="next-steps"></a>后续步骤

- 还可以[使用 PowerShell 附加数据磁盘](attach-disk-ps.md)。
- 如果应用程序需要使用 *D:* 盘存储数据，可以 [更改 Windows 临时磁盘的驱动器号](change-drive-letter.md)。