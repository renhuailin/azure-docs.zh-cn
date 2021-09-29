---
title: Azure 通信服务中的短信概念
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务短信概念。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: c1759ba3bc4f9f516485f42f03d040bec5b4c78a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636052"
---
# <a name="sms-concepts"></a>短信概念

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure 通信服务允许你使用通信服务短信 SDK 发送和接收短信。 这些 SDK 可用于支持客户服务方案、约会提醒、双重身份验证和其他实时通信需求。 使用通信服务短信，可以在公开传送能力和响应指标的同时可靠地发送消息。

Azure 通信服务短信 SDK 的主要功能包括：

-  简单设置体验，用于将短信功能添加到应用程序。
- 为美国 A2P（应用程序到个人）用例提供通过免费电话号码实现的高速消息支持。
- 支持“批量消息传递”，允许一次向多个收件人发送消息。
- 双向会话功能，支持客户支持、警报和约会提醒等方案。
- 可靠交付，同时提供从应用程序发送的消息的实时传送报告。
- 分析功能，可跟踪短信使用模式。
- “选择退出”处理支持，自动检测和执行免费号码的选择退出操作。 美国运营商强制实施美国免费号码的选择退出操作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始发送短信](../../quickstarts/telephony-sms/send.md)

你可能会对下列文档感兴趣：

- 自行熟悉[短信 SDK](../telephony-sms/sdk-features.md)
- 获取支持短信的[电话号码](../../quickstarts/telephony-sms/get-phone-number.md)
- [Azure 通信服务中的电话号码类型](../telephony-sms/plan-solution.md)
