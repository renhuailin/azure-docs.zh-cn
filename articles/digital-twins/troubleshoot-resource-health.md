---
title: 了解资源运行状况
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure 资源运行状况检查 Azure 数字孪生实例的运行状况。
author: baanders
ms.author: baanders
ms.date: 8/27/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 551193ebcddb023010f1cea1029571c99176afb9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113086"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Azure 数字孪生故障排除：资源运行状况

[Azure 服务运行状况](../service-health/index.yml)是一个体验套件，可帮助你诊断影响 Azure 资源的服务问题并获得相关支持。 它包含资源运行状况、服务运行状况和状态信息，并报告当前和过去的运行状况信息。

## <a name="use-azure-resource-health"></a>使用 Azure 资源运行状况

[Azure 资源运行状况](../service-health/resource-health-overview.md)可帮助监视 Azure 数字孪生实例是否已启动并运行。 此外，还可以了解是否发生了影响实例运行状况的区域性服务中断。

若要检查实例的运行状况，请遵循以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到 Azure 数字孪生实例。 在门户搜索栏中键入该实例的名称即可找到它。 

2. 从实例的菜单中，选择“支持 + 故障排除”下的“资源运行状况”。 将转到可以查看资源运行状况历史记录的页面。 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="该屏幕截图显示了“资源运行状况”页面。其中有一个“运行状况历史记录”部分显示最近九天的每日报告。":::

在上图中，此实例显示为“可用”，且最近 9 天一直为“可用”状态。 若要详细了解“可用”状态和可能出现的其他状态类型，请参阅[资源运行状况概述](../service-health/resource-health-overview.md)。

还可以在 Azure 资源运行状况中的资源类型和运行状况检查中详细了解针对不同 Azure 资源类型的资源运行状况执行的各种检查。

## <a name="use-azure-service-health"></a>使用 Azure 服务运行状况

[Azure 服务运行状况](../service-health/service-health-overview.md)可帮助你检查特定区域中整个 Azure 数字孪生服务的运行状况，并了解正在发生的服务问题、即将进行的计划内维护等事件。

若要检查服务运行状况，请登录到 [Azure 门户](https://portal.azure.com)并导航到“服务运行状况”服务。 在门户搜索栏中键入“服务运行状况”即可找到它。 

然后，可以按订阅、区域和服务筛选服务问题。

若要详细了解如何使用 Azure 服务运行状况，请参阅[服务运行状况概述](../service-health/service-health-overview.md)。

## <a name="use-azure-status"></a>使用 Azure 状态

[Azure 状态](../service-health/azure-status-overview.md)页提供 Azure 服务和区域运行状况的全局视图。 它不像 Azure 服务运行状况或 Azure 资源运行状况一样个性化，可用于了解具有广泛影响的事件。

若要检查 Azure 状态，请导航到 [Azure 状态](https://status.azure.com/status/)页。 该页显示了一个表，其中包含每个区域的 Azure 服务和运行状况指示器。 若要查看 Azure 数字孪生，可以在页面上搜索其表条目。

若要详细了解如何使用 Azure 状态页，请参阅 [Azure 状态概述](../service-health/azure-status-overview.md)。

## <a name="next-steps"></a>后续步骤

阅读以下文章，了解监视 Azure 数字孪生实例的其他方法：
* [故障排除：通过 Azure Monitor 查看指标](troubleshoot-metrics.md)
* 故障排除：设置诊断。
* [故障排除：设置警报](troubleshoot-alerts.md)
