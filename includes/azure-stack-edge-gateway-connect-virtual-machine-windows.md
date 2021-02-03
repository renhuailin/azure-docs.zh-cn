---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: e781e3c29a6a22648c6d0596f9fe0a3e425427d7
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500160"
---
使用远程桌面协议 (RDP) 通过在 VM 创建期间传递的 IP 来连接到 Windows VM。

1. 在客户端上，启动 RDP。 中转到 " **开始** " 并键入 " **mstsc**"。
1. 输入 vm 的 IP 地址以及在 VM 模板参数文件中使用的访问凭据。

    ![演示如何通过 RDP 连接到 Windows VM 的屏幕截图。](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)
1. 你可能需要批准连接到不受信任的计算机。 
1. 你现在已登录到在设备上运行的 VM。 