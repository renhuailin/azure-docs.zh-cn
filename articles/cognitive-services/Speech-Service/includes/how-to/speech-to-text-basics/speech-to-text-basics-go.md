---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/15/2020
ms.author: trbye
ms.openlocfilehash: 3c624a9dcdf61796dc4ce4396a0b309f84ceddaf
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113280063"
---
语音服务的核心功能之一是能够识别并转录人类语音（通常称为语音转文本）。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音转文本转换。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [Go 快速入门示例](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/recognizer)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装[适用于 Go 的语音 SDK](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser)，然后才能执行任何操作。

## <a name="speech-to-text-from-microphone"></a>从麦克风将语音转换为文本

使用下面的代码示例从默认的设备麦克风运行语音识别。 将变量 `subscription` 和 `region` 替换为你的语音密钥和位置/区域。 请参阅[查找密钥和位置/区域](../../../overview.md#find-keys-and-locationregion)页面，查找密钥位置/区域对。 运行脚本将在默认麦克风上启动识别会话并输出文本。

```go
package main

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func sessionStartedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
}

func sessionStoppedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
}

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(sessionStartedHandler)
    speechRecognizer.SessionStopped(sessionStoppedHandler)
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

运行以下命令，以创建一个 go.mod 文件并使其关联到 GitHub 上托管的组件。

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

现在生成并运行代码。

```cmd
go build
go run quickstart
```

有关 [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechConfig) 和 [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechRecognizer) 类的详细信息，请参阅参考文档。

## <a name="speech-to-text-from-audio-file"></a>从音频文件将语音转换为文本

使用以下示例从音频文件运行语音识别。 将变量 `subscription` 和 `region` 替换为你的语音密钥和位置/区域。 请参阅[查找密钥和位置/区域](../../../overview.md#find-keys-and-locationregion)页面，查找密钥位置/区域对。 此外，将变量 `file` 替换为 .wav 文件的路径。 运行脚本将从文件识别语音，并输出文本结果。

```go
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"
    file := "path/to/file.wav"

    audioConfig, err := audio.NewAudioConfigFromWavFileInput(file)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Started (ID=", event.SessionID, ")")
    })
    speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Stopped (ID=", event.SessionID, ")")
    })

    task := speechRecognizer.RecognizeOnceAsync()
    var outcome speech.SpeechRecognitionOutcome
    select {
    case outcome = <-task:
    case <-time.After(5 * time.Second):
        fmt.Println("Timed out")
        return
    }
    defer outcome.Close()
    if outcome.Error != nil {
        fmt.Println("Got an error: ", outcome.Error)
    }
    fmt.Println("Got a recognition!")
    fmt.Println(outcome.Result.Text)
}
```

运行以下命令，以创建一个 go.mod 文件并使其关联到 GitHub 上托管的组件。

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

现在生成并运行代码。

```cmd
go build
go run quickstart
```

有关 [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechConfig) 和 [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechRecognizer) 类的详细信息，请参阅参考文档。
