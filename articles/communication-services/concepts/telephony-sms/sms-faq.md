---
title: SMS 常见问题解答
titleSuffix: An Azure Communication Services concept document
description: SMS 常见问题解答
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 658e13aa888e448de723a97bf9cc89c162a774b5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356323"
---
# <a name="sms-faq"></a>SMS 常见问题解答

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>客户是否可以出于紧急目的使用 Azure 通信服务？

在美国，Azure 通信服务不支持“发短信给 911”功能，但根据美国联邦通信委员会 (FCC) 的规则，你可能有义务在必要时执行此操作。  你应评估 FCC 的“发短信给 911”规则是否适用于你的服务或应用程序。 在这些规则的适用范围内，你有义务将 911 短信路由到发送请求的紧急呼叫中心。 你可以根据需要确定自己的“发短信给 911”发送模型，但 FCC 接受的一种方法是在用户的移动设备上自动启动本机拨号器，通过基础移动运营商发送 911 短信。

## <a name="are-there-any-limits-on-sending-messages"></a>发送短信是否有任何限制？

为了确保我们能够持续提供与 SLA 一致的高质量服务，Azure 通信服务会应用速率限制（每个基元不同）。 如果调用 API 的开发者超出限制，将收到 429 HTTP 状态代码响应。 如果你的公司设有超出速率限制的要求，请发送电子邮件至 phone@microsoft.com。

SMS 的速率限制：

|操作|范围|时间范围（秒）| 限制（请求数） | 每分钟请求单位数|
|---------|-----|-------------|-------------------|-------------------------|
|发送消息|按号码|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Azure 通信服务如何处理免费号码的选择退出操作？

美国运营商强制实施美国免费号码的选择退出操作。
- STOP - 如果短信接收者希望选择退出，可以向免费电话号码发送“STOP”。 运营商针对 STOP 发送以下默认响应：“网络短信: 你已回复“stop”，将阻止从该号码发送的所有短信。 回复“unstop”可再次接收短信。”
- START/UNSTOP - 如果接收者希望重新订阅某个免费号码的短信，可以向该免费号码发送“START”或“UNSTOP”。 运营商针对 START/UNSTOP 发送以下默认响应：“网络消息:你已回复“unstop”，将再次从该号码接收短信。”
- Azure 通信服务将检测到 STOP 消息，并阻止后续向接收者发送的所有短信。 发送报告将指示发送失败，状态消息为“给定接收者已阻止发送者”。
- 系统会将 STOP、UNSTOP 和 START 消息返回给你。 Azure 通信服务建议你监视和实施上述选择退出操作，以确保不再尝试向已选择退出通信的接收者发送短信。

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>如何使用 Azure 通信服务接收短信？

Azure 通信服务客户可以使用 Azure 事件网格来接收传入的短信。 按照此[快速入门](../../quickstarts/telephony-sms/handle-sms-events.md)中的操作设置事件网格以接收短信。

## <a name="what-is-the-sms-character-limit"></a>短信字符数限制是多少？
一条短信的字符数限制为 160。 发送消息时，超过 160 个字符的所有消息将拆分为多个段并分开传递，然后接收方的设备会连接这些段。 接收长消息时与此类似，长消息的多个段会自动连接，并作为一条消息传递到 Azure 事件网格中指定的终结点。 

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>可以发送/接收长短信（>2048 字符）？

Azure 通信服务支持通过短信发送和接收长短信。 但是，某些无线运营商或设备在接收长短信时可能会有不同的操作。

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>如何处理发送到座机号码的短信？

在美国，Azure 通信服务不检查座机号码，但会尝试将短信发送给运营商进行转发。 将向客户收取将短信发送到座机号码的费用。 

## <a name="can-i-send-messages-to-multiple-recipients"></a>是否可以向多个收件人发送短信？

是的，可以通过一个请求向多个收件人发送短信。 按照此[快速入门](../../quickstarts/telephony-sms/send.md?pivots=programming-language-csharp)中的操作将短信发送给多个收件人。

##  <a name="i-received-a-http-status-202-from-the-send-sms-api-but-the-sms-didnt-reach-my-phone-what-do-i-do-now"></a>我收到了来自发送短信 API 的 HTTP 状态 202，但短信并未发送至我的手机，我该怎么办？

服务返回的 202 表示消息已排队等候发送且未送达。 使用此[快速入门](../../quickstarts/telephony-sms/handle-sms-events.md)订阅发送报告事件及排除故障。 配置事件后，检查发送报告的“deliveryStatus”字段以验证发送是否成功。
