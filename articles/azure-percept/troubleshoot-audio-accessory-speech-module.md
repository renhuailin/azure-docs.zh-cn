---
title: 排查 Azure Percept 音频和语音模块的问题
description: 获取有关在快速体验过程中发现的一些更常见问题的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a3877ea680e7b4c705f127c54e0fa10c45d3b51d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097969"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept 音频和语音模块故障排除

使用下面的指导原则排查声音助手应用程序问题。

## <a name="collecting-speech-module-logs"></a>收集语音模块日志

若要运行这些命令，请 [连接到 Azure PERCEPT 深色 Wi-Fi 访问点，通过 ssh 连接到开发工具包](./how-to-ssh-into-percept-dk.md) ，并在 ssh 终端中输入命令。

```console
 iotedge logs azureearspeechclientmodule
```

若要将任何输出重定向到 .txt 文件以进行进一步分析，请使用以下语法：

```console
[command] > [file name].txt
```

将输出重定向到 .txt 文件后，请通过 SCP 将文件复制到主机 PC：

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[本地主机文件路径] 是指你要将 .txt 文件复制到的主机 PC 上的位置。 [远程用户名] 是 [在加入体验](./quickstart-percept-dk-set-up.md)期间选择的 SSH 用户名。 如果在 Azure Percept 深色的使用体验期间未设置 SSH 登录，则远程用户名为 root。

## <a name="checking-runtime-status-of-the-speech-module"></a>正在检查语音模块的运行时状态

检查 **azureearspeechclientmodule** 的运行时状态是否显示为 " **正在运行**"。 若要找到设备模块的运行时状态，请打开 [Azure 门户](https://portal.azure.com/)，然后导航到 "**所有资源**"  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** 。 单击 " **模块** " 选项卡，查看所有已安装模块的运行时状态。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure 门户中的 &quot;边缘设备&quot; 页。":::

如果 **azureearspeechclientmodule** 的运行时状态未列为 "**正在运行**"，请单击 "**设置模块**"  ->  **azureearspeechclientmodule**。 在 " **模块设置** " 页上，将 **所需状态** 设置为 " **正在运行** " 并单击 " **更新**"

## <a name="understanding-ear-som-led-indicators"></a>了解 Ear SoM LED 指示灯

您可以使用 LED 指示器来了解设备所处的状态。 通常，在 *开机* 后，模块需要大约2分钟的时间才能完全初始化。 完成初始化步骤后，你将看到：

1. 1中心白 LED-设备已通电。 
2. 1中心白 LED LED 闪烁-正在进行身份验证。 
3. 在对设备进行身份验证并准备好使用后，所有三个 Led 都将变为蓝色。

|LED|   LED 状态|  耳 SoM 状态|
|---|------------|----------------| 
|L02|   1x 白色，静态打开 |开机 |
|L02|   1x 白色，0.5 Hz 闪烁|  正在进行身份验证 |
|L01 & L02 & L03|   3倍速，静态开启|     正在等待关键字|
|L01 & L02 & L03|   LED 阵列闪烁，20fps | 正在侦听或讲述|
|L01 & L02 & L03|   LED 阵列赛车，20fps|    想法|
|L01 & L02 & L03|   3倍速，静态开启 | 静音|

## <a name="next-steps"></a>后续步骤

有关 Azure Percept 深色疑难解答的详细信息，请参阅 [常规故障排除指南](./troubleshoot-dev-kit.md) 。
