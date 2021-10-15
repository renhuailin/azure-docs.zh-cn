---
title: 在 B2B 工作流中交换 EDIFACT 消息
description: 使用 Azure 逻辑应用和 Enterprise Integration Pack 创建工作流，以便在合作伙伴之间交换 EDIFACT 消息。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 19fea6a0405d1fcc4cfbc9b1aa7647c52b4459b6
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401469"
---
# <a name="exchange-edifact-messages-using-workflows-in-azure-logic-apps"></a>使用 Azure 逻辑应用程序中的工作流交换 EDIFACT 消息

若要在使用 Azure 逻辑应用创建的工作流中发送和接收 EDIFACT 消息，请使用 EDIFACT 连接器，它提供用于支持和管理 EDIFACT 通信的触发器和操作。

本文介绍如何向现有的逻辑应用工作流添加 EDIFACT 编码和解码操作。 虽然你可以使用任何触发器来启动工作流，但示例使用的是[请求](../connectors/connectors-native-reqres.md)触发器。 有关 EDIFACT 连接器的触发器、操作和限制版本的详细信息，请查看连接器的 Swagger 文件中记录的[连接器参考页](/connectors/edifact/)。

![显示包含消息解码属性的“解码 EDIFACT 消息”操作的概述屏幕截图。](./media/logic-apps-enterprise-integration-edifact/overview-edifact-message-consumption.png)

## <a name="edifact-encoding-and-decoding"></a>EDIFACT 编码和解码

以下部分介绍可以使用 EDIFACT 编码和解码操作完成的任务。

### <a name="encode-to-edifact-message-action"></a>编码为 EDIFACT 消息操作

* 通过将发送方限定符和标识符与接收方限定符和标识符进行匹配来解析协议。

* 序列化电子数据交换 (EDI)，它将 XML 编码的消息转换为交换中的 EDI 事务集。

* 应用事务集标头和尾部段。

* 为每个传出交换生成交换控制编号、组控制编号和事务集控制编号。

* 替换有效负载数据中的分隔符。

* 验证 EDI 和特定于合作伙伴的属性，例如针对消息架构的事务集数据元素的架构、事务集数据元素，以及对事务集数据元素的扩展验证。

* 为每个事务集生成 XML 文档。

* 请求技术确认和/或功能确认（如果已配置）。

  * 作为技术确认，CONTRL 消息指示接收交换。

  * 作为功能确认，CONTRL 消息指示接受或拒绝收到的交换、组或消息，包括错误或不受支持的功能列表。

### <a name="decode-edifact-message-action"></a>解码 EDIFACT 消息操作

* 针对贸易合作伙伴协议验证信封。

* 通过将发送方限定符和标识符与接收方限定符和标识符进行匹配来解析协议。

* 当交换有多个基于协议的“接收设置”的事务时，将交换拆分为多个事务。

* 拆装交换。

* 验证电子数据交换 (EDI) 和特定于合作伙伴的属性，例如交换信封结构、针对控制架构的信封架构、针对消息架构的事务集数据元素的架构，以及对事务集数据元素的扩展验证。

* 验证交换、组和事务集控制编号不重复（如果已配置），例如：

  * 根据以前收到的交换检查交换控制编号。

  * 针对交换中的其他组控制编号检查组控制编号。

  * 针对该组中的其他事务集控制编号检查事务集控制编号。

* 将交换拆分为事务集，或保留整个交换，例如：

  * 将交换拆分为事务集 - 出错时暂停事务集。

    解码操作将交换拆分为事务集并分析每个事务集。 该操作仅将未通过验证的事务集输出到 `badMessages`，并将剩余事务集输出到 `goodMessages`。

  * 将交换拆分为事务集 - 出错时暂停交换。

    解码操作将交换拆分为事务集并分析每个事务集。 如果交换中的一个或多个事务集未能通过验证，该操作会将该交换中的所有事务集输出到 `badMessages`。

  * 保留交换 - 出错时暂停事务集。

    解码操作会保留交换并处理整个批处理交换。 该操作仅将未通过验证的事务集输出到 `badMessages`，并将剩余事务集输出到 `goodMessages`。

  * 保留交换 - 出错时暂停交换。

    解码操作会保留交换并处理整个批处理交换。 如果交换中的一个或多个事务集未能通过验证，该操作会将该交换中的所有事务集输出到 `badMessages`。

* 生成技术确认和/或功能确认（如果已配置）。

  * 技术确认或 CONTRL ACK，报告收到的完整交换的语法检查结果。

  * 确认接受或拒绝接收的交换或组的功能确认。

## <a name="connector-reference"></a>连接器参考

有关 EDIFACT 连接器的技术信息，请查看[连接器参考页](/connectors/edifact/)，其中介绍了连接器的 Swagger 文件中记录的触发器、操作和限制。 此外，若要了解在[多租户 Azure 逻辑应用、单租户 Azure 逻辑应用或集成服务环境 (ISE)](logic-apps-overview.md#resource-environment-differences) 中运行的工作流，请查看 [B2B 协议的消息大小限制](logic-apps-limits-and-config.md#b2b-protocol-limits)。 例如，在[集成服务环境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) 中，此连接器的 ISE 版本使用 [ISE 的 B2B 消息限制](logic-apps-limits-and-config.md#b2b-protocol-limits)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与逻辑应用资源位于同一个位置或 Azure 区域。

  * 使用[“逻辑应用（消耗）”资源类型](logic-apps-overview.md#resource-environment-differences)和 EDIFACT 操作时，逻辑应用资源不需要链接到你的集成帐户 。 但是，仍需使用此帐户来存储合作伙伴、协议和证书等项目，以及使用 EDIFACT、[X12](logic-apps-enterprise-integration-x12.md) 或 [AS2](logic-apps-enterprise-integration-as2.md) 操作。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

  * 使用[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-environment-differences)和 EDIFACT 操作时，工作流需要连接到你在添加 AS2 操作时直接从工作流创建的集成帐户 。

* 集成帐户中至少有两个[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)。 这两个合作伙伴的定义必须使用相同的业务标识限定符，在此方案中为“ZZZ - 双方约定”。

* 参与工作流的贸易合作伙伴之间的 [EDIFACT 协议](logic-apps-enterprise-integration-agreements.md)存在于你的集成帐户中。 每个协议需要指定主机和来宾合作伙伴。 你与其他合作伙伴之间的消息中的内容必须与协议类型匹配。

  > [!IMPORTANT]
  > EDIFACT 连接器仅支持 UTF-8 字符。 如果输出中包含意外的字符，请检查 EDIFACT 消息是否使用 UTF-8 字符集。

* 要在其中使用 EDIFACT 操作的逻辑应用资源和工作流。

  如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)。

<a name="encode"></a>

## <a name="encode-edifact-messages"></a>为 EDIFACT 消息编码

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 EDIFACT 操作的触发器或操作下，选择“新建步骤”。

1. 在“选择操作”搜索框中，选择“所有” 。 在搜索框中输入 `edifact encode`。 对于此示例，请选择名为“按协议名称编码为 EDIFACT 消息”的操作。

   ![显示 Azure 门户、工作流设计器，以及处于选中状态的“按协议名称编码为 EDIFACT 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-consumption.png)

   > [!NOTE]
   > 你可以选择“按标识编码为 EDIFACT 消息”操作，但是以后必须提供其他值，例如 EDIFACT 协议指定的“发送方标识符”和“接收方标识符”。 你还必须指定“要编码的 XML 消息”，该消息可以是触发器的输出，也可以是前面操作的输出。

1. 当系统提示创建与集成帐户的连接时，请提供以下信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **连接名称** | 是 | 连接名称 |
   | 集成帐户 | 是 | 从可用的集成帐户列表中，选择要使用的帐户。 |
   ||||

   例如：

   ![显示“按协议名称编码为 EDIFACT 消息”连接窗格的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-consumption.png)

1. 完成操作后，选择“创建”。

1. 在设计器上显示 EDIFACT 操作后，提供特定于此操作的以下属性的信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | EDIFACT 协议的名称 | 是 | 要使用的 EDIFACT 协议。 |
   | 要编码的 XML 消息 | 是 | EDIFACT 协议指定的消息发送方的业务标识符 |
   | 其他参数 | 否 | 此操作包括以下其他参数： <p>- 数据元素分隔符 <br>- 转义指示器 <br>- 组件分隔符 <br>- 重复分隔符 <br>- 段终止符 <br>- 段终止符后缀 <br>- 十进制指示器 <p>有关详细信息，请查看 [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact-message-settings.md)。 |
   ||||

   例如，XML 消息有效负载可以是从“请求”触发器输出的“正文”内容：

   ![显示带有消息编码属性的“按协议名称编码为 EDIFACT 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-consumption.png)

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 EDIFACT 操作的触发器或操作下，选择“插入新步骤”（加号），然后选择“添加操作” 。

1. 在“选择操作”搜索框中，选择“Azure” 。 在搜索框中输入 `edifact encode`。 选择名为“按协议名称编码为 EDIFACT 消息”的操作。

   ![显示 Azure 门户、工作流设计器，以及处于选中状态的“按协议名称编码为 EDIFACT 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-standard.png)

   > [!NOTE]
   > 你可以选择“按标识编码为 EDIFACT 消息”操作，但是以后必须提供其他值，例如 EDIFACT 协议指定的“发送方标识符”和“接收方标识符”。 你还必须指定“要编码的 XML 消息”，该消息可以是触发器的输出，也可以是前面操作的输出。

1. 当系统提示创建与集成帐户的连接时，请提供以下信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **连接名称** | 是 | 连接名称 |
   | 集成帐户 | 是 | 从可用的集成帐户列表中，选择要使用的帐户。 |
   ||||

   例如：

   ![显示“按参数名称编码为 EDIFACT 消息”连接窗格的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-standard.png)

1. 完成操作后，选择“创建”。

1. 设计器上显示了 EDIFACT 详细信息窗格后，请提供以下属性的信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | EDIFACT 协议的名称 | 是 | 要使用的 EDIFACT 协议。 |
   | 要编码的 XML 消息 | 是 | EDIFACT 协议指定的消息发送方的业务标识符 |
   | 其他参数 | 否 | 此操作包括以下其他参数： <p>- 数据元素分隔符 <br>- 转义指示器 <br>- 组件分隔符 <br>- 重复分隔符 <br>- 段终止符 <br>- 段终止符后缀 <br>- 十进制指示器 <p>有关详细信息，请查看 [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact-message-settings.md)。 |
   ||||

   例如，消息有效负载是从“请求”触发器输出的“正文”内容：

   ![显示带有消息编码属性的“按参数名称编码为 EDIFACT 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-standard.png)

---

<a name="decode"></a>

## <a name="decode-edifact-messages"></a>为 EDIFACT 消息解码

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 EDIFACT 操作的触发器或操作下，选择“新建步骤”。

1. 在“选择操作”搜索框中，选择“所有” 。 在搜索框中输入 `edifact encode`。 选择名为“解码 EDIFACT 消息”的操作。

1. 当系统提示创建与集成帐户的连接时，请提供以下信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **连接名称** | 是 | 连接名称 |
   | 集成帐户 | 是 | 从可用的集成帐户列表中，选择要使用的帐户。 |
   ||||

   例如：

   ![显示“解码 EDIFACT 消息”连接窗格的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-consumption.png)

1. 完成操作后，选择“创建”。

1. 在设计器上显示 EDIFACT 操作后，提供特定于此操作的以下属性的信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | 要解码的 EDIFACT 平面文件消息 | 是 | 要解码的 XML 平面文件消息。 |
   | 其他参数 | 否 | 此操作包括以下其他参数： <p>- 组件分隔符 <br>- 数据元素分隔符 <br>- 转义指示器 <br>- 重复分隔符 <br>- 段终止符 <br>- 段终止符后缀 <br>- 十进制指示器 <br>- 有效负载字符集 <br>- 段终止符后缀 <br>- 保留交换 <br>- 出错时挂起交换 <p>有关详细信息，请查看 [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact-message-settings.md)。 |
   ||||

   例如，要解码的 XML 消息有效负载可以是从“请求”触发器输出的“正文”内容：

   ![显示包含消息解码属性的“解码 EDIFACT 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-consumption.png)

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)的设计器中，打开你的逻辑应用资源和工作流。

1. 在设计器上，在要添加 EDIFACT 操作的触发器或操作下，选择“插入新步骤”（加号），然后选择“添加操作” 。

1. 在“选择操作”搜索框中，选择“Azure” 。 在搜索框中输入 `edifact encode`。 选择名为“解码 EDIFACT 消息”的操作。

   ![显示 Azure 门户、工作流设计器，以及处于选中状态的“解码 EDIFACT 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/select-decode-edifact-message-standard.png)

1. 当系统提示创建与集成帐户的连接时，请提供以下信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **连接名称** | 是 | 连接名称 |
   | 集成帐户 | 是 | 从可用的集成帐户列表中，选择要使用的帐户。 |
   ||||

   例如：

   ![显示“解码 EDIFACT 消息”连接窗格的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-standard.png)

1. 完成操作后，选择“创建”。

1. 设计器上显示了 EDIFACT 详细信息窗格后，请提供以下属性的信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | EDIFACT 协议的名称 | 是 | 要使用的 EDIFACT 协议。 |
   | 要编码的 XML 消息 | 是 | EDIFACT 协议指定的消息发送方的业务标识符 |
   | 其他参数 | 否 | 此操作包括以下其他参数： <p>- 数据元素分隔符 <br>- 转义指示器 <br>- 组件分隔符 <br>- 重复分隔符 <br>- 段终止符 <br>- 段终止符后缀 <br>- 十进制指示器 <p>有关详细信息，请查看 [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact-message-settings.md)。 |
   ||||

   例如，消息有效负载是从“请求”触发器输出的“正文”内容：

   ![显示包含消息解码属性的“解码 EDIFACT 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-standard.png)

---

## <a name="handle-unh25-segments-in-edifact-documents"></a>处理 EDIFACT 文档中的 UNH2.5 段

在 EDIFACT 文档中，[UNH2.5 段](logic-apps-enterprise-integration-edifact-message-settings.md#receive-settings-schemas)用于架构查找。 例如，在此示例 EDIFACT 消息中，UNH 字段为 `EAN008`：

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

若要处理 EDIFACT 文档或处理具有 UN2.5 段的 EDIFACT 消息，请执行以下步骤：

1. 更新或部署具有 UNH2.5 根节点名称的架构。

   例如，假设示例 UNH 字段的架构根名称为 `EFACT_D03B_ORDERS_EAN008`。 对于具有不同 UNH2.5 段的每个 `D03B_ORDERS`，需要部署单个架构。

1. 在 [Azure 门户](https://portal.azure.com)中，根据你使用的是“逻辑应用（消耗）”还是“逻辑应用（标准）”资源类型，分别将架构添加到集成帐户资源或逻辑应用资源中。

1. 无论是使用 EDIFACT 解码操作还是编码操作，请上传架构，并分别在 EDIFACT 协议的“接收设置”或“发送设置”中设置架构设置。

1. 若要编辑 EDIFACT 协议，请在“协议”窗格中选择协议。 在“协议”窗格的工具栏上，选择“编辑为 JSON”。

   * 在协议的 `receiveAgreement` 部分中，找到 `schemaReferences` 部分，并添加 UNH2.5 值。

     ![显示 Azure 门户的屏幕截图，其中 EDIFACT 协议的“receiveAgreement”部分在 JSON 编辑器中，且“schemaReferences”部分突出显示。](./media/logic-apps-enterprise-integration-edifact/agreement-receive-schema-references.png)

   * 在协议的 `sendAgreement` 部分中，找到 `schemaReferences` 部分，并添加 UNH2.5 值。

     ![显示 Azure 门户的屏幕截图，其中 EDIFACT 协议的“sendAgreement”部分在 JSON 编辑器中，且“schemaReferences”部分突出显示。](./media/logic-apps-enterprise-integration-edifact/agreement-send-schema-references.png)

## <a name="next-steps"></a>后续步骤

* [EDIFACT 消息设置](logic-apps-enterprise-integration-edifact-message-settings.md)
