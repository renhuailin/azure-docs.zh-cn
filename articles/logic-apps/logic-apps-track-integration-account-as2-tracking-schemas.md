---
title: B2B 消息的 AS2 跟踪架构
description: 在 Azure 逻辑应用中创建跟踪架构来监视 AS2 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "76906970"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建架构以跟踪 AS2 消息

若要帮助你监视企业到企业 (B2B) 事务的成功、错误和消息属性，可以在集成帐户中使用以下 AS2 跟踪架构：

* AS2 消息跟踪架构
* AS2 消息处置通知 (MDN) 跟踪架构

## <a name="as2-message-tracking-schema"></a>AS2 消息跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | AS2 消息发送者的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | AS2 消息接收者的合作伙伴名称 |
| as2To | 是 | 字符串 | AS2 消息标头中的 AS2 消息接收者名称 |
| as2From | 是 | 字符串 | AS2 消息标头中的 AS2 消息发送者名称 |
| agreementName | 否 | 字符串 | 解析消息的 AS2 协议名称 |
| direction | 是 | 字符串 | 消息流的方向，为 `receive` 或 `send` |
| messageId | 否 | 字符串 | AS2 消息标头中的 AS2 消息 ID |
| dispositionType | 否 | 字符串 | 邮件处置说明 (MDN) 处置类型值 |
| fileName | 否 | 字符串 | 文件名，来自 AS2 消息的标头 |
| isMessageFailed | 是 | 布尔 | AS2 消息是否失败 |
| isMessageSigned | 是 | 布尔 | AS2 消息是否已签名 |
| isMessageEncrypted | 是 | 布尔 | AS2 消息是否已加密 |
| isMessageCompressed | 是 | 布尔 | AS2 消息是否已压缩 |
| correlationMessageId | 否 | 字符串 | AS2 消息 ID，用于将消息与 MDN 相关联 |
| incomingHeaders | 否 | JToken 字典 | 传入 AS2 消息标头的详细信息 |
| outgoingHeaders | 否 | JToken 字典 | 传出 AS2 消息标头的详细信息 |
| isNrrEnabled | 是 | 布尔 | 值未知时是否使用默认值 |
| isMdnExpected | 是 | 布尔 | 如果值未知，是否使用默认值 |
| mdnType | 是 | 枚举 | 允许的值：`NotConfigured`、`Sync` 和 `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | AS2 消息发送者的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | AS2 消息接收者的合作伙伴名称 |
| as2To | 是 | 字符串 | 接收 AS2 消息的合作伙伴名称 |
| as2From | 是 | 字符串 | 发送 AS2 消息的合作伙伴名称 |
| agreementName | 否 | 字符串 | 解析消息的 AS2 协议名称 |
| direction | 是 | 字符串 | 消息流的方向，为 `receive` 或 `send` |
| messageId | 否 | 字符串 | AS2 消息 ID |
| originalMessageId | 否 | 字符串 | AS2 原始消息 ID |
| dispositionType | 否 | 字符串 | MDN 处置类型值 |
| isMessageFailed | 是 | 布尔 | AS2 消息是否失败 |
| isMessageSigned | 是 | 布尔 | AS2 消息是否已签名 |
| isNrrEnabled | 是 | 布尔 | 如果值未知，是否使用默认值 |
| statusCode | 是 | 枚举 | 允许的值：`Accepted`、`Rejected` 和 `AcceptedWithErrors` |
| micVerificationStatus | 是 | 枚举 | 允许的值：`NotApplicable`、`Succeeded` 和 `Failed` |
| correlationMessageId | 否 | 字符串 | 相关 ID，它是配置了 MDN 的原始消息的 ID |
| incomingHeaders | 否 | JToken 字典 | 传入消息标头的详细信息 |
| outgoingHeaders | 否 | JToken 字典 | 传出消息标头的详细信息 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 日志监视 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)