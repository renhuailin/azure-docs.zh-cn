---
title: Azure 通信服务聊天客户端库概述
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务聊天客户端库。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656101"
---
# <a name="chat-client-library-overview"></a>聊天客户端库概述  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Azure 通信服务聊天客户端库可用于向应用程序添加丰富的实时聊天。
    
## <a name="chat-client-library-capabilities"></a>聊天客户端库功能 

下表列出了通信服务聊天客户端库中当前可用的一组功能。  

| 功能组 | 功能 | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| 核心功能 | 在 2 个或更多用户（最多 250 个用户）之间创建聊天会话                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | 更新聊天会话的主题                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | 在聊天会话中添加或删除参与者                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | 选择是否要与正在添加的参与者共享聊天消息历史记录                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | 获取聊天会话中参与者的列表                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | 删除聊天会话                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | 对于给定通信用户，获取该用户所属聊天会话的列表                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 获取特定聊天会话的信息                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | 在聊天会话中发送和接收消息                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 编辑已发送的消息的内容                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | 删除消息                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | 读取聊天中已被其他参与者阅读的消息的收件人 <br/> 当聊天会话中有超过 20 名参与者时不可用    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | 当参与者在聊天会话中积极键入消息时获得通知 <br/> 当聊天会话中有超过 20 个成员时不可用      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | 获取聊天会话中的所有消息 <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | 将 Unicode 表情符号随附消息内容一起发送                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|实时信号（由专用信号包启用）|  订阅以获取聊天应用中的传入消息和其他操作的实时更新。 若要查看实时信号支持的更新的列表，请参阅[聊天概念](concepts.md#real-time-signaling)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| 事件网格支持             | 使用与 Azure 事件网格的集成，并将通信服务配置为基于聊天活动执行业务逻辑，或插入自定义推送通知服务   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| 监视        | 使用 Azure 门户中发出的 API 请求指标来构建仪表板、监视聊天应用的运行状况，以及设置警报来检测异常      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | 配置通信服务资源，以接收聊天操作日志进行监视和诊断          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


专用信号包通过使用 Web 套接字实现。 如果不支持 Web 套接字，它将回退到长轮询。  

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>OS 和浏览器对 JavaScript 聊天客户端库的支持 

下表显示了当前可用的一组受支持的浏览器和版本。
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **聊天客户端库** | Firefox *、Chrome*、新版 Microsoft Edge | Firefox *、Chrome*、Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*请注意，除前两个版本外，还支持最新版本。<br/>   

## <a name="next-steps"></a>后续步骤   

> [!div class="nextstepaction"] 
> [聊天入门](../../quickstarts/chat/get-started.md)    

你可能会对下列文档感兴趣：  
- 熟悉[聊天的概念](../chat/concepts.md)