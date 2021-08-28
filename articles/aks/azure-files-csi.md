---
title: 在 Azure Kubernetes 服务 (AKS) 中使用 Azure 文件存储的容器存储接口 (CSI) 驱动程序
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中使用 Azure 文件存储的容器存储接口 (CSI) 驱动程序。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c60b2301e6f0ea2767128224c4e76a677df69e0d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746005"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用 Azure 文件存储容器存储接口 (CSI) 驱动程序

Azure 文件存储容器存储接口 (CSI) 驱动程序是符合 [CSI 规范](https://github.com/container-storage-interface/spec/blob/master/spec.md)的驱动程序，供 Azure Kubernetes 服务 (AKS) 用来管理 Azure 文件共享的生命周期。

CSI 是有关对 Kubernetes 上的容器化工作负载公开任意块和文件存储系统的一个标准。 现在，AKS 可以采用和使用 CSI 来编写、部署和迭代插件，以在 Kubernetes 中公开新的存储系统或改进现有的存储系统，而无需改动核心 Kubernetes 代码并等待经历代码发布周期。

若要创建提供 CSI 驱动程序支持的 AKS 群集，请参阅[在 AKS 上为 Azure 磁盘和 Azure 文件存储启用 CSI 驱动程序](csi-storage-drivers.md)。

> [!NOTE]
> “树中驱动程序”是指包含在核心 Kubernetes 代码中的当前存储驱动程序，而不是新的 CSI 驱动程序（插件）。

## <a name="use-a-persistent-volume-with-azure-files"></a>通过 Azure 文件存储使用永久性卷

[永久性卷 (PV)](concepts-storage.md#persistent-volumes) 表示已经过预配的可用于 Kubernetes Pod 的存储块。 一个 PV 可供一个或多个 Pod 使用，并可动态或静态预配。 如果多个 Pod 需要同时访问同一存储卷，你可以使用 Azure 文件存储通过[服务器消息块 (SMB) 协议][smb-overview]进行连接。 本文将介绍如何动态创建 Azure 文件共享以供 AKS 群集中的多个 Pod 使用。 有关静态预配，请参阅[通过 Azure 文件共享手动创建并使用卷](azure-files-volume.md)。

有关 Kubernetes 卷的详细信息，请参阅 [AKS 中应用程序的存储选项][concepts-storage]。

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>使用内置存储类动态创建 Azure 文件存储 PV

存储类用于定义如何创建 Azure 文件共享。 系统会在[节点资源组][node-resource-group]中自动创建一个存储帐户，以便与存储类一起用来保存 Azure 文件共享。 为“skuName”选择以下 [Azure 存储冗余 SKU][storage-skus] 之一：

* Standard_LRS：标准本地冗余存储
* Standard_GRS：标准异地冗余存储
* Standard_ZRS：标准区域冗余存储
* Standard_RAGRS：标准读取访问异地冗余存储
* Premium_LRS：高级本地冗余存储

> [!NOTE]
> Azure 文件存储支持 Azure 高级存储。 最小的高级文件共享为 100 GB。

在 AKS 中使用存储 CSI 驱动程序时，有两个附加的内置 `StorageClasses` 使用 Azure 文件存储 CSI 存储驱动程序。 这些附加的 CSI 存储类是使用群集连同树中默认的存储类一起创建的。

- `azurefile-csi`：使用 Azure 标准存储创建 Azure 文件共享。
- `azurefile-csi-premium`：使用 Azure 高级存储创建 Azure 文件共享。

针对这两个存储类的回收策略可确保在删除相应的 PV 时删除基础 Azure 文件共享。 这些存储类还会将文件共享配置为可扩展，你只需使用新的大小编辑永久性卷声明 (PVC) 即可。

若要使用这些存储类，请创建一个引用并使用这些类的 [PVC](concepts-storage.md#persistent-volume-claims) 和相应 Pod。 PVC 用于基于存储类自动预配存储。 PVC 可以使用一个预先创建的存储类或用户定义的存储类，为所需的 SKU 和大小创建 Azure 文件共享。 创建 Pod 定义时，将指定 PVC 来请求所需的存储。

使用 [kubectl apply][kubectl-apply] 命令创建一个[可将当前日期输出到 `outfile` 的示例 PVC 和 Pod](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

在 Pod 处于运行状态后，可以通过运行以下命令并验证输出中是否包含 `outfile`，来验证是否正确装载了文件共享：

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>创建自定义存储类

默认存储类适合最常见的方案，但并非适合所有方案。 在某些情况下，你可能想要使用自己的参数来自定义自己的存储类。 例如，使用以下清单配置文件共享的 `mountOptions`。

对于 Kubernetes 装载的文件共享，fileMode 和 dirMode 的默认值为 0777  。 可以在存储类对象中指定不同的装载选项。

创建名为 `azure-file-sc.yaml` 的文件，并粘贴以下示例清单：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类：

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure 文件存储 CSI 驱动程序支持创建[持久性卷和基础文件共享的快照](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)。

使用 [kubectl apply][kubectl-apply] 命令创建[卷快照类](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

基于[我们在本教程开头动态创建](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)的 PVC `pvc-azurefile` 创建[卷快照](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml)。

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml

volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

验证是否正确创建了快照：

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>调整永久性卷的大小

可为 PVC 请求较大的卷。 编辑 PVC 对象，并指定较大的大小。 此项更改会触发为 PV 提供支持的基础卷的扩展。

> [!NOTE]
> 永远不会创建新的 PV 来满足声明， 而是会调整现有卷的大小。

在 AKS 中，内置的 `azurefile-csi` 存储类已经能够支持扩展，因此请使用[先前通过此存储类创建的 PVC](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)。 PVC 请求了 100Gi 文件共享。 可通过运行以下命令来确认一点：

```console
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

通过增大 `spec.resources.requests.storage` 字段值来扩展 PVC：

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

验证 Pod 中的 PVC 和文件系统是否都显示新的大小：

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="use-a-persistent-volume-with-private-azure-files-storage-private-endpoint"></a>通过 Azure 文件存储专用存储（专用终结点）使用永久性卷

如果 Azure 文件存储资源受专用终结点保护，则必须使用以下参数创建自己的自定义存储类：

* `resourceGroup`：部署存储帐户的资源组。
* `storageAccount`：存储帐户名称。
* `server`：存储帐户的专用终结点的 FQDN（例如，`<storage account name>.privatelink.file.core.windows.net`）。

创建一个名为“private-azure-file-sc.yaml”的文件，然后将以下示例清单粘贴到该文件中。 替换 `<resourceGroup>` 和 `<storageAccountName>` 的值。

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: private-azurefile-csi
provisioner: file.csi.azure.com
allowVolumeExpansion: true
parameters:
  resourceGroup: <resourceGroup>
  storageAccount: <storageAccountName>
  server: <storageAccountName>.privatelink.file.core.windows.net 
reclaimPolicy: Delete
volumeBindingMode: Immediate
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict  # https://linux.die.net/man/8/mount.cifs
  - nosharesock  # reduce probability of reconnect race
  - actimeo=30  # reduce latency for metadata-heavy workload
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类：

```console
kubectl apply -f private-azure-file-sc.yaml

storageclass.storage.k8s.io/private-azurefile-csi created
```
  
创建一个名为“private-pvc.yaml”的文件，然后将以下示例清单粘贴到该文件中。
  
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: private-azurefile-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: private-azurefile-csi
  resources:
    requests:
      storage: 100Gi
```
  
使用 [kubectl apply][kubectl-apply] 命令创建 PVC：
  
```console
kubectl apply -f private-pvc.yaml
```

## <a name="nfs-file-shares"></a>NFS 文件共享

[Azure 文件存储支持 NFS v4.1 协议](../storage/files/storage-files-how-to-create-nfs-shares.md)。 Azure 文件存储的 NFS 4.1 支持以服务形式提供了一个完全托管的 NFS 文件系统，该系统建立在高度可用且高度持久的分布式弹性存储平台基础之上。

 此选项已针对包含就地数据更新的随机访问工作负载进行优化，提供全面的 POSIX 文件系统支持。 本部分将介绍如何在 AKS 群集上通过 Azure 文件存储 CSI 驱动程序使用 NFS 共享。

### <a name="create-nfs-file-share-storage-class"></a>创建 NFS 文件共享存储类

编辑相应的占位符，然后保存包含以下清单的 `nfs-sc.yaml` 文件。

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  protocol: nfs
```

编辑并保存该文件后，使用 [kubectl apply][kubectl-apply] 命令创建存储类：

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi-nfs created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>使用 NFS 支持的文件共享创建部署

可使用以下 [kubectl apply][kubectl-apply] 命令，部署一个可将时间戳保存到 `data.txt` 文件中的示例 [StatefulSet](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/nfs/statefulset.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nfs/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

运行以下命令验证卷的内容：

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

> [!NOTE]
> 请注意，由于 NFS 文件共享是高级帐户，因此最小文件共享大小为100GB。 如果使用较小的存储大小创建 PVC，可能会遇到错误“无法创建文件共享 ...大小 (5)...”。

## <a name="windows-containers"></a>Windows 容器

Azure 文件存储 CSI 驱动程序还支持 Windows 节点和容器。 如果你要使用 Windows 容器，请遵循 [Windows 容器教程](windows-container-cli.md)添加 Windows 节点池。

添加 Windows 节点池后，使用内置的存储类（例如 `azurefile-csi`）或创建自定义的存储类。 可使用以下 [kubectl apply][kubectl-apply] 命令，部署一个可将时间戳保存到 `data.txt` 文件中的[基于 Windows 的示例 StatefulSet](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

运行以下命令验证卷的内容：

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何为 Azure 磁盘使用 CSI 驱动程序，请参阅[通过 CSI 驱动程序使用 Azure 磁盘](azure-disk-csi.md)。
- 有关存储最佳做法的详细信息，请参阅[有关 Azure Kubernetes 服务中存储和备份的最佳做法][operator-best-practices-storage]。

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md
