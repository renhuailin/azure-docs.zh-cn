---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: pafarley
ms.openlocfilehash: e4377b4c69f484f1e248f7aa74731b9fbe956d52
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539934"
---
针对 macOS 开发时，有三个语音 SDK 可用。

- Objective-C 语音 SDK 以 CocoaPod 包的形式本机提供
- .NET 语音 SDK 可与 Xamarin.Mac  一起使用，因为它实现了 .NET Standard 2.0
- Python 语音 SDK 以 PyPI 模块的形式提供

> [!TIP]
> 有关在 Swift 中使用 Objective-C 语音 SDK 的详细信息，请参阅<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">将 Objective-C 导入 Swift </a>。

### <a name="system-requirements"></a>系统要求

- macOS 版本 10.13 或更高版本

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        macOS CocoaPod 包可供下载并在 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1（或更高版本）</a>集成开发环境 (IDE) 中使用。 首先，<a href="https://aka.ms/csspeech/macosbinary" target="_blank">下载二进制 CocoaPod </a>。 将 Pod 提取到同一目录中以供使用，创建一个 Podfile  并将 `pod` 作为 `target` 列出。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac 向 .NET 开发人员公开了完整的 macOS SDK，以供其使用 C# 构建本机 Mac 应用程序。 有关详细信息，请参阅 <a href="/xamarin/mac/" target="_blank">Xamarin.Mac </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>其他资源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS 语音 SDK 快速入门 Objective-C 源代码 </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS 语音 SDK 快速入门 Swift 源代码 </a>