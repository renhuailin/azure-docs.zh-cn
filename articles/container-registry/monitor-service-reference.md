---
title: 监视 Azure 容器注册表数据参考
description: 监视 Azure 容器注册表时，需要重要的参考材料。 提供有关指标、资源日志和日志架构的详细信息。
author: dlepow
ms.author: danlep
ms.topic: reference
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 03/19/2021
ms.openlocfilehash: 86c8047727711655a262425032b7a9fe3daa6dc1
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112059969"
---
# <a name="monitoring-azure-container-registry-data-reference"></a>监视 Azure 容器注册表数据参考

有关收集和分析 Azure 容器注册表监视数据的详细信息，请参阅[监视 Azure 容器注册表](monitor-service.md)。

## <a name="metrics"></a>指标

### <a name="container-registry-metrics"></a>容器注册表指标

资源提供程序和类型：[Microsoft.ContainerRegistry/registries](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries)

| 指标 | 是否可通过诊断设置导出？ | 计价单位 | 聚合类型 | 说明 | 维度  |  
|:-------|:-----|:-----|:------------|:------------------|:----- |
|     AgentPoolCPUTime   | 是 |   秒   | 总计 |   在专用[代理池](tasks-agent-pools.md)上运行的 [ACR 任务](container-registry-tasks-overview.md)使用的 CPU 时间             | 无 | 
|     RunDuration   | 是 |  毫秒   |  总计 |  [ACR 任务](container-registry-tasks-overview.md)运行的持续时间       | 无 | 
|     StorageUsed   |  否 | 字节   |   平均值 | 容器注册表使用的存储<br/><br/>所有存储库中唯一层和共享层、清单文件和副本的存储总和<sup>1</sup>            | 地理位置 | 
|     SuccessfulPullCount | 是  |   计数   | 总计 | 从注册表成功拉取容器映像和其他项目              | 无 | 
|     SuccessfulPushCount   | 是 |   计数   | 总计 | 将容器映像和其他项目成功推送到注册表          | 无 | 
|     TotalPullCount   |   是 | 计数   |     总计 |  从注册表中拉取的容器映像和其他项目总数         | 无 | 
|     TotalPushCount   | 是 |  计数   |   总计 |  将容器映像和其他项目推送到注册表的总数           | 无 | 

<sup>1</sup> 由于层共享，使用的注册表存储可能小于各个存储库的存储总和。 在你[删除](container-registry-delete.md)存储库或标记时，只能恢复清单文件使用的存储和引用的唯一层。

有关详细信息，请参阅 [Azure Monitor 支持的所有平台指标的列表](../azure-monitor/essentials/metrics-supported.md)。

## <a name="metric-dimensions"></a>指标维度

有关指标维度定义的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

Azure 容器注册表具有以下与其指标关联的维度。

| 维度名称 | 说明 |
| ------------------- | ----------------- |
| **地理位置** | 注册表或[异地副本](container-registry-geo-replication.md)的 Azure 区域。 |


## <a name="resource-logs"></a>资源日志

本节列出了可为 Azure 容器注册表收集的资源日志类型。 

有关参考，请参阅 [Azure Monitor 支持的所有资源日志类别类型](../azure-monitor/essentials/resource-logs-schema.md)列表。

### <a name="container-registries"></a>容器注册表

资源提供程序和类型：[Microsoft.ContainerRegistry/registries](../azure-monitor/essentials/resource-logs-categories.md#microsoftcontainerregistryregistries)

| 类别 | 显示名称 | 详细信息  |
|:---------|:-------------|------------------|
| ContainerRegistryLoginEvents  | 登录事件 | 注册表身份验证事件和状态，包括传入标识和 IP 地址 |
| ContainerRegistryRepositoryEvents | 存储库事件           | 针对映像和注册表存储库中其他项目的操作<br/><br/> 记录以下操作：推送、拉取、取消标记、删除（包括存储库删除）、清除标记和清除清单<sup>1</sup> |

<sup>1</sup> 仅当配置了注册表[保留策略](container-registry-retention-policy.md)时，才记录清除事件。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表

本节介绍与 Azure 容器注册表相关并可通过 Log Analytics 进行查询的所有 Azure Monitor 日志 Kusto 表。 

### <a name="container-registry"></a>容器注册表

| 表 |  说明 | 
|:---------|:-------------|
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)   | Azure 活动日志中的条目，可用于深入了解 Azure 中发生的任何订阅级别或管理组级别事件。 | 
| [AzureMetrics](/azure/azure-monitor/reference/tables/azuremetrics) | 由 Azure 服务发出的指标数据，用于衡量其运行状况和性能。    |  
|  [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/containerregistryloginevents)               | Azure 容器注册表登录审核日志                             |                                                     
|  [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/containerregistryrepositoryevents)               | Azure 容器注册表存储库审核日志                         |                                                    

有关所有 Azure Monitor 日志/Log Analytics 表的参考，请参阅 [Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)。

## <a name="activity-log"></a>活动日志

下表列出了可能在[活动日志](../azure-monitor/essentials/activity-log.md)中创建的与 Azure 容器注册表相关的操作。 此列表并未囊括所有方式。

| 操作 | 说明 |
|:---|:---|
| 创建或更新容器注册表 | 创建一个容器注册表或更新一个注册表属性 |
| 删除容器注册表 | 删除一个容器注册表 |
| 列出容器注册表的登录凭据 | 显示注册表的管理员帐户凭据 |
| 导入映像 | 将映像或其他项目导入注册表 |
| 创建角色分配 | 为标识分配用于访问资源的 RBAC 角色  |


## <a name="schemas"></a>架构

Azure 容器注册表的资源日志正在使用以下架构。

| 架构 | 说明 |
|:--- |:---|
| [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/ContainerRegistryLoginEvents)  | 注册表身份验证事件和状态的架构，包括传入标识和 IP 地址 |
| [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/ContainerRegistryRepositoryEvents) | 针对映像和注册表存储库中其他项目的操作的架构 |
## <a name="next-steps"></a>后续步骤

- 有关监视 Azure 容器注册表的说明，请参阅[监视 Azure 容器注册表](monitor-service.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](/azure/azure-monitor/overview)。
