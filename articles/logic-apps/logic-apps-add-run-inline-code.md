---
title: 使用内联代码添加并运行代码片段
description: 了解如何使用内联代码操作，为通过 Azure 逻辑应用创建的自动化任务和工作流创建并运行代码片段
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3f88fa38d62778bc3c4c1e29571d1d0ae4eeb5ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179599"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用内联代码添加并运行代码片段

如果你想要在逻辑应用中运行一段代码，可以添加内置的内联代码操作，作为逻辑应用工作流中的一个步骤。 如果你想要运行符合以下方案的代码，最适合使用此操作：

* 在 JavaScript 中运行。 即将支持更多语言。

* 在 5 秒或更短时间内完成运行。

* 处理大小高达 50 MB 的数据。

* 不需要使用目前不受支持的[变量操作](../logic-apps/logic-apps-create-variables-store-values.md)。

* 使用 Node.js 版本 8.11.1。 有关详细信息，请参阅[标准内置对象](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)。

  > [!NOTE]
  > 内联代码操作不支持使用 `require()` 函数运行 JavaScript。

此操作将运行代码片段，并将该片段的输出作为名为 `Result` 的标记返回。 可将此标记用于逻辑应用工作流中的后续操作。 对于你要为代码创建函数的其他方案，请尝试[通过 Azure Functions 创建并调用函数](../logic-apps/logic-apps-azure-functions.md)，而不要在逻辑应用中执行此操作。

在本文中，当某个工作或学校帐户收到新电子邮件时，将触发示例逻辑应用。 代码片段将提取并返回电子邮件正文中显示的任何电子邮件地址。

![显示示例逻辑应用的屏幕截图](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要在其中添加代码片段（包括触发器）的逻辑应用。 如果你没有逻辑应用，请参阅[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   本主题中的示例使用名为“收到新电子邮件时”的 Office 365 Outlook 触发器。

* 一个已链接到你的逻辑应用的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

  * 请确保使用适合你的用例或方案的集成帐户。

    例如，[免费层](../logic-apps/logic-apps-pricing.md#integration-accounts)集成帐户仅适用于探索方案和工作负载，而不适用于生产方案，另外，其使用量和吞吐量有限制，且不附带服务级别协议 (SLA) 的支持。 其他层会产生成本，但附带 SLA 支持，提供更高的吞吐量和更高的限制。 详细了解集成帐户[层](../logic-apps/logic-apps-pricing.md#integration-accounts)、[定价](https://azure.microsoft.com/pricing/details/logic-apps/)和[限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。

   * 如果你不想要使用集成帐户，可以尝试使用 [Azure 逻辑应用预览版](logic-apps-overview-preview.md)，并从“逻辑应用(预览版)”资源类型创建逻辑应用。

     在 Azure 逻辑应用预览版中，“内联代码”现已命名为“内联代码操作”，另外，该版本中还做出了以下变化 ：

     * “执行 JavaScript 代码”现已命名为“运行内联 JavaScript” 。

     * 如果你使用的是 macOS 或 Linux，在 Visual Studio Code 中使用“Azure 逻辑应用(预览版)”扩展时，“内联代码操作”操作目前不可用。

     * “内联代码操作”操作的[限制已更新](logic-apps-overview-preview.md#inline-code-limits)。

     可以从以下任一选项开始：

     * [使用 Azure 门户](create-stateful-stateless-workflows-azure-portal.md)从“逻辑应用(预览版)”资源类型创建逻辑应用。

     * [使用 Visual Studio Code 和“Azure 逻辑应用(预览版)”扩展](create-stateful-stateless-workflows-visual-studio-code.md)为逻辑应用创建项目

## <a name="add-inline-code"></a>添加内联代码

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在设计器中，选择要在逻辑应用工作流中的哪个位置添加内联代码操作。

   * 若要在工作流末尾添加该操作，请选择“新建步骤”。

   * 若要在步骤之间添加该操作，请将鼠标指针移到连接这些步骤的箭头之上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

   此示例在 Office 365 Outlook 触发器下添加内联代码操作。

   ![在触发器下添加新步骤](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. 在“选择操作”下的搜索框中输入 `inline code`。 在“操作”列表中，选择名为“执行 JavaScript 代码”的操作。

   ![选择“执行 JavaScript 代码”操作](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   该操作将显示在设计器中，并且默认情况下包含一些示例代码（包括 `return` 语句）。

   ![包含默认示例代码的内联代码操作](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. 在“代码”框中删除示例代码，然后输入你的代码。 编写要放入方法中的但不带方法签名的代码。

   当你开始键入已识别的关键字时，会显示自动完成列表，以方便你从可用关键字中进行选择，例如：

   ![关键字自动完成列表](./media/logic-apps-add-run-inline-code/auto-complete.png)

   此示例代码片段首先创建一个变量用于存储某个正则表达式，该表达式指定要在输入文本中匹配的模式。 然后，该代码创建一个变量用于存储触发器中的电子邮件正文数据。

   ![创建变量](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   为使该触发器和先前操作的结果更易于引用，当光标置于“代码”框内时，会显示动态内容列表。 对于此示例，列表显示了触发器提供的结果，包括你现在可以选择的 Body 标记。

   选择 Body 标记后，内联代码操作会将该标记解析为引用电子邮件的 `Body` 属性值的 `workflowContext` 对象：

   ![选择结果](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   在“代码”框中，你的代码片段可以使用只读的 `workflowContext` 对象作为输入。 此对象包含可供代码用来访问工作流中触发器和先前操作的结果的属性。 有关详细信息，请参阅本主题稍后的[在代码中引用触发器和操作结果](#workflowcontext)。

   > [!NOTE]
   > 如果代码片段引用了使用点 (.) 运算符的操作名称，则必须将这些操作名称添加到 [Actions 参数](#add-parameters)。 这些引用还必须将操作名称括在方括号 ([]) 和引号中，例如：
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   内联代码操作无需 `return` 语句，但在后续操作中可以通过 Result 标记引用 `return` 语句的结果。 例如，该代码片段通过调用 `match()` 函数来返回结果，该函数根据正则表达式查找电子邮件正文中的匹配项。 “撰写”操作使用 Result 标记引用内联代码操作的结果，并创建单个结果 。

   ![完成的逻辑应用](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 完成后，保存逻辑应用。

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>在代码中引用触发器和操作结果

`workflowContext` 对象采用以下结构，其中包含 `actions`、`trigger` 和 `workflow` 子属性：

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

下表包含了有关这些子属性的详细信息：

| 属性 | 类型 | 说明 |
|----------|------|-------|
| `actions` | 对象集合 | 在运行代码片段之前运行的操作的结果对象。 每个对象包含一个键值对，其中的键是操作名称，值等效于结合 `@actions('<action-name>')` 调用 [actions() 函数](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 该操作名称与基础工作流定义中使用的操作名称相同，只是将此定义的操作名称中的空格 (" ") 替换成了下划线 (_)。 此对象提供对当前工作流实例运行中的操作属性值的访问。 |
| `trigger` | 对象 | 触发器中的结果对象，等效于调用 [trigger() 函数](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 此对象提供对当前工作流实例运行中的触发器属性值的访问。 |
| `workflow` | 对象 | 工作流对象，等效于调用 [workflow() 函数](../logic-apps/workflow-definition-language-functions-reference.md#workflow)。 此对象提供对当前工作流实例运行中的工作流属性值（例如工作流名称、运行 ID 等）的访问。 |
|||

在本主题的示例中，`workflowContext` 对象包含代码可以访问的以下属性：

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

在某些情况下，可能必须通过添加 Trigger 或 Actions 参数，显式要求内联代码操作包含触发器的结果，或者由代码作为依赖项引用的特定操作 。 对于在运行时找不到引用结果的情况，此选项很有用。

> [!TIP]
> 如果你打算重用代码，请使用“代码”框添加对属性的引用，使代码包含已解析的标记引用，而不要将触发器或操作添加为显式依赖项。

例如，假设你的代码引用了 Office 365 Outlook 连接器的“发送审批电子邮件”操作的 SelectedOption 结果 。 在创建时，逻辑应用引擎将分析该代码，以确定是否引用了任何触发器或操作结果，并自动包含这些结果。 在运行时，如果遇到错误，指出引用的触发器或操作结果在指定的 `workflowContext` 对象中不可用，则你可以将该触发器或操作添加为显式依赖项。 在此示例中，你将添加 Actions 参数，并指定内联代码操作显式包含“发送审批电子邮件”操作的结果 。

若要添加这些参数，请打开“添加新参数”列表，然后选择所需参数：

   ![添加参数](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 参数 | 说明 |
   |-----------|-------------|
   | **操作** | 包含先前操作的结果。 请参阅[包含操作结果](#action-results)。 |
   | **触发器** | 包含触发器的结果。 请参阅[包含触发器结果](#trigger-results)。 |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>包含触发器结果

如果你选择“触发器”，则系统会提示是否要包含触发器结果。

* 在“触发器”列表中选择“是” 。

<a name="action-results"></a>

### <a name="include-action-results"></a>包含操作结果

如果你选择“操作”，则系统会提示你输入要添加的操作。 但是，在开始添加操作之前，需要获取逻辑应用基础工作流定义中显示的操作名称的版本。

* 此功能不支持变量、循环和迭代索引。

* 逻辑应用工作流定义中的名称使用下划线 (_) 而不是空格。

* 对于使用点运算符 (.) 的操作名称，请包含这些运算符，例如：

  `My.Action.Name`

1. 在设计器工具栏上选择“代码视图”，然后在操作名称的 `actions` 特性中搜索。

   例如，`Send_approval_email_` 是“发送审批电子邮件”操作的 JSON 名称。

   ![在 JSON 中查找操作名称](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 若要返回到设计器视图，请在代码视图工具栏上选择“设计器”。

1. 若要添加第一个操作，请在“操作项 - 1”框中，输入操作的 JSON 名称。

   ![输入第一个操作](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 若要添加另一个操作，请选择“添加新项”。

## <a name="reference"></a>参考

有关“执行 JavaScript 代码”操作在使用工作流定义语言的逻辑应用基础工作流定义中的结构和语法的详细信息，请参阅此操作的[参考部分](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)。

## <a name="next-steps"></a>后续步骤

详细了解[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
