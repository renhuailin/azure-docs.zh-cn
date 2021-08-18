---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: d25d88f3c58310d1f1d1b9d4077355784ed7bc03
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067779"
---
:::row:::
    :::column span="3":::
        语音 SDK 支持 Windows 10 和 Windows Server 2016 或更高版本。 以前的版本不受官方支持  。 部分语音 SDK 可以在早期版本的 Windows 中使用，但不建议这样做。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>系统要求

Windows 版语音 SDK 要求系统上安装有 <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ Redistributable for Visual Studio 2019 </a>。

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">为 x86 安装 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">为 x64 安装 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">为 ARMx64 安装 </a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

对于麦克风输入，必须安装媒体基础库。 这些库包含在 Windows 10 和 Windows Server 2016 中。 只要未将麦克风用作音频输入设备，则可在没有这些库的情况下使用语音 SDK。

所需语音 SDK 文件可部署在与应用程序相同的目录中。 这样，应用程序便可直接访问库。 请确保选择与应用程序匹配的正确版本 (x86/x64)。

| 名称                                            | 函数                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | 核心 SDK，对于本机和托管部署是必需的 |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 对于托管部署是必需的                      |

> [!NOTE]
> 从版本 1.3.0 开始，不再需要 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` 文件（在以前的版本中提供）。 此功能现在集成到核心 SDK 中。

> [!IMPORTANT]
> 对于 Windows 窗体应用 (.NET Framework) C# 项目，请确保项目的部署设置中包含这些库。 你可以在 `Properties -> Publish Section` 下查看此内容。 单击 `Application Files` 按钮并从向下滚动列表中查找相应的库。 请确保将值设置为 `Included`。 Visual Studio 将在发布/部署项目时包含该文件。

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
