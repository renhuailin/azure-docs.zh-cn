---
title: 了解资源运行状况
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure 资源运行状况检查 Azure 数字孪生实例的运行状况。
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 20afd0a696a2bbeda0e2fa8bb114ecb8b5b6a76b
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205532"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Azure 数字孪生故障排除：资源运行状况

[Azure 资源运行状况](../service-health/resource-health-overview.md)有助于在服务问题影响到 Azure 资源时进行诊断和获取支持。 它会报告有关资源的当前和过去运行状况的信息，

本文介绍如何获取 Azure 数字孪生实例的资源运行状况信息。

## <a name="use-azure-resource-health"></a>使用 Azure 资源运行状况

Azure 资源运行状况可帮助监视 Azure 数字孪生实例是否已启动并正在运行。 此外，还可以了解是否发生了影响实例运行状况的区域性服务中断。

若要检查实例的运行状况，请遵循以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来查找它。 

2. 从实例的菜单中，选择“支持 + 故障排除”下的 "资源运行状况"。 将转到可以查看资源运行状况历史记录的页面。 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="显示“资源运行状况”页的屏幕截图。其中有一个“运行状况历史记录”部分显示最近 9 天的每日报告。每天显示的状态为“可用”。":::

在上图中，此实例显示为“可用”，且最近 9 天一直为“可用”状态。 若要了解有关“可用”状态和可能出现的其他状态类型的详细信息，请参阅 Azure 资源运行状况概述。

还可以在 Azure 资源运行状况中的资源类型和运行状况检查中详细了解针对不同 Azure 资源类型的资源运行状况执行的各种检查。

## <a name="next-steps"></a>后续步骤

阅读以下文章，了解监视 Azure 数字孪生实例的其他方法：
* [故障排除：通过 Azure Monitor 查看指标](troubleshoot-metrics.md)
* 故障排除：设置诊断。
* [故障排除：设置警报](troubleshoot-alerts.md)
