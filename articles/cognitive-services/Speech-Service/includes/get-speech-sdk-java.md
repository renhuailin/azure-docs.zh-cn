---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: lajanuar
ms.openlocfilehash: dabaffe4e37bf3d818cd1478eba174c3b75ee4e0
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067777"
---
:::row:::
    :::column span="3":::
        Java SDK for Android 打包为 <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR（Android 库）</a>，其中包括必要的库以及所需的 Android 权限。 它作为包 `com.microsoft.cognitiveservices.speech:client-sdk:1.17.0` 托管在 `https://csspeechstorage.blob.core.windows.net/maven/` 的 Maven 存储库中。 （通过[搜索我们的 GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-speech-sdk/search?q=com.microsoft.cognitiveservices.speech%3Aclient-sdk)，确保 1.17.0 是最新版本。）
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="/media/logos/logo_java.svg" width="60px">
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
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.17.0'
  ```

Java SDK 也是[语音设备 SDK](../speech-devices-sdk.md) 的一部分。

#### <a name="additional-resources"></a>其他资源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 特定的 Java 快速入门源代码</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java 运行时 (JRE) 快速入门源代码</a>
