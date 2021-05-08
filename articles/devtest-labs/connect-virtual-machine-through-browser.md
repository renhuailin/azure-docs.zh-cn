---
title: 通过浏览器连接到 虚拟机 - Azure | Microsoft Docs
description: 了解如何通过浏览器连接到虚拟机。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 352fc5fd3ff53a00d9f62966ecf21417ad898706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288063"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>通过浏览器连接到虚拟机 

开发测试实验室与 [Azure Bastion](../bastion/index.yml) 集成，便可以通过浏览器连接到虚拟机。 有关如何在开发测试实验室中启用此功能的信息，请参阅[在实验室虚拟机上启用浏览器连接](enable-browser-connection-lab-virtual-machines.md)。

启用“浏览器连接”后，实验室用户可以通过浏览器访问虚拟机。  

## <a name="create-a-lab-virtual-machine"></a>创建实验室虚拟机

首先需要在配置了 Bastion 的虚拟网络中创建实验室虚拟机。 选择创建的第二个“子网”，而不是 AzureBastionSubnet。 可以通过转到“高级设置”选项卡，在虚拟机创建过程中选择虚拟网络。

![创建虚拟机](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>在浏览器中启动虚拟机

创建虚拟机后，可以在浏览器中启动该虚拟机，方法是单击“浏览器连接”按钮，然后输入计算机的用户名和密码。  

![在浏览器中启动](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>后续步骤

[向 Azure 开发测试实验室中的实验室添加 VM](devtest-lab-add-vm.md)
