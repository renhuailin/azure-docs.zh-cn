---
title: 语音 CLI 批处理操作 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 CLI 来将语音批处理为文本（语音识别），以及将文本批处理为语音（语音合成）。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: lajanuar
ms.openlocfilehash: c3481edc4b66afc4556ae6d7e957f2dfee9b2e69
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122072329"
---
# <a name="speech-cli-batch-operations"></a>语音 CLI 批处理操作

使用 Azure 语音服务时执行的常见任务就是批处理操作。 在本文中，你将了解如何使用语音 CLI 将语音批处理为文本（语音识别）以及将文本批处理为语音（语音合成）。 具体而言，你将了解如何：

* 在音频文件目录上运行批处理语音识别
* 循环访问 `.tsv` 文件来运行批处理语音合成

## <a name="batch-speech-to-text-speech-recognition"></a>将语音批处理为文本（语音识别）

语音服务通常用于从音频文件中识别语音。 在本例中，你将学习如何使用语音 CLI 循环访问目录来捕获每个 `.wav` 文件的识别输出内容。 `--files` 标志用于指向存储音频文件的目录，通配符 `*.wav` 用于指示语音 CLI 在每个带有扩展名 `.wav` 的文件上运行识别功能。 每个识别文件的输出以制表符分隔的值的形式写入 `speech_output.tsv`。

> [!NOTE]
> `--threads` 参数也可以在下一部分中用于 `spx synthesize` 命令，可用线程将取决于 CPU 及其当前负载百分比。

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

下面是输出文件结构的示例。

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>将文本批处理为语音（语音合成）

运行批处理文本转语音的最简单方法是创建一个新的 `.tsv`（制表符分隔值）文件，并使用语音 CLI 中的 `--foreach` 命令。 可使用你喜欢的文本编辑器来创建一个 `.tsv` 文件；在本例中，让我们将它命名为 `text_synthesis.tsv`：

>[!IMPORTANT]
> 复制此文本文件的内容时，请确保文件在文件位置与文本之间放置一个制表符（不带空格）。 有时，在从此示例复制内容时，制表符会转换为空格，这会导致 `spx` 命令在运行时失败。

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

接下来，运行命令以指向 `text_synthesis.tsv`，对每个 `text` 字段执行合成，然后将结果作为 `.wav` 文件写入相应的 `audio.output` 路径中。

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

此命令等效于对 `.tsv` 文件中的每个记录运行 `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav`。

请注意以下几点：

* 列标题 `audio.output` 和 `text` 分别对应于命令行参数 `--audio output` 和 `--text`。 由多个部分组成的命令行参数（如 `--audio output`）应在文件中格式化，不带空格、无前导短划线且使用句点分隔字符串，例如 `audio.output`。 使用此模式，可将其他任何现有命令行参数作为其他列添加到文件中。
* 以这种方式格式化文件时，不需要将其他参数传递给 `--foreach`。
* 请确保通过选项卡将 `.tsv` 中的每个值分隔开。

但是，如果你具有如下例所示的 `.tsv` 文件（其列标头与命令行参数不匹配）：

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

可以在 `--foreach` 调用中使用以下语法将这些字段名称替代为正确的参数。 此调用与上面相同。

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>后续步骤

* [语音 CLI 概述](./spx-overview.md)
* [语音 CLI 快速入门](./spx-basics.md)
