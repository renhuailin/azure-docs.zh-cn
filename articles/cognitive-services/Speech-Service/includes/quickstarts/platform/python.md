---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: lajanuar
ms.openlocfilehash: 3b4724cf86adc25978dd0886a7cf5354c97e09e1
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070058"
---
本指南介绍如何安装用于 Python 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果只是需要包名称以便自行开始，请运行 `pip install azure-cognitiveservices-speech`。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

- Python 语音 SDK 包适用于以下操作系统：
  - Windows：x64 和 x86
  - Mac：macOS X 10.12 或更高版本
  - Linux：请参阅[受支持的 Linux 分发和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)的列表。

## <a name="prerequisites"></a>先决条件

- 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)。 首次安装时，可能需要重启。

- 在 Linux 上，请参阅[系统要求和安装说明](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)。

- 最后，你需要 [Python 3.6 到 3.9](https://www.python.org/downloads/)。 若要检查安装，请打开命令提示符并键入命令 `python --version`，然后检查结果。 如果安装正确，你将会收到像“Python 3.8.8”这样的响应。

## <a name="install-the-speech-sdk-from-pypi"></a>从 PyPI 安装语音 SDK

如果使用自己的环境或生成工具，请运行以下命令，从 [PyPI](https://pypi.org/) 安装语音 SDK。 对于 Visual Studio Code 的用户，请跳到下一子部分以按照指导安装。

```sh
pip install azure-cognitiveservices-speech
```

如果在 macOS 上操作，可能需要运行以下命令才能让上述 `pip` 命令正常运行：

```sh
python3 -m pip install --upgrade pip
```

成功使用 `pip` 安装 `azure-cognitiveservices-speech` 后，可以通过将命名空间导入 Python 项目来使用语音 SDK。

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>使用 Visual Studio Code 安装语音 SDK

1. 下载并安装适用于平台的最新支持版本 [Python](https://www.python.org/downloads/)（3.6 到 3.9）。
   - 在安装过程中，Windows 用户必须选择“将 Python 添加到 PATH”。
1. 下载并安装 [Visual Studio Code](https://code.visualstudio.com/Download)。
1. 打开 Visual Studio Code 并安装 Python 扩展。 在菜单中选择“文件” > “首选项” > “扩展”。 搜索 **Python** 并单击“安装”。

   ![安装 Python 扩展](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. 同样在 Visual Studio Code 中，通过集成式命令行安装语音 SDK Python 包：
   1. 打开终端（在下拉菜单中选择“终端” > “新终端”） 
   1. 在打开的终端中输入命令 `python -m pip install azure-cognitiveservices-speech`

如果你不熟悉 Visual Studio Code，请参阅更详细的 [Visual Studio Code 文档](https://code.visualstudio.com/docs)。 有关 Visual Studio Code 和 Python 的详细信息，请参阅 [Visual Studio Code Python 教程](https://code.visualstudio.com/docs/python/python-tutorial)。

## <a name="support-and-updates"></a>支持和更新

语音 SDK Python 包的更新将通过 PyPI 分发，[发行说明](~/articles/cognitive-services/speech-service/releasenotes.md)中会发布相关通告。
如果有新版本可用，可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令进行更新。
通过查看 `azure.cognitiveservices.speech.__version__` 变量来检查当前安装的版本。

如果遇到问题或者缺少某项功能，请查看[支持和帮助选项](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]