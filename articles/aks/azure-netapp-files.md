---
title: 将 Azure NetApp 文件与 Azure Kubernetes 服务集成
description: 了解如何将 Azure NetApp 文件与 Azure Kubernetes 服务集成
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 1d5aa8232b5d0aaa68e6d7e3dcbb9a7d70d0e8f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182139"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>将 Azure NetApp 文件与 Azure Kubernetes 服务集成

[Azure NetApp 文件][anf]是 Azure 上运行的一种企业级高性能计量式文件存储服务。 本文介绍如何将 Azure NetApp 文件与 Azure Kubernetes 服务 (AKS) 集成。

## <a name="before-you-begin"></a>准备阶段
本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

> [!IMPORTANT]
> 此外，你的 AKS 群集必须[位于支持 Azure NetApp 文件的区域中][anf-regions]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

### <a name="limitations"></a>限制

使用 Azure NetApp 文件时存在以下限制：

* Azure NetApp 文件只能在[选定的 Azure 区域][anf-regions]中使用。
* 必须拥有 Azure NetApp 文件服务的访问权限才能使用 Azure NetApp 文件。 若要申请访问权限，可以使用 [Azure NetApp 文件候选名单提交表单][anf-waitlist]或访问 https://azure.microsoft.com/services/netapp/#getting-started 。 只有在收到 Azure NetApp 文件团队发送的官方确认电子邮件之后，你才能访问 Azure NetApp 文件服务。
* 在初次部署 AKS 群集之后，仅支持 Azure NetApp 文件的静态预配。
* 若要在 Azure NetApp 文件中使用动态预配，请安装并配置 [NetApp Trident](https://netapp-trident.readthedocs.io/) 19.07 或更高版本。

## <a name="configure-azure-netapp-files"></a>配置 Azure NetApp 文件

> [!IMPORTANT]
> 必须先填写 [Azure NetApp 文件候选名单提交表单][anf-waitlist]或访问订阅的 https://azure.microsoft.com/services/netapp/#getting-started ，才能注册 Microsoft.NetApp 资源提供程序。 只有在收到 Azure NetApp 文件团队发送的官方确认电子邮件之后，你才能注册该资源提供程序。

注册 Microsoft.NetApp 资源提供程序：

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 此操作需要一段时间才能完成。

创建用于 AKS 的 Azure NetApp 帐户时，需要在节点资源组中创建帐户。 首先，使用 [az aks show][az-aks-show] 命令获取资源组名称并添加 `--query nodeResourceGroup` 查询参数。 以下示例获取名为 myResourceGroup 的资源组中名为 myAKSCluster 的AKS 群集的节点资源组 ：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

使用 [az netappfiles account create][az-netappfiles-account-create] 在节点资源组以及你的 AKS 群集所在的同一区域中创建 Azure NetApp 文件帐户。 以下示例在 MC_myResourceGroup_myAKSCluster_eastus 资源组和 eastus 区域中创建名为 myaccount1 的帐户  ：

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

使用 [az netappfiles pool create][az-netappfiles-pool-create] 创建新的容量池。 以下示例创建名为 mypool1、大小为 4 TB、服务级别为“高级”的新容量池 ：

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

使用 [az network vnet subnet create][az-network-vnet-subnet-create] 创建要[委托给 Azure NetApp 文件][anf-delegate-subnet]的子网。 此子网必须与 AKS 群集位于同一虚拟网络中。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

使用 [az netappfiles volume create][az-netappfiles-volume-create] 创建卷。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>创建 PersistentVolume

使用 [az netappfiles volume show][az-netappfiles-volume-show] 列出卷的详细信息

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

创建用于定义 PersistentVolume 的 `pv-nfs.yaml`。 请将 `path` 替换为 creationToken ，将 `server` 替换为上一命令中的 ipAddress 。 例如：

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

将 server 和 path 更新为在上一步骤中创建的 NFS（网络文件系统）卷的值 。 使用 [kubectl apply][kubectl-apply] 命令创建 PersistentVolume：

```console
kubectl apply -f pv-nfs.yaml
```

使用 [kubectl describe][kubectl-describe] 命令验证 PersistentVolume 的状态是否为“可用” ：

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>创建 PersistentVolumeClaim

创建用于定义 PersistentVolume 的 `pvc-nfs.yaml`。 例如：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

使用 [kubectl apply][kubectl-apply] 命令创建 PersistentVolumeClaim：

```console
kubectl apply -f pvc-nfs.yaml
```

使用 [kubectl describe][kubectl-describe] 命令验证 PersistentVolumeClaim 的状态是否为“已绑定” ：

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>装载到 Pod

创建一个 `nginx-nfs.yaml`，用于定义使用 PersistentVolumeClaim 的 Pod。 例如：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod：

```console
kubectl apply -f nginx-nfs.yaml
```

使用 [kubectl describe][kubectl-describe] 命令验证 Pod 是否正在运行：

```console
kubectl describe pod nginx-nfs
```

验证卷是否已装载到 Pod 中：使用 [kubectl exec][kubectl-exec] 连接到 Pod，然后使用 `df -h` 检查该卷是否已装载。

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>后续步骤

有关 Azure NetApp 文件的详细信息，请参阅[什么是 Azure NetApp 文件][anf]。 有关在 AKS 中使用 NFS 的详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中手动创建并使用 NFS（网络文件系统）Linux Server 卷][aks-nfs]。


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
