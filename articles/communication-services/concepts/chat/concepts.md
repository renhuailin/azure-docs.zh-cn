---
title: Azure 通信服务中的聊天概念
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务聊天概念。
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: chat
ms.openlocfilehash: 54a04db65c94adb1200000d027cdd9177c228297
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672127"
---
# <a name="chat-concepts"></a>聊天概念 

Azure 通信服务聊天 SDK 可用于向应用程序添加实时文本聊天。 此页汇总了重要的聊天概念和功能。    

若要详细了解特定 SDK 语言和功能，请参阅[通信服务聊天 SDK 概述](./sdk-features.md)。  

## <a name="chat-overview"></a>聊天概述    

聊天对话在 **聊天线程** 中进行。 聊天线程具有以下属性：

- 聊天线程由其 `ChatThreadId` 唯一标识。 
- 可以有一个或多个用户（作为参与者）向聊天线程发送消息。 
- 用户可以是一个或多个聊天线程的一部分。 
- 只有线程参与者才有权访问给定的聊天线程，并且只有这些参与者才能执行聊天线程操作。 这些操作包括发送和接收消息、添加参与者和删除参与者。 
- 用户将自动添加为他们所创建的任何聊天线程的参与者。

### <a name="user-access"></a>用户访问权限
通常，线程创建者和参与者对线程拥有相同级别的访问权限，他们可以执行 SDK 中可用的所有相关操作（包括删除线程）。 参与者对其他参与者发送的消息不拥有写入访问权限，这意味着，只有消息发送者才能更新或删除他们自己发送的消息。 如果另一参与者尝试执行此操作，将会收到错误。 

如果你想要限制一组用户对聊天功能的访问权限，可将访问权限作为受信任服务的一部分进行配置。 受信任的服务是协调聊天参与者身份验证和授权的服务。 下面将更详细地探讨这一点。  

### <a name="chat-data"></a>聊天数据 
通信服务将存储聊天历史记录，直到这些数据被显式删除。 聊天线程参与者可以使用 `ListMessages` 查看特定线程的消息历史记录。 从聊天线程中删除的用户可以查看以前的消息历史记录，但无法发送或接收作为该聊天线程一部分的新消息。 没有任何参与者的完全空闲线程在 30 天后会自动删除。 若要详细了解通信服务存储的数据，请参阅有关[隐私](../privacy.md)的文档。  

### <a name="service-limits"></a>服务限制  
- 聊天线程中允许的最大参与者数量为 250。   
- 允许的最大消息大小大约为 28 KB。  
- 对于具有 20 个以上参与者的聊天线程，不支持已读回执和键入指示器功能。    

## <a name="chat-architecture"></a>聊天体系结构    

聊天体系结构有两个核心部分：1) 可信服务和 2) 客户端应用程序。    

:::image type="content" source="../../media/chat-architecture.png" alt-text="显示通信服务聊天体系结构的关系图。"::: 

 - **受信任服务：** 若要正确管理聊天会话，需要一种服务，可帮助你使用资源连接字符串连接到通信服务。 此服务负责创建聊天线程、添加和删除参与者，并为用户颁发访问令牌。 有关访问令牌的详细信息可以在[访问令牌](../../quickstarts/access-tokens.md)快速入门中找到。  
 - **客户端应用：** 客户端应用程序连接到受信任的服务，并接收由用户用来直接连接到通信服务的访问令牌。 在受信任的服务创建聊天线程并将用户添加为参与者后，这些用户可以使用客户端应用连接到聊天线程并发送消息。 在客户端应用中使用实时通知功能（下面将予以介绍）可以订阅来自其他参与者的消息和线程更新。
    
        
## <a name="message-types"></a>消息类型    

作为消息历史记录的一部分，“聊天”将共享用户生成的消息以及系统生成的消息。 系统消息是更新聊天线程时生成的，可帮助确定何时添加或删除了参与者，或者何时更新了聊天线程主题。 对聊天线程调用 `List Messages` 或 `Get Messages` 时，结果将包含按时间顺序排序的这两种类型的消息。

对于用户生成的消息，可以在将消息发送到聊天线程时在 `SendMessageOptions` 中设置消息类型。 如果未提供任何值，则通信服务将默认使用 `text` 类型。 发送 HTML 时，设置此值非常重要。 如果指定了 `html`，通信服务将净化内容，以确保在客户端设备上安全呈现内容。
 - `text`：用户在聊天线程过程中撰写和发送的纯文本消息。 
 - `html`：用户在聊天线程中撰写和发送的、使用 HTML 的带格式消息。 

系统消息类型： 
 - `participantAdded`：指示一个或多个参与者已添加到聊天会话的系统消息。
 - `participantRemoved`：指示已从聊天会话中删除参与者的系统消息。
 - `topicUpdated`：指示会话主题已更新的系统消息。

## <a name="real-time-notifications"></a>实时通知  

某些 SDK（例如 JavaScript 聊天 SDK）支持实时通知。 此功能可让客户端在通信服务中侦听聊天线程的实时更新和传入消息，而不必轮询 API。 客户端应用可订阅以下事件：
 - `chatMessageReceived` - 当参与者已将新消息发送到聊天线程时。
 - `chatMessageEdited` - 在聊天线程中编辑消息时。 
 - `chatMessageDeleted` - 在聊天线程中删除消息时。   
 - `typingIndicatorReceived` - 当另一参与者向聊天线程发送键入指示器时。    
 - `readReceiptReceived` - 当另一参与者发送其已读消息的阅读回执时。  
 - `chatThreadCreated` - 当通信用户创建了聊天线程时。    
 - `chatThreadDeleted` - 当通信用户删除了聊天线程时。    
 - `chatThreadPropertiesUpdated` - 当更新了聊天线程属性时；目前仅支持更新线程的主题。 
 - `participantsAdded` - 当已将某个用户添加为聊天线程参与者时。     
 - `participantsRemoved` - 从聊天线程中删除现有参与者时。

实时通知可用于为用户提供实时聊天体验。 为了针对在用户离开时未看到的消息发送推送通知，通信服务与 Azure 事件网格集成，以发布聊天相关事件（POST 操作），这些事件可插入到自定义应用通知服务中。 有关更多详细信息，请参阅[服务器事件](../../../event-grid/event-schema-communication-services.md?bc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fbread%2ftoc.json&toc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fcommunication-services%2ftoc.json)。


## <a name="build-intelligent-ai-powered-chat-experiences"></a>构建 AI 支持的智能聊天体验   

可将 [Azure 认知 API](../../../cognitive-services/index.yml) 与聊天 SDK 配合使用来构建用例，例如：

- 使用户可以通过不同的语言互相聊天。  
- 通过检测来自客户的传入消息的负面情绪，帮助支持代理设置票证优先级。 
- 分析传入消息以进行密钥检测和实体识别，并基于消息内容在应用中向用户提示相关信息。

实现此目标的一种方法是让受信任服务充当聊天线程的参与者。 假设要启用语言翻译。 此服务负责侦听其他参与者 [1] 所交换的消息，调用认知 API 将内容翻译为所需语言 [2,3]，并在聊天线程中将翻译后的结果作为消息进行发送 [4]。

这样，消息历史记录将同时包含原始消息和已翻译消息。 在客户端应用程序中，可以添加逻辑以显示原始消息或已翻译消息。 请参阅[本快速入门](../../../cognitive-services/translator/quickstart-translator.md)以了解如何使用认知 API 将文本翻译为不同的语言。 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="显示与通信服务交互的认知服务的关系图。"::: 

## <a name="next-steps"></a>后续步骤   

> [!div class="nextstepaction"] 
> [聊天入门](../../quickstarts/chat/get-started.md)    

你可能会对下列文档感兴趣：  
- 自行熟悉[聊天 SDK](sdk-features.md)
