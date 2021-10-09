---
title: 连接到 Linux 虚拟机
description: 了解如何连接到实验室（Azure 开发测试实验室）中的 Linux 虚拟机
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 956c27235dcdf9d57c908ce2e840e3c77518875b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676041"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>连接到实验室（Azure 开发测试实验室）中的 Linux VM
本文演示了如何连接到实验室中的 Linux VM。 

## <a name="connect-to-a-linux-vm"></a>连接到 Linux VM
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，搜索并选择“开发测试实验室”。 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="搜索并选择“开发测试实验室”":::    
1. 从实验室列表中，选择你的实验室。

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="选择实验室":::            
1. 在该实验室的主页上，从“我的虚拟机”列表中选择 Linux VM。 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="选择 Linux VM":::        
5. 在“概述”页上，可以看到 VM 的完全限定域名 (FQDN) 或 IP 地址。 还可以看到如下图所示的端口。

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="VM 的完全限定域名":::    

    请注意，即使 VM 已启动，“连接”按钮仍灰显。 这是由设计决定的。
6.  使用 SSH 连接到 Linux VM。 以下示例使用用户名 `vmuser` 和端口 `51637` 连接到具有 FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com` 的 VM。 输入供用户连接到 VM 的密码。 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    可以使用 [Putty](https://www.putty.org/) 等工具或任何其他 SSH 客户端连接到 VM。 

    使用 SSH 进行连接后，可以安装并配置桌面环境 ([xfce](https://www.xfce.org)) 和远程桌面 ([xrdp](http://xrdp.org))。  有关详细信息，请参阅[安装并配置远程桌面以连接到 Azure 中的 Linux VM](../virtual-machines/linux/use-remote-desktop.md)。 

## <a name="next-steps"></a>后续步骤
[如何：连接到 Windows VM](connect-windows-virtual-machine.md)
