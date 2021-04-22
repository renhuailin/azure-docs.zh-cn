---
title: B2B 消息的 X12 跟踪架构
description: 创建跟踪架构以监视 Azure 逻辑应用的 X12 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "76905302"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>创建架构以跟踪 Azure 逻辑应用中的 X12 消息

若要帮助你监视企业到企业 (B2B) 事务的成功、错误和消息属性，可以在集成帐户中使用以下 X12 跟踪架构：

* X12 事务集跟踪架构
* X12 交易集确认跟踪架构
* X12 交换跟踪架构
* X12 交换确认跟踪架构
* X12 功能组跟踪架构
* X12 功能组确认跟踪架构

## <a name="x12-transaction-set-tracking-schema"></a>X12 事务集跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | X12 消息发送方的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | X12 消息接收方的合作伙伴名称 |
| senderQualifier | 是 | 字符串 | 发送合作伙伴限定符 |
| senderIdentifier | 是 | 字符串 | 发送合作伙伴标识符 |
| receiverQualifier | 是 | 字符串 | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | 字符串 | 接收合作伙伴标识符 |
| agreementName | 否 | 字符串 | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，为 `receive` 或 `send` |
| interchangeControlNumber | 否 | 字符串 | 交换控制编号 |
| functionalGroupControlNumber | 否 | 字符串 | 功能控制编号 |
| transactionSetControlNumber | 否 | 字符串 | 事务集控制编号 |
| CorrelationMessageId | 否 | 字符串 | 相关消息 ID，为 {AgreementName} {AgreementName}{GroupControlNumber}{TransactionSetControlNumber} 的组合 |
| messageType | 否 | 字符串 | 交易集或文档类型。 |
| isMessageFailed | 是 | 布尔 | X12 消息是否失败 |
| isTechnicalAcknowledgmentExpected | 是 | 布尔 | 是否已在 X12 协议中配置了技术确认 |
| isFunctionalAcknowledgmentExpected | 是 | 布尔 | 是否已在 X12 协议中配置了功能确认 |
| needAk2LoopForValidMessages | 是 | 布尔 | 有效的消息是否需要 AK2 循环 |
| segmentsCount | 否 | Integer | X12 交易集中的段数 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 交易集确认跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | X12 消息发送方的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | X12 消息接收方的合作伙伴名称 |
| senderQualifier | 是 | 字符串 | 发送合作伙伴限定符 |
| senderIdentifier | 是 | 字符串 | 发送合作伙伴标识符 |
| receiverQualifier | 是 | 字符串 | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | 字符串 | 接收合作伙伴标识符 |
| agreementName | 否 | 字符串 | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，为 `receive` 或 `send` |
| interchangeControlNumber | 否 | 字符串 | 功能确认的交换控制编号。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值。 |
| functionalGroupControlNumber | 否 | 字符串 | 功能确认的功能组控制编号。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值 |
| isaSegment | 否 | 字符串 | 消息的 ISA 段。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值 |
| gsSegment | 否 | 字符串 | 消息的 GS 段。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值 |
| respondingfunctionalGroupControlNumber | 否 | 字符串 | 响应交换控制编号 |
| respondingFunctionalGroupId | 否 | 字符串 | 确认中映射到 AK101 的响应功能组 ID |
| respondingtransactionSetControlNumber | 否 | 字符串 | 响应交易集控制编号 |
| respondingTransactionSetId | 否 | 字符串 | 确认中映射到 AK201 的响应交易集 ID |
| statusCode | 是 | 布尔 | 交易集确认状态代码 |
| segmentsCount | 是 | 枚举 | 确认状态代码，其中包含以下允许的值：`Accepted`、`Rejected` 和 `AcceptedWithErrors` |
| processingStatus | 是 | 枚举 | 确认的处理状态，其中包含以下允许的值：`Received`、`Generated` 和 `Sent` |
| CorrelationMessageId | 否 | 字符串 | 相关消息 ID，为 {AgreementName} {AgreementName}{GroupControlNumber}{TransactionSetControlNumber} 的组合 |
| isMessageFailed | 是 | 布尔 | X12 消息是否失败 |
| ak2Segment | 否 | 字符串 | 对接收的功能组内的交易集的确认 |
| ak3Segment | 否 | 字符串 | 报告数据段中的错误 |
| ak5Segment | 否 | 字符串 | 报告是接受还是拒绝 AK2 段中标识的交易集，以及原因是什么 |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12 交换跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | X12 消息发送方的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | X12 消息接收方的合作伙伴名称 |
| senderQualifier | 是 | 字符串 | 发送合作伙伴限定符 |
| senderIdentifier | 是 | 字符串 | 发送合作伙伴标识符 |
| receiverQualifier | 是 | 字符串 | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | 字符串 | 接收合作伙伴标识符 |
| agreementName | 否 | 字符串 | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，为 `receive` 或 `send` |
| interchangeControlNumber | 否 | 字符串 | 交换控制编号 |
| isaSegment | 否 | 字符串 | 消息 ISA 段 |
| isTechnicalAcknowledgmentExpected | 布尔 | 是否已在 X12 协议中配置了技术确认  |
| isMessageFailed | 是 | 布尔 | X12 消息是否失败 |
| isa09 | 否 | 字符串 | X12 文档交换日期 |
| isa10 | 否 | 字符串 | X12 文档交换时间 |
| isa11 | 否 | 字符串 | X12 交换控制标准标识符 |
| isa12 | 否 | 字符串 | X12 交换控制版本号 |
| isa14 | 否 | 字符串 | 请求了 X12 确认 |
| isa15 | 否 | 字符串 | 表示测试或生产的指示符 |
| isa16 | 否 | 字符串 | 元素分隔符 |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 交换确认跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | X12 消息发送方的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | X12 消息接收方的合作伙伴名称 |
| senderQualifier | 是 | 字符串 | 发送合作伙伴限定符 |
| senderIdentifier | 是 | 字符串 | 发送合作伙伴标识符 |
| receiverQualifier | 是 | 字符串 | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | 字符串 | 接收合作伙伴标识符 |
| agreementName | 否 | 字符串 | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，为 `receive` 或 `send` |
| interchangeControlNumber | 否 | 字符串 | 从合作伙伴收到的技术确认的交换控制编号 |
| isaSegment | 否 | 字符串 | 从合作伙伴收到的技术确认的 ISA 段 |
| respondingInterchangeControlNumber | 否 | 字符串 | 从合作伙伴收到的技术确认的交换控制编号 |
| isMessageFailed | 是 | 布尔 | X12 消息是否失败 |
| statusCode | 是 | 枚举 | 交换确认状态代码，其中包含以下允许的值：`Accepted`、`Rejected` 和 `AcceptedWithErrors` |
| processingStatus | 是 | 枚举 | 确认状态，其中包含以下允许的值：`Received`、`Generated` 和 `Sent` |
| ta102 | 否 | 字符串 | 交换日期 |
| ta103 | 否 | 字符串 | 交换时间 |
| ta105 | 否 | 字符串 | 交换注释代码 |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 功能组跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | X12 消息发送方的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | X12 消息接收方的合作伙伴名称 |
| senderQualifier | 是 | 字符串 | 发送合作伙伴限定符 |
| senderIdentifier | 是 | 字符串 | 发送合作伙伴标识符 |
| receiverQualifier | 是 | 字符串 | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | 字符串 | 接收合作伙伴标识符 |
| agreementName | 否 | 字符串 | 用于解析消息的 X12 协议的名称 |
| direction | 是 | 枚举 | 消息流的方向，为接收或发送 |
| interchangeControlNumber | 否 | 字符串 | 交换控制编号 |
| functionalGroupControlNumber | 否 | 字符串 | 功能控制编号 |
| gsSegment | 否 | 字符串 | 消息 GS 段 |
| isTechnicalAcknowledgmentExpected | 是 | 布尔 | 是否已在 X12 协议中配置了技术确认 |
| isFunctionalAcknowledgmentExpected | 是 | 布尔 | 是否已在 X12 协议中配置了功能确认 |
| isMessageFailed | 是 | 布尔 | X12 消息是否失败 |
| gs01 | 否 | 字符串 | 功能标识符代码 |
| gs02 | 否 | 字符串 | 应用程序发送方的代码 |
| gs03 | 否 | 字符串 | 应用程序接收方的代码 |
| gs04 | 否 | 字符串 | 功能组日期 |
| gs05 | 否 | 字符串 | 功能组时间 |
| gs07 | 否 | 字符串 | 负责机构代码 |
| gs08 | 否 | 字符串 | 版本、发行版或行业的标识符代码 |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 功能组确认跟踪架构

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| 属性 | 必选 | 类型 | 说明 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | 字符串 | X12 消息发送方的合作伙伴名称 |
| receiverPartnerName | 否 | 字符串 | X12 消息接收方的合作伙伴名称 |
| senderQualifier | 是 | 字符串 | 发送合作伙伴限定符 |
| senderIdentifier | 是 | 字符串 | 发送合作伙伴标识符 |
| receiverQualifier | 是 | 字符串 | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | 字符串 | 接收合作伙伴标识符 |
| agreementName | 否 | 字符串 | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，为 `receive` 或 `send` |
| interchangeControlNumber | 否 | 字符串 | 交换控制编号，当从合作伙伴收到了技术确认时，会为发送方填充此值 |
| functionalGroupControlNumber | 否 | 字符串 | 技术确认的功能组控制编号，当从合作伙伴收到了技术确认时，会为发送方填充此值 |
| isaSegment | 否 | 字符串 | 与交换控制编号相同，但仅在特定情况下填充 |
| gsSegment | 否 | 字符串 | 与功能组控制编号相同，但仅在特定情况下填充 |
| respondingfunctionalGroupControlNumber | 否 | 字符串 | 原始功能组的控制编号 |
| respondingFunctionalGroupId | 否 | 字符串 | 在确认功能组 ID 中映射到 AK101 |
| isMessageFailed | 布尔 | X12 消息是否失败 |
| statusCode | 是 | 枚举 | 确认状态代码，其中包含以下允许的值：`Accepted`、`Rejected` 和 `AcceptedWithErrors` |
| processingStatus | 是 | 枚举 | 确认的处理状态，其中包含以下允许的值：`Received`、`Generated` 和 `Sent` |
| ak903 | 否 | 字符串 | 已接收的事务集的数目 |
| ak904 | 否 | 字符串 | 在标识的功能组中接受的交易集的数量 |
| ak9Segment | 否 | 字符串 | 是接受还是拒绝 AK1 段中标识的功能组，以及原因是什么 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 日志监视 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)