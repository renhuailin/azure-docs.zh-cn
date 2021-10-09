---
title: 使用工作流交换 B2B 消息
description: 通过使用 Azure 逻辑应用和 Enterprise Integration Pack 创建工作流，在参与方之间交换消息。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/17/2021
ms.openlocfilehash: 6c9580a051a7473e7009cf8df8d2ea8759834683
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351168"
---
# <a name="exchange-b2b-messages-between-partners-using-workflows-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的工作流在合作伙伴之间交换 B2B 消息

如果你的集成帐户定义了参与方和协议，则你可以创建一个自动化的企业到企业 (B2B) 工作流，用于通过 Azure 逻辑应用在参与方之间交换消息。 工作流可以使用支持行业标准协议（如 AS2、X12、EDIFACT 和 RosettaNet）的连接器。 还可以包括由 [Azure 逻辑应用中的其他连接器](/connectors/connector-reference/connector-reference-logicapps-connectors)提供的操作，例如 Office 365 Outlook、SQL Server 和 Salesforce。

本文介绍如何创建一个示例逻辑应用工作流，该工作流可以通过使用“请求”触发器来接收 HTTP 请求，使用“AS2 解码”和“解码 X12”操作来解码消息内容，并通过使用“响应”操作返回响应。 本示例使用 Azure 门户中的工作流设计器，但你可以在 Visual Studio 中执行与工作流设计器类似的步骤。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用(消耗)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，集成帐户需要一个[指向逻辑应用资源的链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)，然后才能在工作流中使用项目。

  * 如果使用的是[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，集成帐户无需一个指向逻辑应用资源的链接，但仍需存储其他项目，例如合作伙伴、协议和证书，以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 或 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

  > [!NOTE]
  > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

* 集成帐户中至少有两个[参与方](logic-apps-enterprise-integration-partners.md)。 这两个合作伙伴的定义必须使用相同的业务标识限定符，即 AS2、X12、EDIFACT 或 RosettaNet。

* 在此工作流中使用的面向合作伙伴的 [AS2 协议和 X12 协议](logic-apps-enterprise-integration-agreements.md)。 每个协议都需要有主持人合作伙伴和来宾合作伙伴。

* 带有空白工作流的逻辑应用资源，你可以在其中添加[请求](../connectors/connectors-native-reqres.md)触发器，然后执行以下操作：

  * [AS2 解码](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)，它根据 AS2 解码操作是成功还是失败发送[响应](../connectors/connectors-native-reqres.md)

  * [解码 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md)

<a name="add-request-trigger"></a>

## <a name="add-the-request-trigger"></a>添加“请求”触发器

若要在此示例中启动工作流，请添加“请求”触发器。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)的工作流设计器中，打开逻辑应用资源和空白工作流。

1. 在设计器搜索框下选择“全部”（如果未选择）。 在搜索框中输入 `when a http request`。 选择名为“收到 HTTP 请求时”的“请求”触发器。

   ![显示 Azure 门户和多租户设计器的屏幕截图，其中选中了“收到 http 请求时”搜索框和“请求”触发器。](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-consumption.png)

1. 在触发器中，将“请求正文 JSON 架构”框留空。

   原因是触发器将接收平面文件格式的 X12 消息。

   ![显示多租户设计器和“请求”触发器属性的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/request-trigger-consumption.png)

1. 完成后，请在设计器工具栏上选择“保存”。

   此步骤将生成 HTTP POST URL，稍后将使用该 URL 来发送触发逻辑应用工作流的请求。

   ![显示多租户设计器以及为“请求”触发器生成的 URL 的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-consumption.png)

1. 复制并保存 URL 供以后使用。

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)的工作流设计器中，打开逻辑应用资源和空白工作流。

1. 在设计器中，选择“选择操作”。 在搜索框下，选择“内置”（如果未选择）。 在搜索框中输入 `when a http request`。 选择名为“收到 HTTP 请求时”的“请求”触发器。

   ![显示 Azure 门户和单租户设计器的屏幕截图，搜索框中显示“收到 http 请求时”并且选中了“请求”触发器。](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-standard.png)

1. 在触发器中，将“请求正文 JSON 架构”框留空。

   原因是触发器将接收平面文件格式的 X12 消息。

   ![显示单租户设计器和“请求”触发器属性的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/request-trigger-standard.png)

1. 完成后，请在设计器工具栏上选择“保存”。

   此步骤将生成 HTTP POST URL，稍后将使用该 URL 来发送触发逻辑应用工作流的请求。

   ![显示单租户设计器以及为“请求”触发器生成的 URL 的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-standard.png)

1. 复制并保存 URL 供以后使用。

---

<a name="add-decode-as2-trigger"></a>

## <a name="add-the-decode-as2-action"></a>添加解码 AS2 操作

现在，请为此示例添加 B2B 操作，该示例使用 AS2 和 X12 操作。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在触发器下，选择“新建步骤”。

   > [!TIP]
   > 若要隐藏“请求”触发器详细信息，请选择触发器的标题栏。

   ![显示多租户设计器和触发器的屏幕截图，其中选择了“新建步骤”。](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-consumption.png)

1. 在“选择操作”搜索框中，选择“所有”（如果未选择）。 在搜索框中，输入 `as2` 并选择“AS2 解码”。

   ![显示多租户设计器的屏幕截图，其中选择了“AS2 解码”操作。](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-consumption.png)

1. 在操作的“要解码的消息”属性中，输入希望 AS2 操作解码的输入，即“请求”触发器的 `body` 输出。 可通过从动态内容列表中选择或作为表达式，以多种方式将此内容指定为操作的输入：

   * 若要从显示可用触发器输出的列表中进行选择，请在“要解码的消息”框中单击。 显示动态内容列表后，在“收到 HTTP 请求时”下，选择“正文”属性值，例如： 

     ![显示多租户设计器的屏幕截图，其中选择了动态内容列表和“正文”属性。](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-consumption.png)

     > [!TIP]
     > 如果未显示触发器输出，请在动态属性列表中的“收到 HTTP 请求时”下，选择“查看更多”。

   * 若要输入引用触发器的 `body` 输出的表达式，请在“要解码的消息”框中单击。 显示动态内容列表后，选择“表达式”。 在表达式编辑器中，输入以下表达式，然后选择“确定”：

     `triggerOutputs()['body']`

     或者，在“要解码的消息”框中，直接输入以下表达式：

     `@triggerBody()`

     表达式将解析为“正文”标记。

     ![显示多租户设计器的屏幕截图，其中包含已解析的“正文”属性输出。](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-consumption.png)

1. 在操作的“消息标头”属性中，输入 AS2 操作所需的任何标头，这些标头位于“请求”触发器的 `headers` 输出中。

   1. 若要输入引用触发器 `headers` 输出的表达式，请选择“将消息标头切换到文本模式”。

      ![显示多租户设计器的屏幕截图，其中选择了“将消息标头切换为文本模式”。](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-consumption.png)

   1. 在“消息标头”框中单击。 显示动态内容列表后，选择“表达式”。 在表达式编辑器中，输入以下表达式，然后选择“确定”：

      `triggerOutputs()['Headers']`

      在“AS2 解码”操作中，表达式现在显示为一个标记：

      ![显示多租户设计器和“消息标头”框以及“@triggerOutputs()['Headers']”标记的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-consumption.png)

   1. 若要使该表达式标记解析为“标头”标记，请在设计器视图与代码视图之间进行切换。 执行此步骤后，“AS2 解码”操作如以下示例所示：

      ![显示多租户设计器和来自触发器的已解析标头输出的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-consumption.png)

### <a name="standard"></a>[标准](#tab/standard)

1. 在触发器下，选择“插入新步骤”（加号），然后选择“添加操作”。

   ![显示单租户设计器和触发器的屏幕截图，其中选择了加号。](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-standard.png)

1. 在“选择操作”搜索框下，选择“Azure”（如果尚未选择）。 在搜索框中，输入 `as2` 并选择“解码 AS2 消息”。

   ![显示单租户设计器并选择了“解码 AS2 消息”操作的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-standard.png)

1. 当系统提示创建与集成帐户的连接时，请提供一个用于连接的名称，选择集成帐户，然后选择“创建”。

1. 在操作的“正文”属性中，输入希望 AS2 操作解码的输入，即“请求”触发器的 `body` 输出。 可通过从动态内容列表中选择或作为表达式，以多种方式将此内容指定为操作的输入：

   * 若要从显示可用触发器输出的列表进行选择，请单击“正文”属性框。 显示动态内容列表后，在“收到 HTTP 请求时”下，选择“正文”属性值，例如： 

     ![显示单租户设计器的屏幕截图，其中选择了动态内容列表和“正文”属性。](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-standard.png)

     > [!TIP]
     > 如果未显示触发器输出，请在动态属性列表中的“收到 HTTP 请求时”下，选择“查看更多”。

   * 若要输入引用触发器 `body` 输出的表达式，请单击“正文”属性框。 显示动态内容列表后，选择“表达式”。 在表达式编辑器中，输入以下表达式，然后选择“确定”：

     `triggerOutputs()['body']`

     或者，在“正文”属性框中，直接输入以下表达式：

     `@triggerBody()`

     表达式将解析为“正文”标记。

     ![显示单租户设计器的屏幕截图，其中包含已解析的“正文”属性输出。](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-standard.png)

1. 在“标头”属性框中，输入 AS2 操作所需的任何标头，这些标头位于“请求”触发器的 `headers` 输出中。

   1. 若要输入引用触发器 `headers` 输出的表达式，请选择“将消息标头切换到文本模式”。

      ![显示单租户设计器的屏幕截图，其中选择了“将消息标头切换为文本模式”。](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-standard.png)

   1. 单击“标头”属性框。 显示动态内容列表后，选择“表达式”。 在表达式编辑器中，输入以下表达式，然后选择“确定”：

      `triggerOutputs()['Headers']`

      在“解码 AS2 消息”操作中，表达式现在显示为一个标记：

      ![显示单租户设计器和“标头”属性以及“@triggerOutputs()['Headers']”标记的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-standard.png)

   1. 若要使该表达式标记解析为“标头”标记，请在设计器视图与代码视图之间进行切换。 执行此步骤后，“AS2 解码”操作如以下示例所示：

      ![显示单租户设计器和来自触发器的已解析标头输出的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-standard.png)

---

<a name="add-response-action"></a>

## <a name="add-the-response-action-as-a-message-receipt"></a>添加“响应”操作作为消息回执

若要通知参与方已收到消息，可以使用“条件和响应”操作返回包含 AS2 消息处置通知 (MDN) 的响应。 通过紧接在 AS2 操作之后添加这些操作，如果 AS2 操作成功，则逻辑应用工作流可以继续处理。 否则，如果 AS2 操作失败，逻辑应用工作流将停止处理。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在“AS2 解码”操作下，选择“新建步骤”。 

1. 在“选择操作”搜索框下，选择“内置”（如果尚未选择）。 在搜索框中输入 `condition`。 选择“条件”操作。

   ![显示多租户设计器和“条件”操作的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/add-condition-action-consumption.png)

   此时会显示条件形状，包括确定是否满足条件的路径。

   ![显示多租户设计器和路径为空的条件形状的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/added-condition-action-consumption.png)

1. 现在请指定要评估的条件。 在“选择值”框中，输入以下表达式：

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   在中间框中，确保比较操作设置为 `is equal to`。 在右侧框中输入 `Expected` 值。

1. 保存逻辑应用工作流。 若要使该表达式解析为此标记，请在设计器视图与代码视图之间进行切换。

   ![显示多租户设计器和带有操作的条件形状的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-consumption.png)

1. 现在，根据“AS2 解码”操作是否成功指定要返回的响应。

   1. 对于“AS2 解码”操作成功的情况，请在“True”形状中选择“添加操作”。 在“选择操作”搜索框中，输入 `response`，然后选择“响应”。

      ![显示多租户设计器和“响应”操作的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/select-response-consumption.png)

   1. 若要从“AS2 解码”操作的输出访问 AS2 MDN，请指定以下表达式：

      * 在“响应”操作的“标头”属性中，输入以下表达式：

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * 在“响应”操作的“正文”属性中，输入以下表达式：

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 若要使该表达式解析为标记，请在设计器视图与代码视图之间进行切换：

      ![显示访问 AS2 MDN 的多租户设计器和解析表达式的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-consumption.png)

   1. 对于“AS2 解码”操作失败的情况，请在“False”形状中选择“添加操作”。 在“选择操作”搜索框中，输入 `response`，然后选择“响应”。 设置“响应”操作以返回所需的状态和错误。

1. 保存逻辑应用工作流。

### <a name="standard"></a>[标准](#tab/standard)

1. 在“解码 AS2 消息”操作下，选择“插入新步骤”（加号），然后选择“添加操作”。

1. 在“选择操作”搜索框下，选择“内置”（如果尚未选择）。 在搜索框中输入 `condition`。 选择“条件”操作。

   ![显示单租户设计器并选择了“条件”操作的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/add-condition-action-standard.png)

   此时会显示条件形状，包括确定是否满足条件的路径。

   ![显示单租户设计器和路径为空的“条件”形状的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/added-condition-action-standard.png)

1. 现在请指定要评估的条件。 选择“条件”形状，以便显示详细信息面板。 在“选择值”框中，输入以下表达式：

   `@body('Decode_AS2_message')?['AS2Message']?['MdnExpected']`

   在中间框中，确保比较操作设置为 `is equal to`。 在右侧框中输入 `Expected` 值。

1. 保存逻辑应用工作流。 若要使该表达式解析为此标记，请在设计器视图与代码视图之间进行切换。

   ![显示单租户设计器和带有操作的条件形状的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-standard.png)

1. 现在，根据“解码 AS2 消息”操作是否成功指定要返回的响应。

   1. 对于“解码 AS2 消息”操作成功的情况，请在“True”形状中选择加号，然后选择“添加操作”。 在“添加操作”窗格上的“选择操作”搜索框中，输入 `response` 并选择“响应”。

      ![显示单租户设计器和“响应”操作的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/select-response-standard.png)

   1. 若要从“AS2 解码”操作的输出访问 AS2 MDN，请指定以下表达式：

      * 在“响应”操作的“标头”属性中，输入以下表达式：

        `@body('Decode_AS2_message')?['OutgoingMdn']?['OutboundHeaders']`

      * 在“响应”操作的“正文”属性中，输入以下表达式：

        `@body('Decode_AS2_message')?['OutgoingMdn']?['Content']`

   1. 若要使该表达式解析为标记，请在设计器视图与代码视图之间进行切换：

      ![显示访问 AS2 MDN 的单租户设计器和解析表达式的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-standard.png)

   1. 对于“解码 AS2 消息”操作失败的情况，请在“False”形状中选择加号，然后选择“添加操作”。 在“添加操作”窗格上的“选择操作”搜索框中，输入 `response` 并选择“响应”。 设置“响应”操作以返回所需的状态和错误。

1. 保存逻辑应用工作流。

---

<a name="add-decode-x12-action"></a>

## <a name="add-the-decode-x12-message-action"></a>添加“解码 X12 消息”操作

现在添加“解码 X12 消息”操作。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在“响应”操作下，选择“添加操作”。 

1. 在“选择操作”下的搜索框中，输入 `x12 decode` 并选择“解码 X12 消息”。

   ![显示多租户设计器的屏幕截图，其中选择了“解码 X12 消息”操作。](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-consumption.png)

1. 如果 X12 操作提示输入连接信息，请提供连接的名称，选择要使用的集成帐户，然后选择“创建”。

   ![显示多租户设计器和与集成帐户的连接的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-consumption.png)

1. 现在请指定 X12 操作的输入。 本示例使用 AS2 操作的输出（消息内容），但请注意，此内容采用 JSON 对象格式且已经过 base64 编码。 因此，必须将此内容转换为字符串。

   在“要解码的 X12 平面文件消息”框中，输入以下表达式来转换 AS2 输出：

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

1. 保存逻辑应用工作流。 若要使该表达式解析为此标记，请在设计器视图与代码视图之间进行切换。

    ![显示多租户设计器以及从 base64 编码的内容转换为字符串的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-consumption.png)

1. 保存逻辑应用工作流。

   如果需要在此逻辑应用工作流中添加其他步骤（例如，解码消息内容并以 JSON 对象格式输出该内容），请继续向逻辑应用工作流添加所需的操作。

### <a name="standard"></a>[标准](#tab/standard)

1. 在“响应”操作下，选择加号，然后选择“添加操作”。 在“添加操作”窗格上的“选择操作”搜索框下，选择“Azure”（如果尚未选择）。 在搜索框中，输入 `x12 decode` 并选择“解码 X12 消息”。

   ![显示单租户设计器的屏幕截图，其中选择了“解码 X12 消息”操作。](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-standard.png)

1. 如果 X12 操作提示输入连接信息，请提供连接的名称，选择要使用的集成帐户，然后选择“创建”。

   ![显示单租户设计器和与集成帐户的连接的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-standard.png)

1. 现在请指定 X12 操作的输入。 本示例使用 AS2 操作的输出（消息内容），但请注意，此内容采用 JSON 对象格式且已经过 base64 编码。 因此，必须将此内容转换为字符串。

   在“要解码的 X12 平面文件消息”框中，输入以下表达式来转换 AS2 输出：

   `@base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])`

1. 保存逻辑应用工作流。 若要使该表达式解析为此标记，请在设计器视图与代码视图之间进行切换。

    ![显示单租户设计器以及从 base64 编码的内容转换为字符串的屏幕截图。](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-standard.png)

1. 再次保存逻辑应用工作流。

   如果需要在此逻辑应用工作流中添加其他步骤（例如，解码消息内容并以 JSON 对象格式输出该内容），请继续向逻辑应用工作流添加所需的操作。

---

现已完成 B2B 逻辑应用工作流的设置。 在真实应用中，可能需要在业务线 (LOB) 应用或数据存储中存储解码的 X12 数据。 例如，请查看以下文档：

* [从 Azure 逻辑应用连接到 SAP 系统](logic-apps-using-sap-connector.md)

* [使用 SSH 与 Azure 逻辑应用监视、创建和管理 SFTP 文件](../connectors/connectors-sftp-ssh.md)

若要连接自己的 LOB 应用并在逻辑应用中使用这些 API，可以添加其他操作或[编写自定义 API](logic-apps-create-api-app.md)。

## <a name="next-steps"></a>后续步骤

* [接收和响应传入的 HTTPS 调用](../connectors/connectors-native-reqres.md)
* [交换用于 B2B 企业集成的 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [交换用于 B2B 企业集成的 X12 消息](../logic-apps/logic-apps-enterprise-integration-x12.md)
* 详细了解 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
