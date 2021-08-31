---
title: 语音 SDK 日志记录 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在语音 SDK（C++、C#、Python、Objective-C、Java）中启用日志记录。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 584e200beac484ea742d51341a9cb93f0cfc4a41
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745645"
---
# <a name="enable-logging-in-the-speech-sdk"></a>在语音 SDK 中启用日志记录

将日志记录到文件是语音 SDK 的一项可选功能。 在开发期间，日志记录可以提供语音 SDK 核心组件的附加信息和诊断数据。 将语音配置对象中的属性 `Speech_LogFilename` 设置为日志文件的位置和名称可以启用日志记录。 日志记录由语音 SDK 本机库中的静态类处理。 你可以启用任何语音 SDK 识别器或合成器实例的日志记录。 同一进程中的所有实例都将日志条目写入相同日志文件。

> [!NOTE]
> 从语音 SDK 版本 1.4.0 开始，日志记录可在所有支持的语音 SDK 编程语言中使用，但 JavaScript 除外。

## <a name="sample"></a>示例

日志文件名在配置对象中指定。 以 `SpeechConfig` 为例，假设你已创建名为 `config` 的实例：

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

```go
import ("github.com/Microsoft/cognitive-services-speech-sdk-go/common")

config.SetProperty(common.SpeechLogFilename, "LogfilePathAndName")
```

可以基于配置对象创建识别器。 这会为所有识别器启用日志记录。

> [!NOTE]
> 如果基于配置对象创建 `SpeechSynthesizer`，则不会启用日志记录。 不过，如果启用了日志记录，则还会从 `SpeechSynthesizer` 收到诊断数据。

## <a name="create-a-log-file-on-different-platforms"></a>在不同的平台上创建日志文件

对于 Windows 或 Linux，日志文件可以位于用户有权写入的任何路径。 默认情况下，对其他操作系统中的文件系统位置的写入权限可能会受到限制。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 应用程序需将日志文件放在某个应用程序数据位置（本地、漫游或临时位置）。 可在本地应用程序文件夹中创建日志文件：

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

在 Unity UWP 应用程序中，可以使用应用程序持久数据路径文件夹创建日志文件，如下所示：

```csharp
#if ENABLE_WINMD_SUPPORT
    string logFile = Application.persistentDataPath + "/logFile.txt";
    config.SetProperty(PropertyId.Speech_LogFilename, logFile);
#endif
```
有关 UWP 应用程序中文件访问权限的详细信息，请参阅[文件访问权限](/windows/uwp/files/file-access-permissions)。

### <a name="android"></a>Android

可将日志文件保存到内部存储、外部存储或缓存目录。 在内部存储或缓存目录中创建的文件专用于应用程序。 最好是在外部存储中创建日志文件。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

以上代码将日志文件保存到应用程序特定的目录的根目录中的外部存储。 用户可以使用文件管理器访问该文件（通常位于 `Android/data/ApplicationName/logfile.txt`）。 卸载应用程序时，会删除该文件。

还需要请求清单文件中的 `WRITE_EXTERNAL_STORAGE` 权限：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

在 Unity Android 应用程序中，可以使用应用程序持久数据路径文件夹创建日志文件，如下所示：

```csharp
string logFile = Application.persistentDataPath + "/logFile.txt";
config.SetProperty(PropertyId.Speech_LogFilename, logFile);
```
此外，还需要在适用于 Android 的 Unity Player 设置中将写入权限设置为“外部 (SDCard)”。 日志将写入可以使用 AndroidStudio 设备文件资源管理器等工具获取的目录中。 确切的目录路径可能因 Android 设备而异，通常位于 `sdcard/Android/data/your-app-packagename/files` 目录。

[此处](https://developer.android.com/guide/topics/data/data-storage.html)提供了有关适用于 Android 应用程序的数据和文件存储的详细信息。

#### <a name="ios"></a>iOS

只能访问应用程序沙盒中的目录。 可以在文档、库和临时目录中创建文件。 文档目录中的文件可供用户使用。 

如果在 iOS 上使用 Objective-C，请使用以下代码片段在应用程序文档目录中创建日志文件：

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

若要访问创建的文件，请将以下属性添加到应用程序的 `Info.plist` 属性列表中：

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

如果在 iOS 上使用 Swift，请使用以下代码片段启用日志：
```swift
let documentsDirectoryPathString = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true).first!
let documentsDirectoryPath = NSURL(string: documentsDirectoryPathString)!
let logFilePath = documentsDirectoryPath.appendingPathComponent("swift.log")
self.speechConfig!.setPropertyTo(logFilePath!.absoluteString, by: SPXPropertyId.speechLogFilename)
```

[此处](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)提供了有关 iOS 文件系统的详细信息。

## <a name="logging-with-multiple-recognizers"></a>使用多个识别器进行日志记录

尽管将日志文件输出路径指定为 `SpeechRecognizer` 或其他 SDK 对象中的配置属性，但 SDK 日志记录是一个在整个进程中持续的单一功能，没有单个实例的概念。 可以将其视为 `SpeechRecognizer` 构造函数（或类似方法），使用相应的 `SpeechConfig` 中可用的属性数据隐式调用一个静态的内部“配置全局日志记录”例程。

这意味着，例如，不能将六个并行识别器配置为同时输出到六个单独的文件。 相反，创建的最新识别器将配置全局日志记录实例，以输出到其配置属性中指定的文件，并将所有 SDK 日志记录发送到该文件。

这也意味着已配置日志记录的对象的生存期与日志记录的持续时间无关。 日志记录不会因为 SDK 对象的发布而停止，只要没有提供新的日志记录配置，日志记录就会继续。 一旦启动，可以通过在创建新对象时将日志文件路径设置为空字符串来停止整个进程的日志记录。

为了减少在为多个实例配置日志记录时可能出现的混乱，将日志记录的控制从执行实际工作的对象中抽象出来可能很有用。 下面是帮助程序例程的一个示例对：

```cpp
void EnableSpeechSdkLogging(const char* relativePath)
{
    auto configForLogging = SpeechConfig::FromSubscription("unused_key", "unused_region");
    configForLogging->SetProperty(PropertyId::Speech_LogFilename, relativePath);
    auto emptyAudioConfig = AudioConfig::FromStreamInput(AudioInputStream::CreatePushStream());
    auto temporaryRecognizer = SpeechRecognizer::FromConfig(configForLogging, emptyAudioConfig);
}

void DisableSpeechSdkLogging()
{
    EnableSpeechSdkLogging("");
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
