---
title: 如何发送电子邮件
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂或 Azure Synapse 管道发送电子邮件。
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: 64f7fcf5cfd5c24cb8d34e29572f7fbb1314414a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668896"
---
# <a name="send-an-email-with-an-azure-data-factory-or-azure-synapse-pipeline"></a>使用 Azure 数据工厂或 Azure Synapse 管道发送电子邮件

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在执行管道期间或之后，通常需要发送通知。 通知提供主动警报并降低为发现问题所需的反应监视。  本文介绍如何在 Azure 数据工厂或 Azure Synapse 管道中配置电子邮件通知。 

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- 逻辑应用。 若要从管道触发电子邮件发送，请使用[逻辑应用](../logic-apps/logic-apps-overview.md)来定义工作流。 有关创建逻辑应用工作流的详细信息，请参阅[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-the-email-workflow-in-your-logic-app"></a>在逻辑应用中创建电子邮件工作流

创建名为 `SendEmailFromPipeline` 的逻辑应用工作流。 将工作流触发器定义为 `When an HTTP request is received`，并添加 `Office 365 Outlook – Send an email (V2)` 操作。

:::image type="content" source="media/how-to-send-email/logic-app-workflow-designer.png" alt-text="显示逻辑应用工作流设计器，其中含有来自 HTTP 请求触发器的“发送电子邮件(V2)”操作。":::

对于 HTTP 请求触发器，请为 `Request Body JSON Schema` 提供此 JSON：

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

逻辑应用设计器中的 HTTP 请求应如下所示：

:::image type="content" source="media/how-to-send-email/logic-app-http-request-trigger.png" alt-text="显示 HTTP 请求触发器的逻辑应用工作流设计器，其中已填充“请求正文 JSON 架构”字段。":::

对于“发送电子邮件(V2)”操作，请使用请求正文 JSON 架构的属性来自定义如何设置电子邮件的格式：

:::image type="content" source="media/how-to-send-email/logic-app-email-action.png" alt-text="显示“发送电子邮件(V2)”操作的的逻辑应用工作流设计器。":::

保存工作流。 然后记下新工作流的工作流 URL：

:::image type="content" source="media/how-to-send-email/logic-app-workflow-url.png" alt-text="显示逻辑应用工作流概述选项卡，其中突出显示了工作流 URL。":::

## <a name="create-a-pipeline-to-trigger-your-logic-app-email-workflow"></a>创建管道以触发逻辑应用电子邮件工作流

创建用于发送电子邮件的逻辑应用工作流后，可以使用 Web 活动从管道触发该工作流。  

1. 创建新管道，并在“常规”类别下找到“Web”活动，并将其拖到编辑画布 。

1. 选择新 Web1 活动，然后选择“设置”选项卡 。

   提供之前在 URL 字段创建的逻辑应用工作流的 URL。

   为“正文”提供以下 JSON：
    ```json
       {
        "message" : "This is a custom dynamic message from your pipeline with run ID @{pipeline().RunId}.",
        "dataFactoryName" : "@{pipeline().DataFactory}", 
        "pipelineName" : "@{pipeline().Pipeline}", 
        "receiver" : "@{pipeline().parameters.receiver}"
       }
    ```
    
    使用动态表达式为管道中的事件生成有用的消息。  请注意，此处的 JSON 格式与在逻辑应用中定义的 JSON 格式匹配，还可以根据需要对其进行自定义。
    
    :::image type="content" source="media/how-to-send-email/pipeline-with-web-activity-calling-logic-app.png" alt-text="显示配置了 Web 活动的管道，并含有逻辑应用工作流 URL 和 JSON 消息正文。":::

1. 选择管道设计器的背景区域来选择管道属性页面，并添加名为接收器的新参数，提供电子邮件地址作为其默认值。
   
   在此示例中，我们将从我们任意定义的管道参数提供接收方电子邮件。  接收方值可以来自任何表达式，甚至是链接的数据源。

   :::image type="content" source="media/how-to-send-email/pipeline-receiver-email-parameter.png" alt-text="显示管道设计器中接收方参数的配置。":::

1. 发布管道，然后手动触发，确认电子邮件已按预期方式发送。

   :::image type="content" source="media/how-to-send-email/pipeline-manually-trigger.png" alt-text="显示如何手动触发管道。"::: 

## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>使用系统变量和表达式添加动态消息

可使用[系统变量](control-flow-system-variables.md)和[表达式](control-flow-expression-language-functions.md)使消息变得动态。 例如：  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

上述表达式将从复制活动失败中返回相关错误消息，然后这些错误消息可以重定向到发送电子邮件的 Web 活动。 有关详细信息，请参阅[复制活动输出属性](copy-activity-monitoring.md)一文。

## <a name="next-steps"></a>后续步骤

[如何从管道发送 Teams 通知](how-to-send-notifications-to-teams.md)
