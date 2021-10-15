---
title: 在 B2B 工作流中交换 AS2 消息
description: 使用 Azure 逻辑应用和 Enterprise Integration Pack 创建工作流，以便在合作伙伴之间交换 AS2 消息。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8023073aecae3a1f97c82a16a5be952df7425186
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399636"
---
# <a name="exchange-as2-messages-using-workflows-in-azure-logic-apps"></a>使用 Azure 逻辑应用程序中的工作流交换 AS2 消息

若要在使用 Azure 逻辑应用创建的工作流中发送和接收 AS2 消息，请使用 AS2 连接器，它提供用于支持和管理 AS2（版本 1.2）通信的触发器和操作。

* 如果你要处理“逻辑应用（消耗）”资源类型并且不需要跟踪功能，请使用AS2 (v2) 连接器，而不要使用正在被弃用的原始 AS2 连接器  。

  除了跟踪功能之外，AS2 (v2) 连接器保留了与原始版本相同的功能，但它的性能更好，它是 Azure 逻辑应用运行时原生的连接器，在消息大小、吞吐量和延迟方面有显著的性能提升。 另外，v2 连接器不要求与集成帐户建立连接。 但要求执行先决条件中所述的操作，确保将集成帐户链接到你计划在其中使用连接器的逻辑应用资源。

* 如果你要处理“逻辑应用（标准）”资源类型，则目前只能使用原始 AS2 连接器 。

  有关原始 AS2 连接器版本的技术信息，请查看[连接器参考页](/connectors/as2/)，其中介绍了连接器的 Swagger 文件中记录的触发器、操作和限制。

### <a name="consumption"></a>[消耗](#tab/consumption)

以下列表描述了 AS2 (v2) 连接器提供的用于在传输消息时建立安全性和可靠性的操作：

* [**AS2 编码** 操作](#encode)：可以通过消息处置通知 (MDN) 提供加密、数字签名和确认功能，这有助于为不可否认性提供支持。 例如，此操作会应用 AS2/HTTP 标头并执行以下任务（在配置好以后）：

  * 对传出消息进行签名。
  * 加密传出消息。
  * 压缩消息。
  * 在 MIME 标头中传输文件名。

* [**AS2 解码** 操作](#decode)：可以通过消息处置通知 (MDN) 提供解密、数字签名和确认功能。 例如，该操作执行以下任务：

  * 处理 AS2/HTTP 标头。
  * 协调收到的 MDN 和原始的出站消息。
  * 更新并关联不可否认性数据库中的记录。
  * 写入 AS2 状态报告的记录。
  * 输出 base64 编码的有效负载内容。
  * 确定是否需要 MDN。 根据 AS2 协议，确定 MDN 应该是同步还是异步。
  * 根据 AS2 协议生成同步或异步 MDN。
  * 在 MDN 上设置关联令牌和属性。

  此操作还会在进行了配置的情况下执行以下任务：

  * 验证签名。
  * 对消息进行解密。
  * 解压缩消息。
  * 检查并禁止消息 ID 重复。

### <a name="standard"></a>[标准](#tab/standard)

有关原始 AS2 连接器的触发器、操作和限制版本的详细信息，请查看连接器的 Swagger 文件中记录的[连接器参考页](/connectors/as2/)。

---

本文介绍如何向现有的逻辑应用工作流添加 AS2 编码和解码操作。 虽然你可以使用任何触发器来启动工作流，但示例使用的是[请求](../connectors/connectors-native-reqres.md)触发器。

## <a name="limits"></a>限制

若要了解在[多租户 Azure 逻辑应用、单租户 Azure 逻辑应用或集成服务环境 (ISE)](logic-apps-overview.md#resource-environment-differences) 中运行的工作流的 AS2 连接器限制，请查看 [B2B 协议的消息大小限制](logic-apps-limits-and-config.md#b2b-protocol-limits)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与逻辑应用资源位于同一个位置或 Azure 区域。

  * 使用[“逻辑应用（消耗）”资源类型](logic-apps-overview.md#resource-environment-differences)和 AS2 (v2) 操作时，逻辑应用资源不需要链接到你的集成帐户 。 但是，仍需使用此帐户来存储合作伙伴、协议和证书等项目，以及使用 AS2、[X12](logic-apps-enterprise-integration-x12.md) 或 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

  * 使用[“逻辑应用（标准）”资源类型](logic-apps-overview.md#resource-environment-differences)和原始 AS2 操作时，工作流需要连接到你在添加 AS2 操作时直接从工作流创建的集成帐户 。

* 集成帐户中至少有两个[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)。 这两个合作伙伴的定义必须使用相同的业务标识限定符，即此方案的 AS2Identity。

* 参与工作流的贸易合作伙伴之间的 [AS2 协议](logic-apps-enterprise-integration-agreements.md)存在于你的集成帐户中。 每个协议需要指定主方和客方合作伙伴。 你与其他合作伙伴之间的消息中的内容必须与协议类型匹配。

* 要在其中使用 AS2 操作的逻辑应用资源和工作流。

  > [!NOTE]
  > AS2 (v2) 连接器仅提供操作，而不提供触发器。 在本文中，此连接器的示例使用[请求](../connectors/connectors-native-reqres.md)触发器。 原始 AS2 连接器包括触发器和操作。 有关原始 AS2 连接器的触发器、操作和限制版本的详细信息，请查看连接器的 Swagger 文件中记录的[连接器参考页](/connectors/as2/)。

  如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)。

* 如果使用 [Azure Key Vault](../key-vault/general/overview.md) 进行证书管理，请检查保管库密钥是否允许“加密”  和“解密”  操作。 否则，编码和解码操作会失败。

  1. 在 Azure 门户中打开你的密钥保管库。 在密钥保管库菜单中的“设置”下，选择“密钥” 。

  1. 在“密钥”窗格中选择你的密钥。 在“版本”窗格中，选择你正在使用的密钥版本。

  1. 在“密钥版本”窗格中的“允许的操作”下，确认选择了“加密”和“解密”操作，例如   ：

     ![显示 Azure 门户的屏幕截图，其中打开了“密钥保管库”、“密钥”和“密钥版本”窗格，并选择了“加密”和“解密”操作。](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>为 AS2 消息编码

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 AS2 操作的触发器或操作下，选择“新建步骤”。

1. 在“选择操作”搜索框中，选择“所有” 。 在搜索框中输入 `as2 encode`。 选择名为“AS2 编码”的操作。

   ![该屏幕截图显示 Azure 门户、工作流设计器，以及处于选中状态的“AS2 编码”操作。](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 设计器上显示了 AS2 操作后，请提供以下属性的信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **要编码的消息** | 是 | 消息有效负载 |
   | **AS2 发件人** | 是 | AS2 协议指定的消息发送方的业务标识符 |
   | **AS2 收件人** | 是 | AS2 协议指定的消息接收方的业务标识符 |
   ||||

   例如，消息有效负载是从“请求”触发器输出的“正文”内容：

   ![显示具有消息编码属性的“AS2 编码”操作的屏幕截图。](./media/logic-apps-enterprise-integration-as2/as2-message-encode-details.png)

   > [!TIP]
   > 如果在发送已签名或加密的消息时遇到问题，请考虑尝试不同的 SHA256 算法格式。 AS2 规范不提供有关 SHA256 格式的任何信息，因此每个提供者都使用其自己的实现或格式。

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 AS2 操作的触发器或操作下，选择“插入新步骤”（加号），然后选择“添加操作” 。

1. 在“选择操作”搜索框中，选择“Azure” 。 在搜索框中输入 `as2 encode`。 选择名为“对 AS2 消息进行编码”的操作。

   ![该屏幕截图显示 Azure 门户、工作流设计器，以及处于选中状态的“对 AS2 消息进行编码”操作。](./media/logic-apps-enterprise-integration-as2/select-encode-as2-message.png)

1. 当系统提示创建与集成帐户的连接时，请提供以下信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **连接名称** | 是 | 连接名称 |
   | **集成帐户** | 是 | 从可用的集成帐户列表中，选择要使用的帐户。 |
   ||||

   例如：

   ![显示“对 AS2 消息进行编码”连接窗格的屏幕截图。](./media/logic-apps-enterprise-integration-as2/create-as2-encode-connection-standard.png)

1. 完成操作后，选择“创建”。

1. 设计器上显示了 AS2 详细信息窗格后，请提供以下属性的信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **要编码的消息** | 是 | 消息有效负载 |
   | **AS2 发件人** | 是 | AS2 协议指定的消息发送方的业务标识符 |
   | **AS2 收件人** | 是 | AS2 协议指定的消息接收方的业务标识符 |
   ||||

   例如，消息有效负载是从“请求”触发器输出的“正文”内容：

   ![显示具有消息编码属性的“AS2 编码”操作的屏幕截图。](./media/logic-apps-enterprise-integration-as2/encode-as2-message-details.png)

   > [!TIP]
   > 如果在发送已签名或加密的消息时遇到问题，请考虑尝试不同的 SHA256 算法格式。 AS2 规范不提供有关 SHA256 格式的任何信息，因此每个提供者都使用其自己的实现或格式。

---

<a name="decode"></a>

## <a name="decode-as2-messages"></a>为 AS2 消息解码

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 AS2 操作的触发器或操作下，选择“新建步骤”。 此示例使用[请求](../connectors/connectors-native-reqres.md)触发器。

1. 在“选择操作”搜索框中，选择“所有” 。 在搜索框中输入 `as2 decode`。 选择名为“AS2 解码”的操作。

   ![该屏幕截图显示 Azure 门户、工作流设计器，以及处于选中状态的“AS2 解码”操作。](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 在 AS2 操作形状中，从先前的触发器或操作输出中选择“要编码的消息”和“消息头”属性的值 。

   在此示例中，可以选择“请求”触发器的输出。

   ![该屏幕截图显示 Azure 门户、工作流设计器以及“AS2 解码”操作（带有从“请求”触发器选择的“正文”和“头”输出）。](media/logic-apps-enterprise-integration-as2/as2-message-decode-details.png)

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 AS2 操作的触发器或操作下，选择“插入新步骤”（加号），然后选择“添加操作” 。

1. 在“选择操作”搜索框中，选择“Azure” 。 在搜索框中输入 `as2 decode`。 选择名为“解码 AS2 消息”的操作。

   ![该屏幕截图显示 Azure 门户、工作流设计器，以及处于选中状态的“解码 AS2 消息”操作。](./media/logic-apps-enterprise-integration-as2/select-decode-as2-message.png)

1. 当系统提示创建与集成帐户的连接时，请提供以下信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **连接名称** | 是 | 连接名称 |
   | **集成帐户** | 是 | 从可用的集成帐户列表中，选择要使用的帐户。 |
   ||||

   例如：

   ![显示“解码 AS2 消息”连接窗格的屏幕截图。](./media/logic-apps-enterprise-integration-as2/create-as2-decode-connection-standard.png)

1. 完成操作后，选择“创建”。

1. 在 AS2 详细信息窗格中，从先前的触发器或操作输出中选择“要编码的消息”和“消息头”属性的值 。

   在此示例中，可以选择“请求”触发器的输出。

   ![该屏幕截图显示 Azure 门户、工作流设计器以及“解码 AS2 消息”操作（带有从“请求”触发器选择的“正文”和“头”输出）。](media/logic-apps-enterprise-integration-as2/decode-as2-message-details.png)

---

## <a name="sample"></a>示例

若要尝试部署完全正常运行的逻辑应用和示例 AS2 (v2) 方案，请查看 [AS2 (v2) 逻辑应用模板和方案](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
