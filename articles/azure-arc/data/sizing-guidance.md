---
title: 大小调整指南
description: 计划已启用 Azure Arc 的数据服务的部署大小。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 6ac2397823dffd2c31cbe534c8fea1886e84558d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733496"
---
# <a name="sizing-guidance"></a>调整大小指南


## <a name="overview-of-sizing-guidance"></a>调整大小指南概述

在计划部署 Azure Arc 数据服务时，应计划运行 Azure Arc 数据控制器所需的适当的计算、内存和存储容量，以及要部署的 SQL 托管实例和超大规模 PostgreSQL 服务器组的数量。  由于已启用 Azure Arc 的数据服务部署在 Kubernetes 上，因此随时间推移可以通过添加其他计算节点或存储的方式，灵活地为 Kubernetes 群集添加更多容量。  本指南将提供最低要求的指导，并提供某些常见要求推荐大小的指导。

## <a name="general-sizing-requirements"></a>一般调整大小要求

> [!NOTE]
> 如果不熟悉本文中的概念，可以阅读有关 [Kubernetes 资源治理](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)和 [Kubernetes 大小表示法](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)的详细信息。

内核数必须是大于或等于一的整数值。

使用 Azure CLI (az) 进行部署时，应使用 2 的幂数的形式来指定内存值，即使用后缀：Ki、Mi 或 Gi。

限制值必须始终大于请求值（如果指定）。

在 SQL 托管实例和超大规模 PostgreSQL 服务器组上，内核的限制值是计费指标。

## <a name="minimum-deployment-requirements"></a>最低部署要求

已启用 Azure Arc 的数据服务的最低部署大小可以视为：Azure Arc 数据控制器加一个 SQL 托管实例，再加一个包含两个工作器节点的超大规模 PostgreSQL 服务器组。  对于这种配置，Kubernetes 群集上至少需要提供 16 GB RAM 和 4 个内核的可用容量。  应确保所用 Kubernetes 节点大小至少提供 8 GB RAM 和 4 个内核，并且所有 Kubernetes 节点总共有 16 GB RAM 可用容量。  例如，可以使用 1 个节点，具有 32 GB RAM 和 4 个核心；或者使用 2 个节点，分别具有 16 GB RAM 和 4 个核心。

有关存储调整大小的详细信息，请参阅“[存储配置](storage-configuration.md)”一文。

## <a name="data-controller-sizing-details"></a>数据控制器调整大小详细信息

数据控制器是部署到 Kubernetes 群集的 pod 集合，用于提供 API、控制器服务、引导程序以及监视数据库和仪表板。  下表描述了内存和 CPU 请求及限制的默认值。

|Pod 名称|CPU 请求|内存请求|CPU 限制|内存限制|说明|
|---|---|---|---|---|---|
|bootstrapper|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|controldb|200m|3Gi|800m|6Gi||
|logsdb|200m|1600Mi|2|1600Mi||
|logsui|100m|500Mi|2|2Gi||
|metricsdb|200m|800Mi|400m|2Gi||
|metricsdc|100m|200Mi|200m|300Mi|Metricsdc 是在群集中每个 Kubernetes 节点上创建的守护程序集。  表中的数字按节点列出。 如果创建数据控制器之前在部署配置文件中设置了 allowNodeMetricsCollection = false，则不会创建 metricsdc 守护程序集。|
|metricsui|20m|200Mi|500m|200Mi||

可以在部署配置文件或 datacontroller YAML 文件中替代 controldb 和 control Pod 的默认设置。  示例：

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

有关存储调整大小的详细信息，请参阅“[存储配置](storage-configuration.md)”一文。

## <a name="sql-managed-instance-sizing-details"></a>SQL 托管实例调整大小详细信息

每个 SQL 托管实例都必须具有以下最低资源请求和限制：

|服务层级|常规用途|业务关键（预览版）|
|---|---|---|
|CPU 请求|最小值：1；最大值：24；默认值：2|最小值：1；最大值：无限制；默认值：4|
|CPU 限制|最小值：1；最大值：24；默认值：2|最小值：1；最大值：无限制；默认值：4|
|内存请求|最小值：2Gi；最大值：128Gi；默认值：4Gi|最小值：2Gi；最大值：无限制；默认值：4Gi|
|内存限制|最小值：2Gi；最大值：128Gi；默认值：4Gi|最小值：2Gi；最大值：无限制；默认值：4Gi|

创建的每个 SQL 托管实例 Pod 都有三个容器：

|容器名称|CPU 请求|内存请求|CPU 限制|内存限制|说明|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|未指定|未指定|fluentbit 容器资源请求是除了为 SQL 托管实例指定的请求之外的请求。|
|arc-sqlmi|用户指定或未指定。|用户指定或未指定。|用户指定或未指定。|用户指定或未指定。|
|collectd|未指定|未指定|未指定|未指定|

所有永久性卷的默认卷大小均为 5Gi。

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>超大规模 PostgreSQL 服务器组的调整大小详细信息

每个超大规模 PostgreSQL 服务器组节点都必须满足以下最低资源请求：
- 内存：256Mi
- 内核数：1 个

创建的每个超大规模 PostgreSQL 服务器组协调器或工作器 Pod 都具有三个容器：

|容器名称|CPU 请求|内存请求|CPU 限制|内存限制|说明|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|未指定|未指定|fluentbit 容器资源请求是除了为超大规模 PostgreSQL 服务器组节点指定的请求之外的请求。|
|postgres|用户指定或未指定。|用户指定或 256Mi（默认）。|用户指定或未指定。|用户指定或未指定。||
|telegraf|未指定|未指定|未指定|未指定||

## <a name="cumulative-sizing"></a>累计调整大小

已启用 Azure Arc 的数据服务所需的环境整体大小主要取决于要创建的数据库实例的数量和大小。  整体大小可能很难提前预测，我们知道实例数量会不断增加和减少，每个数据库实例所需的资源量也会变化。

给定的已启用 Azure Arc 的数据服务环境的基线大小为需要 4 个内核和 16 GB RAM 的数据控制器的大小。  在此基础之上，可以添加数据库实例所需的累计总内核数和总内存。  对于 SQL 托管实例，Pod 数量等于创建的 SQL 托管实例数量。  对于超大规模 PostgreSQL 服务器组，Pod 数量等于工作器节点数加上一个协调器节点。  例如，如果 PostgreSQL 服务器组中有 3 个工作器节点，则 Pod 总数为 4 个。

除为每个数据库实例请求的内核和内存之外，还应为代理容器添加250m 内核和 250Mi RAM 容量。

下面是一个计算大小的示例。

要求：

- “SQL1”：1 个 SQL 托管实例，具有 16 GB RAM、4 个内核
- “SQL2”：1 个 SQL 托管实例，具有 256 GB RAM、16 个内核
- “Postgres1”：1 个超大规模 PostgreSQL 服务器组，具有 12 GB RAM、4 个内核的 4 个工作器

大小计算方式：

- “SQL1”的大小为：1 个 Pod * 每个 Pod 的代理（[16 Gi RAM、4 个内核] + [250Mi RAM、250m 内核]） = 16.25 Gi RAM、4.25 个内核。
- “SQL2”的大小为：1 个 Pod * 每个 Pod 的代理（[256 Gi RAM、16 个内核] + + [250Mi RAM、250m 内核]） = 256.25 Gi RAM、16.25 个内核。
- SQL 1 和 SQL 2 的总大小为：（16.25 GB + 256.25 Gi） = 272.5 GB RAM；（4.25 个内核 + 16.25 个内核）= 20.5 个内核。

- “Postgres1”的大小为：（4 个工作器 Pod + 1 个协调器 Pod）*（[12 GB RAM，4 个内核] + 每个 Pod 的代理 [250Mi RAM，250m 内核]）= 61.25 GB RAM、21.25 个内核。

- 该数据库实例所需的总容量为：SQL 272.5 GB RAM、20.5 个内核 (SQL) +超大规模 PostgreSQL 服务器组 61.25 GB RAM、21.25 个内核 = 333.75 GB RAM、42.5 个内核。

- 数据库实例和数据控制器所需的总容量为：数据库实例 333.75 GB RAM、42.5 个内核 + 数据控制器 16 GB RAM、4 个内核 = 349.75 GB RAM、46.5 个内核。

有关存储调整大小的详细信息，请参阅“[存储配置](storage-configuration.md)”一文。

## <a name="other-considerations"></a>其他注意事项

切记，对于内核或 RAM 指定的数据库实例请求大小不能超过群集中 Kubernetes 节点的可用容量。  例如，如果 Kubernetes 群集中的最大 Kubernetes 节点为 256 GB RAM 和 24 个内核，则无法创建请求 512 GB RAM 和 48 个内核的数据库实例。  

最好在 Kubernetes 节点中至少保留 25% 的可用容量，以便 Kubernetes 高效地安排要创建的 Pod，实现弹性缩放和 Kubernetes 节点的滚动升级，以及按需规划长期发展。

在计算大小时，不要忘记添加 Kubernetes 系统 Pod 和任何其他工作负载的资源需求，它们可能与同一 Kubernetes 群集中已启用 Azure Arc 的数据服务共享容量。

如果要在进行计划内维护和灾难持续期间保持高可用性，应针对群集中至少一个 Kubernetes 节点在任何指定时间点都无法使用的情况做好计划。  Kubernetes 将尝试重新安排因维护或故障而停止的节点上运行的 Pod。  如果剩余的节点上没有可用容量，则在重新具有可用容量之前不会重新安排创建这些 Pod。  对于大型数据库实例要特别小心。  例如，如果只有一个大型 Kubernetes 节点可以满足大型数据库实例的资源需求，但该节点发生故障，则 Kubernetes 无法再将该数据库实例 Pod 安排到其他 Kubernetes 节点上。

切记 [Kubernetes 群集大小的上限](https://kubernetes.io/docs/setup/best-practices/cluster-large/)。

Kubernetes 管理员可能已对你的命名空间/项目设置[资源配额](https://kubernetes.io/docs/concepts/policy/resource-quotas/)。  在计划数据库实例大小时，切记这一点。
