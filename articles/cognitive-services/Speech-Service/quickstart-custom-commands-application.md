---
title: 快速入门：使用自定义命令创建语音助理
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用 Speech Studio 创建并测试一个基本的自定义命令应用程序。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: e046f8cbf6fa0418244f20e9a0c6f75f6da34136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434621"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>使用自定义命令创建语音助手

在本快速入门中，你将使用 Speech Studio 创建并测试一个基本的自定义命令应用程序。 还可以从 Windows 客户端应用访问此应用程序。

## <a name="region-availability"></a>区域可用性
目前，自定义命令支持以下区域中创建的语音订阅：
* 美国西部
* 美国西部 2
* 美国东部
* 美国东部 2
* 美国中西部
* 北欧
* 西欧
* 东亚
* 东南亚
* 印度中部

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">在支持自定义命令的区域中创建 Azure 语音资源。</a> 有关受支持区域的列表，请参阅上面的“区域可用性”部分。
> * 下载 [Smart Room Lite](https://aka.ms/speech/cc-quickstart) 示例 JSON 文件。
> * 下载最新版本的 [Windows 语音助理客户端](https://aka.ms/speech/va-samples-wvac)。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>转到适用于自定义命令的 Speech Studio

1. 在 Web 浏览器中转到 [Speech Studio](https://speech.microsoft.com/)。
1. 输入登录到门户所需的凭据。

   默认视图为你的语音订阅列表。
   > [!NOTE]
   > 如果看不到“选择订阅”页，可以通过在顶部栏上的设置菜单中选择“语音资源”来导航到该页。

1. 选择你的语音订阅，然后选择“转到 Studio”。
1. 选择“自定义命令”。

   默认视图为你在所选订阅下拥有的自定义命令应用程序列表。

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>将现有应用程序导入为新的自定义命令项目

1. 选择“新建项目”以创建项目。

1. 在“名称”框中，输入 `Smart-Room-Lite`（或你选择的其他内容）作为项目名称。
1. 在“语言”列表中选择“英语(美国)”。 
1. 选择“浏览文件”，然后在浏览窗口中选择“SmartRoomLite.json”文件。 

    > [!div class="mx-imgBorder"]
    > ![创建项目](media/custom-commands/import-project.png)

1.  在“LUIS 创作资源”列表中选择一个创作资源。 如果没有有效的创作资源，请选择“创建新的 LUIS 创作资源”来创建一个资源。

    > [!div class="mx-imgBorder"]
    > ![创建资源](media/custom-commands/create-new-luis-resource.png)
    
    
    1. 在“资源名称”框中输入资源的名称。
    1. 在“资源组”列表中选择一个资源组。
    1. 在“位置”列表中选择一个位置。
    1. 在“定价层”列表中选择一个层。
    
    
    > [!NOTE]
    > 可以通过在“资源组”字段中输入所需的资源组名称来创建资源组。 选择“创建”后，即会创建资源组。


1. 接下来，选择“创建”以创建项目。
1. 创建项目后，选择你的项目。
此时应会看到新的自定义命令应用程序的概述。

## <a name="try-out-some-voice-commands"></a>尝试运行一些语音命令
1. 在右窗格的顶部选择“训练”。
1. 训练完成后，选择“测试”并尝试输入以下言语：
    - 打开电视
    - 将温度设为 80 度
    - 关机
    - 电视
    - 将闹钟设为下午 5 点

## <a name="integrate-custom-commands-application-in-an-assistant"></a>在助理中集成自定义命令应用程序
需要先发布此应用程序，然后才能从 Speech Studio 外部访问它。 要发布应用程序，需要配置预测 LUIS 资源。  

### <a name="update-prediction-luis-resource"></a>更新预测 LUIS 资源


1. 在左窗格中选择“设置”，然后在中间窗格中选择“LUIS 资源”。 
1. 选择一个预测资源，或选择“创建新资源”以创建一个预测资源。
1. 选择“保存”。
    
    > [!div class="mx-imgBorder"]
    > ![设置 LUIS 资源](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> 由于创作资源每月仅支持 1,000 个预测终结点请求，因此在发布自定义命令应用程序之前必须设置 LUIS 预测资源。

### <a name="publish-the-application"></a>发布应用程序

在右窗格的顶部选择“发布”。 发布完成后，会显示一个新窗口。 记下该窗口中的“应用程序 ID”和“语音资源密钥”值。  需要提供这两个值才能从 Speech Studio 外部访问该应用程序。

或者，也可以选择“设置” > “常规”部分来获取这些值。 

### <a name="access-application-from-client"></a>从客户端访问应用程序

根据本文的范围，我们将使用在满足先决条件部分所述要求时下载的 Windows 语音助理客户端。 解压缩文件夹。
1. 启动 **VoiceAssistantClient.exe**。
1. 创建新的发布配置文件，输入“连接配置文件”的值。 在“常规设置”部分，输入“订阅密钥”（与发布应用程序时所保存的“语音资源密钥”值相同）、“订阅密钥区域”和“自定义命令应用 ID”值。    
    > [!div class="mx-imgBorder"]
    > ![突出显示用于创建 WVAC 配置文件的“常规设置”部分的屏幕截图。](media/custom-commands/create-profile.png)
1. 选择“保存并应用配置文件”。
1. 现在，尝试通过语音/文本提供以下输入
    > [!div class="mx-imgBorder"]
    > ![WVAC - 创建配置文件](media/custom-commands/conversation.png)


> [!TIP]
> 可以单击“活动日志”中的条目来检查从自定义命令服务发送的原始响应。

## <a name="next-steps"></a>后续步骤

在本文中，你使用了一个现有的应用程序。 接下来，请在[操作方法部分](./how-to-develop-custom-commands-application.md)学习如何从头开始设计、开发、调试、测试和集成自定义命令应用程序。