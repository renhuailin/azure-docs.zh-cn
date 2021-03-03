---
title: 通过串行连接到 Azure Percept 深色
description: 了解如何使用 PuTTY 和 USB 到 TTL 串行电缆设置到 Azure Percept 深色的串行连接
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661858"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>通过串行连接到 Azure Percept 深色

按照以下步骤设置与 Azure Percept 深色到 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)的串行连接。

> [!WARNING]
> 请勿 **尝试将** devkit 与串行连接，但在极端故障情况下除外 (例如，bricked 设备) 。 分离托架箱以连接串行电缆是非常困难的，并且会断开 Wi-Fi 天线电缆。

## <a name="prerequisites"></a>先决条件

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- 宿主 PC
- Azure Percept 深色
- [USB 到 TTL 串行电缆](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB 到 TTL 串行电缆。":::

## <a name="initiate-the-serial-connection"></a>启动串行连接

1. 如果承运人连接到80/20 导轨，请使用 devkit 欢迎卡) 中包含的十六进制 (密钥将其从导轨中取出。

1. 卸下托架箱的底部螺钉，并拔出主板。

    > [!WARNING]
    > 卸下主板将损坏 Wi-Fi 天线电缆。 不要 **继续进行** 串行连接，除非这是恢复设备的最后手段。

1. 卸下散热器。

1. 从 GPIO pin 中删除跳线。

    > [!TIP]
    > 请注意，在删除跳线之前，跳线的方向。 例如，在上绘制一个箭头，或将一个不干胶标签贴到指电路的线上以供参考。 跳线不是固定的，在 reassembling 承运商板时可能会意外地向后连接。

1. 将 [USB 到 TTL 串行电缆](https://www.adafruit.com/product/954) 连接到主板上的 GPIO pin，如下所示。 请注意，红色线路未连接。

    - 将黑色缆线 (GND) 连接到 pin 6。
    - 将 (RX) 的白色电缆连接到引脚8。
    - 将绿色电缆 (TX) 连接到 pin 10。

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="承运商板串行 pin 连接。":::

1. 开机并将串行电缆的 devkit 连接到您的 PC。

1. 在 Windows 中，请参阅 **开始**  ->  **Windows 更新设置**  ->  **查看可选更新**  ->  **驱动程序更新**。 在列表中查找 "串行到 USB 更新"，选中它旁边的框，然后单击 " **下载并安装**"。  

1. 接下来，打开 Windows Device Manager (**Start**  ->  **Device Manager** ") "。 请中转到 " **端口** "，然后单击 " **USB 到 UART** " 以打开 " **属性**"。 请注意设备连接到的 COM 端口。

1. 单击 " **端口设置** " 选项卡。确保 **每秒位数** 设置为115200。

1. 打开 PuTTY。 输入以下内容，并单击 " **打开** " 以通过串行连接到 devkit：

    1. 串行行： COM [端口号]
    1. 速度：115200
    1. 连接类型：串行

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="已选择串行参数的 PuTTY 会话窗口。":::

## <a name="next-steps"></a>后续步骤

若要使用 [usb 到 TTL 串行电缆](https://www.adafruit.com/product/954)更新通过串行的设备，请参阅 usb 更新指南以了解非标准情况。

[comment]: # (添加指向 USB 更新指南的链接（如果可用）。)