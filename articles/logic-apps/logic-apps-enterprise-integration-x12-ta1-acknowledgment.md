---
title: X12 TA1 确认和错误代码
description: 了解 Azure 逻辑应用中用于 X12 消息的 TA1 技术确认和错误代码。
services: logic-apps
ms.suite: integration
author: praveensri
ms.author: psrivas
ms.reviewer: estfan, divswa, azla
ms.topic: reference
ms.date: 07/15/2021
ms.openlocfilehash: be168489b6cdccd3662a174ceab34d093db41244
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481231"
---
# <a name="ta1-technical-acknowledgments-and-error-codes-for-x12-messages-in-azure-logic-apps"></a>Azure 逻辑应用中 X12 消息的 TA1 技术确认和错误代码

在 Azure 逻辑应用中，可以创建工作流来处理使用 X12 操作时电子数据交换 (EDI) 通信的 X12 消息。 在 EDI 消息传送中，确认会提供处理 EDI 交换的状态。 接收交换时，[X12 解码操作](logic-apps-enterprise-integration-x12-decode.md)可以基于启用的确认类型和指定的验证级别，向发送方返回一种或多种确认类型。

例如，接收方通过发送 TA1 技术确认 (ACK) 来报告在收到的 X12 编码消息中验证交换控制标头 (ISA) 和交换控制尾部 (IEA) 时的状态。 如果此标头和尾部有效，接收方将发送一个正的 TA1 ACK，无论其他内容的状态如何。 如果标头和尾部无效，接收方将发送带有错误代码的 TA1 ACK。

X12 TA1 ACK 符合 X12_<version number>_TA1.xsd 的架构。 接收方以 ISA 和 IEA 信封发送 TA1 ACK。 但是，此 ISA 和 IEA 信封与任何其他交换中没有什么不同。

本主题简要概述了 X12 TA1 ACK，其中包括交换中的 TA1 ACK 段以及这些段中使用的错误代码。 如需其他详细信息，请查看以下文档：

* [X12 997 功能确认和错误代码](logic-apps-enterprise-integration-x12-997-acknowledgment.md)
* [交换用于 B2B 企业集成的 X12 消息](logic-apps-enterprise-integration-x12.md)
* [交换用于 B2B 企业集成的 EDIFACT 消息](logic-apps-enterprise-integration-edifact.md)
* [Azure 逻辑应用是什么](logic-apps-overview.md)
* [使用 Azure 逻辑应用和 Enterprise Integration Pack 的 B2B 企业集成解决方案](logic-apps-enterprise-integration-overview.md)

<a name="ta1-ack-segments"></a>

## <a name="ta1-ack-segments"></a>TA1 ACK 段

下表描述了交换中的 TA1 ACK 段：

| TA1 字段 | 字段名称 | 映射到传入交换 | 值 |
|-----------|------------|--------------------------------|-------|
| TA101 | 交换控制编号 | ISA13 - 交换控制编号 | - |
| TA102 | 交换日期 | ISA09 - 交换日期 | - |
| TA103 | 交换时间 | ISA10 - 交换时间 | - |
| TA104 | 交换确认代码* | 不适用 | * 引擎行为基于数据元素验证，安全信息和身份验证信息除外，因为这些信息基于配置信息中的字符串比较。 <p>根据以下定义，引擎行为 (TA104) 值为 A、E 或 R： <p><p>A = 接受 <br>E = 已接受但存在错误的交换 <br>R = 已拒绝或已挂起的交换。 <p><p>如需获取详细信息，请查看 [TA1 ACK 错误代码](#ta1-ack-error-codes)。 |
| TA105 | 交换注释代码 | 不适用 | 处理结果错误代码。 如需获取详细信息，请查看 [TA1 ACK 错误代码](#ta1-ack-error-codes)。 |
|||||

<a name="ta1-ack-error-codes"></a>

## <a name="ta1-ack-error-codes"></a>TA1 ACK 错误代码

本部分介绍 [TA1 ACK 段](#ta1-ack-segments)中使用的错误代码。 下表列出了 Azure 逻辑应用中 X12 消息处理的 X12 规范定义的支持和不支持的错误代码。 在“引擎行为”列中，TA104 值具有以下定义：

* A = 接受
* E = 已接受但存在错误的交换
* R = 已拒绝或已挂起的交换

| 条件 | 引擎行为 <br>（TA104 值） | TA105 值 | 是否支持？ |
|-----------|-----------------------------------|-------------|------------|
| Success | A | 000 | 是 |
| 标头 ISA 13 和尾部 IEA02 的交换控制编号不匹配 | E | 001 | 是 |
| 不支持 ISA11（控制标准）中的标准 | E | 002 | 是，如果存在 ID 不匹配的情况。 |
| 不支持控件的版本 | E | 003 | 否，改用错误代码 017。 |
| 段终止符无效* <p><p>* 段终止符可以具有以下有效组合： <p><p>- 仅段终止符的字符型。 <br>- 段终止符的字符，后跟后缀 1 和后缀 2。 | R | 004 | 是 |
| 发送方的交换 ID 限定符无效 | R | 005 | 是，如果存在 ID 不匹配的情况。 |
| 交换发送方 ID 无效 | E | 006 | 是，如果在接收端口接收交换要求身份验证。 <p><p>注意：检查与发送方 ID 相关的属性。 如果这些属性不一致，或者由于未设置而使参与方设置不可用，则交换将被拒绝。 |
| 接收方的交换 ID 限定符无效 | R | 007 | 是，如果存在 ID 不匹配的情况。 |
| 交换接收方 ID 无效 | E | 008 | 否* <p><p>* 如果在接收端口接收交换要求身份验证则支持。 检查与发送方 ID 相关的属性。 如果这些属性不一致，或者由于未设置而使参与方设置不可用，则交换将被拒绝。 |
| 未知交换接收方 ID | E | 009 | 是 |
| 授权信息限定符值无效 | R | 010 | 是，如果存在 ID 不匹配的情况。 |
| 授权信息值无效 | R | 011 | 是，如果已设置参与方或已设定参与方的值。 |
| 安全信息限定符值无效 | R | 012 | 是，如果存在 ID 不匹配的情况。 |
| 安全信息值无效 | R | 013 | 是，如果已设置参与方或已设定参与方的值。 |
| 交换日期值无效 | R | 014 | 是 |
| 交换时间值无效 | R | 015 | 是 |
| 交换标准标识符值无效 | R | 016 | 是 |
| 交换版本 ID 值无效 | R | 017 | 是，指示枚举值无效。 |
| 交换控制编号值无效 | R | 018 | 是 |
| 确认请求值无效 | E | 019 | 是 |
| 测试指示器值无效 | E | 020 | 是 |
| 包含的组数值无效 | E | 021 | 是 |
| 控制结构无效 | R | 022 | 是 |
| 文件结尾（传输）不正确（过早结束） | R | 023 | 是 |
| 交换内容无效，例如 GS 段无效 | R | 024 | 是 |
| 交换控制编号重复 | R，基于设置 | 025 | 是 |
| 数据元素分隔符无效 | R | 026 | 是 |
| 组件元素分隔符无效 | R | 027 | 是 |
| 延迟送达请求中的送达日期无效 | - | - | 否 |
| 延迟送达请求中的送达时间无效 | - | - | 否 |
| 延迟送达请求中的送达时间代码无效 | - | - | 否 |
| 服务的级别无效 | - | - | 否 |
||||

## <a name="next-steps"></a>后续步骤

* [交换用于 B2B 企业集成的 X12 消息](logic-apps-enterprise-integration-x12.md)