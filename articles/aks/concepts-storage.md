---
title: 概念 - Azure Kubernetes 服务 (AKS) 中的存储
description: 了解 Azure Kubernetes 服务 (AKS) 中的存储，其中包括卷、永久性卷、存储类和声明
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105776"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的应用程序存储选项

在 Azure Kubernetes 服务 (AKS) 中运行的应用程序可能需要存储和检索数据。 虽然某些应用程序工作负载可以在不需要的已清空节点上使用本地快速存储，但其他工作负载需要在 Azure 平台中更常规的数据卷上持久的存储。 

多个 pod 可能需要：
* 共享相同的数据卷。 
* 重新附加数据卷（如果在不同节点上重新计划 pod）。 

最后，你可能需要将敏感数据或应用程序配置信息注入 Pod。

本文介绍为 AKS 中的应用程序提供存储的核心概念：

- [卷](#volumes)
- [永久性卷](#persistent-volumes)
- [存储类](#storage-classes)
- [永久性卷声明](#persistent-volume-claims)

![Azure Kubernetes 服务 (AKS) 群集中的应用程序存储选项](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>卷

Kubernetes 通常将各个 pod 视为短暂的可处置资源。 应用程序可使用不同的方法来使用和保持数据。 *卷* 表示一种跨 Pod 和应用程序生命周期存储、检索及保存数据的方法。

传统卷作为 Azure 存储支持的 Kubernetes 资源进行创建。 你可以手动创建数据卷以直接分配给 Pod，也可以让 Kubernetes 自动创建它们。 数据卷可以使用 Azure 磁盘或 Azure 文件。

### <a name="azure-disks"></a>Azure 磁盘

使用 Azure 磁盘可创建 Kubernetes DataDisk 资源 。 磁盘可以使用：
* 由高性能 SSD 支持的 Azure 高级存储，或是 
* 由普通 HDD 支持的 Azure 标准存储。 

> [!TIP]
>对于大部分生产和开发工作负荷，请使用高级存储。 

由于 Azure 磁盘以 ReadWriteOnce 的形式装载，因此仅可用于单个 Pod。 对于可同时由多个 Pod 访问的存储卷，请使用 Azure 文件存储。

### <a name="azure-files"></a>Azure 文件
使用 Azure 文件存储可将 Azure 存储帐户支持的 SMB 3.0 共享装载到 Pod。 借助文件存储可跨多个节点和 Pod 共享数据，并且可以使用：
* 由高性能 SSD 支持的 Azure 高级存储，或是 
* 由普通 HDD 支持的 Azure 标准存储。

### <a name="volume-types"></a>卷类型
Kubernetes 卷不仅仅表示用于存储和检索信息的传统磁盘。 Kubernetes 卷还可以用于将数据注入 Pod 以供容器使用。 

Kubernetes 中常见的卷类型包括：

#### <a name="emptydir"></a>emptyDir

通常用作 Pod 的临时空间。 Pod 中的所有容器都可以访问卷上的数据。 写入此卷类型的数据仅在 Pod 的生命周期内持续保存。 删除 Pod 后，卷也会删除。 此卷通常使用基础本地节点磁盘存储，但它也可以仅存在于节点的内存中。

#### <a name="secret"></a>secret

可以使用 secret 卷将敏感数据注入 Pod，例如密码。 
1. 使用 Kubernetes API 创建机密。 
1. 定义 pod 或部署，并请求特定机密。 
    * 机密只会提供给具有需要它们的计划 pod 的节点。
    * 机密存储在 tmpfs 中，而不是写入磁盘。 
1. 删除节点上最后一个需要机密的 pod 后，会从节点的 tmpfs 中删除机密。 
   * 机密存储在给定的命名空间中，只有同一命名空间中的 pod 能访问该机密。

#### <a name="configmap"></a>configMap

可以使用 configMap 将键值对属性注入 Pod，例如应用程序配置信息。 将应用程序配置信息定义为 Kubernetes 资源（可在部署 pod 的新实例时轻松更新并应用于它们）。 

例如使用机密：
1. 使用 Kubernetes API 创建 ConfigMap。 
1. 在定义 pod 或部署时请求 ConfigMap。 
   * ConfigMap 存储在给定命名空间内，且只能由同一命名空间中的 Pod 访问。

## <a name="persistent-volumes"></a>永久性卷

作为 Pod 生命周期的一部分定义和创建的卷仅在删除 Pod 之前存在。 如果在维护事件期间（尤其是在 StatefulSets 中）于另一台主机上重新计划 Pod，则 Pod 通常会预期其存储会被保留。 *永久性卷* (PV) 是由 Kubernetes API 创建和管理的存储资源，可以在单个 Pod 的生命周期之外存在。

可以使用 Azure 磁盘或文件存储提供 PersistentVolume。 如[卷](#volumes)部分中所述，选择磁盘还是文件存储通常取决于对并发访问数据或性能层的需求。

![Azure Kubernetes 服务 (AKS) 群集中的永久性卷](media/concepts-storage/persistent-volumes.png)

PersistentVolume 可以由群集管理员 *静态* 创建，或者由 Kubernetes API 服务器 *动态* 创建。 如果 Pod 进行了计划并请求当前不可用的存储，则 Kubernetes 可以创建基础 Azure 磁盘或文件存储并将其附加到 Pod。 动态预配使用 *StorageClass* 来标识需要创建的 Azure 存储类型。

## <a name="storage-classes"></a>存储类

若要定义不同的存储层（例如高级和标准），可创建 *StorageClass*。 

StorageClass 还定义 *reclaimPolicy*。 删除 Pod 且可能不再需要永久性卷时，reclaimPolicy 可控制基础 Azure 存储资源的行为。 可删除基础存储资源，也可保留基础存储资源以便与未来的 Pod 配合使用。

在 AKS 中，将使用树中存储插件为群集创建四个初始 `StorageClasses`：

| 权限 | 原因 |
|---|---|
| `default` | 使用 Azure StandardSSD 存储创建托管磁盘。 回收策略确保在删除使用基础 Azure 磁盘的持久卷时会删除该磁盘。 |
| `managed-premium` | 使用 Azure 高级存储创建托管磁盘。 同样，回收策略确保在删除使用基础 Azure 磁盘的持久卷时会删除该磁盘。 |
| `azurefile` | 使用 Azure 标准存储创建 Azure 文件共享。 回收策略确保在删除使用基础 Azure 文件共享的永久卷时会删除该文件共享。 |
| `azurefile-premium` | 使用 Azure 高级存储创建 Azure 文件共享。 回收策略确保在删除使用基础 Azure 文件共享的永久卷时会删除该文件共享。|

对于使用新的容器存储接口 (CSI) 外部插件（预览版）的群集，将创建以下额外的 `StorageClasses`：

| 权限 | 原因 |
|---|---|
| `managed-csi` | 使用 Azure StandardSSD 本地冗余存储 (LRS) 创建托管磁盘。 回收策略确保在删除使用基础 Azure 磁盘的持久卷时会删除该磁盘。 存储类还会将永久卷配置为可扩展的卷，你只需使用新的大小编辑永久卷声明即可。 |
| `managed-csi-premium` | 使用 Azure 高级本地冗余存储 (LRS) 创建托管磁盘。 同样，回收策略确保在删除使用基础 Azure 磁盘的持久卷时会删除该磁盘。 同样，此存储类也允许扩展永久卷。 |
| `azurefile-csi` | 使用 Azure 标准存储创建 Azure 文件共享。 回收策略确保在删除使用基础 Azure 文件共享的永久卷时会删除该文件共享。 |
| `azurefile-csi-premium` | 使用 Azure 高级存储创建 Azure 文件共享。 回收策略确保在删除使用基础 Azure 文件共享的永久卷时会删除该文件共享。|

除非为永久性卷指定 StorageClass，否则会使用默认 StorageClass。 请求永久性卷时，请确保卷使用你需要的适当存储。 

可使用 `kubectl` 创建 StorageClass 来满足其他需求。 以下示例使用高级托管磁盘并指定在删除 Pod 时应该保留基础 Azure 磁盘：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS 会协调默认存储类，并将覆盖你对这些存储类所做的任何更改。

## <a name="persistent-volume-claims"></a>永久性卷声明

PersistentVolumeClaim 会请求特定 StorageClass、访问模式和大小的磁盘或文件存储。 如果根据定义的 StorageClass 没有现有资源可以实现声明，Kubernetes API 服务器可动态预配基础 Azure 存储资源。 

卷连接到 Pod 后，Pod 定义即会包含装载。

![Azure Kubernetes 服务 (AKS) 群集中的永久性卷声明](media/concepts-storage/persistent-volume-claims.png)

可用存储资源分配给请求存储的 Pod 后，PersistentVolume 就会绑定到 PersistentVolumeClaim。 永久性卷会 1:1 映射到声明。

以下示例 YAML 清单显示使用 *managed-premium* StorageClass 并请求 *5Gi* 存储的永久性卷声明：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

创建 pod 定义时，还需指定：
* 用于请求所需存储的永久性卷声明。 
* 供应用程序用于读取和写入数据的 volumeMount。 

以下示例 YAML 清单说明如何使用先前的永久性卷声明来将卷装载到 */mnt/azure*：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

若要在 Windows 容器中装载卷，请指定驱动器号和路径。 例如：

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>后续步骤

如需相关的最佳做法，请参阅 [AKS 中的存储和备份最佳做法][operator-best-practices-storage]。

若要了解如何创建使用 Azure 磁盘或 Azure 文件的动态和静态卷，请参阅以下操作指南：

- [使用 Azure 磁盘创建静态卷][aks-static-disks]
- [使用 Azure 文件存储创建静态卷][aks-static-files]
- [使用 Azure 磁盘创建动态卷][aks-dynamic-disks]
- [使用 Azure 文件存储创建动态卷][aks-dynamic-files]

有关核心 Kubernetes 和 AKS 概念的详细信息，请参阅以下文章：

- [Kubernetes/AKS 群集和工作负荷][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 标识][aks-concepts-identity]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 虚拟网络][aks-concepts-network]
- [Kubernetes/AKS 缩放][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
