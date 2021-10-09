---
title: 连接到 Windows 虚拟机
description: 了解如何连接到实验室（Azure 开发测试实验室）中的 Windows 虚拟机
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 8fe7b93ce7aa2cf5521bd53393b086e221e84d2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588003"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>连接到实验室（Azure 开发测试实验室）中的 Windows VM
本文演示了如何连接到实验室中的 Windows VM。 

## <a name="connect-to-a-windows-vm"></a>连接到 Windows VM
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，搜索并选择“开发测试实验室”。 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="搜索并选择“开发测试实验室”":::    
1. 从实验室列表中，选择你的实验室。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="选择实验室":::            
1. 在该实验室的主页上，从“我的虚拟机”列表中选择 Windows VM。 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="选择 Windows VM":::                
1. 在该 VM 的“虚拟机”页上，从工具栏上选择“连接”。 如果该 VM 已停止，请先选择“启动”以启动 VM。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="在工具栏上选择连接":::                    
1. 如果使用的是 Edge 浏览器，则会看到浏览器底部出现指向已下载 RDP 文件的链接。 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="RDP 已下载":::                        
1. 打开 RDP 文件，输入在创建 VM 时键入的 VM 凭据。 现在应该已连接到 Windows VM。 

## <a name="next-steps"></a>后续步骤
[操作说明：连接到 Linux VM](connect-linux-virtual-machine.md)
