---
title: Azure 通信服务的聊天 SDK 概述
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务聊天 SDK。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 36b528d7899b2b5cda7c05752a7cbb42dec6adbc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459289"
---
# <a name="chat-sdk-overview"></a>聊天 SDK 概述 

Azure 通信服务聊天 SDK 可用于向应用程序添加丰富的实时聊天。
    
## <a name="chat-sdk-capabilities"></a>聊天 SDK 功能    

下表列出了通信服务聊天 SDK 中当前可用的一组功能。  

| 功能组 | 功能 | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| 核心功能 | 创建 2 个或更多个用户之间的聊天会话                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | 更新聊天会话的主题                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | 在聊天会话中添加或删除参与者                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | 选择是否要与正在添加的参与者共享聊天消息历史记录                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | 获取聊天会话中参与者的列表                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | 删除聊天会话                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | 对于给定通信用户，获取该用户所属聊天会话的列表                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 获取特定聊天会话的信息                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | 在聊天会话中发送和接收消息                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 更新已发送消息的内容                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | 删除以前发送的消息                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | 读取聊天中已被其他参与者阅读的消息的收件人                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | 当参与者在聊天会话中积极键入消息时获得通知                                         | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
|                   | 获取聊天会话中的所有消息                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | 将 Unicode 表情符号随附消息内容一起发送                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | 向聊天消息添加元数据                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | 将显示名称添加到键入指示器通知                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|实时通知（由专用信号包**启用）|  聊天客户端可以通过订阅获取聊天会话中的传入消息和其他操作的实时更新。 若要查看实时通知支持的更新的列表，请参阅[聊天概念](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| 与 Azure 事件网格的集成             | 使用 Azure 事件网格中可用的聊天事件来插入自定义通知服务，或将该事件发布到 Webhook 以执行业务逻辑（例如，在聊天结束后更新 CRM 记录）   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| 报告 </br>（此信息可在 Azure 门户上“通信服务”资源的“监视”选项卡下找到）      | 通过监视 Azure 指标资源管理器中已发布的指标并设置警报以检测异常，了解聊天应用中的 API 流量     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | 通过启用资源的诊断日志记录来监视和调试通信服务解决方案    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


专用信号包通过使用 Web 套接字实现。 如果不支持 Web 套接字，它将回退到长轮询。  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>OS 和浏览器提供的 JavaScript 聊天 SDK 支持    

下表显示了当前可用的一组受支持的浏览器和版本。
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **聊天 SDK** | Firefox *、Chrome*、新版 Microsoft Edge | Firefox *、Chrome*、Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*请注意，除前两个版本外，还支持最新版本。<br/>   

## <a name="next-steps"></a>后续步骤   

> [!div class="nextstepaction"] 
> [聊天入门](../../quickstarts/chat/get-started.md)    

你可能会对下列文档感兴趣：  
- 熟悉[聊天的概念](../chat/concepts.md)
- 了解如何对聊天进行[定价](../pricing.md#chat)
