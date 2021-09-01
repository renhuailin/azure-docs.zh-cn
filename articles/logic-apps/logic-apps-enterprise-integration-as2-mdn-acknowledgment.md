---
title: AS2 MDN 确认
description: 了解 Azure 逻辑应用中 AS2 消息的消息处置通知 (MDN) 确认。
services: logic-apps
ms.suite: integration
author: S-Jindal
ms.author: shivamjindal
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 510be5ca1343f115bcdf13c2d67b598b4d410564
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862794"
---
# <a name="mdn-acknowledgments-for-as2-messages-in-azure-logic-apps"></a>Azure 逻辑应用中对 AS2 消息的 MDN 确认

在 Azure 逻辑应用中，可创建工作流来处理使用 AS2 操作时，用于电子数据交换 (EDI) 通信的 AS2 消息。 在 EDI 消息传送中，确认会提供处理 EDI 交换的状态。 接收交换时，[AS2 解码操作](logic-apps-enterprise-integration-as2.md#decode)可向发送方返回消息处置通知 (MDN) 或确认。 MDN 验证以下各项：

* 接收方已成功接收到原始消息。

  发送方将最初发送的消息 `MessageID` 与接收方在 MDN 中包含的 `original-message-id` 字段进行比较。

* 接收方验证了交换数据的完整性。

  消息完整性检查 (MIC) 或 MIC digest 是通过最初发送消息中的有效负载计算而得。 发送方将此 MIC 与接收方通过所收到消息的有效负载计算得出的 MIC（如已签名，则也包含在 MDN 的 `Received-content-MIC` 字段中）进行比较。

  > [!NOTE]
  > 可对 MDN 进行签名，但不能对其进行加密或压缩。

* 回执的不可否认性

  发送方将签名的 MDN 与接收方的公共密钥进行比较，验证 MDN 中返回的 MIC 值与存储在不可否认数据库中原始消息的 MIC 值是否相同。

> [!NOTE]
> 若允许发送 MDN，即便在处理过程中发生错误，逻辑应用仍会尝试返回 MDN 来报告 AS2 消息处理的状态。 待发送方接收并验证 MDN 后，AS2 传输才会完成。
> 同步 MDN 可用作 HTTP 响应，如 `200 OK` 状态。

这一主题简要概述了 AS2 MDN 确认，其中包括用于生成确认的属性、要使用的 MDN 标头和 MIC。 如需其他详细信息，请查看以下文档：

* [使用 Azure 逻辑应用交换 B2B 企业集成的 AS2 消息](logic-apps-enterprise-integration-as2.md)
* [AS2 消息设置](logic-apps-enterprise-integration-as2-message-settings.md)
* [Azure 逻辑应用是什么](logic-apps-overview.md)

## <a name="mdn-generation"></a>生成 MDN

当协议的接收设置选择了“发送 MDN”选项时，AS2 解码操作将根据参与方的 AS2 协议属性生成 MDN。 在此实例中，消息标头中的 AS2-From 属性用于生成 MDN，但其他属性及其值取自参与方的 AS2 协议设置。

默认情况下，传入 AS2 消息标头用于验证和生成 MDN。 若要改为使用协议的验证和 MDN 设置，请在协议的接收设置中选择替代消息属性。 否则，若此选项保持未选中状态或协议不可用，则 AS2 解码操作将改为使用传入消息标头。

## <a name="mdn-headers"></a>MDN 标头

若要将回复形式的 MDN 关联到 AS2 消息，请使用 `AS2-From` 标头、`AS2-To` 标头和 `MessageID` 上下文属性。 在 MDN 中，`Original-Message-ID` 标头来自 AS2 消息中的 `Message-ID` 标头，MDN 是后者的响应。 MDN 包含下列标头：

| 头文件 | 说明 |
|---------|-------------|
| HTTP 和 AS2 | 更多详细信息，请查看 [AS2 消息设置](logic-apps-enterprise-integration-as2-message-settings.md)。
| 传送层 | 此标头包括 `Content-Type` 标头，其中含有已签名的多部分消息、MIC 的算法、签名格式化协议以及最外面的多部分边界子标头。 |
| 第一部分 | 多部分签名消息的第一部分是嵌入式 MDN。 此部分可支持用户读取。 |
| 第二部分 | 多部分签名消息的第二部分包含数字签名、原始消息的引用、处置类型和状态以及 MIC 值。 此部分可支持计算机读取。 |
|||

## <a name="mic-digest"></a>MIC 摘要

MIC digest 或 MIC 验证 MDN 是否与最初发送消息中的有效负载相关联。 此 MIC 包含在 `Received-Content-MIC` 扩展字段中已签名的多部分 MDN 消息中第二部分。

MIC 采用 base64 编码，由 MIC 算法属性确定，在 AS2 协议的接收设置页面上选择发送 MDN 和发送签名的 MDN 属性时启用。 对于 MIC 生成，可从以下受支持的哈希算法中进行选择：

* SHA1
* MD5
* SHA2-256
* SHA2-384
* SHA2-512

例如，下列屏幕截图显示了 AS2 协议的接收设置页中的 MDN 属性：

![此屏幕截图显示了 Azure 门户中 AS2 协议的“接收设置”与 MDN 确认设置。](./media/logic-apps-enterprise-integration-as2-mdn-acknowledgment/mdn-ack-settings.png)

## <a name="next-steps"></a>后续步骤

* [AS2 消息设置](logic-apps-enterprise-integration-as2-message-settings.md)
