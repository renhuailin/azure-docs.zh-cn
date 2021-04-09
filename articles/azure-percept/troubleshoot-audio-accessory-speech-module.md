---
title: 排查 Azure Percept Audio 和语音模块问题
description: 获取 Azure Percept Audio 和 azureearspeechclientmodule 的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f34013bdb14481bfe872a9b3c4234d603bc2d7ec
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635563"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio 和语音模块故障排除

使用下面的指南排查声音助理应用程序问题。

## <a name="collecting-speech-module-logs"></a>收集语音模块日志

若要运行这些命令，请[连接 Azure Percept DK Wi-Fi 接入点，通过 SSH 连接开发工具包](./how-to-ssh-into-percept-dk.md)，并在 SSH 终端中输入命令。

```console
sudo iotedge logs azureearspeechclientmodule
```

若要将任何输出重定向到 .txt 文件以做进一步分析，请使用以下语法：

```console
sudo [command] > [file name].txt
```

将输出重定向到 .txt 文件后，通过 SCP 将该文件复制到主机电脑：

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[本地主机文件路径]是指主机电脑上要将 .txt 文件复制到的位置。 [远程用户名]是在[载入体验](./quickstart-percept-dk-set-up.md)期间选择的 SSH 用户名。 如果在 Azure Percept DK 载入体验期间未设置 SSH 登录，则远程用户名为 root。

## <a name="checking-runtime-status-of-the-speech-module"></a>检查语音模块的运行时状态

检查 azureearspeechclientmodule 的运行时状态是否显示为“运行”。 若要找到设备模块的运行时状态，请打开 [Azure 门户](https://portal.azure.com/)，然后导航到“所有资源” -> “\<your IoT hub>” -> “IoT Edge” -> “\<your device ID>”。 单击“模块”选项卡，查看所有已安装模块的运行时状态。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure 门户中的边缘设备页。":::

如果 **azureearspeechclientmodule** 的运行时状态未列为“运行”，请单击“设置模块” -> “azureearspeechclientmodule”。 在“模块设置”页上，将“所需状态”设置为“运行”并单击“更新”。

## <a name="understanding-ear-som-led-indicators"></a>了解 Ear SoM LED 指示器

可以使用 LED 指示器来了解设备所处的状态。 通常，在开机后，模块需要大约 2 分钟的时间才能完全初始化。 完成初始化步骤后，你将看到：

1. 1 中心白色 LED - 设备已通电。
2. 1 中心白色 LED 闪烁 - 正在进行身份验证。
3. 在对设备进行身份验证并准备好使用后，所有三个 LED 都将变为蓝色。

|LED|LED 状态|Ear SoM 状态|
|---|---------|--------------|
|L02|1x 白色，静态开启|开机 |
|L02|1x 白色，0.5 Hz 闪烁|正在进行身份验证 |
|L01 & L02 & L03|3x 蓝色，静态开启|关键字待定|
|L01 & L02 & L03|LED 数组闪烁，20fps |正在侦听或说话|
|L01 & L02 & L03|LED 阵列 racing，20fps|正在思考|
|L01 & L02 & L03|3x 红色，静态开启 |静音|

## <a name="next-steps"></a>后续步骤

有关 Azure Percept DK 疑难解答的详细信息，请参阅[常规故障排除指南](./troubleshoot-dev-kit.md)。
