---
title: 使用用于容器的 Azure Monitor 配置 PV 监视 | Microsoft Docs
description: 本文介绍如何使用用于容器的 Azure Monitor 对具有永久性卷的 Kubernetes 群集配置监视。
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: d7da6bc88e7c8526e3940714502d3c92d2f37dd8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704467"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>使用用于容器的 Azure Monitor 配置 PV 监视

从代理版本 *ciprod10052020* 开始，容器集成代理 Azure Monitor 现在支持监视 PV (永久性卷) 使用情况。

## <a name="pv-metrics"></a>PV 指标

通过以 60 秒的间隔收集以下指标并将其存储在 InsightMetric 表中，用于容器的 Azure Monitor 会自动开始监视 PV。

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|特定永久性卷的已用空间（以字节为单位），并包含由特定 pod 使用的声明。 `pvCapacityBytes` 在“标记”字段中折叠为一个维度，以便降低数据引入成本并简化查询。|

## <a name="monitor-persistent-volumes"></a>监视永久性卷

用于容器的 Azure Monitor 在每个集群的工作簿中包含此指标的预配置图表。 可以通过在左侧窗格中选择“工作簿”，直接从 AKS 群集的“工作负载详细信息”工作簿的“永久性卷”选项卡中找到图表，也可以通过 Insight 中的“查看工作簿”下拉列表找到 。 还可以启用针对 PV 使用情况的建议警报，并在 Log Analytics 中查询这些指标。  

![Azure Monitor PV 工作负载工作簿示例](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>后续步骤

- [在此处](./container-insights-agent-config.md)详细了解收集的 PV 指标。
