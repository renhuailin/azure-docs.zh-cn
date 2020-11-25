---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026503"
---
此示例涉及使用 [Twilio](https://www.twilio.com/) 服务向移动电话发送短信。 Azure Functions 已通过 [Twilio 绑定](../articles/azure-functions/functions-bindings-twilio.md)提供对 Twilio 的支持，此示例使用了这一功能。

首先，需要一个 Twilio 帐户。 可以通过 https://www.twilio.com/try-twilio 免费创建一个帐户。 创建帐户以后，向函数应用添加以下三个 **应用设置**。

| 应用设置名称 | 值说明 |
| - | - |
| TwilioAccountSid  | Twilio 帐户的 SID |
| TwilioAuthToken   | Twilio 帐户的身份验证令牌 |
| TwilioPhoneNumber | 与 Twilio 帐户关联的电话号码。 此电话号码用于发送短信。 |