---
title: Azure 通信服务的短信 SDK 概述
titleSuffix: An Azure Communication Services concept document
description: 概述了短信 SDK 及其产品/服务。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5e30f7741352945fb634b2f8066a47f2c47c879b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258132"
---
# <a name="sms-sdk-overview"></a>短信 SDK 概述

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure 通信服务短信 SDK 可用于向应用程序添加短信。

## <a name="sms-sdk-capabilities"></a>短信 SDK 功能

下表列出了我们的 SDK 目前提供的一组功能。

| 功能组 | 功能                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| 核心功能 | 发送和接收短信                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 为发送的消息启用传送报告                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 所有字符集（语言/unicode 支持）                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 支持长消息（最多 2048 个字节）                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 长消息的自动串联                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 一次向多个收件人发送消息                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 支持幂等性                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 消息的自定义标记。                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| 事件            | 使用事件网格配置 Webhook 以接收入站消息和传送报告 | ✔️   | ✔️    | ✔️    | ✔️      |
| 电话号码      | 免费号码                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN 呼叫      | 向启用短信的免费电话号码添加 PSTN 呼叫功能                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始发送短信](../../quickstarts/telephony-sms/send.md)

你可能会对下列文档感兴趣：

- 熟悉常规[短信概念](../telephony-sms/concepts.md)
- 获取支持短信的[电话号码](../../quickstarts/telephony-sms/get-phone-number.md)
- [Azure 通信服务中的电话号码类型](../telephony-sms/plan-solution.md)
