---
title: 存储配置
description: 介绍已启用 Azure Arc 的数据服务存储配置选项
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: conceptual
ms.openlocfilehash: 503bfec47621e5663e6626e1285840625e92c46c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732161"
---
# <a name="storage-configuration"></a>存储配置


## <a name="kubernetes-storage-concepts"></a>Kubernetes 存储概念

Kubernetes 在基础虚拟化技术堆栈（可选）和硬件之上提供基础结构抽象层。 Kubernetes 通过[存储类](https://kubernetes.io/docs/concepts/storage/storage-classes/)的方式来抽象掉存储。 预配 Pod 时，可以为每个卷指定存储类。 预配 Pod 之后，调用存储类[配置器](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)来预配存储，然后在该预配的存储上创建[永久性卷](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)，再使用[永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)将 Pod 装载到永久性卷上。

Kubernetes 提供了一种方法使存储基础结构提供程序能插入扩展 Kubernetes 的驱动程序（也称为“加载项”）。 存储加载项必须符合[容器存储接口标准](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)。 可在这个非最终的 [CSI 驱动程序列表](https://kubernetes-csi.github.io/docs/drivers.html)中找到数十个加载项。 使用哪个 CSI 驱动程序将取决于多种因素，例如是否在云托管的托管 Kubernetes 服务中运行，或者对硬件使用哪个 OEM 提供程序。

可以通过运行以下命令来查看在 Kubernetes 群集中配置的存储类：

```console
kubectl get storageclass
```

Azure Kubernetes 服务 (AKS) 群集的示例输出：

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

可以通过运行以下命令获取有关存储类的详细信息：

```console
kubectl describe storageclass/<storage class name>
```

示例：

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

可以通过运行以下命令来查看当前预配的永久性卷和永久性卷声明：

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

显示永久性卷的示例：

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

显示永久性卷声明的示例：

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>当选择存储配置时要考虑的因素

选择正确的存储类对于数据复原能力和性能非常重要。 如果选择了错误的存储类，那么在出现硬件失败的情况下数据会面临全部数据丢失的风险，也可能导致性能不佳的情况。

通常两种类型的存储：

- **本地存储** - 给定节点的本地硬盘驱动器上预配的存储。 这种类型的存储在性能方面可能是理想的选择，但需要通过在多个节点间复制数据来进行专门设计以实现数据冗余。
- **远程共享存储** - 在某些远程存储设备上预配的存储，例如 SAN、NAS 或者 EBS 或 Azure 文件存储等云存储服务。 这种类型的存储通常自动提供数据冗余，但不如本地存储那样快。

## <a name="nfs-based-storage-classes"></a>基于 NFS 的存储类

根据 NFS 服务器和存储类配置程序的配置，可能需要在数据库实例的 Pod 配置中设置 `supplementalGroups`，并将 NFS 服务器配置更改为使用客户端传入的组 ID（而不是使用传入的用户 ID 在服务器上查找）。 请咨询 NFS 管理员，确定是否为这种情况。

`supplementalGroups` 属性采用值的数组，其可被设置为 Azure Arc 数据控制器部署的一部分，并将由 Azure Arc 数据控制器配置的所有数据库实例使用。

为设置此属性，请运行以下命令：

```azurecli
az arcdata dc config add --path custom/control.json --json-values 'spec.security.supplementalGroups="1234556"'
```

### <a name="data-controller-storage-configuration"></a>数据控制器存储配置

Azure Arc 中用于数据服务的某些服务需要配置为使用远程共享存储，因为这些服务不能复制数据。 这些服务位于数据控制器 Pod 的集合中：

|**服务**|**永久性卷声明**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**控制器 SQL 实例**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**控制器 API 服务**|`<namespace>/data-controller`|

预配数据控制器时，用于其中每个永久性卷的存储类通过以下方式之一进行指定：将 --storage-class | -sc 参数传递到 `az arcdata dc create` 命令，或者在所用的 control.json 部署模板文件中设置存储类。  如果使用 Azure 门户在直接连接模式下创建数据控制器，则所选的部署模板将在模板中预定义存储类，或者如果选择的模板没有预定义的存储类，则系统会提示你输入一个。  如果使用自定义部署模板，则可以指定存储类。

部署模板是现成可用的，为其指定的默认存储类适用于目标环境，但可以在部署过程中重写该默认存储类。 若要在部署时更改数据控制器 Pod 的存储类配置，请参阅[部署配置文件](create-data-controller.md)中的详细步骤。

如果使用 --storage-class | -sc 参数设置存储类，则该存储类将用于日志和数据存储类。 如果在部署模板文件中设置存储类，则可以为日志和数据指定不同的存储类。

为数据控制器 Pod 选择存储类时要考虑的重要因素：

- 必须使用远程的共享存储类才能确保数据持续性，这样如果 Pod 或节点出现故障并且重启 Pod 后，它就可以再次连接到永久性卷。
- 写入控制器 SQL 实例、指标 DB 和日志 DB 的数据的容量通常非常低，对延迟不敏感，因此超快性能存储并不重要。 如果你的用户频繁使用 Grafana 和 Kibana 接口，并且你有大量的数据库实例，则你的用户可能从更快的存储中获益。
- 所需的存储容量随已部署的数据库实例数量而变化，因为针对每个数据库实例收集日志和指标。 数据在日志和指标 DB 中保留两 (2) 周，然后才被清除。 
- 更改存储类后期部署很困难，并且不会对其进行记录，也不支持这么做。 请确保在部署时正确选择存储类。

> [!NOTE]
> 如果未指定存储类，将使用默认的存储类。 每个 Kubernetes 群集只能有一个默认的存储类。 你可以[更改默认的存储类](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)。

### <a name="database-instance-storage-configuration"></a>数据库实例存储配置

每个数据库实例都包含数据、日志和备份永久性卷。 可以在部署时指定这些永久性卷的存储类。 如果未指定存储类，将使用默认的存储类。

使用 `az sql mi-arc create` 或 `az postgres arc-server create` 创建实例时，可以使用四个参数来设置存储类：

|参数名称，简短名称|用于|
|---|---|
|`--storage-class-data`, `-d`|用于指定所有数据文件（包括事务日志文件）的存储类|
|`--storage-class-logs`, `-g`|用于指定所有日志文件的存储类|
|`--storage-class-data-logs`|用于指定数据库事务日志文件的存储类。|
|`--storage-class-backups`|用于指定所有备份文件的存储类。|

下表列出了映射到数据和日志永久性卷的 Azure SQL 托管实例容器内的路径：

|参数名称，简短名称|mssql-miaa 容器内的路径|说明|
|---|---|---|
|`--storage-class-data`, `-d`|/var/opt|包含 mssql 安装和其他系统进程的目录。 mssql 目录包含默认数据（包括事务日志）、错误日志和备份目录|
|`--storage-class-logs`, `-g`|/var/log|包含存储控制台输出（stderr、stdout）、容器内进程的其他日志记录信息的目录|

下表列出了映射到数据和日志永久性卷的 PostgreSQL 实例容器内的路径：

|参数名称，简短名称|postgres 容器内的路径|说明|
|---|---|---|
|`--storage-class-data`, `-d`|/var/opt/postgresql|包含 postgres 安装的数据和日志目录|
|`--storage-class-logs`, `-g`|/var/log|包含存储控制台输出（stderr、stdout）、容器内进程的其他日志记录信息的目录|

对于数据文件、日志和备份，每个数据库实例都有一个单独的永久性卷。 这意味着根据卷配置程序预配存储的方式，将为每种文件类型分离 I/O。 每个数据库实例都有其自己的永久性卷声明和永久性卷。

如果给定数据库实例上有多个数据库，则所有数据库都将使用相同的永久卷声明、永久性卷和存储类。 所有备份（差异日志备份和完整备份）都将使用相同的永久卷声明和永久性卷。 数据库实例 Pod 的永久性卷声明如下所示：

|**实例**|**永久性卷声明**|
|---|---|
|**Azure SQL 托管实例**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure database for PostgreSQL 实例**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL 超大规模**|`<namespace>/logs-<instance namme>-<ordinal>``<namespace>/data-<instance namme>-<ordinal>` *（序数范围为 0 到 W，其中 W 为辅助角色的数目）*|

为数据库实例 Pod 选择存储类时要考虑的重要因素：

- 可以在单个 Pod 模式或多个 Pod 模式下部署数据库实例。 单个 Pod 模式的示例是常规用途定价层 Azure SQL 托管实例。 多个 Pod 模式的示例是高度可用的业务关键型定价层 Azure SQL 托管实例。 使用单个 Pod 模式部署的数据库实例必须使用远程的共享存储类才能确保数据持续性，这样如果 Pod 或节点出现故障并且重启 Pod 后，它就可以再次连接到永久性卷。 与此相反，高度可用 Azure SQL 托管实例使用 Always On 可用性组，以同步或异步方式将数据从一个实例复制到另一个实例。 尤其是在同步复制数据的情况下，更是如此，届时始终会存在多个数据副本（通常为三个副本）。 因此可以为数据和日志文件使用本地存储或远程共享存储类。 如果利用本地存储，即使 Pod、节点或存储硬件发生故障，仍会保留数据，因为有多个数据副本。 由于这种灵活性，你可以选择使用本地存储来提高性能。
- 数据库性能很大程度上取决于给定存储设备的 I/O 吞吐量。 如果数据库进行大量读取或大量写入，则选择的存储类应具有为该类型工作负荷设计的硬件。 例如，如果数据库主要用于写入，建议选择使用 RAID 0 的本地存储。 如果数据库主要用于读取少量“热数据”，但总体上冷数据存储量很大，则可以选择支持分层存储的 SAN 设备。 选择适当的存储类与选择用于任何数据库的存储类型的差别不大。
- 如果使用本地存储卷配置程序，请确保为数据、日志和备份预配的本地卷各自登录到不同的基础存储设备，以避免磁盘 I/O 争用。 OS 还应位于装载到单独磁盘的卷上。 这实质上是物理硬件上数据库实例应遵循的相同指导原则。
- 由于给定实例上的所有数据库共享永久性卷声明和永久性卷，因此请确保不要将繁忙数据库实例同时放在同一数据库实例上。 如果可能，请将繁忙数据库单独置于其自己的数据库实例中以避免 I/O 争用。 此外，使用节点标签以将数据库实例置于不同节点上，以便在多个节点之间分配总体 I/O 流量。 如果使用虚拟化，则务必要考虑不只是在节点级别分配 I/O 流量，还要在给定物理主机上所有节点 VM 所产生的组合 I/O 活动级别进行分配。

## <a name="estimating-storage-requirements"></a>预估存储要求
每个包含有状态数据的 Pod 都使用至少两个永久性卷：一个永久性卷用于数据，另一个永久性卷用于日志。 下表列出了单个数据控制器、Azure SQL 托管实例、Azure Database for PostgreSQL 实例和 Azure PostgreSQL 超大规模实例所需的永久性卷数量：

|资源类型|有状态 Pod 数量|所需的永久性卷数量|
|---|---|---|
|数据控制器|4 (`control`, `controldb`, `logsdb`, `metricsdb`)|4 * 2 = 8|
|Azure SQL 托管实例|1|2|
|Azure Database for PostgreSQL 实例|1| 2|
|Azure PostgreSQL 超大规模|1 + W（W = 辅助角色数量）|2 * (1 + W)|

下表显示了示例部署所需的永久性卷的总数：

|资源类型|实例数|所需的永久性卷数量|
|---|---|---|
|数据控制器|1|4 * 2 = 8|
|Azure SQL 托管实例|5|5 * 2 = 10|
|Azure Database for PostgreSQL 实例|5| 5 * 2 = 10|
|Azure PostgreSQL 超大规模|2（辅助角色数 = 每个实例 4 个）|2 * 2 * (1 + 4) = 20|
|***永久性卷总数***||8 + 10 + 10 + 20 = 48|

此计算可用于基于存储配置程序或环境为 Kubernetes 群集规划存储。 例如，如果本地存储配置程序用于具有五 (5) 个节点的 Kubernetes 群集，那么对于上述示例部署，每个节点至少需要 10 个永久性卷的存储。 同样地，在预配具有五 (5) 个节点的 Azure Kubernetes 服务 (AKS) 群集时，为可以附加 10 个数据磁盘的节点池挑选合适的 VM 大小是非常重要的。 可在[此处](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)找到有关如何为 AKS 节点设置节点大小以满足存储需求的详细信息。

## <a name="choosing-the-right-storage-class"></a>选择适当的存储类

### <a name="on-premises-and-edge-sites"></a>本地和边缘站点

Microsoft 及其 OEM、OS 和 Kubernetes 合作伙伴为 Azure Arc 数据服务提供认证计划。 此计划将向客户提供与认证测试工具包的结果相似的测试结果。 这些测试将评估功能兼容性、压力测试结果以及性能和可伸缩性。 其中每个测试结果将指出已使用的 OS、已使用的 Kubernetes 分发、已使用的 HW、已使用的 CSI 附加产品以及已使用的存储类。 这将帮助客户选择符合其要求的最佳存储类、OS、Kubernetes 分发软件和硬件。 点击[此处](validation-program.md)，了解有关此计划和测试结果的详细信息。

#### <a name="public-cloud-managed-kubernetes-services"></a>公有云，托管 Kubernetes 服务

对于基于公有云的托管 Kubernetes 服务，我们提出以下建议：

|公有云服务|建议|
|---|---|
|**Azure Kubernetes 服务 (AKS)**|Azure Kubernetes 服务 (AKS) 有两种类型的存储 - Azure 文件存储和 Azure 托管磁盘。 每种类型的存储有两个定价/性能层 - 标准 (HDD) 和高级 (SSD)。 因此，在 AKS 中提供四个存储类：`azurefile`（Azure 文件存储标准层）、`azurefile-premium`（Azure 文件存储高级层）、`default`（Azure 磁盘标准层）和 `managed-premium`（Azure 磁盘高级层）。 默认存储类是 `default`（Azure 磁盘标准层）。 类型和层之间存在重大的[定价差异](https://azure.microsoft.com/pricing/details/storage/)，决策时应考虑这些差异。 对于具有高性能要求的生产工作负荷，我们建议对所有存储类使用 `managed-premium`。 对于需要考虑成本的开发/测试工作负荷、概念证明等，`azurefile` 是成本最低的选项。 所有这四种选项都可用于需要远程共享存储的情况，因为它们都是 Azure 中网络附加存储设备。 详细了解 [AKS 存储](../../aks/concepts-storage.md)。|
|**AWS 弹性 Kubernetes 服务 (EKS)**| 亚马逊的 Elastic Kubernetes Service 有一个基于 [EBS CSI 存储驱动程序](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)的主存储类。 建议对于生产工作负荷使用该存储类。 可将新的存储驱动程序 - [EFS CSI 存储驱动程序](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html)添加到 EKS 群集，但它目前处于 beta 阶段，可能会有所更改。 尽管 AWS 指出此存储驱动程序支持用于生产，但我们不建议使用它，因为它仍处于 beta 阶段并且可能有所更改。 EBS 存储类为默认值，它称为 `gp2`。 详细了解 [EKS 存储](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)。|
|**Google Kubernetes 引擎 (GKE)**|Google Kubernetes Engine (GKE) 只包含一个名为 `standard` 的存储类，它用于 [GCE 永久性磁盘](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)。 它是唯一的，也是默认的。 虽然为 GKE 提供了一个可用于直连式 SSD 的[本地静态卷配置程序](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner)，但我们不建议使用它，因为它不是由 Google 维护或提供支持的。 详细了解 [GKE 存储](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)。
