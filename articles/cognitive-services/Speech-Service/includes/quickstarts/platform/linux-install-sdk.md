---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188203"
---
## <a name="install-speech-sdk"></a>安装语音 SDK

适用于 Linux 的语音 SDK 可用于构建 64 位和 32 位应用程序。 可以从 https://aka.ms/csspeech/linuxbinary 以 tar 文件格式下载必需的库和头文件。

下载并安装 SDK，如下所示：

1. 选择应将语音 SDK 文件提取到的目录，然后将 `SPEECHSDK_ROOT` 环境变量设置为指向该目录。 使用此变量，在将来的命令中可以轻松引用目录。 例如，如果要使用主目录中的 `speechsdk` 目录，请使用如下所示的命令：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 如果该目录尚不存在，请创建该目录。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下载并提取包含语音 SDK 二进制文件的 `.tar.gz` 存档：

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 验证所提取的程序包的顶级目录的内容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   目录列表应当包含第三方通告和许可证文件，以及一个包含头文件 (`.h`) 的 `include` 目录和一个包含库的 `lib` 目录。

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]