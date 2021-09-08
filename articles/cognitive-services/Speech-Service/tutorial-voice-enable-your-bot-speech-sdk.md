---
title: 教程：使用语音 SDK 为机器人赋予语音能力 - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将使用 Microsoft Bot Framework 创建一个复述机器人，将其部署到 Azure，再将其注册到 Bot Framework Direct Line Speech 通道。 然后配置一个适用于 Windows 的示例客户端应用，以便对机器人讲话并收听机器人做出的响应。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: c29477a7e9f5ed7ea9f26d4169b092ba8307e30a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539562"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>教程：使用语音 SDK 为机器人赋予语音能力

可以使用语音服务来为聊天机器人赋予语音能力。

在本教程中，你将创建一个可重复你所讲内容的机器人。
你将使用 Microsoft Bot Framework 创建机器人，将其部署到 Azure，再将其注册到 Bot Framework Direct Line Speech 通道。
然后，配置一个适用于 Windows 的示例客户端应用，以便对机器人讲话并收听机器人做出的响应。

本教程适用于对 Azure、Bot Framework 机器人、Direct Line Speech 或语音 SDK 不太熟悉，并想要使用有限代码快速生成可正常工作的系统的开发人员。 读者不需要相关经验，也不需要熟知这些服务。

在本教程中创建具有语音能力的聊天机器人的步骤如下：

1. 将示例客户端应用程序配置为连接到 Direct Line Speech 通道和复述机器人。
1. 当用户按下相应按钮时，将从麦克风流式传输语音音频。 （或者，在使用了自定义关键字时，则会连续录制音频。）
1. 如果使用了自定义关键字，关键字检测将在本地设备上进行，这会限制发往云中的音频流。
1. 示例客户端应用程序使用语音 SDK 连接到 Direct Line Speech 通道并流式传输音频。
1. （可选）更高准确度的关键字验证在服务中进行。
1. 音频将传递到语音识别服务并听录为文本。
1. 识别的文本作为 Bot Framework 活动传递到复述机器人。
1. 响应文本由文本转语音 (TTS) 服务转换为音频，并流式传输回到客户端应用程序进行播放。

<!-- svg src in User Story 1754106 -->
![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "语音通道流")

> [!NOTE]
> 本教程中的步骤无需使用付费服务。 Azure 新用户可以使用 Azure 免费试用订阅及语音服务免费层的额度来完成本教程。

本教程的内容：
> [!div class="checklist"]
> * 创建新的 Azure 资源
> * 生成、测试复述机器人示例并将其部署到 Azure 应用服务
> * 将机器人注册到 Direct Line Speech 通道
> * 生成并运行 Windows 语音助理客户端以便与复述机器人交互
> * 添加自定义关键字激活
> * 了解如何更改已识别语音和讲话语音的语言

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要做好以下准备：

- 一台 Windows 10 电脑以及正常工作的麦克风和扬声器（或耳机）
- 装有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更高版本
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) 或更高版本
- 一个 Azure 帐户。 [免费注册](https://azure.microsoft.com/free/cognitive-services/)
- 一个 [GitHub](https://github.com/) 帐户
- [用于 Windows 的 Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>创建资源组

在本教程中创建的客户端应用将使用少量的 Azure 服务。 为了减少机器人所做响应的往返时间，请确保这些服务位于同一 Azure 区域。 在本部分，你将在“美国西部”区域创建一个资源组。 在为 Bot Framework、Direct Line Speech 通道和语音服务创建各个资源时，将使用此资源组。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">创建资源组</a>
1. 系统会提示你提供一些信息：
   * 将“订阅”设置为“免费试用版”（也可以使用现有的订阅）。 
   * 输入 **资源组** 的名称。 建议输入 **SpeechEchoBotTutorial-ResourceGroup**。
   * 在“区域”下拉列表中选择“美国西部”。 
1. 单击“审阅并创建”。 此时应会出现一个显示“已通过验证”的横幅。
1. 单击“创建”。 创建资源组可能需要几分钟时间。
1. 最好是将此资源组固定到仪表板以便于访问，对于稍后在本教程中创建的资源，也是如此。 如果要固定此资源组，请单击资源组名称右侧的图钉图标。

### <a name="choosing-an-azure-region"></a>选择 Azure 区域

如果要在本教程中使用其他区域，以下因素可能会限制你的选择：

* 确保使用[支持的 Azure 区域](regions.md#voice-assistants)。
* Direct Line Speech 通道使用文本转语音服务，该服务提供标准语音和神经语音。 这些 [Azure 区域](regions.md#neural-and-standard-voices)皆提供神经和标准语音。

有关区域的详细信息，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。

## <a name="create-resources"></a>创建资源

在支持的区域中创建资源组后，下一步是为要在本教程中使用的每个服务创建各个资源。

### <a name="create-a-speech-service-resource"></a>创建语音服务资源

按照以下说明创建一个语音资源：

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">创建语音服务资源</a>
4. 系统会提示你提供一些信息：
   * 为资源 **命名**。 建议输入 **SpeechEchoBotTutorial-Speech**
   * 对于“订阅”，请确保选择“免费试用版”。 
   * 对于“位置”，请选择“美国西部”。 
   * 对于“定价层”，请选择“F0”。  它是免费层。
   * 对于“资源组”，请选择“SpeechEchoBotTutorial-ResourceGroup”。 
5. 输入全部所需信息后，单击“创建”。 创建资源可能需要几分钟时间。
6. 稍后在本教程中，需要用到此服务的订阅密钥。 随时可以从资源的“概述”（“管理密钥”）或“密钥”访问这些密钥。 

此时，请检查资源组 (**SpeechEchoBotTutorial-ResourceGroup**) 是否包含语音资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | 认知服务 | 美国西部 |

### <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

下一步是创建应用服务计划。 应用服务计划为要运行的 Web 应用定义一组计算资源。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">创建 Azure 应用服务计划</a>
4. 系统会提示你提供一些信息：
   * 将“订阅”设置为“免费试用版”（也可以使用现有的订阅）。 
   * 对于“资源组”，请选择“SpeechEchoBotTutorial-ResourceGroup”。 
   * 为资源 **命名**。 建议输入 **SpeechEchoBotTutorial-AppServicePlan**
   * 对于“操作系统”，请选择“Windows” 。
   * 对于“区域”，请选择“美国西部”。 
   * 对于“定价层”，请确保选择“标准 S1”。  这应是默认值。 如果不是，请确保如前所述将“操作系统”设置为“Windows”。 
5. 单击“审阅并创建”。 此时应会出现一个显示“已通过验证”的横幅。
6. 单击“创建”。 创建资源组可能需要几分钟时间。

此时，请检查资源组 (**SpeechEchoBotTutorial-ResourceGroup**) 是否包含两个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-Speech | 认知服务 | 美国西部 |

## <a name="build-an-echo-bot"></a>生成复述机器人

创建一些资源后，接下来让我们来生成机器人。 我们将从复述机器人示例着手，顾名思义，它会复述你输入的文本作为响应。 不用担心，示例代码无需任何更改即可供使用。 它已配置为使用 Direct Line Speech 通道，稍后我们将机器人部署到 Azure 后会连接该通道。

> [!NOTE]
> [GitHub 上该示例的 README 文件](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)中提供了以下说明，以及有关复述机器人的其他信息。

### <a name="run-the-bot-sample-on-your-machine"></a>在计算机上运行机器人示例

1. 克隆示例存储库：

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. 启动 Visual Studio。
3. 在工具栏中选择“文件” > “打开” > “项目/解决方案”，然后打开复述机器人项目解决方案：  

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 加载项目后，按 <kbd>F5</kbd> 生成并运行该项目。
5. 浏览器将启动，出现如下所示的屏幕。
    > [!div class="mx-imgBorder"]
    > [![显示“EchoBot”页的屏幕截图，其中显示了“你的机器人已准备就绪!”](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "在 localhost 上运行的 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>使用 Bot Framework Emulator 测试机器人示例

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) 是一个桌面应用，可让机器人开发人员在本地（或通过隧道在远程）测试和调试其机器人。 Emulator 接受键入的文本作为输入（不是语音）。 机器人也用文本做出响应。 请遵循以下步骤，使用 Bot Framework Emulator 通过文本输入和文本输出测试本地运行的复述机器人。 将机器人部署到 Azure 后，我们将通过语音输入和语音输出对其进行测试。

1. 安装 [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 4.3.0 或更高版本
2. 启动 Bot Framework Emulator 并打开机器人：
   * “文件” -> “打开机器人”。 
3. 输入机器人的 URL。 例如：

   ```
   http://localhost:3978/api/messages
   ```
   按下“连接”。
4. 机器人应会发出“你好，欢迎!”问候 消息作为响应。 键入任意文本消息，并确认你收到了来自机器人的响应。
5. 与复述机器人实例通信时的信息交换方式如下：[![该屏幕截图显示 Bot Framework Emulator。](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework Emulator")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>将机器人部署到 Azure 应用服务

下一步是将复述机器人部署到 Azure。 可通过多种方法部署机器人，本教程重点介绍如何直接从 Visual Studio 发布。

> [!NOTE]
> 也可以使用 [Azure CLI](/azure/bot-service/bot-builder-deploy-az-cli) 和[部署模板](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)部署机器人。

> [!NOTE]
> 如果在执行以下步骤时未出现“发布...”，请使用 Visual Studio 安装程序添加“ASP.NET 和 Web 开发”工作负载。 

1. 在 Visual Studio 中，打开已配置为与 Direct Line Speech 通道配合使用的复述机器人：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 在“解决方案资源管理器”中，右键单击“EchoBot”项目并选择“发布...”  
1. 此时会打开标题名为“发布”的新窗口。
1. 选择“Azure”，单击“下一步”，选择“Azure 应用服务(Windows)”，单击“下一步”，然后单击绿色加号旁边的“创建新的 Azure 应用服务...”。    
1. 当“应用服务(Windows)”窗口出现时：
   * 单击“添加帐户”，然后使用 Azure 帐户凭据登录。 如果已登录，请从下拉列表中选择所需的帐户。
   * 对于“名称”，需要输入机器人的全局唯一名称。 此名称用于创建唯一的机器人 URL。 系统会填充默认值，包括日期和时间（例如“EchoBot20190805125647”）。 对于本教程，可以使用默认名称。
   * 对于“订阅”，请将其设置为“免费试用版” 
   * 对于“资源组”，请选择“SpeechEchoBotTutorial-ResourceGroup” 
   * 对于“托管计划”，请选择“SpeechEchoBotTutorial-AppServicePlan” 
1. 单击“创建”。 在最后一个向导屏幕上，单击“完成”。
1. 单击“发布”屏幕右侧的“发布”。 Visual Studio 会将该机器人部署到 Azure。
1. Visual Studio 的输出窗口中应会出现如下所示的成功消息：

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 默认浏览器应会打开并显示一个页面，其中指出“你的机器人已就绪!”。
1. 此时，请在 Azure 门户中检查资源组“SpeechEchoBotTutorial-ResourceGroup”，确认存在以下三个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| EchoBot20190805125647 | 应用服务 | 美国西部 |
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-Speech | 认知服务 | 美国西部 |

## <a name="enable-web-sockets"></a>启用 Web 套接字

需要进行少量的配置更改，使机器人能够使用 Web 套接字来与 Direct Line Speech 通道通信。 遵循以下步骤启用 Web 套接字：

1. 导航到 [Azure 门户](https://portal.azure.com)，单击你的应用服务。 该资源的名称应类似于 **EchoBot20190805125647**（你的唯一应用名称）。
2. 在左侧导航窗格中的“设置”下，单击“配置”。 
3. 选择“常规设置”选项卡。
4. 找到“Web 套接字”的切换开关，将其设置为“打开”。 
5. 单击“保存”。

> [!TIP]
> 可以使用 Azure 应用服务页顶部的控件来停止或重启该服务。 在进行故障排除时，这种做法可能很方便。

## <a name="create-a-channel-registration"></a>创建通道注册

创建用于托管机器人的 Azure 应用服务后，下一步是创建 **机器人通道注册**。 创建通道注册是将机器人注册到 Bot Framework 通道（包括 Direct Line Speech 通道）的先决条件。 若要详细了解机器人如何使用通道，请参阅[将机器人连接到通道](/azure/bot-service/bot-service-manage-channels)。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">创建 Azure 机器人通道注册</a>
2. 系统会提示你提供一些信息：
   * 对于“机器人句柄”，请输入 **SpeechEchoBotTutorial-BotRegistration-####** 并将 **####** 替换为所选的编号。 请注意，机器人句柄必须全局唯一。 如果输入机器人句柄后收到错误消息“请求的机器人 ID 不可用”，请选择其他编号。 以下示例使用了 8726
   * 对于“订阅”，请选择“免费试用版”。 
   * 对于“资源组”，请选择“SpeechEchoBotTutorial-ResourceGroup”。 
   * 对于“位置”，请选择“美国西部”。 
     * 对于“定价层”，请选择“F0”。 
     * 对于“消息传送终结点”，请输入 Web 应用的 URL 并在末尾追加 `/api/messages` 路径。 例如：如果全局唯一的应用名称为 **EchoBot20190805125647**，则消息传送终结点将是：`https://EchoBot20190805125647.azurewebsites.net/api/messages/`。
     * 对于“Application Insights”，可将其设置为“关闭”。  有关详细信息，请参阅[机器人分析](/azure/bot-service/bot-service-manage-analytics)。
     * 忽略“自动创建应用 ID 和密码”。
5. 在“机器人通道注册”边栏选项卡的底部单击“创建”。 

此时，请在 Azure 门户中检查资源组“SpeechEchoBotTutorial-ResourceGroup”。 它现在应显示至少四个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| EchoBot20190805125647 | 应用服务 | 美国西部 |
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-BotRegistration-8726 | 机器人通道注册 | 全球 |
| SpeechEchoBotTutorial-Speech | 认知服务 | 美国西部 |

> [!IMPORTANT]
> 尽管选择的是“美国西部”，但机器人通道注册资源却显示“全球”区域。 这是正常情况。

## <a name="optional-test-in-web-chat"></a>可选：网络聊天测试

“Azure 机器人通道注册”页的“机器人管理”下有一个“网络聊天测试”选项。  默认情况下，此功能不适用于你的机器人，因为网络聊天需要对机器人进行身份验证。 若要使用文本输入来测试已部署的机器人，请遵循以下步骤。 请注意，这些步骤是可选的，不执行这些步骤也能继续完成本教程的后续步骤。 

1. 在 [Azure 门户](https://portal.azure.com)中找到并打开“EchoBotTutorial-BotRegistration-####”资源
1. 从“机器人管理”导航栏中选择“设置”。  复制“Microsoft 应用 ID”下的值
1. 打开 Visual Studio EchoBot 解决方案。 在解决方案资源管理器中，找到并双击“appsettings.json”
1. 将 JSON 文件中“MicrosoftAppId”旁边的空字符串替换为复制的 ID 值
1. 返回 Azure 门户，在“机器人管理”导航栏中选择“设置”，然后单击“Microsoft 应用 ID”旁边的“(管理)”   
1. 单击“新建客户端机密”。 添加说明（例如“网络聊天”）并单击“添加”。 复制新机密
1. 将 JSON 文件中“MicrosoftAppPassword”旁边的空字符串替换为复制的机密值
1. 保存 JSON 文件。 结果应如下所示：
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. 重新发布应用（在 Visual Studio 解决方案资源管理器中右键单击“EchoBot”项目，选择“发布...”，然后单击“发布”按钮）  
10. 现已准备好在网络聊天中测试机器人！

## <a name="register-the-direct-line-speech-channel"></a>注册 Direct Line Speech 通道

现在是将机器人注册到 Direct Line Speech 通道的时候了。 此通道在机器人与使用语音 SDK 编译的客户端应用之间创建连接。

1. 在 [Azure 门户](https://portal.azure.com)中找到并打开“SpeechEchoBotTutorial-BotRegistration-####”资源。
1. 从“机器人管理”导航栏中选择“通道”。 
   * 在“更多通道”下，单击“Direct Line Speech”。 
   * 查看标题为“配置 Direct Line Speech”的页面上的文本，然后展开“认知服务帐户”下拉菜单。 
   * 从菜单中选择前面创建的语音资源（例如“SpeechEchoBotTutorial-Speech”），将机器人关联到语音订阅密钥。
   * 忽略余下的可选字段。
   * 单击“保存”。

1. 在“机器人管理”导航栏中单击“设置”。 
   * 选中标有“启用流式处理终结点”的框。 只有这样，才能在机器人与 Direct Line Speech 通道之间创建基于 Web 套接字的通信协议。
   * 单击“保存”。

> [!TIP]
> 若要了解详细信息，请参阅[将机器人连接到 Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech)。 此页包含了更多信息和已知问题。

## <a name="run-the-windows-voice-assistant-client"></a>运行 Windows 语音助理客户端

在此步骤中，你将运行 Windows 语音助理客户端。 该客户端以 C# 编写的一个 Windows Presentation Foundation (WPF) 应用，它使用[语音 SDK](./speech-sdk.md) 通过 Direct Line Speech 通道来管理与机器人的通信。 在编写自定义客户端应用之前，请使用语音助理客户端来与机器人交互并对其进行测试。 该客户端是开源的，因此你可以下载并运行可执行文件，或者自行生成。

Windows 语音助理客户端提供一个简单 UI，可让你配置与机器人的连接、查看文本对话、以 JSON 格式查看 Bot Framework 活动，以及显示自适应卡片。 它还支持使用自定义关键字。 你将使用此客户端来与机器人对话，并接收语音响应。

> [!NOTE]
> 此时，请确认麦克风和扬声器已启用并可正常工作。

1. 导航到 [Windows 语音助理客户端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)的 GitHub 存储库。
1. 按照其中的说明执行以下操作
   * 下载要运行的预生成可执行文件 ZIP 包，或
   * 通过克隆存储库并生成项目来自行生成可执行文件。

1. 按照 GitHub 存储库中的说明，启动 `VoiceAssistantClient.exe` 客户端应用程序并将其配置为连接到你的机器人。
1. 单击“重新连接”，确保看到消息“已启动新对话 - 请键入内容或按下麦克风按钮”。 
1. 让我们进行测试。单击麦克风按钮，并讲出几句英语。 当你讲话时，识别到的文本将会显示。 在你说完后，机器人将以自己的语音给出回复，它会说“复述”，然后讲出识别到的话语。
1. 也可以使用文本来与机器人交谈。 只需在底部栏中键入文本即可。 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>排查 Windows 语音助理客户端中的错误

如果在主应用窗口中收到错误消息，请参考下表来确定并排查错误：

| 错误 | 你应该如何操作？ |
|-------|----------------------|
|错误(AuthenticationFailure): WebSocket 升级失败并出现身份验证错误(401)。 请检查订阅密钥(或授权令牌)和区域名称是否正确| 在应用的“设置”页中，确保正确输入了语音订阅密钥及其区域。<br>确保正确输入了语音密钥和密钥区域。 |
|错误(ConnectionFailure): 远程主机关闭了连接。 错误代码: 1011。 错误详细信息: 在发送消息之前无法连接到机器人 | 确保[已选中“启用流式处理终结点”](#register-the-direct-line-speech-channel)框并且/或者[已将“Web 套接字”切换为“打开”](#enable-web-sockets)。<br>确保 Azure 应用服务正在运行。 如果已运行，请尝试重启应用服务。|
|错误(ConnectionFailure): 远程主机关闭了连接。 错误代码: 1002。 错误详细信息: 当状态代码应为 '101' 时，服务器返回了状态代码 '503' | 确保[已选中“启用流式处理终结点”](#register-the-direct-line-speech-channel)框并且/或者[已将“Web 套接字”切换为“打开”](#enable-web-sockets)。<br>确保 Azure 应用服务正在运行。 如果已运行，请尝试重启应用服务。|
|错误(ConnectionFailure): 远程主机关闭了连接。 错误代码: 1011。 错误详细信息: 响应状态代码不是指示成功: 500 (InternalServerError)| 机器人在其输出活动的[讲话](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)字段中指定了神经语音，但与你的语音订阅密钥关联的 Azure 区域不支持神经语音。 请参阅[神经和标准语音](./regions.md#neural-and-standard-voices)。|

如果表格中的方法未能解决你的问题，请参阅[语音助理：常见问题解答](faq-voice-assistants.yml)。 如果在遵循本教程中的所有步骤后仍无法解决问题，请在[语音助理 GitHub 页](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues)中输入新问题。

#### <a name="a-note-on-connection-time-out"></a>有关连接超时的说明

如果你已连接到机器人，但在过去 5 分钟内未发生任何活动，则服务将自动关闭与客户端和机器人建立的 websocket 连接。 这是设计的结果。 底部栏中会出现一条消息：“活动连接已超时，但可按需重新连接”。 无需按下“重新连接”按钮，只需按下麦克风按钮并开始讲话、键入文本消息，或者讲出关键字（如果已启用）。 随后将自动重新建立连接。  
### <a name="view-bot-activities"></a>查看机器人活动

每个机器人都会发送和接收 **活动** 消息。 在 Windows 语音助理客户端的“活动日志”窗口中，你将看到带时间戳的日志，其中包含客户端从机器人收到的每个活动。 还可以查看客户端使用 [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) 方法发送到机器人的活动。 选择某个日志项时，将以 JSON 格式显示关联活动的详细信息。

下面是客户端收到的活动的示例 JSON：

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

若要详细了解 JSON 输出中返回的内容，请参阅[活动中的字段](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)。 对于本教程，你可以将重点放在[文本](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)和[讲话](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)字段上。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>查看对语音 SDK 发出的调用的客户端源代码

Windows 语音助理客户端使用 NuGet 包 [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)，其中包含语音 SDK。 使用 [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 文件中的 InitSpeechConnector() 方法可以非常方便地查看示例代码。该方法创建以下两个语音 SDK 对象：
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) - 用于配置设置（例如语音订阅密钥、密钥区域）
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) - 管理用于处理识别到的语音和机器人响应的通道连接和客户端订阅事件。

## <a name="add-custom-keyword-activation"></a>添加自定义关键字激活

语音 SDK 支持自定义关键字激活。 类似于在 Microsoft 助手中讲出“你好小娜”，你可以编写一个应用，以便持续收听所选的关键字。 请记住，关键字可以是单词，也可以是词组。

> [!NOTE]
> 术语“关键字”通常可与术语“唤醒词”换用，你可能已看到，Microsoft 文档中使用了这两种说法。 

关键字检测是在客户端应用中完成的。 如果使用关键字，则只有在检测到关键字时，才会将音频流式传输到 Direct Line Speech 通道。 Direct Line Speech 通道包含一个称作“关键字验证 (KWV)”的组件，该组件在云中执行更复杂的处理，以验证所选关键字是否位于音频流的开头。 如果关键字验证成功，则通道将与机器人通信。

遵循以下步骤创建关键字模型，将 Windows 语音助理客户端配置为使用此模型，最后在机器人上对其进行测试。

1. 按照这些说明[使用语音服务创建自定义关键字](./custom-keyword-basics.md)。
2. 解压缩在上一步骤中下载的模型文件。 该文件应已根据你的关键字命名。 查找的文件应该名为 `kws.table`。
3. 在 Windows 语音助理客户端中，找到“设置”菜单（查看右上角的齿轮图标）。 找到“模型文件路径”，输入步骤 2 中获取的 `kws.table` 文件的完整路径名。
4. 确保选中标有“已启用”的框。 在复选框的旁边应会看到以下消息：“下次连接时将会收听关键字”。 如果提供了错误的文件或无效路径，应会看到一条错误消息。
5. 输入语音 **订阅密钥**、**订阅密钥区域**，然后单击“确定”关闭“设置”菜单。 
6. 单击“重新连接”。 此时应会看到一条消息，指出：“已启动新对话 - 请键入内容、按下麦克风按钮，或者讲出关键字”。 应用现在会持续收听。
7. 讲出以关键字开头的任何短语。 例如：“{你的关键字}，现在是什么时间？”。 说出关键字后无需暂停。 完成后，将发生两种情况：
   * 你会看到讲话内容的听录版本
   * 不久之后，即可听到机器人的回答
8. 继续使用机器人支持的三种输入类型进行试验：
   * 在底部栏中键入的文本
   * 按下麦克风图标并讲话
   * 讲出以关键字开头的短语

### <a name="view-the-source-code-that-enables-keyword"></a>查看启用关键字的源代码

在 Windows 语音助理客户端源代码中，浏览以下文件以查看用于启用关键字检测的代码：

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) 包含对语音 SDK 方法 [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) 的调用，该方法用于通过磁盘上的本地文件实例化模型。
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 包含对语音 SDK 方法 [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) 的调用，该方法激活连续关键字检测。

## <a name="optional-change-the-language-and-bot-voice"></a>（可选）更改语言和机器人语音

创建的机器人将会收听英语讲话内容并以英语作答，使用默认的美式英语“文本转语音”语音。 但是，你并不局限于使用英语，也就是默认语音。 本部分介绍如何更改机器人收听和回答语言。 其中还介绍了如何为该语言选择不同的语音。

### <a name="change-the-language"></a>更改语言

可以从[语音转文本](language-support.md#speech-to-text)表格中提到的语言中选择任何一种。 在以下示例中，我们将语言更改为“德语”。

1. 打开 Windows 语音助理客户端应用，单击设置按钮（右上角的齿轮图标），然后在“语言”字段中输入 `de-de`（即[语音转文本](language-support.md#speech-to-text)表格中提到的“区域设置”值）。 这会设置要识别的讲话语言，并重写默认的 `en-us`。 这还会指示 Direct Line Speech 通道使用默认的德语语音作为机器人的回复语音。
2. 关闭设置页，单击“重新连接”按钮来与复述机器人建立新连接。
3. 单击麦克风按钮，并讲出一个德语短语。 你将看到已识别的文本，而复述机器人将以默认的德语语音给出回复。

### <a name="change-the-default-bot-voice"></a>更改默认的机器人语音

如果机器人以[语音合成标记语言](speech-synthesis-markup.md) (SSML) 而不是简单文本的形式指定了回复，则可以选择“文本转语音”语音并控制发音。 复述机器人不使用 SSML，但我们可以轻松修改代码来让它这样做。 在以下示例中，我们将 SSML 添加到复述机器人回复，以使用德语语音 Stefan Apollo（男性语音），而不是默认的女性语音。 参阅你的语言所支持的[标准语音](language-support.md#standard-voices)和[神经语音](language-support.md#neural-voices)列表。

1. 首先我们打开 `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`。
2. 找到以下两行：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 将其替换为：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. 在 Visual Studio 中生成解决方案并修复任何生成错误。

方法“MessageFactory.Text”中的第二个参数在机器人回复中设置[活动讲话字段](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)。 做出上述更改后，回复形式即已从简单文本替换为 SSML，以指定非默认的德语语音。

### <a name="redeploy-your-bot"></a>重新部署机器人

对机器人做出所需的更改后，下一步是将它重新发布到 Azure 应用服务并尝试使用它：

1. 在“解决方案资源管理器”窗口中，右键单击“EchoBot”项目并选择“发布”。 
2. 以前的部署配置已作为默认值加载。 只需单击“EchoBot20190805125647 - Web 部署”旁边的“发布”即可。 
3. “发布成功”消息将显示在 Visual Studio 输出窗口中，同时会启动一个网页，其中显示了消息“你的机器人已准备就绪!”。
4. 打开 Windows 语音助理客户端应用，单击设置按钮（右上角的齿轮图标），确保“语言”字段中仍包含 `de-de`。
5. 按照[运行 Windows 语音助理客户端](#run-the-windows-voice-assistant-client)中的说明重新连接到新部署的机器人，以新的语言讲话，然后将会听到机器人使用该语言以新的语音给出回复。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用本教程中部署的复述机器人，只需删除 Azure 资源组“SpeechEchoBotTutorial-ResourceGroup”，即可删除该机器人及其关联的所有 Azure 资源。

1. 在 [Azure 门户](https://portal.azure.com)上的“Azure 服务”导航栏中单击“资源组”。 
2. 找到名为“SpeechEchoBotTutorial-ResourceGroup”的资源组。 单击三点图标 (...)。
3. 选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用语音 SDK 生成自己的客户端应用](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>另请参阅

* 部署到[你附近的 Azure 区域](https://azure.microsoft.com/global-infrastructure/locations/)以改进机器人的响应时间
* 部署到[支持优质神经 TTS 语音的 Azure 区域](./regions.md#neural-and-standard-voices)
* 与 Direct Line Speech 通道相关的定价：
  * [机器人服务定价](https://azure.microsoft.com/pricing/details/bot-service/)
  * [语音服务](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 生成和部署自己的拥有语音能力的机器人：
  * 生成 [Bot Framework 机器人](https://dev.botframework.com/)。 将其注册到 [Direct Line Speech 通道](/azure/bot-service/bot-service-channel-connect-directlinespeech)并[根据语言自定义机器人](/azure/bot-service/directline-speech-bot)
  * 探索现有的 [Bot Framework 解决方案](https://microsoft.github.io/botframework-solutions/index)：生成[虚拟助理](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/)并[将其扩展到 Direct Line Speech](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
