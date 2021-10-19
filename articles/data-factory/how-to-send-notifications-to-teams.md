---
title: 如何向 Microsoft Teams 通道发送通知
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何从 Azure 数据工厂或 Synapse Analytics 管道向 Microsoft Teams 通道发送通知
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.custom: synapse
ms.topic: how-to
ms.subservice: tutorials
ms.date: 09/29/2021
ms.openlocfilehash: 98fa799cc5fd933d21fb0130093381c628f8e23c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660944"
---
# <a name="send-notifications-to-a-microsoft-teams-channel-from-an-azure-data-factory-or-synapse-analytics-pipeline"></a>从 Azure 数据工厂或 Synapse Analytics 管道向 Microsoft Teams 通道发送通知

在执行管道期间或之后，通常需要发送通知。 通知提供主动警报并降低为发现问题所需的反应监视。  可了解[如何使用数据工厂或 Synapse 管道可以调用的逻辑应用发送电子邮件通知](tutorial-control-flow-portal.md#create-email-workflow-endpoints)。  许多企业也越来越多地使用 Microsoft Teams 进行协作。  本文介绍如何将来自管道警报的通知配置到 Microsoft Teams 中。 

## <a name="prerequisites"></a>先决条件

在可以从管道向 Teams 发送通知之前，必须为 Teams 通道创建[传入 Webhook](/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using)。 如果需要为此目的创建新的 Teams 通道，请参阅 [Teams 文档](https://support.microsoft.com/office/create-a-channel-in-teams-fda0b75e-5b90-4fb8-8857-7e102b014525)。  

1.  打开 Microsoft Teams 并转到“应用”选项卡。搜索“传入 Webhook”并选择传入 Webhook 连接器。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-incoming-webhook-connector.png" alt-text="显示 Teams 中的“应用”选项卡下的传入 Webhook 应用。":::

1.  将连接器添加到要向其发送通知的 Teams 站点。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-connector-to-site.png" alt-text="突出显示传入 Webhook 应用的“添加到团队”按钮。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-prod-notifications.png" alt-text="显示 Teams 中的传入 Webhook 应用配置对话框上的团队选择提示。":::

1.  根据情况为 Webhook 命名，并可选择上传图标来标识消息。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-icon.png" alt-text="突出显示“传入 Webhook 选项”页中的名称属性、可选图像上传和“创建”按钮。":::  

1.  复制创建时生成的 Webhook URL 的存储，供以后在 ADF 中使用。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-copy-webhook-url.png" alt-text="显示创建后在“传入 Webhook 选项”页上的新的 Webhook URL。":::

1.  可在添加 Webhook 连接器的通道中查看通知。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-channel-notification.png" alt-text="显示在其中添加了 Webhook 连接器的 Teams 通道中的通知。":::
        
## <a name="steps-to-send-notifications-on-teams-channel-from-a-pipeline"></a>在 Teams 通道上从管道发送通知的步骤：

# <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

1.  创建新的“来自模板的管道”。 模板库提供了管道模板，便于开始使用 Teams 通知。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template.png" alt-text="显示 Azure 数据工厂工作室中的“来自模板的管道”菜单。":::

1.  搜索“Teams”，然后选择并使用“向 Microsoft Teams 中的通道发送通知”模板。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog.png" alt-text="显示模板库中的“向 Microsoft Teams 中的通道发送通知”模板。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template.png" alt-text="显示在模板库中选择后的“向 Microsoft Teams 中的通道发送通知”模板详细信息。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="显示由“向 Microsoft Teams 中的通道发送通知”模板创建的管道属性。":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

1.  创建新的“来自模板的管道”。 模板库提供了管道模板，便于开始使用 Teams 通知。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template-synapse.png" alt-text="显示 Azure 数据工厂工作室中的“来自模板的管道”菜单。":::

1.  搜索“Teams”，然后选择并使用“向 Microsoft Teams 中的通道发送通知”模板。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog-synapse.png" alt-text="显示模板库中的“向 Microsoft Teams 中的通道发送通知”模板。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template-synapse.png" alt-text="显示在模板库中选择后的“向 Microsoft Teams 中的通道发送通知”模板详细信息。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="显示由“向 Microsoft Teams 中的通道发送通知”模板创建的管道属性。":::

---

3.  我们建议为相关参数的默认值添加当前的数据工厂订阅 ID、资源组和 Teams Webhook URL（请参阅[先决条件](#prerequisites)）  。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/webhook-recommended-properties.png" alt-text="显示由“向 Microsoft Teams 中的通道发送通知”模板创建的管道建议属性。":::

    这些参数用于构造监视 URL。 假设未提供（管道所属的同一数据工厂的）有效的订阅和资源组。 在这种情况下，通知将不包含有效的管道监视 URL，但消息仍可正常工作。  此外，添加这些参数有助于防止需要始终从另一管道传递这些值。 如果要通过元数据驱动方法控制这些值，则应相应地对其进行修改。
    
1.  将“执行管道”活动添加到你希望在 Teams 通道上从其中发送通知的管道中。 选择根据“向 Microsoft Teams 中的通道发送通知”模板生成的管道作为“执行管道”活动中的调用管道  。

     :::image type="content" source="media/how-to-send-notifications-to-teams/execute-pipeline-activity.png" alt-text="显示由“向 Microsoft Teams 中的通道发送通知”模板创建的管道中的“执行管道”活动。":::

1.  根据活动类型按需要自定义参数。

    :::image type="content" source="media/how-to-send-notifications-to-teams/customize-parameters-by-activity-type.png" alt-text="显示由“向 Microsoft Teams 中的通道发送通知”模板创建的管道中的参数的自定义。":::   
  
1.  接收 Teams 中的通知。

    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-notifications-view-pipeline-run.png" alt-text="显示 Teams 通道中的管道通知。":::
## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>使用系统变量和表达式添加动态消息

可使用[系统变量](control-flow-system-variables.md)和[表达式](control-flow-expression-language-functions.md)使消息变为动态。 例如：  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

上述表达式将根据失败返回相关错误消息，这些错误消息可在 Teams 通道上作为通知发送出去。 有关详细信息，请参阅[复制活动输出属性](copy-activity-monitoring.md)一文。

我们还鼓励你查看 Microsoft Teams 支持的[通知有效负载架构](https://adaptivecards.io/explorer/AdaptiveCard.html)，并根据需要进一步自定义上述模板。

## <a name="next-steps"></a>后续步骤

[如何从管道发送电子邮件](how-to-send-email.md)