---
title: 了解 Azure Stack Edge Pro 设备上的 Kubernetes 存储管理 | Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro 设备上管理 Kubernetes 存储。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d848d663121474085935c68e62b8aa38f195ba8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442173"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备上的 Kubernetes 存储管理

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

在 Azure Stack Edge Pro 设备上，配置计算角色时，会创建 Kubernetes 群集。 创建 Kubernetes 群集后，便可以在 Pod 中的 Kubernetes 群集上部署容器化应用程序。 你可以通过不同的方式在 Kubernetes 群集中将存储提供给 Pod。 

本文大致介绍在 Kubernetes 群集上预配存储的方法，特别是在 Azure Stack Edge Pro 设备中的情况下。 

## <a name="storage-requirements-for-kubernetes-pods"></a>Kubernetes Pod 的存储要求

Kubernetes Pod 是无状态的，但它们运行的应用程序通常有状态。 由于 Pod 存在时间很短，它们会重启、故障转移或在 Kubernetes 节点之间移动，因此必须满足以下要求才能存储与 Pod 相关的存储。 

存储必须：

- 位于 Pod 外。
- 独立于 Pod 生命周期。
- 可从所有 Kubernetes 节点访问。

若要了解如何为 Kubernetes 管理存储，需要了解两项 API 资源： 

- **PersistentVolume (PV)** ：这是 Kubernetes 群集中的一块存储。 可以静态方式将 Kubernetes 存储预配为 `PersistentVolume`。 也可将其动态预配为 `StorageClass`。

- **PersistentVolumeClaim (PVC)** ：是指用户的存储请求。 PVC 使用 PV 资源。 PVC 可以请求特定的大小和访问模式。 

    由于用户需要 `PersistentVolumes` 具有不同的属性来解决不同的问题，因此群集管理员需要能够提供各种不仅仅是大小和访问模式不同，还在许多其他方面有所不同的 `PersistentVolumes`。 为了满足这些需求，需要 `StorageClass` 资源。

可以静态预配存储，也可以动态预配存储。 以下各部分讨论了上述每种预配类型。

## <a name="static-provisioning"></a>静态预配

Kubernetes 群集管理员可静态地配置存储。 为此，他们可使用基于 SMB/NFS 文件系统的存储后端，或者使用在本地环境中通过网络本地附加的 iSCSI 磁盘，甚至可以在云中使用 Azure 文件存储或 Azure 磁盘。 默认情况下不预配这种存储类型，群集管理员必须计划和管理此预配。 
 
下图描述了如何在 Kubernetes 中使用静态预配的存储： 

![通过 PersistentVolumes 进行静态预配](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

将执行以下步骤： 

1. **预配存储**：群集管理员预配存储。 在本例中，群集管理员创建一个或多个 SMB 共享，这些共享会在其对应的 Kubernetes 群集中自动创建永久性卷对象。 

1. **声明存储**：提交请求存储的 PVC 部署。 此存储声明是 PersistentVolumeClaim (PVC)。 如果 PV 与 PVC 的大小和访问模式相匹配，则 PVC 将绑定到 PV。 PVC 和 PV 之间是一对一映射。

1. **将 PVC 装载到容器**：将 PVC 绑定到 PV 后，可以将该 PVC 装载到容器中的路径上。 当容器中的应用程序逻辑从此路径读取数据或向其写入数据时，数据将写入 SMB 存储。
 

## <a name="dynamic-provisioning"></a>动态预配

下图描述了如何在 Kubernetes 中使用静态预配的存储： 

![通过 StorageClasses 进行动态预配](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

将执行以下步骤： 

1. **定义存储类**：群集管理定义存储类，具体取决于 Kubernetes 群集的操作环境。 群集管理员还部署了一个预配程序，它是部署在 Kubernetes 群集上的另一个 Pod 或应用程序。 预配程序具有用于动态预配共享的所有详细信息。  

1. **声明存储**：提交将声明存储的应用程序。 使用此存储类引用创建 PVC 后，将调用预配程序。 

1. **动态预配存储**：预配程序动态创建与本地磁盘存储关联的共享。 创建共享后，它还会自动创建对应于此共享的 PV 对象。

1. **将 PVC 装载到容器**：将 PVC 绑定到 PV 后，可以使用与静态预配相同的方式将 PVC 装载到路径上，然后在共享中进行读取或写入。


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的存储预配

在 Azure Stack Edge Pro 设备上，静态预配的 `PersistentVolumes` 是使用设备的存储功能创建的。 当你在预配共享并且已启用“将共享用于 Edge 计算”选项时，此操作将在 Kubernetes 群集中自动创建 PV 资源。

![Azure 门户中用于静态预配的本地共享创建过程](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

若要使用云分层，可以通过启用“将该共享用于 Edge 计算”选项来创建 Edge 云共享。 系统将再次为此共享自动创建 PV。 写入 Edge 共享的任何应用程序数据都在云中分层。 

![Azure 门户中用于静态预配的云共享创建过程](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

你可创建 SMB 和 NFS 共享，以便在 Azure Stack Edge Pro 设备上静态预配 PV。 预配 PV 后，将提交 PVC 来声明此存储。 下面是一个示例 PVC 部署 `yaml`，它声明存储并使用预配的共享。


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

有关详细信息，请参阅[通过 kubectl 在 Azure Stack Edge Pro 上通过静态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。

若要访问同一静态预配存储，用于将存储绑定到 IoT 的相应卷装载选项将如下所示。 `/home/input` 是容器中卷的访问路径。

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge Pro 还有一个名为 `ase-node-local` 的内置 `StorageClass`，它使用附加到 Kubernetes 节点的数据磁盘存储。 该 `StorageClass` 支持动态预配。 你可以在 Pod 应用程序中创建 `StorageClass` 引用，系统将自动为你创建 PV。 有关详细信息，请参阅 [Kubernetes 仪表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)以查询 `ase-node-local StorageClass`。

![Kubernetes 仪表板中的内置存储类](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

有关详细信息，请参阅[通过 kuebctl 在 Azure Stack Edge Pro 上通过动态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)。

## <a name="choose-storage-type"></a>选择存储类型

建议你根据要部署的工作负载选择存储类型。 

- 若要为 `PersistentVolumes` 使用 `ReadWriteMany` 访问模式（在这种模式下，许多部署节点都以读写方式装载卷），请对 SMB/NFS 共享使用静态预配。

- 如果要部署的应用程序具有 POSIX 合规性要求，例如 MongoDB、PostgreSQL、MySQL 或 Prometheus 等应用程序，请使用内置的 StorageClass。 访问模式为 `ReadWriteOnce`，或者单个节点以读写方式装载卷。 


有关访问模式的详细信息，请参阅 [Kubernetes 卷访问模式](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)。


## <a name="next-steps"></a>后续步骤

若要了解如何静态预配 `PersistentVolume`，请参阅：

- [通过 kubectl 在 Azure Stack Edge Pro 上通过静态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。

若要了解如何动态预配 `StorageClass`，请参阅：

- [通过 kuebctl 在 Azure Stack Edge Pro 上通过动态预配部署有状态应用程序](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)。
