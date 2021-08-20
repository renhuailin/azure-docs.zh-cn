---
title: 包含文件
description: 包含文件
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/21/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 026e505e1ce5fe4d561289b2a98c8c0324136cbc
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108222"
---
1. 在 [Azure 门户](https://portal.azure.com)中，导航到要连接的虚拟机。 在“概述”页上，选择“连接”，然后从下拉列表选择“Bastion”  。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="选择“Bastion”":::

1. 从下拉列表中选择“Bastion”后，将显示一条侧边栏，其中包含三个选项卡：RDP、SSH 和 Bastion。 由于已针对虚拟网络预配了 Bastion，因此默认情况下，“Bastion”选项卡处于活动状态。 选择“使用 Bastion”。

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="选择“使用 Bastion”":::

1. 在“使用 Azure Bastion 连接”页上，输入虚拟机的用户名和密码，然后选择“连接” 。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="“连接”":::

1. 通过 Bastion 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。 

   * 进行连接时，VM 的桌面看起来可能与示例屏幕截图有所不同。 
   * 连接到 VM 时，使用键盘快捷键可能不会产生与本地计算机上的快捷键相同的行为。 例如，从 Windows 客户端连接到 Windows VM 时，CTRL+ALT+END 是本地计算机上 CTRL+ALT+Delete 的键盘快捷方式。 若要在连接到 Windows VM 时从 Mac 上执行此操作，键盘快捷方式为 Fn+CTRL+ALT+Backspace。

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="使用端口 443 连接":::
