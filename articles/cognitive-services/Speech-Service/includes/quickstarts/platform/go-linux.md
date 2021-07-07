---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: c2222918106de8594881f105cc86ab1b45135027
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110164607"
---
本指南介绍如何安装用于 Linux 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系统要求

Linux：请参阅[受支持的 Linux 分发和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)的列表。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

* [Go 二进制文件（1.13 或更高版本）](https://golang.org/dl/)
* 根据[系统要求和安装说明](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)设置 Linux 环境。


[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>配置 Go 环境

执行以下步骤，设置 Go 环境，以查找语音 SDK。 在两个步骤中，将 `<architecture>` 替换为 CPU 的处理器体系结构。 即为 `x86`、`x64`、`arm32` 或 `arm64`。

1. 由于绑定依赖于 `cgo`，因此需要设置环境变量，以便 Go 可以找到 SDK：

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. 为了运行包含 SDK 的应用程序，我们需要告诉 OS 在哪里可以找到这些库：

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list-go.md)]
