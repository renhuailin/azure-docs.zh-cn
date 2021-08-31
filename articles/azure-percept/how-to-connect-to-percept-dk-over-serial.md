---
title: 通过串行连接到 Azure Percept DK
description: 如何使用 USB 转 TTL 串行电缆设置与 Azure Percept DK 的串行连接
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: a9b864f5d8341253c70fbde6d111b04a4d924f1d
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371479"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>通过串行连接到 Azure Percept DK

按照以下步骤通过 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 设置与 Azure Percept DK 的串行连接。

> [!WARNING]
> **如果有个人预览版开发工具包**，我们不建议您尝试通过串行接口连接你的开发工具包，除非出现极端故障情况（例如，你的设备无法启动了）。 通过串行连接需要反汇编个人预览版开发工具包，以访问 GPIO 引脚。 拆开载板外壳非常困难，可能会损坏 Wi-Fi 天线电缆。

## <a name="prerequisites"></a>先决条件

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- 主机 PC
- Azure Percept DK
- [USB 转 TTL 的串行电缆](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB 转 TTL 的串行电缆。":::

## <a name="start-the-serial-connection"></a>启动串行连接

1. 将 [USB 转 TTL 的串行电缆](https://www.adafruit.com/product/954)连接到主板上的三个 GPIO 管脚，如下所示。

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/apdk-serial-pins.jpg" alt-text="承载板串行管脚连接。":::

1. 启动 devkit 并将串行电缆的 USB 端连接到 PC。

1. 在 Windows 中，请转到“开始” -> “Windows 更新”设置  -> “查看可选更新” -> “驱动程序更新”。 在列表中查找“串行到 USB 更新”，选中它旁边的复选框，然后选择“下载并安装”。  

1. 接下来，打开 Windows 设备管理器（“开始” -> “设备管理器”） 。 转到“端口”并选择“USB 转 UART”，以打开“属性”。 请注意设备连接到的 COM 端口。

1. 选择“端口设置”选项卡。确保“每秒位数”设置为 115200 。

1. 打开 PuTTY。 输入以下内容并选择“打开”，以通过串行方式连接到 devkit：

    1. 串行线路：COM[端口号]
    1. 速度：115200
    1. 连接类型 - 串行

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="已选择串行参数的 PuTTY 会话窗口。":::
