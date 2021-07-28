---
title: 通过串行连接到 Azure Percept DK
description: 如何使用 USB 转 TTL 串行电缆设置与 Azure Percept DK 的串行连接
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: b1cb975c65f2234892cfef919220c68ebf5b2dca
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2021
ms.locfileid: "109488260"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>通过串行连接到 Azure Percept DK

按照以下步骤通过 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 设置与 Azure Percept DK 的串行连接。

> [!WARNING]
> 除非出现极端故障（例如，设备无法启动），否则请勿尝试通过串行连接到你的 devkit。 拆开承载板机箱来连接串行电缆非常困难，并且会断开 Wi-Fi 天线电缆。

## <a name="prerequisites"></a>先决条件

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- 主机 PC
- Azure Percept DK
- [USB 转 TTL 的串行电缆](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB 转 TTL 的串行电缆。":::

## <a name="start-the-serial-connection"></a>启动串行连接

1. 如果承载板已与 80/20 滑轨连接，请使用内六角扳手（devkit 欢迎卡中提供）从滑轨卸下承载板。

1. 卸下承载板机箱底部的螺丝，并拔出主板。

    > [!WARNING]
    > 卸下主板将断开 Wi-Fi 天线电缆。 请勿继续进行串行连接，除非这是恢复设备的最后手段。

1. 卸下散热器。

1. 从 GPIO 管脚卸下端子排。

    > [!TIP]
    > 在卸下端子排之前，请记下它的方向。 例如，在端子排上画一个箭头或贴上标签指向电路以供参考。 端子排不是固定的，在重新装配承载板时可能会意外接反。

1. 将 [USB 转 TTL 的串行电缆](https://www.adafruit.com/product/954)连接到主板上的 GPIO 管脚，如下所示。

    - 将黑色电缆 (GND) 连接到管脚 6。
    - 将白色电缆 (RX) 连接到管脚 8。
    - 将绿色电缆 (TX) 连接到管脚 10。

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="承载板串行管脚连接。":::

1. 启动 devkit 并将串行电缆的 USB 端连接到 PC。

1. 在 Windows 中，请转到“开始” -> “Windows 更新”设置  -> “查看可选更新” -> “驱动程序更新”。 在列表中查找“串行到 USB 更新”，选中它旁边的复选框，然后选择“下载并安装”。  

1. 接下来，打开 Windows 设备管理器（“开始” -> “设备管理器”） 。 转到“端口”并选择“USB 转 UART”，以打开“属性”。 请注意设备连接到的 COM 端口。

1. 选择“端口设置”选项卡。确保“每秒位数”设置为 115200 。

1. 打开 PuTTY。 输入以下内容并选择“打开”，以通过串行方式连接到 devkit：

    1. 串行线路：COM[端口号]
    1. 速度：115200
    1. 连接类型 - 串行

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="已选择串行参数的 PuTTY 会话窗口。":::
