---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: pafarley
ms.openlocfilehash: 151be76c80334048fc88ce392e91e833ad15c91a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539298"
---
本快速入门介绍使用语音 SDK 进行文本到语音合成的常见设计模式。 首先，请进行基本的配置和合成，然后通过更高级的示例来了解自定义应用程序开发，其中包括：

* 获取内存中流形式的响应
* 自定义输出采样率和比特率
* 使用 SSML（语音合成标记语言）提交合成请求
* 使用神经语音

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>将语音合成到扬声器

现在，可以运行语音 CLI，以从文本合成语音。 在命令行中，更改为包含语音 CLI 二进制文件的目录。 然后，运行以下命令。

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

语音 CLI 将通过计算机扬声器生成英语的自然语言。

## <a name="synthesize-speech-to-a-file"></a>将语音合成到文件中

运行以下命令，将扬声器的输出更改为 `.wav` 文件。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

语音 CLI 将采用英文向 `greetings.wav` 音频文件生成自然语言。
在 Windows 中，输入 `start greetings.wav` 可以播放音频文件。