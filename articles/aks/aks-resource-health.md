---
title: 检查影响 AKS 群集的资源运行状况事件（预览）
description: 使用 Azure 资源运行状况检查 AKS 群集的运行状况。
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070650"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>检查影响 AKS 群集的资源运行状况事件（预览）


在 AKS 上运行容器工作负载时，需要确保在出现问题时立即进行故障排除和修复，以最大程度地降低对工作负载可用性的影响。 [Azure 资源运行状况](../service-health/resource-health-overview.md)可让你了解可能导致 AKS 群集不可用的各种运行状况事件。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>打开资源运行状况

### <a name="to-access-resource-health-for-your-aks-cluster"></a>若要访问 AKS 群集的资源运行状况，请执行以下操作：

- 在 [Azure 门户](https://portal.azure.com)中导航到 AKS 群集。
- 在左侧导航栏中，选择“资源运行状况”。

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>若要访问你的订阅上的所有群集的资源运行状况，请执行以下操作：

- 在 [Azure 门户](https://portal.azure.com)中搜索“服务运行状况”并导航到它。
- 在左侧导航栏中，选择“资源运行状况”。
- 选择订阅，将资源类型设置为 Azure Kubernetes Service (AKS)。

![屏幕截图显示了 AKS 群集的资源运行状况。](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>检查运行状态

Azure 资源运行状况有助于在服务问题影响到 Azure 资源时进行诊断和获取支持。 资源运行状况报告资源的当前和过去运行状况，并帮助确定问题是由用户启动的操作还是平台事件引起的。

资源运行状况接收托管群集的信号，以确定群集的运行状况状态。 它检查 AKS 群集的运行状况状态，并报告每个运行状况信号所需的操作。 这些信号包括自动解决问题、计划内更新、计划外运行状况事件以及用户启动的操作导致的不可用。 这些信号是使用 Azure 资源运行状况的运行状况分类的：可用、不可用、未知和降级   。

- 可用：如果没有影响群集的运行状况的已知问题，资源运行状况将群集报告为“可用”。

- 不可用：如果有影响群集的运行状况的平台或非平台事件，资源运行状况将群集报告为“不可用”。

- 未知：当群集的运行状况指标暂时丢失连接时，资源运行状况将群集报告为“未知”。

- 降级：当存在要求你进行操作的运行状况问题时，资源运行状况会将你的群集报告为“降级”。

有关每个运行状况状态指示的详细信息，请访问[资源运行状况概述](../service-health/resource-health-overview.md#health-status)。

### <a name="view-historical-data"></a>查看历史数据

还可以在“运行状况历史记录”部分中查看过去 30 天的历史资源运行状况信息。

## <a name="next-steps"></a>后续步骤

在群集上运行检查，通过使用 [AKS 诊断](./concepts-diagnostics.md)进一步排查群集问题。