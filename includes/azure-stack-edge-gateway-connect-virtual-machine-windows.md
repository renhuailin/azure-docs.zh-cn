---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730608"
---
使用远程桌面协议 (RDP) 通过在 VM 创建期间传递的 IP 来连接到 Windows VM。

1. 在客户端上，打开 RDP。 
1. 中转到 " **开始**"，然后键入 **mstsc**。
1. 在 " **远程桌面连接** " 窗格上，输入 VM 的 IP 地址以及在 vm 模板参数文件中使用的访问凭据，然后选择 " **连接**"。

   ![用于通过 RDP 连接到 Windows VM 的 "远程桌面连接" 窗格的屏幕截图。](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > 你可能需要批准连接到不受信任的计算机。 

你现在已登录到设备上运行的 VM。 
