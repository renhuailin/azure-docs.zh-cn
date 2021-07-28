---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 2b01b4f11ac7777075848287626e021b89254acb
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108758279"
---
通过在 VM 创建期间传递的 IP，使用远程桌面协议 (RDP) 连接到 Windows VM。

1. 在客户端上，打开 RDP。 
1. 转到“开始”，然后输入 mstsc。
1. 在“远程桌面连接”窗格上，输入 VM 的 IP 地址以及你在 VM 模板参数文件中使用的访问凭据。 然后选择“连接”  。

   ![通过 RDP 连接到 Windows VM 时“远程桌面连接”窗格的屏幕截图。](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > 可能需要批准连接到某个不受信任的计算机。 

你现在已经登录到在设备上运行的 VM 了。 
