---
title: 配置采用容器 insights 的 PV 监视 |Microsoft Docs
description: 本文介绍如何通过容器见解配置监视包含持久卷的 Kubernetes 群集。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 578cfe128b7445f8b09771999d1e653e92c4befa
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200693"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>配置采用容器 insights 的 PV 监视

从代理版本 ciprod10052020 开始，集成了用于容器的 Azure Monitor 的代理现在支持监视 PV（永久性卷）使用情况。 使用代理版本 *ciprod01112021*，代理支持监视 PV 清单，其中包括有关状态、存储类、类型、访问模式和其他详细信息的信息。
## <a name="pv-metrics"></a>PV 指标

通过收集以下60指标，并将其存储在 **InsightMetrics** 表中，Container insights 自动开始监视 PV 使用情况。

|指标名称 |指标维度 (标记) |指标说明 | | `pvUsedBytes`| podUID、podName、pvcName、pvcNamespace、capacityBytes、clusterId、clusterName |特定永久性卷的已用空间（以字节为单位），具有特定 pod 使用的声明。 `capacityBytes` 作为 "标记" 字段中的维度折叠，以减少数据引入成本并简化查询。 |

[在此处](https://aka.ms/ci/pvconfig)了解有关配置收集的 PV 度量值的详细信息。

## <a name="pv-inventory"></a>PV 库存

容器 Azure Monitor 自动开始监视 PVs，方法是：以60秒的时间间隔收集以下信息并将其存储在 **KubePVInventory** 表中。

|数据 |数据源| 数据类型| 字段|
|-----|-----------|----------|-------|
|在 Kubernetes 群集中清点持久卷 |Kube API |`KubePVInventory` | PVName、PVCapacityBytes、PVCName、PVCNamespace、PVStatus、PVAccessModes、PVType、PVTypeInfo、PVStorageClassName、PVCreationTimestamp、TimeGenerated、ClusterId、ClusterName、_ResourceId |

## <a name="monitor-persistent-volumes"></a>监视永久性卷

容器 Azure Monitor 包括此使用情况指标的预配置图表和每个群集的工作簿模板中的清单信息。 你还可以针对 PV 使用启用建议的警报，并在 Log Analytics 中查询这些指标。  

### <a name="workload-details-workbook"></a>工作负荷详细信息工作簿

您可以直接从 AKS 群集的 " **工作负荷详细信息** " 工作簿的 "永久卷" 选项卡中，通过从 "insights" 窗格的 " **查看工作簿** " 下拉列表中选择工作簿，或从 "insights" 窗格的 " **报表 (预览") "选项卡** 中找到特定工作负荷的使用情况图表。


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Azure Monitor PV 工作负载工作簿示例":::

### <a name="persistent-volume-details-workbook"></a>永久性卷详细信息工作簿

通过从 AKS **群集的 "** 查看工作簿" 下拉列表中选择 "工作簿"，从 "见解" 窗格的 " **查看工作簿** " 下拉列表中选择 "工作簿"，或从 "insights" 窗格的 " **报表 (预览")** "选项卡中，可以直接从群集中找到永久卷清单。


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Monitor PV 详细信息工作簿示例":::

### <a name="persistent-volume-usage-recommended-alert"></a>永久性卷使用建议的警报
你可以启用建议的警报，以便在 pod 的平均 PV 用量高于80% 时向你发出警报。 [在](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts#configure-alertable-metrics-in-configmaps)[此处了解有关警报的](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts)详细信息以及如何覆盖默认阈值。
## <a name="next-steps"></a>后续步骤

- [在此处](./container-insights-agent-config.md)详细了解收集的 PV 指标。
