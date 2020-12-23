---
title: 了解资源运行状况
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 资源运行状况来检查 Azure 数字孪生实例的运行状况。
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616543"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>排查 Azure 数字孪生：资源运行状况

[Azure 资源运行状况](../service-health/resource-health-overview.md) 有助于诊断和获取影响 Azure 资源的服务问题。 它会报告有关资源的当前和过去运行状况的信息，

本文介绍如何获取 Azure 数字孪生实例的 **资源运行状况** 信息。

## <a name="use-azure-resource-health"></a>使用 Azure 资源运行状况

Azure 资源运行状况可帮助你监视 Azure 数字孪生实例是否已启动并正在运行。 你还可以使用它来了解区域中断是否会影响实例的运行状况。

若要检查实例的运行状况，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从实例的菜单中，选择 " *支持 + 故障排除* " 下的 " _**资源运行状况**_ "。 这会转到查看资源运行状况历史记录的页面。 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="显示 &quot;资源运行状况&quot; 页的屏幕截图。有一个 &quot;运行状况历史记录&quot; 部分，其中显示了过去9天的每日报告。每天显示 &quot;可用&quot; 状态。":::

在上面的图像中，此实例显示为 " *可用* "，过去9天。 若要了解有关 *可用* 状态和可能出现的其他状态类型的详细信息，请参阅 [*Azure 资源运行状况概述*](../service-health/resource-health-overview.md)。

你还可以在 [*azure 资源运行状况中的资源类型和运行状况检查*](../service-health/resource-health-checks-resource-types.md)中详细了解不同类型的 Azure 资源的资源运行状况。

## <a name="next-steps"></a>后续步骤

阅读以下文章，了解监视 Azure 数字孪生实例的其他方法：
* [*故障排除：通过 Azure Monitor 查看指标*](troubleshoot-metrics.md)
* [*故障排除：设置诊断*](troubleshoot-diagnostics.md)。
* [*故障排除：设置警报*](troubleshoot-alerts.md)
