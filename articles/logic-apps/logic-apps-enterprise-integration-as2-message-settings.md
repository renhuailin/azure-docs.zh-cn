---
title: AS2 消息设置
description: 有关在配有 Enterprise Integration Pack 的 Azure 逻辑应用协议中进行 AS2 消息设置的参考指南。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 07/20/2021
ms.openlocfilehash: 2bec9c598c0f08e9e114fcf5a83bfb0d238dd2b1
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400984"
---
# <a name="reference-for-as2-message-settings-in-agreements-for-azure-logic-apps"></a>有关在 Azure 逻辑应用协议中进行 AS2 消息设置的参考

本参考将介绍为了指定如何处理[参与方](logic-apps-enterprise-integration-partners.md)之间的消息而可在 AS2 协议中设置的属性。 请根据与消息交换合作伙伴之间达成的协议来配置这些属性。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收设置

![选择“接收设置”](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 属性 | 必须 | 说明 |
|----------|----------|-------------|
| **替代消息属性** | 否 | 用属性设置替代传入消息的属性。 |
| **应对消息进行签名** | 否 | 指定是否必须对所有传入消息进行数字签名。 若需要签名，在“证书”列表中选择现有来宾合作伙伴公共证书来验证消息的签名。 若没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **对消息进行加密** | 否 | 指定是否必须对所有传入消息进行加密。 未加密的消息会被拒绝。 若需要加密，在“证书”列表中选择现有宿主合作伙伴私有证书来解密传入的消息。 若没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **对消息进行压缩** | 否 | 指定是否必须对所有传入消息进行压缩。 未压缩的消息会被拒绝。 |
| 禁用消息 ID 重复项 | 否 | 指定是否允许消息具有重复 ID。 若禁用重复 ID，请选择检查的间隔天数。 还可选择是否暂停重复项。 |
| **MDN 文本** | 否 | 指定要发送到消息发送方的默认消息处置通知 (MDN)。 |
| **发送 MDN** | 否 | 指定是否为接收的消息发送同步 MDN。  |
| **发送已签名的 MDN** | 否 | 指定是否为接收的消息发送已签名的 MDN。 若需要签名，请在“MIC 算法”列表中选择用于对消息进行签名的算法。 |
| **发送异步 MDN** | 否 | 指定是否异步发送 MDN。 若选择异步 MDN，请在 URL 框中指定要将 MDN 发送到的 URL。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 发送设置

![选择“发送设置”](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 属性 | 必须 | 说明 |
|----------|----------|-------------|
| **启用消息签名** | 否 | 指定是否必须对所有传出消息进行数字签名。 若需要签名，请选择以下值： <p>- 请在“签名算法”列表中，选择用于对消息进行签名的算法。 <br>- 请在“证书”列表中，选择用于对消息进行签名的现有宿主合伙伙伴私有证书。 若没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息加密** | 否 | 指定是否必须对所有传出消息进行加密。 若需要加密，请选择以下值： <p>- 请在“加密算法”列表中，选择用于对消息进行加密的来宾合作伙伴公共证书算法。 <br>- 请在“证书”列表中，选择用于对传出信息进行加密的现有来宾合作伙伴公用证书。 若没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息压缩** | 否 | 指定是否必须对所有传出消息进行压缩。 |
| **展开 HTTP 标头** | 否 | 将 HTTP `content-type` 标头置于单行上方。 |
| 在 MIME 标头中传输文件名 | 否 | 指定是否在 MIME 标头中包含文件名。 |
| **请求 MDN** | 否 | 指定是否接收所有传出消息的消息处置通知 (MDN)。 |
| **要求对 MDN 进行签名** | 否 | 指定是否接收所有传出消息的已签名 MDN。 若需要签名，请在“MIC 算法”列表中选择用于对消息进行签名的算法。 |
| **要求发送异步 MDN** | 否 | 指定是否异步接收 MDN。 若选择异步 MDN，请在 URL 框中指定要将 MDN 发送到的 URL。 |
| **启用 NRR** | 否 | 指定是否需要不可否认性回执 (NRR)。 这种通信属性提供数据已按址接收到的证据。 |
| **SHA2 算法格式** | 否 | 指定用于对传出 AS2 消息或 MDN 的标头进行签名的 MIC 算法格式 |
||||

## <a name="next-steps"></a>后续步骤

[交换 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
