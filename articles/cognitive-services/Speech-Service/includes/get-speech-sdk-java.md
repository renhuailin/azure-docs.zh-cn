---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: fcc04cc4599681e3542b275d238b75352d690a95
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135732"
---
:::row:::
    :::column span="3":::
        Java SDK for Android 打包为 <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR（Android 库）<span class="docon docon-navigate-external x-hidden-focus"></span></a>，其中包括必要的库以及所需的 Android 权限。 它作为包 `com.microsoft.cognitiveservices.speech:client-sdk:1.14.0` 托管在 `https://csspeechstorage.blob.core.windows.net/maven/` 的 Maven 存储库中。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

若要从你的 Android Studio 项目中使用该包，请进行以下更改：

1. 在项目级 build.gradle  文件中，向 `repositories` 部分添加以下内容：
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. 在模块级 build.gradle  文件中，向 `dependencies` 部分添加以下内容：
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.14.0'
  ```

Java SDK 也是[语音设备 SDK](../speech-devices-sdk.md) 的一部分。

#### <a name="additional-resources"></a>其他资源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 特定的 Java 快速入门源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java 运行时 (JRE) 快速入门源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>
