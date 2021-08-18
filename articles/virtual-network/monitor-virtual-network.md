---
title: 监视 Azure 虚拟网络
description: 在本文中开始了解如何监视 Azure 虚拟网络
services: virtual-network
author: duongau
ms.author: duau
ms.service: virtual-network
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: bec4d18ebc762ecf573281341e907db4376f26b2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297630"
---
# <a name="monitoring-azure-virtual-network"></a>监视 Azure 虚拟网络

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 

本文介绍 Azure 虚拟网络生成的监视数据。 Azure 虚拟网络使用 [Azure Monitor](../azure-monitor/overview.md)。 如果不熟悉所有 Azure 服务普遍使用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。

## <a name="monitoring-data"></a>监视数据 

Azure 虚拟网络收集与[监视 Azure 资源中的数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。 

有关 Azure 虚拟网络创建的指标和日志指标的详细信息，请参阅[有关监视 Azure 虚拟网络数据的参考](monitor-virtual-network-reference.md)。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 [Azure 虚拟网络监视数据参考](monitor-virtual-network-reference.md#resource-logs)中已列出 Azure 虚拟网络的类别。

> [!IMPORTANT]
> 启用这些设置需要额外的 Azure 服务（存储帐户、事件中心或 Log Analytics），这可能会增加成本。 若要估算成本，请访问 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标

Azure Monitor 目前不支持在指标资源管理器中分析 Azure 虚拟网络指标。 若要查看 Azure 虚拟网络指标，请在要分析的虚拟网络中，选择“监视”下的“指标” 。

:::image type="content" source="./media/monitor-virtual-network/metrics.png" alt-text="虚拟网络的指标仪表板的屏幕截图。" lightbox="./media/monitor-virtual-network/metrics-expanded.png":::

有关为 Azure 虚拟网络收集的平台指标列表，请参阅[有关监视 Azure 虚拟网络数据的参考 - 指标](monitor-virtual-network-reference.md#metrics)。

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

## <a name="analyzing-logs"></a>分析日志

Azure 虚拟网络不支持资源日志。

有关为虚拟网络中的资源收集的资源日志类型列表，请参阅[有关监视虚拟网络数据的参考](monitor-virtual-network-reference.md#resource-logs)   

[活动日志](../azure-monitor/essentials/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别的事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点。

下表列出了针对 Azure 虚拟网络的常用和建议使用的活动警报规则。

| 警报类型 | 条件 | 说明  |
|:---|:---|:---|
| 创建或更新虚拟网络 | 事件级别：所有选定项；状态：所有选定项；事件发起者：所有服务和用户 | 当用户创建虚拟网络或者对虚拟网络进行配置更改时。 |
| 删除虚拟网络 | 事件级别：所有选定项；状态：已启动 | 当用户删除虚拟网络时。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 虚拟网络创建的指标、日志和其他重要值的参考信息，请参阅[有关监视虚拟网络数据的参考](monitor-virtual-network-reference.md)。
* 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/overview.md)。