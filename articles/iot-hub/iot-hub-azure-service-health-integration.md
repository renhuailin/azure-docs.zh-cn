---
title: 检查 Azure IoT 中心服务和资源运行状况 |Microsoft Docs
description: 使用 Azure 服务运行状况和 Azure 资源运行状况来监视 IoT 中心
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548469"
---
# <a name="check-iot-hub-service-and-resource-health"></a>检查 IoT 中心服务和资源运行状况

Azure IoT 中心与 [Azure 服务运行状况服务](../service-health/overview.md) 集成，使你能够监视 iot 中心服务的服务级别运行状况和各个 iot 中心。 你还可以设置警报，以便在 IoT 中心服务或 IoT 中心的状态发生更改时收到通知。 Azure 服务运行状况服务是三个较小服务的组合： "Azure 资源运行状况"、"Azure 服务运行状况" 和 "Azure 状态" 页。 本文中的部分更详细地介绍了每个服务及其与 IoT 中心之间的关系。

Azure 服务运行状况服务可帮助监视服务级别事件，如中断和升级，它们可能会影响 IoT 中心服务和你的单个 IoT 中心的可用性。 IoT 中心还与 Azure Monitor 集成，提供 IoT 中心平台指标和 IoT 中心资源日志，可用于监视特定 IoT 中心发生的操作错误和条件。 若要了解详细信息，请参阅 [监视 IoT 中心](monitor-iot-hub.md)。

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>使用 Azure 资源运行状况检查 IoT 中心的运行状况

Azure 资源运行状况是 Azure 服务运行状况服务的一部分，用于跟踪单个资源的运行状况。 可以直接从门户检查 IoT 中心的运行状况状态。

若要使用门户查看 IoT 中心的状态和状态历史记录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，请在 Azure 门户中转到 IoT 中心。

1. 在左侧窗格中的 " **支持 + 故障排除** " 下，选择 " **资源运行状况** "。

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="IoT 中心的 &quot;资源运行状况&quot; 页":::

若要详细了解 Azure 资源运行状况以及如何解释运行状况数据，请参阅 Azure 服务运行状况文档中的 [资源运行状况概述](../service-health/resource-health-overview.md) 。

你还可以选择 " **添加资源运行状况警报** "，将警报设置为在 IoT 中心的运行状态发生更改时触发。 若要了解详细信息，请参阅 [配置服务运行状况事件的警报](../service-health/alerts-activity-log-service-notifications-portal.md) 和 Azure 服务运行状况文档中的相关主题。

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>在 Azure 服务运行状况中检查 IoT 中心的运行状况

利用 Azure 服务运行状况，你可以查看订阅中所有 IoT 中心的运行状况状态。

若要检查 IoT 中心的运行状况，请遵循以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 导航到“服务运行状况” > “资源运行状况”。 

3. 从下拉列表框中，选择你的订阅，然后选择“IoT 中心”作为资源类型。

若要了解有关 Azure 服务运行状况以及如何解释运行状况数据的详细信息，请参阅 Azure 服务运行状况文档中的 [服务运行状况概述](../service-health/service-health-overview.md) 。

若要了解如何设置 Azure 服务运行状况的警报，请参阅 [配置服务运行状况事件的警报](../service-health/alerts-activity-log-service-notifications-portal.md) 和 Azure 服务运行状况文档中的相关主题。

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>在 Azure 状态页上按区域检查 IoT 中心服务的运行状况

若要在全球范围内查看 IoT 中心和其他服务的状态，可以使用 " [Azure 状态" 页](https://status.azure.com/status)。 有关 "Azure 状态" 页的详细信息，请参阅 azure 服务运行状况文档中的 [azure 状态概述](../service-health/azure-status-overview.md) 。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 服务运行状况、Azure 资源运行状况和 Azure 状态页的详细信息，请参阅 [Azure 服务运行状况服务](../service-health/overview.md) 。
* 有关监视 Azure IoT 中心的说明，请参阅 [监视 Azure Iot 中心](monitor-iot-hub.md) 。
