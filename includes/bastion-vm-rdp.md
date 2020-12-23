---
title: include 文件
description: include 文件
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521521"
---
1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接的虚拟机，然后选择“连接”。 从下拉列表中选择“Bastion”。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="选择“Bastion”":::

1. 从下拉列表中选择“Bastion”后，将显示一条侧边栏，其中包含三个选项卡：RDP、SSH 和 Bastion。 由于已针对虚拟网络预配了 Bastion，因此默认情况下，“Bastion”选项卡处于活动状态。 选择“使用 Bastion”。

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="选择“Bastion”":::

1. 在“使用 Azure Bastion 连接”页上，输入虚拟机的用户名和密码，然后选择“连接” 。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="选择“Bastion”":::

1. 通过 Bastion 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="选择“Bastion”":::
