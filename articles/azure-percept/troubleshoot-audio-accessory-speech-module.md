---
title: 排查 Azure Percept Audio 和语音模块问题
description: 获取 Azure Percept Audio 和 azureearspeechclientmodule 的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c02c93acd23b71f73d5e27d8914d08fb1a1f99f9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071391"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio 和语音模块故障排除

使用下面的指南排查声音助理应用程序问题。

## <a name="understanding-ear-som-led-indicators"></a>了解 Ear SoM LED 指示器

可以使用 LED 指示器来了解设备所处的状态。 设备开机并且模块完全初始化需要大约 4-5 分钟。 完成初始化步骤后，你将看到：

1. 中央的白色 LED 亮起（静态）：设备已通电。
1. 中央的白色 LED 亮起（闪烁）：正在进行身份验证。
1. 中央的白色 LED 亮起（静态）：设备已通过身份验证，但未配置关键字。
1. 部署完演示并且设备准备好供使用后，所有三个 LED 都将变为蓝色。

有关更多参考资料，请参阅有关 [Azure Percept Audio 按钮和 LED 行为](./audio-button-led-behavior.md)的文章。

### <a name="troubleshooting-led-issues"></a>排查 LED 问题
- 如果中央的 LED 白色常亮，请尝试[使用模板创建语音助理](./tutorial-no-code-speech.md)。
- 如果中央的 LED 始终闪烁，则表明有身份验证问题。 请尝试以下故障排除步骤：
    - 请确保 USB-A 和 micro USB 连接受到保护 
    - 查看[语音模块是否正在运行](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module)
    - 重启设备
    - [收集日志](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs)并将其附加到支持请求
    - 查看开发工具包是否正在运行最新软件，并应用更新（如果可用）。

## <a name="checking-runtime-status-of-the-speech-module"></a>检查语音模块的运行时状态

检查 azureearspeechclientmodule 的运行时状态是否显示为“运行”。 若要查找设备模块的运行时状态，请打开 [Azure 门户](https://portal.azure.com/)，然后导航到“所有资源” ->  [你的 IoT 中心]  -> “IoT Edge” ->  [你的设备 ID]   。 单击“模块”选项卡，查看所有已安装模块的运行时状态。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure 门户中的边缘设备页。":::

如果 **azureearspeechclientmodule** 的运行时状态未列为“运行”，请单击“设置模块” -> “azureearspeechclientmodule”。 在“模块设置”页上，将“所需状态”设置为“运行”并单击“更新”。

## <a name="collecting-speech-module-logs"></a>收集语音模块日志

若要运行这些命令，请[通过 SSH 连接到开发工具包](./how-to-ssh-into-percept-dk.md)，并将命令输入到 SSH 客户端提示符下。

收集语音模块日志：

```console
sudo iotedge logs azureearspeechclientmodule
```

若要将输出重定向到 .txt 文件以做进一步分析，请使用以下语法：

```console
sudo [command] > [file name].txt
```

更改 .txt 文件的权限，以便可以复制它：

```console
sudo chmod 666 [file name].txt
```

将输出重定向到 .txt 文件后，通过 SCP 将该文件复制到主机电脑：

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[本地主机文件路径] 是指主机电脑上要将 .txt 文件复制到的位置。 [remote username] 是[安装体验](./quickstart-percept-dk-set-up.md)过程中选择的 SSH 用户名。

## <a name="known-issues"></a>已知问题
- 如果使用的是免费试用版，则语音模型可能不在免费试用价格计划内。 在这种情况下，模型将停止工作而不会出现错误消息。
- 如果连接了 5 个以上 IoT Edge 设备，则报告（通过遥测发送到 IoT 中心和 Speech Studio 的文本）可能会被阻止。
- 如果设备与资源处于不同的区域，则报告消息可能会延迟。 

## <a name="useful-links"></a>有用链接
- [Azure Percept 音频设置](./quickstart-percept-audio-setup.md)
- [Azure Percept Audio 按钮和 LED 行为](./audio-button-led-behavior.md)
- [使用 Azure Percept DK 和 Azure Percept 音频创建语音助理](./tutorial-no-code-speech.md)
- [Azure Percept DK 常规故障排除指南](./troubleshoot-dev-kit.md)
