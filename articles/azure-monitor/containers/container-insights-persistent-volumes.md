---
title: 配置采用容器 insights 的 PV 监视 |Microsoft Docs
description: 本文介绍如何通过容器见解配置监视包含持久卷的 Kubernetes 群集。
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713722"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>配置采用容器 insights 的 PV 监视

从代理版本 *ciprod10052020* 开始，Container insights 集成代理现在支持监视 PV (持久卷) 使用情况。

## <a name="pv-metrics"></a>PV 指标

容器见解会按60sec 间隔收集以下度量值并将其存储在 **InsightMetrics** 表中，从而自动开始监视 PV。

|指标名称 |指标维度（标记） |说明 |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|特定永久性卷的已用空间（以字节为单位），并包含由特定 pod 使用的声明。 `pvCapacityBytes` 在“标记”字段中折叠为一个维度，以便降低数据引入成本并简化查询。|

## <a name="monitor-persistent-volumes"></a>监视永久性卷

容器见解为每个群集的工作簿中的此指标包含预配置的图表。 可以通过在左侧窗格中选择“工作簿”，直接从 AKS 群集的“工作负载详细信息”工作簿的“永久性卷”选项卡中找到图表，也可以通过 Insight 中的“查看工作簿”下拉列表找到 。 还可以启用针对 PV 使用情况的建议警报，并在 Log Analytics 中查询这些指标。  

![Azure Monitor PV 工作负载工作簿示例](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>后续步骤

- [在此处](./container-insights-agent-config.md)详细了解收集的 PV 指标。
