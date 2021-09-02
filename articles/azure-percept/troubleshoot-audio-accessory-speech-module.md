---
title: Azure Percept Audio 和语音模块疑难解答
description: 获取 Azure Percept Audio 和 azureearspeechclientmodule 的故障排除提示
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 00f3694201d3aaf876d642e67e5b523f6fb50547
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222721"
---
# <a name="troubleshoot-azure-percept-audio-and-speech-module"></a>Azure Percept Audio 和语音模块疑难解答

使用下面的指南排查声音助理应用程序问题。

## <a name="checking-runtime-status-of-the-speech-module"></a>检查语音模块的运行时状态

检查 azureearspeechclientmodule 的运行时状态是否显示为“运行”。 若要查找设备模块的运行时状态，请打开 [Azure 门户](https://portal.azure.com/)，然后导航到“所有资源” ->  [你的 IoT 中心]  -> “IoT Edge” ->  [你的设备 ID]   。 选择“模块”选项卡，查看所有已安装模块的运行时状态。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure 门户中的边缘设备页。":::

如果 azureearspeechclientmodule 的运行时状态未列为“正在运行”，请选择“设置模块” -> “azureearspeechclientmodule”。 在“模块设置”页上，将“所需状态”设置为“正在运行”并选择“更新”。

## <a name="voice-assistant-application-doesnt-load"></a>语音助理应用程序未加载
尝试[部署语音助理模板之一](./tutorial-no-code-speech.md)。 部署模板可确保创建语音助理应用程序所需的所有支持资源。

## <a name="voice-assistant-template-doesnt-get-created"></a>无法创建语音助理模板
创建语音助理模板时出现故障通常是支持资源之一出现问题。
1. [删除之前创建的所有语音助理资源](./delete-voice-assistant-application.md)。
1. 部署新的[语音助理模板](./tutorial-no-code-speech.md)。

## <a name="voice-assistant-was-created-but-doesnt-respond-to-commands"></a>已创建语音助理，但它不响应命令
按照 [LED 行为和故障排除指南](audio-button-led-behavior.md)上的说明来排查此问题。

## <a name="voice-assistant-doesnt-respond-to-custom-keywords-created-in-speech-studio"></a>语音助理不响应在 Speech Studio 中创建的自定义关键字
如果语音模块已过期，可能会发生这种情况。 请按照以下步骤将语音模块更新到最新版本：

1. 在 Azure Percept Studio 主页的左侧菜单面板中，选择“设备”。
1. 找到并选择你的设备。

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Azure Percept Studio 中设备列表的屏幕截图。":::
1. 在设备窗口中，选择“语音”选项卡。
1. 检查语音模块版本。 如果有可用更新，版本号旁边会出现“更新”按钮。
1. 选择“更新”以部署语音模块更新。 更新过程通常需要 2-3 分钟才能完成。

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
- [如何返回到以前创建的语音助理应用程序](return-to-voice-assistant-application-window.md)
