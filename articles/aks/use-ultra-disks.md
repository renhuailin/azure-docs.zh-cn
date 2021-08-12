---
title: 启用 Azure Kubernetes 服务 (AKS) 上的超级磁盘支持
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集上启用和配置超级磁盘
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: d42834252416a2aeed40db5fe307cd97f1bbada9
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112007294"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>使用 Azure Kubernetes 服务上的 Azure 超级磁盘（预览）

[Azure 超级磁盘](../virtual-machines/disks-enable-ultra-ssd.md)为有状态应用程序提供高吞吐量、高 IOPS 和一致且低延迟的磁盘存储。 超级磁盘的一个主要优点是能够动态更改 SSD 的性能和工作负载，而无需重启代理节点。 超级磁盘适用于数据密集型工作负载。

## <a name="before-you-begin"></a>在开始之前

此功能只能在创建群集或创建节点池时设置。

> [!IMPORTANT]
> 如果要使用 Azure 超级磁盘，需要在支持这些磁盘的可用性区域和其他区域中以及特定的 VM 系列上部署节点池。 查看[超级磁盘 GA 范围和限制](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建可使用超级磁盘的 AKS 群集或节点池，需要最新的 aks-preview CLI 扩展。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展，或使用 [az extension update][az-extension-update] 命令安装任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>限制
- 查看[超级磁盘 GA 范围和限制](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- 超级磁盘支持的大小范围为 100 到 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>创建可以使用超级磁盘的新群集

使用以下 CLI 命令创建能够利用超级磁盘的 AKS 群集。 使用 `--enable-ultra-ssd` 标志设置 `EnableUltraSSD` 功能。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

创建支持超级磁盘的 AKS 群集。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_D2s_v3 --zones 1 2 --node-count 2 --enable-ultra-ssd
```

如果要创建不支持超级磁盘的群集，可以省略 `--enable-ultra-ssd` 参数。

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>启用现有群集上的超级磁盘

可以通过将新的节点池添加到支持超级磁盘的群集上，在现有群集上启用超级磁盘。 使用 `--enable-ultra-ssd` 标志将新的节点池配置为使用超级磁盘。

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_D2s_v3 --zones 1 2 --node-count 2 --enable-ultra-ssd
```

如果要创建不支持超级磁盘的新节点池，可以省略 `--enable-ultra-ssd` 参数。

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>通过存储类动态使用超级磁盘

若要在部署或 StatefulSet 中使用超级磁盘，可以使用[存储类进行动态预配](azure-disks-dynamic-pv.md)。

### <a name="create-the-storage-class"></a>创建存储类

存储类用于定义使用永久性卷动态创建存储单位的方式。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

在本例中，我们将创建一个引用超级磁盘的存储类。 创建名为 `azure-ultra-disk-sc.yaml` 的文件，并将其复制到以下清单中。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类，并指定 azure-ultra-disk-sc.yaml 文件：

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>创建永久性卷声明

永久卷声明 (PVC) 用于基于存储类自动预配存储。 在本例中，PVC 可以使用以前创建的存储类来创建超级磁盘。

创建名为 `azure-ultra-disk-pvc.yaml` 的文件，并将其复制到以下清单中。 该声明请求名为 `ultra-disk`、大小为 1000 GB、具有 ReadWriteOnce 访问权限的磁盘 。 ultra-disk-sc 存储类指定为存储类。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

使用 [kubectl apply][kubectl-apply] 命令创建永久性卷声明，并指定 azure-ultra-disk-pvc.yaml 文件：

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>使用永久性卷

创建永久性卷声明并成功预配磁盘以后，即可创建可以访问磁盘的 Pod。 以下清单创建的基本 NGINX Pod 使用名为 ultra-disk 的永久性卷声明将 Azure 磁盘装载到 `/mnt/azure` 路径。

创建名为 `nginx-ultra.yaml` 的文件，并将其复制到以下清单中。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: ultra-disk
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod，如以下示例所示：

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

现在你有一个正在运行的 Pod，其中 Azure 磁盘被装载到 `/mnt/azure` 目录中。 通过 `kubectl describe pod nginx-ultra` 检查 Pod 时可以看到此配置，如以下精简示例所示：

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>后续步骤

- 有关超级磁盘的详细信息，请参阅[使用 Azure 超级磁盘](../virtual-machines/disks-enable-ultra-ssd.md)。
- 有关存储最佳做法的详细信息，请参阅[有关 Azure Kubernetes 服务 (AKS) 中存储和备份的最佳做法][operator-best-practices-storage]

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
