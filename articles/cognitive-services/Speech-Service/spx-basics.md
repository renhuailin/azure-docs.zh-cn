---
title: 语音 CLI 快速入门 - 语音服务
titleSuffix: Azure Cognitive Services
description: 开始使用 Azure 语音 CLI。 无需编写代码，即可与语音转文本、文本转语音和语音翻译等语音服务进行交互。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/28/2021
ms.author: nitinme
ms.openlocfilehash: 8a58c1e9aab722496f5cdb481d2da52aca2ca74e
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706708"
---
# <a name="get-started-with-the-azure-speech-cli"></a>开始使用 Azure 语音 CLI

在本文中，学习如何使用语音 CLI（一个命令行接口）访问语音转文本、文本转语音和语音翻译等语音服务，而无需编写代码。 语音 CLI 随时可投入生产，可用通过 `.bat` 或 shell 脚本，使用它自动化语音服务中的简单工作流。

本文假定你具备命令提示符、终端或 PowerShell 的相关工作知识。

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>基本用法

本部分介绍了一些基本的 SPX 命令，这些命令对于首次测试和试验通常非常有用。 首先，运行以下命令，查看内置于该工具的帮助。

```console
spx
```

可按关键字搜索帮助主题。 例如，输入以下命令，查看语音 CLI 用法示例列表：

```console
spx help find --topics "examples"
```

输入以下命令，查看用于识别命令的选项：

```console
spx help recognize
```

右侧列中列出了其他帮助命令。 可输入这些命令以获取有关子命令的详细帮助。

## <a name="speech-to-text-speech-recognition"></a>语音转文本（语音识别）

利用语音 CLI，使用系统的默认麦克风将语音转换为文本（语音识别）。 SPX 将在输入命令后开始侦听当前活动输入设备上的音频，并在你按下 ENTER 时停止。 然后，识别所录制的语音，并将其转换为控制台输出中的文本。

>[!IMPORTANT]
> 如果使用的是 Docker 容器，则 `--microphone` 不起作用。

运行以下命令：

```console
spx recognize --microphone
```

使用语音 CLI，你还可以识别音频文件中的语音。

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> 如果要识别 Docker 容器中音频文件的语音，请确保音频文件位于上一步中安装的目录中。

记住，若你遇到问题或者想要详细了解语音 CLI 的识别选项，只需键入：

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>文本转语音（语音合成）

运行以下命令会将文本作为输入，并将合成的语音输出到当前活动的输出设备（例如计算机扬声器）。

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

你还可将合成的输出保存到文件中。 在此示例中，我们在运行该命令的目录中创建一个名为 `my-sample.wav` 的文件。

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

这些示例假定你是以英语进行测试的。 但我们支持多种语言的语音合成。 你可使用此命令或通过访问[语言支持页面](./language-support.md)来拉取完整的语音列表。

```console
spx synthesize --voices
```

下面介绍如何使用发现的一种语音。

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

记住，若你遇到问题或者想要详细了解语音 CLI 的合成选项，只需键入：

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>语音到文本的转换

借助语音 CLI，你还可以执行语音到文本的转换。 运行此命令，可从默认的麦克风捕获音频，并以文本形式输出转换。 记住，你需要通过 `translate` 命令提供 `source` 和 `target` 语言。

```console
spx translate --microphone --source en-US --target ru-RU
```

转换成多种语言时，请用 `;` 分隔语言代码。

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

如果要保存转换的输出，请使用 `--output` 标志。 在此示例中，你还将从一个文件中读取内容。

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> 有关所有受支持的语言及其相应的区域设置代码列表，请参阅[语言和区域设置文章](language-support.md)。

记住，若你遇到问题或者想要详细了解语音 CLI 的转换选项，只需键入：

```console
spx help translate
```

## <a name="next-steps"></a>后续步骤

* [安装 GStreamer 以使用 MP3 和其他格式的语音 CLI](./how-to-use-codec-compressed-audio-input-streams.md)
* [语音 CLI 配置选项](./spx-data-store-configuration.md)
* [使用语音 CLI 的批处理操作](./spx-batch-operations.md)
