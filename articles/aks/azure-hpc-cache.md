---
title: 将 Azure HPC 缓存与 Azure Kubernetes 服务集成
description: 了解如何将 HPC 缓存与 Azure Kubernetes 服务集成
services: container-service
author: jbut
ms.author: jebutl
ms.topic: article
ms.date: 09/08/2021
ms.openlocfilehash: 9cf8e3a6450787a65e21fcca79f8886efe016d8b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699097"
---
# <a name="integrate-azure-hpc-cache-with-azure-kubernetes-service"></a>将 Azure HPC 缓存与 Azure Kubernetes 服务集成

[Azure HPC 缓存][hpc-cache]可为高性能计算 (HPC) 任务加快对数据的访问。 通过在 Azure 中缓存文件，Azure HPC 缓存将云计算的可伸缩性带到现有工作流中。 本文介绍如何将 Azure HPC 缓存与 Azure Kubernetes 服务 (AKS) 集成。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

> [!IMPORTANT]
> AKS 群集必须[位于支持 Azure HPC 缓存的区域中][hpc-cache-regions]。

此外，需要安装并配置 Azure CLI 2.7 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。  有关将 Azure CLI 与 HPC 缓存结合使用的详细信息，请参阅 [hpc-cache-cli-prerequisites]。

此外，还需要安装 hpc-cache Azure CLI 扩展。  请执行以下操作：

```azurecli
az extension add --upgrade -n hpc-cache
```

## <a name="set-up-azure-hpc-cache"></a>设置 Azure HPC 缓存

本部分介绍创建和配置 HPC 缓存的步骤。

### <a name="1-find-the-aks-node-resource-group"></a>1. 查找 AKS 节点资源组

首先，使用 [az aks show][az-aks-show] 命令获取资源组名称并添加 `--query nodeResourceGroup` 查询参数。 你将在同一个资源组中创建 HPC 缓存。

以下示例获取名为 myResourceGroup 的资源组中名为 myAKSCluster 的AKS 群集的节点资源组名称 ：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

### <a name="2-create-the-cache-subnet"></a>2. 创建缓存子网

在运行 HPC 缓存之前，必须满足一些[先决条件][hpc-cache-prereqs]。  最重要的是，缓存需要一个至少有 64 个可用 IP 地址的专用子网。 此子网不得托管其他 VM 或容器。  必须可以从 AKS 节点访问此子网。

创建专用 HPC 缓存子网：

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --address-prefixes 10.0.0.0/26
```

注册 Microsoft.StorageCache 资源提供程序：

```azurecli
az provider register --namespace Microsoft.StorageCache --wait
```

> [!NOTE]
> 资源提供程序注册可能需要一段时间才能完成。

### <a name="3-create-the-hpc-cache"></a>3. 创建 HPC 缓存

创建一个 HPC 缓存，该 HPC 缓存位于从步骤 1 获取的节点资源组中，并且位于你的 AKS 群集所在的区域中。 使用 [az hpc-cache create][az-hpc-cache-create]。

> [!NOTE]
> 创建该 HPC 缓存大约需要 20 分钟。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
az hpc-cache create \
  --resource-group $RESOURCE_GROUP \
  --cache-size-gb "3072" \
  --location eastus \
  --subnet $SUBNET_ID \
  --sku-name "Standard_2G" \
  --name MyHpcCache
```

### <a name="4-create-a-storage-account-and-new-container"></a>4. 创建存储帐户和新容器

为 Blob 存储容器创建 Azure 存储帐户。  HPC 缓存将缓存此 Blob 存储容器中存储的内容。

> [!IMPORTANT]
> 需要选择唯一的存储帐户名称。  请将“uniquestorageaccount”替换为对你而言唯一的名称。

检查选择的存储帐户名称是否可用。

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account check-name --name $STORAGE_ACCOUNT_NAME
```

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account create \
  -n $STORAGE_ACCOUNT_NAME \
  -g $RESOURCE_GROUP \
  -l eastus \
  --sku Standard_LRS
```

在存储帐户中创建 Blob 容器。

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
AD_USER=$(az ad signed-in-user show --query objectId -o tsv)
CONTAINER_NAME=mystoragecontainer
az role assignment create --role "Storage Blob Data Contributor" --assignee $AD_USER --scope $STORAGE_ACCOUNT_ID
az storage container create --name $CONTAINER_NAME --account-name jebutlaksstorage --auth-mode login
```

向 Azure HPC 缓存服务帐户提供对你的存储帐户和 Blob 容器的访问权限。

```azurecli
HPC_CACHE_USER="StorageCache Resource Provider"
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
$HPC_CACHE_ID=$(az ad sp list --display-name "${HPC_CACHE_USER}" --query "[].objectId" -o tsv)
az role assignment create --role "Storage Account Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
az role assignment create --role "Storage Blob Data Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
```

### <a name="5-configure-the-storage-target"></a>5. 配置存储目标

将 Blob 容器作为存储目标添加到 HPC 缓存。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
CONTAINER_NAME=mystoragecontainer
az hpc-cache blob-storage-target add \
  --resource-group $RESOURCE_GROUP \
  --cache-name MyHpcCache \
  --name MyStorageTarget \
  --storage-account $STORAGE_ACCOUNT_ID \
  --container-name $CONTAINER_NAME \
  --virtual-namespace-path "/myfilepath"
```

### <a name="6-set-up-client-load-balancing"></a>6. 设置客户端负载均衡

为面向客户端的 IP 地址创建 Azure 专用 DNS 区域。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
PRIVATE_DNS_ZONE="myhpccache.local"
az network private-dns zone create \
  -g $RESOURCE_GROUP \
  -n $PRIVATE_DNS_ZONE
az network private-dns link vnet create \
  -g $RESOURCE_GROUP \
  -n MyDNSLink \
  -z $PRIVATE_DNS_ZONE \
  -v $VNET_NAME \
  -e true
```

创建轮循机制 DNS 名称。

```azurecli
DNS_NAME="server"
PRIVATE_DNS_ZONE="myhpccache.local"
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
HPC_MOUNTS0=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[0]" -o tsv | tr --delete '\r')
HPC_MOUNTS1=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[1]" -o tsv | tr --delete '\r')
HPC_MOUNTS2=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[2]" -o tsv | tr --delete '\r')
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS0
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS1
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS2
```

## <a name="create-the-aks-persistent-volume"></a>创建 AKS 永久性卷

创建一个 `pv-nfs.yaml` 文件，用于定义[永久性卷][persistent-volume]。

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 10000Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: server.myhpccache.local
    path: /
```

首先请确保具有 Kubernetes 群集的凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

将 server 和 path 更新为在上一步骤中创建的 NFS（网络文件系统）卷的值 。 使用 [kubectl apply][kubectl-apply] 命令创建永久性卷：

```console
kubectl apply -f pv-nfs.yaml
```

使用 [kubectl describe][kubectl-describe] 命令验证永久性卷的状态是否为“Available”：

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistent-volume-claim"></a>创建永久性卷声明

创建一个用于定义[永久性卷声明][persistent-volume-claim]的 `pvc-nfs.yaml`。 例如：

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
      storage: 100Gi
```

使用 [kubectl apply][kubectl-apply] 命令创建永久性卷声明：

```console
kubectl apply -f pvc-nfs.yaml
```

使用 [kubectl describe][kubectl-describe] 命令验证永久性卷声明的状态是否为“Bound”：

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-the-hpc-cache-with-a-pod"></a>使用 Pod 装载 HPC 缓存

创建一个 `nginx-nfs.yaml` 文件，用于定义使用永久性卷声明的 Pod。 例如：

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
    - while true; do echo $(date) >> /mnt/azure/myfilepath/outfile; sleep 1; done
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
kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
server.myhpccache.local:/myfilepath 8.0E         0      8.0E   0% /mnt/azure/myfilepath
...
```

## <a name="frequently-asked-questions-faq"></a>常见问题解答 (FAQ)

### <a name="running-applications-as-non-root"></a>以非 root 身份运行应用程序

如果需要以非 root 用户身份运行应用程序，可能需要禁用 root squash 功能，以通过 chown 将目录所有者修改为另一用户。  非 root 用户需要拥有某个目录才能访问文件系统。  要使用户拥有某个目录，root 用户必须通过 chown 将该目录的所有者修改为该用户。但是，如果 HPC 缓存已启用 root squash，则此操作将被拒绝，因为 root 用户 (UID 0) 正在映射到匿名用户。  在[此处][hpc-cache-access-policies]可以找到有关 root squash 功能和客户端访问策略的详细信息。

### <a name="sending-feedback"></a>发送反馈

欢迎咨询！  请向 <aks-hpccache-feed@microsoft.com> 发送任何反馈或问题。

## <a name="next-steps"></a>后续步骤

* 有关 Azure HPC 缓存的详细信息，请参阅 [HPC 缓存概述][hpc-cache]。
* 有关在 AKS 中使用 NFS 的详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中手动创建并使用 NFS（网络文件系统）Linux Server 卷][aks-nfs]。

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[hpc-cache]: ../hpc-cache/hpc-cache-overview.md
[hpc-cache-access-policies]: ../hpc-cache/access-policies.md
[hpc-cache-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache&regions=all
[hpc-cache-cli-prerequisites]: ../hpc-cache/az-cli-prerequisites.md
[hpc-cache-prereqs]: ../hpc-cache/hpc-cache-prerequisites.md
[az-hpc-cache-create]: /cli/azure/hpc-cache#az_hpc_cache_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[persistent-volume]: concepts-storage.md#persistent-volumes
[persistent-volume-claim]: concepts-storage.md#persistent-volume-claims
