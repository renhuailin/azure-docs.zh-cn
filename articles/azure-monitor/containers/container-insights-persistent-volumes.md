---
title: 使用容器见解配置 PV 监视 | Microsoft Docs
description: 本文介绍如何使用容器见解对具有永久性卷的 Kubernetes 群集配置监视。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 593ee36bf01a7f2979b7db9ef8b28e0f805077ad
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110065649"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>使用容器见解配置 PV 监视

从代理版本“ciprod10052020”开始，集成了容器见解的代理现在支持监视 PV（永久性卷）使用情况。 对于代理版本 ciprod01112021，该代理支持监视 PV 清单，包括有关状态、存储类别、类型、访问模式和其他详细信息的信息。
## <a name="pv-metrics"></a>PV 指标

通过以 60 秒的间隔收集以下指标并将其存储在“InsightMetric”表中，容器见解会自动开始监视 PV 使用情况。

| 指标名称 | 指标维度（标记） | 指标说明 |
|-----|-----------|----------|
| `pvUsedBytes`| `podUID`, `podName`, `pvcName`, `pvcNamespace`, `capacityBytes`, `clusterId`, `clusterName`| 特定永久性卷的已用空间（以字节为单位），并包含由特定 pod 使用的声明。 `capacityBytes` 在“标记”字段中折叠为一个维度，以便降低数据引入成本并简化查询。|

请在[此处](./container-insights-agent-config.md)详细了解如何配置收集的 PV 指标。

## <a name="pv-inventory"></a>PV 清单

通过以 60 秒的间隔收集以下信息并将其存储在“KubePVInventory”表中，容器见解会自动开始监视 PV。

|数据 |数据源| 数据类型| 字段|
|-----|-----------|----------|-------|
|Kubernetes 群集中永久性卷的清单 |Kube API |`KubePVInventory` |    `PVName`, `PVCapacityBytes`, `PVCName`, `PVCNamespace`, `PVStatus`, `PVAccessModes`, `PVType`, `PVTypeInfo`, `PVStorageClassName`, `PVCreationTimestamp`, `TimeGenerated`, `ClusterId`, `ClusterName`, `_ResourceId` |

## <a name="monitor-persistent-volumes"></a>监视永久性卷

容器见解在每个群集的工作簿模板中都包含此使用情况指标的预配置图表和清单信息。 还可以启用针对 PV 使用情况的建议警报，并在 Log Analytics 中查询这些指标。  

### <a name="workload-details-workbook"></a>工作负载详细信息工作簿

通过在左侧窗格中，从“见解”窗格中的“查看工作簿”下拉菜单或“报表(预览)”选项卡选择“工作簿”，可以直接从 AKS 群集中的“工作负载详细信息”工作簿的“永久性卷”选项卡中找到特定工作负载的使用情况图表  。


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Azure Monitor PV 工作负载工作簿示例":::

### <a name="persistent-volume-details-workbook"></a>永久性卷详细信息工作簿

通过从左侧窗格中选择“工作簿”，可以直接从 AKS 群集的“永久性卷详细信息”工作簿中找到永久性卷清单的概述。 也可以从“见解”窗格的“查看工作簿”下拉列表中或从“见解”窗格的“报表”选项卡中打开此工作簿。 


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Monitor PV 详细信息工作簿示例":::

### <a name="persistent-volume-usage-recommended-alert"></a>永久性卷使用情况建议警报
可以启用建议的警报，以在 Pod 的平均 PV 使用率超过 80% 时发出警报。 在[此处](./container-insights-metric-alerts.md)详细了解警报，并在[此处](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps)了解如何替代默认阈值。
## <a name="next-steps"></a>后续步骤

- [在此处](./container-insights-agent-config.md)详细了解收集的 PV 指标。