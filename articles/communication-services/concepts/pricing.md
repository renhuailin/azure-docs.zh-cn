---
title: 呼叫（语音/视频）和聊天的定价方案
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务的定价模型。
author: nmurav
manager: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d2ea2c510aa9e6225de215da128670514f1dba3b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360435"
---
# <a name="pricing-scenarios"></a>定价方案

Azure 通信服务的价格通常基于即用即付模型。 以下示例中的价格用于说明目的，可能不会反映最新的 Azure 定价。

## <a name="voicevideo-calling-and-screen-sharing"></a>语音/视频呼叫和屏幕共享

Azure 通信服务允许向应用程序添加语音/视频呼叫和屏幕共享。 可以使用 JavaScript、Objective-C (Apple)、Java (Android) 或 .NET SDK 在应用程序中嵌入该体验。 参阅我们的[可用 SDK 完整列表](./sdk-options.md)。

### <a name="pricing"></a>定价

对于群呼，呼叫和屏幕共享按每个参与者每分钟 0.004 美元进行收费。 Azure 通信服务不收取数据传出费。 若要了解各种可能的呼叫流，请参阅[此页面](./call-flows.md)。

呼叫的每个参与者连接到呼叫的每一分钟都会计入费用。 无论用户是进行视频呼叫、语音呼叫还是屏幕共享，情况都是如此。

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-sdks"></a>定价示例：使用 JS 和 iOS SDK 发起音频/视频群呼

Alice 与其同事 Bob 和 Charlie 进行了一次群呼。 Alice 和 Bob 使用了 JS SDK，Charlie 使用了 iOS SDK。

- 该呼叫总共持续 60 分钟。
- Alice 和 Bob 参与了整个呼叫。 Alice 将其视频打开了 5 分钟，并将其屏幕共享了 23 分钟。 Bob 在整个呼叫期间（60 分钟）都打开了其视频，并将其屏幕共享了 12 分钟。
- Charlie 在 43 分钟之后离开了呼叫。 Charlie 在其参与的持续时间（43 分钟）内使用了音频和视频。

成本计算

- 2 个参与者 x 60 分钟 x 0.004 美元/参与者/分钟 = 0.48 美元 [视频和音频以相同费率进行收费]
- 1 个参与者 x 43 分钟 x 0.004 美元/参与者/分钟 = 0.172 美元 [视频和音频以相同费率进行收费]

群呼的总成本：0.48 美元 + 0.172 美元 = 0.652 美元


### <a name="pricing-example-outbound-call-from-app-using-js-sdk-to-a-pstn-number"></a>定价示例：在应用中使用 JS SDK 向 PSTN 号码发起出站呼叫

Alice 在应用中向 Bob 的美国电话号码（以 `+1-425` 开头）发起了 PSTN 呼叫。

- Alice 使用 JS SDK 生成了应用。
- 呼叫总共持续了 10 分钟。

成本计算

- VoIP 端有 1 名参与者 (Alice) 从应用向通信服务服务器发起呼叫 x 10 分钟 x 每个参与者端每分钟 $0.004 = $0.04
- PSTN 出站端有 1 名参与者 (Bob) 从通信服务服务器向美国电话号码发起呼叫 x 10 分钟 x 每个参与者端每分钟 0.013 美元 = 0.13 美元。

> [!Note]
> 在美国呼叫 `+1-425` 的混合费率为 0.013 美元。 有关详细信息，请参阅以下链接： https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)


此次呼叫的总费用：0.04 美元 + 0.13 美元 = 0.17 美元

### <a name="pricing-example-outbound-call-from-app-using-js-sdk-via-azure-communication-services-direct-routing"></a>定价示例：在应用中使用 JS SDK 通过 Azure 通信服务直接路由拨打呼出电话

Alice 在 Azure 通信服务应用中通过 Azure 通信服务直接路由向某个电话号码 (Bob) 拨打呼出电话。
- Alice 使用 JS SDK 生成了应用。
- 呼叫接入通过通信服务直接路由连接的会话边界控制器 (SBC)
- 呼叫总共持续了 10 分钟。 

成本计算

- VoIP 端有 1 名参与者 (Alice) 从应用向通信服务服务器发起呼叫 x 10 分钟 x 每个参与者端每分钟 $0.004 = $0.04
- 通信服务直接路由呼出端有 1 名参与者 (Bob) 从通信服务服务器向 SBC 发起呼叫 x 10 分钟 x 每个参与者端每分钟 0.004 美元 = 0.04 美元。

此次呼叫的总费用：0.04 美元 + 0.04 美元 = 0.08 美元

### <a name="pricing-example-group-audio-call-using-js-sdk-and-one-pstn-leg"></a>定价示例：使用 JS SDK 和一个 PSTN 端发起音频群呼

Alice 和 Bob 使用 VOIP 呼叫。 Bob 升级了对 Charlie 的呼叫方式，呼叫的是 Charlie 的 PSTN 号码（以 `+1-425` 开头的美国电话号码）。

- Alice 使用 JS SDK 生成了应用。 他们交谈了 10 分钟，然后呼叫 Charlie 的 PSTN 号码。
- 在 Bob 将呼叫方式升级为呼叫 Charlie 的 PSTN 号码后，他们三个又交谈了 10 分钟。

成本计算

- VoIP 端有 2 名参与者（Alice 和 Bob）从应用向通信服务服务器发起呼叫 x 20 分钟 x 每个参与者端每分钟 $0.004 = $0.16
- PSTN 出站端有 1 名参与方 (Charlie) 从通信服务服务器向美国电话号码发起呼叫 x 10 分钟 x 每个参与者端每分钟 $0.013 = $0.13

注意：在美国呼叫 `+1-425` 的混合费率为 $0.013。 有关详细信息，请参阅以下链接： https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**VoIP + 升级呼叫总成本**：$0.16 + $0.13 = $0.29


### <a name="pricing-example-a-user-of-the-communication-services-javascript-sdk-joins-a-scheduled-microsoft-teams-meeting"></a>定价示例：通信服务 JavaScript SDK 用户加入计划的 Microsoft Teams 会议

Alice 医生与她的患者 Bob 会面。 Alice 将从 Teams 桌面应用程序加入探访。 Bob 将收到一个通过医疗保健提供商网站加入的链接，该网站使用通信服务 JavaScript SDK 连接到会议。 Bob 将使用移动电话通过 Web 浏览器（iPhone 通过 Safari）进入会议。 虚拟探访期间可以聊天。

- 通话总时长为 30 分钟。
- 当 Bob 加入会议时，根据 Teams 策略，他被放置在 Teams 会议厅中。 一分钟后，Alice 让他进入会议。
- 在 Bob 被允许参加会议后，Alice 和 Bob 参与了整个通话。 Alice 在通话开始五分钟后打开她的视频，并共享屏幕 13 分钟。 Bob 在整个通话期间均打开了视频。
- Alice 发送了 5 条消息，Bob 回复了 3 条消息。


成本计算

- 1 个参与者 (Bob) 连接到 Teams 会议厅 x 1 分钟 x 每个参与者每分钟 0.004 美元（会议厅按常规会议费率收费）= 0.004 美元
- 1 个参与者 (Bob) x 29 分钟 x 0.004 美元/参与者/分钟 = 0.116 美元 [视频和音频以相同费率收费]
- 1 个参与者 (Alice) x 30 分钟 x 0.000 美元/参与者/分钟 = 0.0* 美元。
- 1 个参与方 (Bob) x 3 条聊天消息 x 0.0008 美元 = 0.0024 美元。
- 1 个参与者 (Alice) x 5 条聊天消息 x 0.000 美元 = 0.0* 美元。

*Alice 的参与费用由她的 Teams 许可证涵盖。 为了方便起见，Azure 发票将显示 Teams 用户与通信服务用户之间产生的分钟数和聊天消息数，但来自 Teams 客户端的这些分钟数和消息数不会产生费用。

**此次探访的总费用**：
- 用户使用通信服务 JavaScript SDK 加入：0.004 美元 + 0.116 美元 + 0.0024 美元 = 0.1224 美元
- 用户通过 Teams 桌面应用程序加入：0 美元（Teams 许可证涵盖）


## <a name="chat"></a>聊天

借助通信服务，可以利用在 2 个或更多用户之间发送和接收聊天消息的功能来强化应用程序。 聊天 SDK 适用于 JavaScript、.NET、Python 和 Java。 请参阅[此页了解 SDK](./sdk-options.md)

### <a name="price"></a>价格

发送每条聊天消息的费用为 0.0008 美元。

### <a name="pricing-example-chat-between-two-users"></a>定价示例：两个用户之间的聊天

Geeta 启动了与 Emily 的聊天线程以共享更新并发送 5 条消息。 聊天时长为 10 分钟。 Geeta 和 Emily 每人另外发了 15 条消息。

成本计算
- 发送的消息数 (5 + 15 + 15) x 0.0008 美元 = 0.028 美元

### <a name="pricing-example-group-chat-with-multiple-users"></a>定价示例：与多个用户进行的群聊

Charlie 启动了与好友 Casey 和 Jasmine 的聊天线程以规划休假。 他们聊了一段时间，其中 Charlie、Casey 和 Jasmine 分别发送了 20、30 和 18 条消息。 他们意识到其好友 Rose 也可能会对加入旅行感兴趣，因此将她添加到聊天线程中，并与她共享所有消息历史记录。

Rose 会看到消息并开始聊天。 与此同时，Casey 收到了一个电话，决定稍后再进行对话。 Charlie、Jasmine 和 Rose 决定了旅行日期，并分别发送了另外 30、25、35 条消息。

成本计算

- 发送的消息数 (20 + 30 + 18 + 30 + 25 + 35) x 0.0008 美元 = 0.1264 美元


## <a name="telephony-and-sms"></a>电话服务和短信

## <a name="price"></a>价格

电话服务按每分钟计费，短信服务按每条短信计费。 定价取决于所用号码的类型和位置以及呼叫和短信的目的地。

### <a name="telephone-number-leasing"></a>电话号码租赁

先收取电话号码租赁预付费用，然后按月定期收费：

|数字类型   |每月费用   |
|--------------|-----------|
|本地（美国）     |1 美元/月        |
|免费电话（美国） |2 美元/月 |


### <a name="telephone-calling"></a>电话呼叫

对于美国境内的电话号码，传统电话呼叫（通过公用电话交换网进行的呼叫）提供即用即付定价。 价格为基于所用号码类型和呼叫目的地的每分钟费用。 下表列出了最受欢迎呼叫目的地的价格详细信息。 有关目的地的完整列表，请参阅[详细定价列表](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)。


#### <a name="united-states-calling-prices"></a>美国呼叫价格

以下价格包含了所需的通信税和费用：

|数字类型   |拨打电话   |接听电话|
|--------------|-----------|------------|
|Local     |每分钟 0.013 美元起       |每分钟 0.0085 美元        |
|免费 |每分钟 0.013 美元   |每分钟 0.0220 美元 |

#### <a name="other-calling-destinations"></a>其他呼叫目的地

以下价格包含了所需的通信税和费用：

|呼叫目的地   |每分钟价格|
|-----------|------------|
|加拿大     |每分钟 0.013 美元起   |
|英国     |每分钟 0.015 美元起   |
|德国     |每分钟 0.015 美元起   |
|法国     |每分钟 0.016 美元起   |


### <a name="sms"></a>SMS

短信提供即用即付定价。 价格为基于短信目的地的每条短信段费用。 请参阅[此处](./telephony-sms/sms-faq.md#what-is-the-sms-character-limit)以详细了解短信段。 可以通过免费电话号码向美国境内的电话号码发送短信。 请注意，本地（地理）电话号码不能用于发送短信。

以下价格包含了所需的通信税和费用：

|国家/地区   |发送消息|接收消息|
|-----------|------------|------------|
|美国（免费）    |$0.0075/短信段  | $0.0075/短信段 |
