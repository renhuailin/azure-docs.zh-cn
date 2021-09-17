---
title: 使用 Azure 服务总线交换消息
description: 在 Azure 逻辑应用中创建使用 Azure 服务总线发送和接收消息的自动化任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/18/2021
tags: connectors
ms.openlocfilehash: 43ea4bd0eea40e5e74b92f67241adbe7bee1dcc3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515724"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>使用 Azure 逻辑应用和 Azure 服务总线在云中交换消息

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)连接器，可以创建用于传输数据的自动化任务和工作流，例如，在组织的应用程序之间移动销售和采购订单、日记帐以及库存。 连接器不仅会监视、发送和管理消息，而且还能对队列、会话、主题、订阅等执行操作，例如：

* 监视消息何时抵达（自动完成），或者在队列、主题和主题订阅中接收（扫视-锁定）。
* 发送消息。
* 创建和删除主题订阅。
* 管理队列和主题订阅中的消息，例如，获取、获取延期消息、完成、延期、丢弃和加入死信。
* 续订队列和主题订阅中的消息和会话上的锁。
* 关闭队列和主题中的会话。

可以使用触发器从服务总线获取响应，并使输出可供逻辑应用工作流中的其他操作使用。 还可以让其他操作使用服务总线操作的输出。 如果你不太熟悉服务总线和 Azure 逻辑应用，请查看[什么是 Azure 服务总线？](../service-bus-messaging/service-bus-messaging-overview.md)和[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)？

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 服务总线命名空间和消息传送实体，例如队列。 如果没有这些项，请了解如何[创建服务总线命名空间和队列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* 有关[如何创建逻辑应用工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 在其中使用服务总线命名空间和消息传送实体的逻辑应用工作流。 若要通过服务总线触发器启动工作流，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要在工作流中使用服务总线操作，请使用另一个触发器（例如[定期触发器](../connectors/connectors-native-recurrence.md)）启动逻辑应用工作流。

## <a name="considerations-for-azure-service-bus-operations"></a>Azure 服务总线操作注意事项

### <a name="infinite-loops"></a>无限循环

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

### <a name="large-messages"></a>大型消息

仅在将内置服务总线操作与[单租户 Azure 逻辑应用（标准）](../logic-apps/single-tenant-overview-compare.md)工作流配合使用时，才提供大型消息支持。 你可以在内置版本中通过触发器或操作来发送和接收大型消息。

  为了接收消息，可以通过[在 Azure Functions 扩展中更改以下设置](../azure-functions/functions-bindings-service-bus.md#hostjson-settings)来增加超时值：

  ```json
  {
     "version": "2.0",
     "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle.Workflows",
        "version": "[1.*, 2.0.0)"
     },
     "extensions": {
        "serviceBus": {
           "batchOptions": {
              "operationTimeout": "00:15:00"
           }
        }  
     }
  }
  ```

  为了发送消息，可以通过[添加 `ServiceProviders.ServiceBus.MessageSenderOperationTimeout` 应用设置](../logic-apps/edit-app-settings-host-settings.md)来增加超时值。

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>检查权限

确认逻辑应用资源有权访问服务总线命名空间。

1. 在 [Azure 门户](https://portal.azure.com)中，使用 Azure 帐户登录。

1. 转到服务总线的命名空间。 在命名空间页上的“设置”下，选择“共享访问策略”。  在“声明”下，检查你是否有该命名空间的“管理”权限。 

   ![管理服务总线命名空间的权限](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. 获取服务总线命名空间的连接字符串。 在逻辑应用中提供连接信息时需要此字符串。

   1. 在“共享访问策略”窗格中，选择“RootManageSharedAccessKey”。

   1. 在主连接字符串旁边选择复制按钮。 保存连接字符串供以后使用。

      ![复制服务总线命名空间连接字符串](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 若要确认连接字符串是与服务总线命名空间关联还是与消息传送实体（例如队列）关联，请在该连接字符串中搜索 `EntityPath` 参数。 如果找到该参数，则表示连接字符串适用于特定实体，不是适用于逻辑应用工作流的正确字符串。

## <a name="add-service-bus-trigger"></a>添加服务总线触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录 [Azure 门户](https://portal.azure.com)，并在工作流设计器中打开空白逻辑应用。

1. 在门户的搜索框中，输入 `azure service bus`。 从出现的“触发器”列表中选择所需的触发器。

   例如，若要在有新项发送到服务总线队列时触发逻辑应用工作流，请选择“队列中收到消息时（自动完成）”触发器。

   ![选择服务总线触发器](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   下面是使用服务总线触发器时的一些注意事项：

   * 所有服务总线触发器都是长轮询触发器。 此说明意味着，触发器在激发时会处理所有消息，然后等待 30 秒，让更多的消息出现在队列或主题订阅中。 如果在 30 秒内未显示任何消息，则会跳过触发器运行。 否则，该触发器将继续读取消息，直到队列或主题订阅为空。 下一次触发器轮询将基于在触发器的属性中指定的重复周期间隔。

   * 某些触发器（例如“一条或多条消息抵达队列时(自动完成)”触发器）可能会返回一条或多条消息。 这些触发器在触发时返回的消息数至少为 1，至多为触发器的 **最大消息计数** 属性指定的消息数。

     > [!NOTE]
     > 自动完成触发器会自动完成消息，但只有在下一次调用服务总线时才会完成。 此行为可能会影响逻辑应用的设计。 例如，应避免更改自动完成触发器的并发性，因为如果逻辑应用工作流进入受限制状态，此更改可能会导致重复的消息。 更改并发控制会形成以下情况：跳过受限制的触发器并显示 `WorkflowRunInProgress` 代码、完成操作不会发生以及下一次触发器运行会在轮询间隔后发生。 必须将服务总线锁定持续时间设置为比轮询间隔更长的值。 但即使存在此设置，如果逻辑应用工作流在下一次轮询间隔内仍保持受限制状态，也可能无法完成消息传送任务。

   * 如果为服务总线触发器[启用并发设置](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)，则 `maximumWaitingRuns` 属性的默认值为 10。 根据服务总线实体的锁定持续时间设置和逻辑应用工作流的运行持续时间，此默认值可能太大，并可能导致“锁丢失”异常。 若要为方案找到最佳值，请对 `maximumWaitingRuns` 属性使用值 1 或值 2 开始测试。 若要更改最大等待运行值，请参阅[更改等待运行限制](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)。

1. 如果触发器是首次连接到服务总线命名空间，则在工作流设计器提示输入连接信息时执行以下步骤。

   1. 请提供连接名称，并选择服务总线命名空间。

      ![显示提供连接名称和选择服务总线命名空间的屏幕截图](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      若要改为手动输入连接字符串，请选择“手动输入连接信息”。 如果没有连接字符串，请了解[如何查找连接字符串](#permissions-connection-string)。

   1. 选择服务总线策略，然后选择“创建”。

      ![显示选择服务总线策略的屏幕截图](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. 选择所需的消息传送实体，例如某个队列或主题。 在此示例中，请选择服务总线队列。

      ![显示选择服务总线队列的屏幕截图](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 为所选触发器提供必需信息。 若要向操作添加其他可用属性，请打开“添加新参数”列表，然后选择所需属性。

   对于此示例的触发器，请选择轮询间隔和检查队列的频率。

   ![显示对服务总线触发器设置轮询间隔的屏幕截图](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   有关可用触发器和属性的详细信息，请参阅连接器的[参考页](/connectors/servicebus/)。

1. 通过添加所需操作，继续构建逻辑应用工作流。

   例如，可以添加一个可在收到新消息时发送电子邮件的操作。 当触发器检查队列并找到新消息时，逻辑应用工作流会针对找到的消息执行所选操作。

## <a name="add-service-bus-action"></a>添加服务总线操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 在 [Azure 门户](https://portal.azure.com)的工作流设计器中打开逻辑应用。

1. 在要添加操作的步骤下，选择“新建步骤”。

   或者将鼠标指针移到这些步骤之间的箭头上，以便在步骤之间添加操作。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在“选择操作”下的搜索框中输入 `azure service bus`。 从出现的“操作”列表中选择所需的操作。 

   在此示例中，请选择“发送消息”操作。

   ![显示选择服务总线操作的屏幕截图](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 如果你的操作是首次关联服务总线命名空间，则在工作流设计器提示输入关联信息时执行以下步骤。

   1. 请提供连接名称，并选择服务总线命名空间。

      ![显示提供连接名称和选择服务总线命名空间的屏幕截图](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      若要改为手动输入连接字符串，请选择“手动输入连接信息”。 如果没有连接字符串，请了解[如何查找连接字符串](#permissions-connection-string)。

   1. 选择服务总线策略，然后选择“创建”。

      ![显示选择服务总线策略和选择“创建”按钮的屏幕截图](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. 选择所需的消息传送实体，例如某个队列或主题。 在此示例中，请选择服务总线队列。

      ![显示选择服务总线队列的屏幕截图](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 为所选操作提供必要的详细信息。 若要向操作添加其他可用属性，请打开“添加新参数”列表，然后选择所需属性。

   例如，选择“内容”和“内容类型”属性，以便将其添加到操作。  然后，指定要发送的消息的内容。

   ![显示提供消息内容类型和详细信息的屏幕截图](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   有关可用操作及其属性的详细信息，请参阅连接器的[参考页](/connectors/servicebus/)。

1. 通过添加需要的任何其他操作，继续构建逻辑应用工作流。

   例如，可以添加一个操作来发送电子邮件，确认消息已发送。

1. 保存逻辑应用。 在设计器工具栏上选择“保存”。

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>按顺序发送相关消息

如果需要按特定顺序发送相关消息，则可通过 [Azure 服务总线连接器](../connectors/connectors-create-api-servicebus.md)使用[顺序保护](/azure/architecture/patterns/sequential-convoy)模式。 相关消息具有一个可定义这些消息之间的关系的属性，例如服务总线中的[会话](../service-bus-messaging/message-sessions.md)的 ID。

创建逻辑应用时，可以选择“使用服务总线会话的相关的按序送达”模板，该模板可实现“顺序保护”模式。 有关详细信息，请参阅[按顺序发送相关消息](../logic-apps/send-related-messages-sequential-convoy.md)。

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>逻辑应用的更新延迟生效

如果服务总线触发器的轮询间隔很短（例如 10 秒），则对逻辑应用工作流的更新可能在长达 10 分钟的时间内都不会生效。 若要解决此问题，可禁用逻辑应用，进行更改，然后重新启用逻辑应用工作流。

<a name="connector-reference"></a>

## <a name="connector-reference"></a>连接器参考

对于每个[服务总线消息传递实体（如订阅或主题）](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)，服务总线连接器一次最多可以从服务总线将 1,500 个唯一会话保存到连接器缓存。 如果会话计数超过此限制，则将从缓存中删除旧会话。 有关详细信息，请参阅[消息会话](../service-bus-messaging/message-sessions.md)。

有关触发器、操作和限制（请参阅连接器的 Swagger 说明）的其他技术详细信息，请查看[连接器参考页](/connectors/servicebus/)。 有关 Azure 服务总线消息传送的详细信息，请参阅[什么是 Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)？

## <a name="next-steps"></a>后续步骤

* 了解其他 [Azure 逻辑应用连接器](../connectors/apis-list.md)
