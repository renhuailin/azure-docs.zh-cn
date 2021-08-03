---
title: 复制并粘贴到虚拟机以及从中进行复制和粘贴：Azure Bastion
description: 了解如何使用 Bastion 复制并粘贴到 Azure VM 以及从中进行复制和粘贴。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: b8c90e3c8bea7f6471a60804f546957f5236b70b
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536006"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>复制并粘贴到虚拟机：Azure Bastion

本文可帮助你在使用 Azure Bastion 时将内容复制并粘贴到虚拟机，以及从中进行复制和粘贴。 使用 VM 之前，请确保已按照[创建 Bastion 主机](./tutorial-create-host-portal.md)的步骤进行操作。 然后，连接到要通过 [RDP](bastion-connect-vm-rdp.md) 或 [SSH](bastion-connect-vm-ssh.md) 使用的 VM。

对于支持高级剪贴板 API 访问的浏览器，可以使用在本地设备上的应用程序之间进行复制和粘贴的相同方式在本地设备与远程会话之间复制和粘贴文本。 对于其他浏览器，可以使用 Bastion 剪贴板访问工具面板。

>[!NOTE]
>目前仅支持文本复制/粘贴。
>

   ![允许剪贴板](./media/bastion-vm-manage/allow.png)

仅支持文本复制/粘贴。 对于直接复制和粘贴，浏览器可能会在 Bastion 会话进行初始化时提示你它需要访问剪贴板。 允许网页访问剪贴板。 如果使用的是 Mac，则粘贴的键盘快捷方式为 SHIFT-CTRL-V。

## <a name="copy-to-a-remote-session"></a><a name="to"></a>复制到远程会话

使用 [Azure 门户](https://portal.azure.com)连接到虚拟机后，请完成以下步骤：

1. 将本地设备中的文本/内容复制到本地剪贴板。
1. 在远程会话期间，通过选择两个箭头启动 Bastion 剪贴板访问工具面板。 这些箭头位于会话的左中位置。

   ![屏幕截图显示了在窗口左侧突出显示的工具面板的启动箭头。](./media/bastion-vm-manage/left.png)

   ![屏幕截图显示了在 Bastion 中复制的文本的剪贴板。](./media/bastion-vm-manage/clipboard.png)
1. 通常，已复制的文本会自动显示在 Bastion 复制粘贴面板上。 如果文本不在那里，则将文本粘贴到面板上的文本区域中。
1. 文本位于文本区域中后，可以将其粘贴到远程会话。

   ![屏幕截图显示了突出显示的复制/粘贴按钮和复制到远程会话的示例文本字符串。](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>从远程会话复制

使用 [Azure 门户](https://portal.azure.com)连接到虚拟机后，请完成以下步骤：

1. 将远程会话中的文本/内容复制到远程剪贴板（使用 Ctrl-C）。

   ![工具面板](./media/bastion-vm-manage/remote.png)
1. 在远程会话期间，通过选择两个箭头启动 Bastion 剪贴板访问工具面板。 这些箭头位于会话的左中位置。

   ![剪贴板](./media/bastion-vm-manage/clipboard2.png)
1. 通常，已复制的文本会自动显示在 Bastion 复制粘贴面板上。 如果文本不在那里，则将文本粘贴到面板上的文本区域中。
1. 文本位于文本区域中后，可以将其粘贴到本地设备。

   ![粘贴](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
