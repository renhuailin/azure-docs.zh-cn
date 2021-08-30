---
title: 数据引入和传出
titleSuffix: Azure Digital Twins
description: 了解将 Azure 数字孪生与其他服务集成时对入口和出口的要求。
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 55aa3f69976a413e1c23e9d935ad99fde88065ec
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252761"
---
# <a name="data-ingress-and-egress-for-azure-digital-twins"></a>Azure 数字孪生的数据入口和出口

Azure 数字孪生通常与其他服务一起使用，创造灵活、关联的解决方案，从而以多种方式使用数据。

借助事件路由，Azure 数字孪生可以接收来自上游服务（如 [IoT 中心](../iot-hub/about-iot-hub.md)或[逻辑应用](../logic-apps/logic-apps-overview.md)）的数据，用于传递遥测数据和通知。 

Azure 数字孪生还可以将数据路由到下游服务（如 [Azure Maps](../azure-maps/about-azure-maps.md) 和[时序见解](../time-series-insights/overview-what-is-tsi.md)），用于存储、工作流集成、分析等。 

## <a name="data-ingress"></a>数据入口

Azure 数字孪生可以由任何服务（[IoT 中心](../iot-hub/about-iot-hub.md)、[逻辑应用](../logic-apps/logic-apps-overview.md)、自定义服务等）中的数据和事件驱动。 这样，便可以从环境中的物理设备收集遥测数据，并使用云中的 Azure 数字孪生图处理此数据。

通过 Azure 数字孪生可以“自带”IoT 中心来使用该服务，而不是在后台使用内置的 IoT 中心。 可以使用当前生产环境中现有的 IoT 中心，或部署一个新的 IoT 中心用于此目的。 这样，便可以使用 IoT 中心的所有设备管理功能。

若要将数据从任何源引入 Azure 数字孪生，请使用 Azure 函数。 请参阅[从 IoT 中心引入遥测数据](how-to-ingest-iot-hub-data.md)，详细了解此模式，或参阅 Azure 数字孪生[连接端到端解决方案](tutorial-end-to-end.md)进行试用。 

此外，还可以参阅[与逻辑应用集成](how-to-integrate-logic-apps.md)，了解如何将 Azure 数字孪生连接到逻辑应用触发器。

## <a name="data-egress-services"></a>数据传出服务

Azure 数字孪生可以将数据发送到连接的终结点。 支持的终结点可以是：
* [事件中心](../event-hubs/event-hubs-about.md)
* [事件网格](../event-grid/overview.md)
* [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)

使用管理 API 或 Azure 门户将终结点附加到 Azure 数字孪生。 请参阅[管理终结点和路由](how-to-manage-routes.md)，详细了解如何将终结点附加到 Azure 数字孪生。

还有许多你可能希望将数据最终定向到的其他服务，例如 [Azure 存储](../storage/common/storage-introduction.md)、[Azure Maps](../azure-maps/about-azure-maps.md)、[Azure 数据资源管理器](/azure/data-explorer/data-explorer-overview)或[时序见解](../time-series-insights/overview-what-is-tsi.md)。 若要将数据发送到此类服务，需将目标服务附加到终结点。

例如，如果还要使用 Azure Maps 并且想要将位置与 Azure 数字孪生[孪生图](concepts-twins-graph.md)关联，则可以将 Azure Functions 与事件网格结合使用，以便在部署中的所有服务之间建立通信。 有关详细信息，请参阅[使用 Azure 数字孪生更新 Azure Maps 室内地图](how-to-integrate-maps.md)

还可以参阅[与时序见解集成](how-to-integrate-time-series-insights.md)，了解如何将数据以类似方式路由到时序见解。

## <a name="next-steps"></a>后续步骤

详细了解终结点及将事件路由到外部服务：
* [路由 Azure 数字孪生事件](concepts-route-events.md)

请参阅如何设置 Azure 数字孪生以从 IoT 中心引入数据：
* [从 IoT 中心引入遥测数据](how-to-ingest-iot-hub-data.md)