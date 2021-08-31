---
title: 如何通过 C++ 语音 SDK 使用简单语言模式匹配
titleSuffix: Azure Cognitive Services
description: 本指南介绍如何从简单模式中识别意向和实体。
services: cognitive-services
author: chschrae
manager: travisw
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: chschrae
ms.custom: devx-track-cpp
ms.openlocfilehash: abb89fc460915d60402e6b259a8d34c0c5cdf05c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713353"
---
# <a name="how-to-use-simple-language-pattern-matching-with-the-c-speech-sdk"></a>如何通过 C++ 语音 SDK 使用简单语言模式匹配

认知服务[语音 SDK](speech-sdk.md) 内置了一项功能，可通过简单语言模式匹配来提供意向识别。 意向是用户想要执行的操作：关闭窗口、标记复选框、插入一些文本等。

在本指南中，我们将使用语音 SDK 开发一个 C++ 控制台应用程序，用于通过设备麦克风从用户语句中派生意向。 将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 创建引用语音 SDK NuGet 包的 Visual Studio 项目
> - 创建语音配置并获取意向识别器
> - 通过语音 SDK API 添加意向和模式
> - 从文件中识别语音
> - 使用异步的事件驱动的连续识别

## <a name="when-should-you-use-this"></a>在什么情况下使用？

可以在以下情况下使用此示例代码： 
* 你只对严格匹配用户所说的内容感兴趣。 这些模式的匹配比 LUIS 更严格。
* 你没有访问 LUIS 应用的权限，但仍需要意向。 这很有用，因为它嵌入在 SDK 中。
* 你不能或不想要创建 LUIS 应用，但仍需要某些语音命令功能。

如果你没有访问 LUIS 应用的权限，但仍需要意向，这将非常有用，因为它嵌入在 SDK 中。


## <a name="prerequisites"></a>先决条件

在开始阅读本指南之前，请务必准备好以下各项：

- [认知服务 Azure 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)或[统一语音资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)（版本不限）。

## <a name="speech-and-simple-patterns"></a>语音和简单模式

简单模式是语音 SDK 的一项功能，需要具有认知服务资源或统一语音资源。

模式是一个短语，其中包括一个实体。 实体是通过将字词扩在大括号中来定义的。 例如：

```
    Take me to the {floorName}
```

这定义了一个 ID 为“floorName”的实体，它区分大小写。

所有其他特殊字符和标点符号将被忽略。

将使用对 IntentRecognizer->AddIntent() API 的调用来添加意向。

## <a name="create-a-speech-project-in-visual-studio"></a>在 Visual Studio 中创建语音项目

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

在 Visual Studio 中打开项目。下一步，在 Visual Studio 中打开项目。

启动 Visual Studio 2019。
加载项目并打开 helloworld.cpp。
让我们从一些模板代码开始，添加一些代码，作为项目的框架。 请注意，你已创建名为 recognizeIntent() 的异步方法。

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中打开项目

接下来，在 Visual Studio 中打开项目。

1. 启动 Visual Studio 2019。
2. 加载项目并打开 `helloworld.cpp`。

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。

```cpp
    #include <iostream>
    #include <speechapi_cxx.h>

    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Intent;

    int main()
    {
        std::cout << "Hello World!\n";

        auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    }
```

## <a name="create-a-speech-configuration"></a>创建语音配置

在初始化 `IntentRecognizer` 对象之前，需要创建一个配置来使用认知服务预测资源的密钥和位置。

* 将 `"YOUR_SUBSCRIPTION_KEY"` 替换为认知服务预测密钥。
* 将 `"YOUR_SUBSCRIPTION_REGION"` 替换为认知服务资源区域。

此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](/cpp/cognitive-services/speech/speechconfig)。

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

现在创建一个 `IntentRecognizer`。 将此代码插入语音配置下。

```cpp
    auto intentRecognizer = IntentRecognizer::FromConfig(config);
```

## <a name="add-some-intents"></a>添加一些意向

需要通过调用 `AddIntent()` 将一些模式与 `IntentRecognizer` 关联。
我们将添加 2 个具有相同 ID 的意向用于更改楼层，再添加另一个具有单独 ID 的意向用于开门和关门。

```cpp
    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");
```

> [!NOTE]
> 对可以声明的实体数量没有限制，但它们是松散匹配的。 如果添加一个短语，如“{action} door”，每当单词“door”前面有文本时，它都会匹配。 意向是根据其实体数量来评估的。 如果有两种模式匹配，则返回具有更多已定义实体的模式。

## <a name="recognize-an-intent"></a>识别意向

在 `IntentRecognizer` 对象中，我们将调用 `RecognizeOnceAsync()` 方法。 此方法要求语音服务识别单个短语中的语音，并在识别到短语后停止识别语音。 此为简写内容，今后将回头补充。

将下面的代码插入到你的意向下方：

```cpp
    std::cout << "Say something ..." << std::endl;
    auto result = intentRecognizer->RecognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

当语音服务返回识别结果后，我们可以直接打印结果。

将此代码插在 `auto result = intentRecognizer->RecognizeOnceAsync().get();` 下：

```cpp
auto entities = result->GetEntities();

switch (result->Reason)
{
case ResultReason::RecognizedSpeech:
case ResultReason::RecognizedIntent:
    std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
    std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
    if (entities.find("floorName") != entities.end())
    {
        std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
    }

    if (entities.find("action") != entities.end())
    {
        std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
    }

    break;
case ResultReason::NoMatch:
{
    auto noMatch = NoMatchDetails::FromResult(result);
    switch (noMatch->Reason)
    {
    case NoMatchReason::NotRecognized:
        std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
        break;
    case NoMatchReason::InitialSilenceTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::InitialBabbleTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::KeywordNotRecognized:
        std::cout << "NOMATCH: Keyword not recognized" << std::endl;
        break;
    }
    break;
}
case ResultReason::Canceled:
{
    auto cancellation = CancellationDetails::FromResult(result);

    if (!cancellation->ErrorDetails.empty())
    {
        std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
        std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
    }
}
default:
    break;
}
```

## <a name="check-your-code"></a>查看代码

此时，代码应如下所示：

```cpp
#include <iostream>
#include <speechapi_cxx.h>

using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

int main()
{
    auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    auto intentRecognizer = IntentRecognizer::FromConfig(config);

    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");

    std::cout << "Say something ..." << std::endl;

    auto result = intentRecognizer->RecognizeOnceAsync().get();
    auto entities = result->GetEntities();

    switch (result->Reason)
    {
    case ResultReason::RecognizedSpeech:
    case ResultReason::RecognizedIntent:
        std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
        std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
        if (entities.find("floorName") != entities.end())
        {
            std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
        }

        if (entities.find("action") != entities.end())
        {
            std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
        }

        break;
    case ResultReason::NoMatch:
    {
        auto noMatch = NoMatchDetails::FromResult(result);
        switch (noMatch->Reason)
        {
        case NoMatchReason::NotRecognized:
            std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
            break;
        case NoMatchReason::InitialSilenceTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::InitialBabbleTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::KeywordNotRecognized:
            std::cout << "NOMATCH: Keyword not recognized." << std::endl;
            break;
        }
        break;
    }
    case ResultReason::Canceled:
    {
        auto cancellation = CancellationDetails::FromResult(result);

        if (!cancellation->ErrorDetails.empty())
        {
            std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
            std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }
    default:
        break;
    }
}
```
## <a name="build-and-run-your-app"></a>生成并运行应用

现在，可以使用语音服务构建应用并测试语音识别。

1. “编译代码”- 在 Visual Studio 菜单栏中，选择“生成” > “生成解决方案”  。
2. **启动应用** - 在菜单栏中，选择“调试” > “开始调试”，或按 F5   。
3. 开始识别 - 它将提示你说点什么。 默认语言为英语。 语音将发送到语音服务，转录为文本，并在控制台中呈现。

例如，如果你说“Take me to floor 7”，则输出如下所示：

```
Say something ...
RECOGNIZED: Text = Take me to floor 7.
  Intent Id = ChangeFloors
  Floor name: = seven
```
