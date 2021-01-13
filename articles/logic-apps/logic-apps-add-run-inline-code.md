---
title: 使用内联代码添加和运行代码片段
description: 了解如何通过对使用 Azure 逻辑应用创建的自动任务和工作流使用内联代码操作来创建和运行代码片段
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3f88fa38d62778bc3c4c1e29571d1d0ae4eeb5ff
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179599"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用内联代码添加和运行代码片段

若要在逻辑应用中运行一段代码，可以添加内置内联代码操作，作为逻辑应用工作流中的一个步骤。 当你想要运行适合此方案的代码时，此操作最有效：

* 在 JavaScript 中运行。 即将推出更多语言。

* 在5秒或更少的时间内完成运行。

* 处理最大大小为 50 MB 的数据。

* 不需要使用 [**变量** 操作](../logic-apps/logic-apps-create-variables-store-values.md)，这些操作尚不受支持。

* 使用 Node.js 版本8.11.1。 有关详细信息，请参阅 [标准内置对象](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)。

  > [!NOTE]
  > `require()`用于运行 JavaScript 的内联代码操作不支持该函数。

此操作运行代码片段，并将该代码段的输出作为名为的标记返回 `Result` 。 可以在逻辑应用的工作流中将此标记与后续操作一起使用。 对于想要为代码创建函数的其他方案，请尝试 [通过 Azure Functions 而不是逻辑应用来创建和调用函数](../logic-apps/logic-apps-azure-functions.md) 。

本文介绍了在工作或学校帐户中收到新电子邮件时，将触发示例逻辑应用。 代码段提取并返回电子邮件正文中显示的任何电子邮件地址。

![显示示例逻辑应用的屏幕截图](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要在其中添加代码段（包括触发器）的逻辑应用。 如果你没有逻辑应用，请参阅[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   本主题中的示例使用在 **收到新电子邮件时** 命名的 Office 365 Outlook 触发器。

* 链接到逻辑应用的 [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 。

  * 请确保使用适合你的用例或方案的集成帐户。

    例如， [免费层](../logic-apps/logic-apps-pricing.md#integration-accounts) 集成帐户仅适用于探索方案和工作负载，而不是生产方案，使用和吞吐量有限， (SLA) 不支持服务级别协议。 其他层会产生成本，但包含 SLA 支持，提供更高的吞吐量和更高的限制。 了解有关集成帐户 [层](../logic-apps/logic-apps-pricing.md#integration-accounts)、 [定价](https://azure.microsoft.com/pricing/details/logic-apps/)和 [限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)的详细信息。

   * 如果不想使用集成帐户，可以尝试使用 [Azure 逻辑应用预览](logic-apps-overview-preview.md)，并从 **逻辑应用 (预览)** 资源类型创建逻辑应用。

     在 Azure 逻辑应用预览中， **内联代码** 现在命名为 **内联代码操作** 以及以下其他差异：

     * **执行 JavaScript 代码** 现在命名为 **"运行内联 JavaScript"**。

     * 如果你使用的是 macOS 或 Linux，则在 Visual Studio Code 中使用 Azure 逻辑应用 (预览) 扩展时，内联代码操作操作当前不可用。

     * 内联代码操作操作具有 [更新的限制](logic-apps-overview-preview.md#inline-code-limits)。

     你可以从以下任一选项开始：

     * [使用 Azure 门户](create-stateful-stateless-workflows-azure-portal.md) **(预览)** 资源类型创建逻辑应用。

     * [使用 Visual Studio Code 和 Azure 逻辑应用 (预览版) 扩展](create-stateful-stateless-workflows-visual-studio-code.md)为逻辑应用创建项目

## <a name="add-inline-code"></a>添加内联代码

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在设计器中，选择要在逻辑应用的工作流中添加内联代码操作的位置。

   * 若要在工作流末尾添加操作，请选择 " **新建步骤**"。

   * 若要在步骤之间添加操作，请将鼠标指针移到连接这些步骤的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

   此示例在 Office 365 Outlook 触发器下添加内联代码操作。

   ![在触发器下添加新步骤](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. 在“选择操作”下的搜索框中输入 `inline code`。 从 "操作" 列表中，选择名为 " **执行 JavaScript 代码**" 的操作。

   ![选择 "执行 JavaScript 代码" 操作](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   该操作将显示在设计器中，并且在默认情况下包含一些示例代码（包括 `return` 语句）。

   ![带有默认示例代码的内联代码操作](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. 在 " **代码** " 框中，删除示例代码，然后输入代码。 编写要放入方法中的代码，但不使用方法签名。

   如果开始键入识别关键字，将显示自动完成列表，以便您可以从可用关键字中进行选择，例如：

   ![关键字自动完成列表](./media/logic-apps-add-run-inline-code/auto-complete.png)

   此代码段示例首先创建一个变量，该变量存储 *正则表达式*，该表达式指定输入文本中要匹配的模式。 然后，该代码将创建一个变量，用于存储触发器中的电子邮件正文数据。

   ![创建变量](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   若要使触发器和前一操作的结果更易于引用，当光标位于 **代码** 框中时，将显示动态内容列表。 在此示例中，列表显示了触发器的可用结果，其中包括 **正文** 标记，你现在可以选择它。

   选择 **正文** 标记后，内联代码操作将令牌解析为 `workflowContext` 引用电子邮件的 `Body` 属性值的对象：

   ![选择结果](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   在 " **代码** " 框中，代码段可以使用只读 `workflowContext` 对象作为输入。 此对象包括一些属性，这些属性可使您的代码访问工作流中的触发器和前一操作的结果。 有关详细信息，请参阅本主题后面的 [代码中的引用触发器和操作结果](#workflowcontext) 。

   > [!NOTE]
   > 如果你的代码段引用使用点 ( ) 运算符的操作名称，则必须将这些操作名称添加到 [**操作** 参数](#add-parameters)。 这些引用还必须将操作名称括在方括号 ( [] ) 和引号中，例如：
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   内联代码操作无需 `return` 语句，但 `return` 可在后续操作中通过 **结果** 令牌引用语句中的结果。 例如，代码段通过调用函数来返回结果 `match()` ，该函数可在电子邮件正文中查找正则表达式的匹配项。 " **撰写** " 操作使用 **结果** 令牌引用内联代码操作的结果，并创建单个结果。

   ![完成的逻辑应用](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 完成后，保存逻辑应用。

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>代码中的引用触发器和操作结果

`workflowContext`对象具有此结构，其中包括 `actions` 、和子 `trigger` `workflow` 属性：

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

此表包含有关这些子属性的详细信息：

| properties | 类型 | 说明 |
|----------|------|-------|
| `actions` | 对象集合 | 在运行代码段之前运行的操作的结果对象。 每个对象都有一个 *键-值* 对，其中键是操作的名称，并且值等效于调用 [ ( # A1 函数的操作](../logic-apps/workflow-definition-language-functions-reference.md#actions) `@actions('<action-name>')` 。 操作的名称使用的操作名称与基础工作流定义中使用的操作名称相同，后者使用下划线 (_) 替换操作名称中的空格 ( "" ) 。 此对象提供对当前工作流实例运行的操作属性值的访问。 |
| `trigger` | 对象 | 触发器中的 Result 对象并等效于调用 [ ( # A1 函数的触发器](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 此对象提供对当前工作流实例运行的触发器属性值的访问。 |
| `workflow` | 对象 | 工作流对象和等效于调用 [工作流 ( # A1 函数](../logic-apps/workflow-definition-language-functions-reference.md#workflow)。 此对象提供对当前工作流实例运行的工作流属性值（如工作流名称、运行 ID 等）的访问权限。 |
|||

在本主题的示例中， `workflowContext` 对象具有您的代码可以访问的以下属性：

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>添加参数

在某些情况下，您可能必须显式要求内联代码操作包含触发器的结果或代码通过添加 **触发器** 或 **操作** 参数作为依赖项引用的特定操作。 对于在运行时找不到引用的结果的情况，此选项很有用。

> [!TIP]
> 如果你计划重用你的代码，则通过使用 " **代码** " 框添加对属性的引用，使你的代码包含解析的令牌引用，而不是作为显式依赖项添加触发器或操作。

例如，假设你有引用 Office 365 Outlook connector 的 "**发送审批电子邮件**" 操作的 **SelectedOption** 结果的代码。 在创建时，逻辑应用引擎会分析你的代码，以确定是否已引用任何触发器或操作结果并自动包含这些结果。 在运行时，如果遇到所引用的触发器或操作结果在指定对象中不可用的错误， `workflowContext` 则可以将该触发器或操作作为显式依赖项添加。 在此示例中，将添加 **操作** 参数，并指定内联代码操作显式包括 " **发送审批电子邮件** " 操作的结果。

若要添加这些参数，请打开 " **添加新参数** " 列表，然后选择所需的参数：

   ![添加参数](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 参数 | 说明 |
   |-----------|-------------|
   | **操作** | 包括先前操作的结果。 请参阅 [包括操作结果](#action-results)。 |
   | **触发器** | 包含触发器的结果。 请参阅 [包含触发器结果](#trigger-results)。 |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>包含触发器结果

如果选择 " **触发器**"，系统将提示您是否包含触发器结果。

* 从 **触发器** 列表中，选择 **"是"**。

<a name="action-results"></a>

### <a name="include-action-results"></a>包含操作结果

如果你选择 " **操作**"，系统会提示你输入要添加的操作。 但是，在开始添加操作之前，需要在逻辑应用的基础工作流定义中显示的操作名称的版本。

* 此功能不支持变量、循环和迭代索引。

* 逻辑应用的工作流定义中的名称使用下划线 (_) ，而不是空格。

* 对于使用点运算符 )  ( 的操作名称，请包括这些运算符，例如：

  `My.Action.Name`

1. 在设计器工具栏上，选择 " **代码视图**"，然后在属性中搜索 `actions` "操作名称"。

   例如， `Send_approval_email_` 是 " **发送审批电子邮件** " 操作的 JSON 名称。

   ![在 JSON 中查找操作名称](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 若要返回设计器视图，请在 "代码视图" 工具栏上选择 " **设计器**"。

1. 若要添加第一个操作，请在 " **操作项目-1** " 框中，输入操作的 JSON 名称。

   ![输入第一个操作](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 若要添加其他操作，请选择 " **添加新项**"。

## <a name="reference"></a>参考

有关使用工作流定义语言在逻辑应用的基础工作流定义中 **执行 JavaScript 代码** 操作的结构和语法的详细信息，请参阅此操作的 [参考部分](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 逻辑应用的连接器](../connectors/apis-list.md)
