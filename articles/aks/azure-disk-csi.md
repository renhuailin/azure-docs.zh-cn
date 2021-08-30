---
title: 在 Azure Kubernetes 服务 (AKS) 中使用 Azure 磁盘的容器存储接口 (CSI) 驱动程序
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中使用 Azure 磁盘的容器存储接口 (CSI) 驱动程序。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 19606a1b529fcd7e6140d81361c5434324836198
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724737"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用 Azure 磁盘容器存储接口 (CSI) 驱动程序
Azure 磁盘容器存储接口 (CSI) 驱动程序是符合 [CSI 规范](https://github.com/container-storage-interface/spec/blob/master/spec.md)的驱动程序，供 Azure Kubernetes 服务 (AKS) 用来管理 Azure 磁盘的生命周期。

CSI 是有关在 Kubernetes 上的容器化工作负载中公开任意块和文件存储系统的一套标准。 AKS 可以采用 CSI 来编写、部署和迭代插件，以在 Kubernetes 中公开新的或改进现有的存储系统，而无需改动核心 Kubernetes 代码并等待经历代码发布周期。

若要创建提供 CSI 驱动程序支持的 AKS 群集，请参阅[在 AKS 上启用 Azure 磁盘和 Azure 文件存储的 CSI 驱动程序](csi-storage-drivers.md)。

> [!NOTE]
> “树中驱动程序”是指包含在核心 Kubernetes 代码中的当前存储驱动程序，而不是新的 CSI 驱动程序（插件）。

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>使用含 Azure 磁盘的 CSI 永久性卷

[永久性卷 (PV)](concepts-storage.md#persistent-volumes) 表示已经过预配的可用于 Kubernetes Pod 的存储块。 PV 可供一个或多个 Pod 使用，并可动态或静态预配。 本文介绍了如何动态创建含 Azure 磁盘的 PV，以供 AKS 群集中的单个 Pod 使用。 有关静态预配，请参阅[手动创建并使用含 Azure 磁盘的卷](azure-disk-volume.md)。

有关 Kubernetes 卷的详细信息，请参阅 [AKS 中应用程序的存储选项][concepts-storage]。

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>使用内置存储类动态创建 Azure 磁盘 PV

存储类用于定义使用永久性卷动态创建存储单位的方式。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。 在 AKS 中使用存储 CSI 驱动程序时，有两个附加的内置 `StorageClasses` 使用 Azure 磁盘 CSI 存储驱动程序。 这些附加的 CSI 存储类是使用群集连同树中默认的存储类一起创建的。

- `managed-csi`：使用 Azure Standard SSD 本地冗余存储 (LRS) 创建托管磁盘。
- `managed-csi-premium`：使用 Azure 高级 LRS 创建托管磁盘。

这两个存储类的回收策略可确保在删除相应的 PV 时删除基础 Azure 磁盘。 存储类还会将 PV 配置为可扩展。 只需使用新大小编辑永久性卷声明 (PVC) 即可。

若要使用这些存储类，请创建一个引用并使用这些类的 [PVC](concepts-storage.md#persistent-volume-claims) 和相应 Pod。 PVC 用于基于存储类自动预配存储。 PVC 可以使用一个预先创建的存储类或用户定义的存储类，创建所需 SKU 和大小的 Azure 托管磁盘。 创建 Pod 定义时，将指定 PVC 来请求所需的存储。

使用 [kubectl apply][kubectl-apply] 命令创建示例 Pod 和相应 PVC：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

在 Pod 处于运行状态后，创建一个名为 `test.txt` 的新文件。

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

现在可以运行以下命令并验证输出是否显示 `test.txt` 文件，以验证磁盘是否正确载入：

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>创建自定义存储类

默认存储类适合最常见的方案，但并非适合所有方案。 在某些情况下，你可能想要使用自己的参数来自定义自己的存储类。 例如，假设你需要更改 `volumeBindingMode` 类。

你可以使用 `volumeBindingMode: Immediate` 类，此类可保证创建 PVC 后立即生效。 例如，节点池使用可用性区域约束了拓扑时，则在绑定或预配 PV 时不会考虑 Pod 的计划要求（在此示例中为在特定区域内）。

为处理这种情况，可以使用 `volumeBindingMode: WaitForFirstConsumer`，它会延迟 PV 绑定和预配，直至创建使用该 PVC 的 Pod。 这样，PV 将与 Pod 的计划约束指定的可用性区域（或其他拓扑）一致并预配到其中。 默认存储类使用 `volumeBindingMode: WaitForFirstConsumer` 类。

创建名为 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 的文件，并粘贴以下清单。
此存储类与 `managed-csi` 存储类相同，但它使用不同的 `volumeBindingMode` 类。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

使用 [kubectl apply][kubectl-apply] 命令创建此存储类，并指定 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 文件：

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>卷快照

Azure 磁盘 CSI 驱动程序支持创建[永久性卷的快照](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)。 通过此功能，此驱动程序可以创建完整快照或[增量快照](../virtual-machines/disks-incremental-snapshots.md)，具体取决于 `incremental` 参数中设置的值（默认情况下为 true）。

有关所有参数的详细信息，请参阅[卷快照类参数](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)。

### <a name="create-a-volume-snapshot"></a>创建卷快照

为演示此功能，我们使用 [kubectl apply][kubectl-apply] 命令创建[卷快照类](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

现在基于[我们在本教程开头动态创建](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)的 PVC `pvc-azuredisk` 创建[卷快照](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml)。

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

查看是否正确创建了快照：

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>基于卷快照创建新的 PVC

可以基于卷快照创建新的 PVC。 使用在上一步中创建的快照，并创建[新的 PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) 和要使用它的[新 Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml)。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

最后，通过检查内容，确保它与之前创建的 PVC 相同。

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

与预期一样，我们仍然可以看到以前创建的 `test.txt` 文件。

## <a name="clone-volumes"></a>克隆卷

克隆卷表示现有 Kubernetes 卷的副本。 有关在 Kubernetes 中克隆卷的详细信息，请参阅有关[卷克隆](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)的概念文档。

Azure 磁盘的 CSI 驱动程序支持卷克隆。 为了进行演示，我们创建[之前创建的](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` 的[克隆卷](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)和[要使用它的新 Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

现在，可以运行以下命令并确认仍会看到以前创建的文件 `test.txt`，以检查克隆卷的内容。

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>调整永久性卷的大小

可改为为 PVC 请求更大的卷。 编辑 PVC 对象，并指定更大的大小。 此项更改会触发为 PV 提供支持的基础卷的扩展。

> [!NOTE]
> 永远不会创建新的 PV 来满足声明， 而是会调整现有卷的大小。

在 AKS 中，内置的 `managed-csi` 存储类已经能够实现扩展，因此请使用[先前通过此存储类创建的 PVC](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)。 此 PVC 请求了 10 Gi 永久性卷。 可通过运行以下命令来确认这一点：

```console
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> 目前，Azure 磁盘 CSI 驱动程序只支持调整未关联 Pod 的 PVC（以及未装载到特定节点的卷）的大小。

鉴于此，删除以前创建的 Pod：

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

通过增大 `spec.resources.requests.storage` 字段值来扩展 PVC：

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

确认该卷现在增大：

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> PVC 重新关联 Pod 后才会显示新的大小。

创建新的 Pod：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

最后，在 Pod 内确认 PVC 的大小：

```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>共享磁盘

[Azure 共享磁盘](../virtual-machines/disks-shared.md)是一种 Azure 托管磁盘功能，它可将 Azure 磁盘同时附加到多个代理节点。 例如，通过将托管磁盘附加到多个代理节点，可以向 Azure 部署新的群集应用程序或迁移现有的群集应用程序。

> [!IMPORTANT]
> 目前，Azure 磁盘 CSI 驱动程序仅支持原始块设备 `volumeMode: Block`。 应用程序应负责协调和控制作为原始块设备公开的共享磁盘上的写入、读取、锁定、缓存、装载和隔离。

通过复制以下包含共享磁盘存储类和 PVC 的命令，创建一个名为 `shared-disk.yaml` 的文件：

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类，并指定 `shared-disk.yaml` 文件：

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
```

现在，通过复制以下命令，创建一个名为 `deployment-shared.yml` 的文件：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

使用 [kubectl apply][kubectl-apply] 命令创建部署，并指定 `deployment-shared.yml` 文件：

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

最后，在 Pod 中检查块设备：

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows 容器

Azure 磁盘 CSI 驱动程序还支持 Windows 节点和容器。 如果你要使用 Windows 容器，请遵循 [Windows 容器教程](windows-container-cli.md)添加 Windows 节点池。

具有 Windows 节点池后，现在可以使用内置的存储类，如 `managed-csi`。 可使用以下 [kubectl apply][kubectl-apply] 命令，部署一个可将时间戳保存到 `data.txt` 文件中的[基于 Windows 的示例 StatefulSet 集](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

现在可以运行以下命令验证卷的内容：

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何将 CSI 驱动程序用于 Azure 文件，请参阅[结合使用 Azure 文件与 CSI 驱动程序](azure-files-csi.md)。
- 有关存储最佳做法的详细信息，请参阅[有关 Azure Kubernetes 服务中存储和备份的最佳做法][operator-best-practices-storage]。

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
