---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: ebaa86a60ae11c50658bfcfe58bbd0184bf15576
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699331"
---
1. 在 [Azure 门户](https://portal.azure.com)中，导航到要连接的虚拟机。 在“概述”页上，选择“连接”，然后从下拉列表选择“Bastion”  。

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="连接的屏幕截图。" lightbox="./media/bastion-vm-rdp/connect.png":::

1. 选择 Bastion 后，接着选择“使用 Bastion”。 如果未为虚拟网络预配 Bastion，请参阅[配置 Bastion](../articles/bastion/quickstart-host-portal.md)。

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="显示“使用 Bastion”的屏幕截图。":::

1. 在“使用 Azure Bastion 进行连接”页上，展开“连接设置”部分，然后选择“RDP”  。 如果计划使用不同于标准 RDP 端口 (3389) 的入站端口，请输入“端口”。

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/connection-settings.png" alt-text="显示端口的屏幕截图。" lightbox="./media/bastion-connect-vm-rdp-linux/connection-settings.png":::

1. 输入“用户名”和“密码”，然后选择“连接”以连接到 VM。  

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/username-password.png" alt-text="显示单击“连接”的屏幕截图。" lightbox="./media/bastion-connect-vm-rdp-linux/username-password.png":::

1. 通过 Bastion 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。 

   连接到 VM 时，使用键盘快捷键可能不会产生与本地计算机上的快捷键相同的行为。 例如，从 Windows 客户端计算机连接到 Windows VM 时，“CTRL+ALT+END”是“CTRL+ALT+Delete”的键盘快捷键。 从 Mac 客户端计算机连接到 Windows VM 时，键盘快捷键是“Fn+CTRL+ALT+Backspace”。