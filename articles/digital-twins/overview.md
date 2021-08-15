---
title: Azure 数字孪生是什么？
titleSuffix: Azure Digital Twins
description: Azure 数字孪生用途的概述。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 519a64ac9e921624f4e8c03fc91f842eacd2672c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472948"
---
# <a name="what-is-azure-digital-twins"></a>Azure 数字孪生是什么？

Azure 数字孪生是一个服务型平台 (PaaS)，可用于基于整个环境的数字模型创建孪生图。 这些环境可能是建筑物、工厂、农场、能源网络、铁路，体育场等，甚至是整个城市。 这些数字模型可用于获取洞察力，以推动产品改进、运营优化、成本降低和客户体验突破。

利用你基于 Azure 数字孪生的领域专业知识来构建自定义的连接解决方案，这些解决方案：
* 为任何环境建模，并以可缩放且安全的方式将数字孪生引入生活
* 连接 IoT 设备和现有业务系统等资产
* 使用可靠的事件系统来构建动态业务逻辑和数据处理
* 与 Azure 数据、分析和 AI 服务集成，以帮助你跟踪过去的数据并预测未来

## <a name="azure-digital-twins-capabilities"></a>Azure 数字孪生功能

以下是 Azure 数字孪生提供的功能摘要。

### <a name="open-modeling-language"></a>开放式建模语言

在 Azure 数字孪生中，可以使用称为[模型](concepts-models.md)的自定义孪生类型来定义表示物理环境中的人物、位置和事物的数字实体。 

可以将这些模型定义视为描述业务的专门词汇。 例如，对于建筑物管理解决方案，可以定义诸如“建筑物”、“地板”和“电梯”之类的模型。 然后，可以根据这些模型创建 **数字孪生**，以表示你的特定环境。

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

模型是采用类似 JSON 语言（称为[数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)）进行定义的，它们根据状态属性、遥测事件、命令、组件和关系描述孪生。
* 模型定义实体之间的语义关系，以便可以将孪生连接到反映其交互的图。 可以将模型视为描述你世界的名词，将关系视为动词。
* 还可以使用模型继承来专用化孪生。 一个模型可以继承自另一个模型。

DTDL 用于其他 Azure IoT 服务中的数据模型，包括 [IoT 即插即用 (PnP)](../iot-develop/overview-iot-plug-and-play.md) 和[时序见解 (TSI)](../time-series-insights/overview-what-is-tsi.md)。 这有助于使 Azure 数字孪生解决方案与 Azure 生态系统的其他部分保持连接并兼容。

### <a name="live-execution-environment"></a>实时执行环境

Azure 数字孪生中的数字模型是真实世界的实时最新表示。 使用自定义 DTDL 模型中的关系，可以将孪生连接到表示你环境的 **实时图形**。

可以在 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 中可视化 Azure 数字孪生图，其中提供了用来与图交互的以下界面：

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，其中显示了表示数字孪生的节点图。" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

Azure 数字孪生提供了丰富的 **事件系统**，以使该图形跟上数据处理和业务逻辑的变化。 可以连接外部计算资源（例如 [Azure Functions](../azure-functions/functions-overview.md)），以灵活的自定义方式来驱动此数据处理。

还可以使用 Azure 数字孪生的功能强大 **查询 API**，从实时执行环境中提取见解。 API 让你可以使用丰富的搜索条件进行查询，包括属性值、关系、关系属性、模型信息等。 还可以组合查询，以便收集有关你环境的广泛见解，并回答对你很重要的自定义问题。

### <a name="input-from-iot-and-business-systems"></a>来 IoT 和业务系统的输入

为了使 Azure 数字孪生的实时执行环境始终跟上真实世界的变化，可以使用 [IoT 中心](../iot-hub/about-iot-hub.md)将解决方案连接到 IoT 和 IoT Edge 设备。 这些中心受管理设备表示为孪生图的一部分，并提供用于驱动模型的数据。

为此，可以使用 Azure 数字孪生创建一个新的 IoT 中心，也可以将现有 IoT 中心与其已经管理的设备连接起来。

还可以使用 REST API 或其他服务（例如[逻辑应用](../logic-apps/logic-apps-overview.md)）的连接器，从其他数据源驱动 Azure 数字孪生。

### <a name="output-to-adx-tsi-storage-and-analytics"></a>输出到 ADX、TSI、存储和分析

可以将 Azure 数字孪生模型中的数据路由到下游 Azure 服务，以进行其他分析或存储。 这是通过 **事件路由** 提供的，该事件路由使用 [事件中心](../event-hubs/event-hubs-about.md)、[事件网格](../event-grid/overview.md)或 [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)来驱动所需的数据流。

可以使用事件路由执行的操作包括：
* 将数字孪生数据发送到 ADX，以使用 [Azure 数据资源管理器 (ADX) 的 Azure 数字孪生查询插件](concepts-data-explorer-plugin.md)查询这些数据
* [将 Azure 数字孪生连接到时序见解 (TSI)](how-to-integrate-time-series-insights.md)，以跟踪每个孪生的时序历史记录
* 将时序见解中的时序模型与 Azure 数字孪生中的源保持一致
* 在 [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md) 中存储 Azure 数字孪生数据
* 使用 [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 或其他 Microsoft 数据分析工具分析 Azure 数字孪生数据
* 将较大的工作流与逻辑应用集成

这是 Azure 数字孪生可以连接到较大解决方案并支持自定义需求以继续利用这些见解进行工作的另一种方式。

## <a name="azure-digital-twins-in-a-solution-context"></a>解决方案上下文中的 Azure 数字孪生

Azure 数字孪生通常与其他 Azure 服务结合使用，作为较大 IoT 解决方案的一部分。 

一个使用 Azure 数字孪生的完整解决方案可能包含以下各部分：
* Azure 数字孪生服务实例。 这会将孪生模型和孪生图及其状态一起存储，并编排事件处理。
* 一个或多个客户端应用程序，通过配置模型、创建拓扑并从孪生图中提取见解来驱动 Azure 数字孪生实例。
* 一种或多种外部计算资源，用于处理由 Azure 数字孪生或连接的数据源（例如设备）生成的事件。 提供计算资源的一种常见方法是通过 [Azure Functions](../azure-functions/functions-overview.md)。
* IoT 中心，提供设备管理和 IoT 数据流功能。
* 下游服务，处理工作流集成（如[逻辑应用](../logic-apps/logic-apps-overview.md)、冷存储、Azure 数据资源管理器、时序集成或分析）等任务。

下图显示了 Azure 数字孪生在较大 Azure IoT 解决方案的上下文中所处的位置。

:::image type="content" source="media/overview/solution-context.png" alt-text="该图显示了输入源、输出服务以及与客户端应用程序和外部计算资源的双向通信。" border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>服务限制

可以在 [Azure 数字孪生服务限制文章](reference-service-limits.md)中了解 Azure 数字孪生的服务限制。 在使用服务来了解服务的功能和速率限制，以及在必要时可以调整哪些限制时，这一点非常有用。

## <a name="terminology"></a>术语

可以在 [Azure IoT 词汇表](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json)中查看 Azure IoT 服务（包括 Azure 数字孪生）的常见 IoT 术语及其用法列表。 参考此资源可帮助你获取 Azure 数字孪生的入门知识和生成 IoT 解决方案。

## <a name="next-steps"></a>后续步骤

* 在 [Azure Digital Twins Explorer 入门](quickstart-azure-digital-twins-explorer.md)中深入了解如何使用 Azure 数字孪生。

* 或者，通过[自定义模型](concepts-models.md)开始了解 Azure 数字孪生概念。