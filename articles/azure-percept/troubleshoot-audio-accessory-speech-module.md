---
title: 排查 Azure Percept 音频和语音模块的问题
description: 获取有关在快速体验过程中发现的一些更常见问题的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679128"
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

[本地主机文件路径] 是指你要将 .txt 文件复制到的主机 PC 上的位置。 [远程用户名] 是在 [安装](./quickstart-percept-dk-set-up.md)过程中选择的 SSH 用户名。 如果未在 OOBE 期间设置 SSH 登录，则远程用户名为 root。

## <a name="checking-runtime-status-of-the-speech-module"></a>正在检查语音模块的运行时状态

检查 **azureearspeechclientmodule** 的运行时状态是否显示为 " **正在运行**"。 若要找到设备模块的运行时状态，请打开 [Azure 门户](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)，然后导航到 "**所有资源**"  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** 。 单击 " **模块** " 选项卡，查看所有已安装模块的运行时状态。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure 门户中的 &quot;边缘设备&quot; 页。":::

如果 **azureearspeechclientmodule** 的运行时状态未列为 "**正在运行**"，请单击 "**设置模块**"  ->  **azureearspeechclientmodule**。 在 " **模块设置** " 页上，将 **所需状态** 设置为 " **正在运行** " 并单击 " **更新**"

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Azure 门户中的 &quot;设置模块&quot; 屏幕。":::

## <a name="understanding-ear-som-led-indicators"></a>了解 Ear SoM LED 指示灯

您可以使用 LED 指示器来了解设备所处的状态。 通常，在 *开机* 后，模块需要大约2分钟的时间才能完全初始化。 完成初始化步骤后，你将看到：

1. 1左绿色指示灯-设备已通电。 
2. 1左侧绿色指示灯和中心 LED 闪烁绿色-正在进行身份验证。 
3. 在对设备进行身份验证并准备好使用后，所有三个 Led 都将变为蓝色。

|LED 状态                  |耳 SoM 状态            |
|----------------------------|---------------------------|
|1x 绿色 (左 LED)          |开机 |
|1x 绿色 (左 LED)  <br> 1x 绿色 (中心 LED 闪烁)  |正在进行身份验证 |
|3倍                      |初始化完成 |
|蓝蓝                     |可供使用 |
|蓝3倍速闪烁            |已识别关键字 |
|蓝色3倍速              |处理 |
|红色3                      |全部 |

## <a name="next-steps"></a>后续步骤

有关 Azure Percept 深色疑难解答的详细信息，请参阅 [常规故障排除指南](./troubleshoot-dev-kit.md) 。