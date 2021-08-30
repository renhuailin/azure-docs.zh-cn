---
title: 连接到 Azure 事件中心
description: 连接到事件中心，并将触发器或操作添加到 Azure 逻辑应用中的工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/16/2021
tags: connectors
ms.openlocfilehash: 079d131cac55c6d7a54547a3720546ab6422f7d5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473069"
---
# <a name="connect-to-an-event-hub-from-workflows-in-azure-logic-apps"></a>通过 Azure 逻辑应用中的工作流连接到事件中心

Azure 事件中心连接器可帮助你将逻辑应用工作流连接到 Azure 中的事件中心。 然后，你可以让工作流监视并管理发送到事件中心的事件。 例如，工作流可以从事件中心检查、发送并接收事件。 本文通过介绍如何连接到事件中心并将事件中心触发器或操作添加到工作流，提供了有关使用 Azure 事件中心连接器的入门指南。

有关 Azure 事件中心或 Azure 逻辑应用的详细信息，请参阅[什么是 Azure 事件中心](../event-hubs/event-hubs-about.md)或[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

## <a name="available-operations"></a>可用操作

有关所有操作和其他技术信息（例如属性、限制等），请查看[事件中心连接器的参考页](/connectors/eventhubs/)。

> [!NOTE]
> 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中托管的逻辑应用，连接器的 ISE 标版本将改用 [ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 一个[事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)

* 你要从中访问事件中心的逻辑应用工作流

  若要使用事件中心触发器启动工作流，需要一个空工作流。 如果你不熟悉 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)，请尝试[阅读此快速入门来创建一个示例逻辑应用工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>检查权限并获取连接字符串

为了确保工作流可以访问事件中心，请检查你的权限，然后获取事件中心命名空间的连接字符串。

1. 在 [Azure 门户](https://portal.azure.com)中，转到事件中心命名空间而不是特定的事件中心。

1. 在命名空间菜单上的“设置”下，选择“共享访问策略”。  在“声明”列中，检查你对该命名空间是否至少拥有“管理”权限 。

   ![显示 Azure 门户和事件中心命名空间的屏幕截图，“声明”列中显示了“管理”权限。](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. 如果你希望稍后再手动输入连接信息，请获取事件中心命名空间的连接字符串。

   1. 在“策略”列中，选择“RootManageSharedAccessKey” 。

   1. 查找主键的连接字符串。 复制并保存该连接字符串供稍后使用。

      ![显示主密钥连接字符串的屏幕截图，其中已选择“复制”按钮。](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > 若要确认连接字符串是与事件中心命名空间关联，还是与特定的事件中心关联，请确保该连接字符串不具有 `EntityPath` 参数。 如果找到了此参数，则说明该连接字符串用于特定的事件中心“实体”，而不是可在工作流中使用的正确字符串。

<a name="create-connection"></a>

## <a name="create-an-event-hub-connection"></a>创建事件中心连接

首次添加事件中心触发器或操作时，系统会提示你创建与事件中心的连接。

1. 出现提示时，请选择以下选项之一：

   * 提供以下连接信息：

     | 属性 | 必须 | 值 | 说明 |
     |----------|----------|-------|-------------|
     | **连接名称** | 是 | <*connection-name*> | 将要为连接创建的名称 |
     | **事件中心命名空间** | 是 | <*event-hubs-namespace*> | 选择要使用的事件中心命名空间。 |
     |||||

   * 若要手动输入前面保存的连接字符串，请选择“手动输入连接信息”。 了解[如何查找连接字符串](#permissions-connection-string)。

1. 选择要使用的事件中心策略（如果尚未选择），然后选择“创建”。

   ![显示已提供的连接信息的屏幕截图，其中已选择“创建”。](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

1. 创建连接后，继续[添加事件中心触发器](#add-trigger)或[添加事件中心操作](#add-action)。

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>添加事件中心触发器

在 Azure 逻辑应用中，每个工作流都必须通过一个[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)启动，该触发器在满足特定的条件时会触发。 每次触发该触发器时，逻辑应用服务就会创建一个工作流实例，并开始运行工作流中的步骤。

以下步骤说明添加触发器（例如，“当事件中心内提供了事件时”）的一般方法。 此示例演示如何添加一个触发器，用于检查事件中心内是否有新事件，并在存在新事件时启动工作流运行。

1. 在逻辑应用设计器中，打开空白的逻辑应用工作流（如果尚未打开）。

1. 在操作搜索框中，输入 `event hubs`。 在触发器列表中，选择名为“当事件中心内提供了事件时”的触发器。

   ![选择触发器](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. 如果系统提示创建与事件中心的连接，请[提供请求的连接信息](#create-connection)。

1. 在触发器中，提供有关要监视的事件中心的信息，例如：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **事件中心名称** | 是 | 要监视的事件中心的名称 |
   | **内容类型** | 否 | 事件的内容类型。 默认为 `application/octet-stream`。 |
   | **使用者组名称** | 否 | 用于读取事件的[事件中心使用者组的名称](../event-hubs/event-hubs-features.md#consumer-groups)。 如果未指定，则使用默认使用者组。 |
   | **最大事件计数** | 否 | 最大事件数。 触发器返回的事件数至少为 1，至多为此属性指定的事件数。 |
   | **时间间隔** | 是 | 一个正整数，描述工作流基于频率运行的频繁度 |
   | **频率** | 是 | 定期计划的时间单位 |
   ||||

   如需更多属性，请打开“添加新参数”列表。  选择一个参数会将该属性添加到触发器，例如：

   ![触发器属性](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   更多属性

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **内容架构** | 否 | 要从事件中心读取的事件的 JSON 内容架构。 例如，如果指定内容架构，则只能对匹配该架构的事件触发工作流。 |
   | **最小分区键** | 否 | 输入要读取的最小[分区](../event-hubs/event-hubs-features.md#partitions) ID。 默认读取所有分区。 |
   | **最大分区键** | 否 | 输入要读取的最大[分区](../event-hubs/event-hubs-features.md#partitions) ID。 默认读取所有分区。 |
   | **时区** | 否 | 仅当指定启动时间时才适用，因为此触发器不接受 UTC 时差。 选择要应用的时区。 <p>有关详细信息，请参阅[使用 Azure 逻辑应用创建和运行重复任务和工作流](../connectors/connectors-native-recurrence.md)。 |
   | **开始时间** | 否 | 按以下格式提供开始时间： <p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss<p>-或-<p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ<p>有关详细信息，请参阅[使用 Azure 逻辑应用创建和运行重复任务和工作流](../connectors/connectors-native-recurrence.md)。 |
   ||||

1. 完成后，请在设计器工具栏上选择“保存”。

1. 现在继续添加一个或多个操作，以便可以使用触发器输出执行其他任务。

   例如，若要根据特定值（例如类别）来筛选事件，可以添加一个条件，这样“发送事件”操作就只发送符合条件的事件。  

## <a name="trigger-polling-behavior"></a>触发器轮询行为

所有事件中心触发器都是长轮询触发器。 此行为意味着当触发器触发时，触发器将处理所有事件，然后等待 30 秒，让更多事件出现在事件中心。 按照设计，如果在 30 秒内没有出现任何事件，则会跳过该触发器。 否则，该触发器将继续读取事件，直到事件中心为空。 下一次触发器轮询的发生将基于触发器属性中设置的重复周期间隔。

例如，如果对触发器设置了四个分区，则此延迟可能需要最长两分钟时间，触发器才能完成对所有分区的轮询。 如果在此延迟内未收到任何事件，则会跳过触发器运行。 否则，该触发器将继续读取事件，直到事件中心为空。 下一次触发器轮询的发生将基于触发器的属性中指定的重复周期间隔。

如果知道消息出现的特定分区，可以通过设置触发器的最大和最小分区键来更新触发器，以仅从特定分区读取事件。 有关详细信息，请查看[添加事件中心触发器](#add-trigger)部分。
     
## <a name="trigger-checkpoint-behavior"></a>触发器检查点行为

当事件中心触发器从事件中心内的每个分区读取事件时，它将使用自身的状态来保留有关流偏移量（分区中的事件位置）以及它从中读取事件的分区的信息。

每次运行工作流时，触发器都会在某个分区中从按照触发器状态保留的流偏移量开始读取事件。 触发器以轮循方式循环访问事件中心内的每个分区，并在后续的触发器运行中读取事件。 单个运行一次从一个分区中获取事件。

触发器不会在存储中使用此检查点功能，因此不会产生额外的成本。 但关键的问题在于，更新事件中心触发器会重置该触发器的状态，从而可能导致触发器在流的开始位置读取事件。

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>添加事件中心操作

在 Azure 逻辑应用中，一个[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)会紧跟触发器或另一操作，并在工作流中执行某个操作。 以下步骤说明了添加操作（例如“发送事件”）的一般方法。 对于此示例，工作流通过一个中心触发器启动，该触发器检查事件中心内是否有新事件。

1. 在逻辑应用设计器中，打开你的逻辑应用工作流（如果尚未打开）。

1. 在触发器或另一操作下，添加一个新步骤。

   若要在现有步骤之间添加一个步骤，请将鼠标移到箭头上方。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在操作搜索框中，输入 `event hubs`。 在操作列表中，选择名为“发送事件”的操作。

   ![选择“发送事件”操作](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. 如果系统提示创建与事件中心的连接，请[提供请求的连接信息](#create-connection)。

1. 在操作中，提供要发送的事件的相关信息。

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **事件中心名称** | 是 | 要在其中发送事件的事件中心 |
   | **内容** | 否 | 要发送的事件内容 |
   | **属性** | 否 | 要发送的应用属性和值 |
   | **分区键** | 否 | [分区](../event-hubs/event-hubs-features.md#partitions) ID，表明应该向何处发送事件 |
   ||||

   如需更多属性，请打开“添加新参数”列表。  选择一个参数会将该属性添加到操作，例如：

   ![选择事件中心名称并提供事件内容](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   例如，可将事件中心触发器的输出发送到另一事件中心：

   ![发送事件示例](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. 完成后，请在设计器工具栏上选择“保存”。

## <a name="connector-reference"></a>连接器参考

有关所有操作和其他技术信息（例如属性、限制等），请查看[事件中心连接器的参考页](/connectors/eventhubs/)。

> [!NOTE]
> 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中托管的逻辑应用，连接器的 ISE 标版本将改用 [ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
