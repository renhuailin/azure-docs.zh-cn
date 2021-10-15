---
title: 将 Azure NetApp 文件与 Azure Kubernetes 服务集成 | Microsoft Docs
description: 了解如何使用 Azure Kubernetes 服务预配 Azure NetApp 文件。
services: container-service
ms.topic: article
ms.date: 10/04/2021
ms.openlocfilehash: 177526fa98ada37341fadc90e183f224e1aa0830
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544785"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>将 Azure NetApp 文件与 Azure Kubernetes 服务集成

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 本文将介绍如何在 Azure Kubernetes 服务 (AKS) 群集中创建可供 Pod 使用的 [Azure NetApp 文件卷][anf]。

[Azure NetApp 文件][anf]是 Azure 上运行的一种企业级高性能计量式文件存储服务。 Kubernetes 用户在使用适用于 Kubernetes 工作负载的 Azure NetApp 文件时拥有以下两个选项：

* 静态创建 Azure NetApp 文件卷：在本方案中，可在 AKS 外部实现创建卷。使用 `az`/Azure UI 创建卷，然后通过创建 `PersistentVolume` 将卷向 Kubernetes 公开。
* 按需创建 Azure NetApp 文件卷，通过 Kubernetes 进行协调：此方法是直接通过 Kubernetes 创建多个卷的首选操作模式，需通过 [Astra Trident](https://netapp-trident.readthedocs.io/) 实现。

强烈建议使用可供 Kubernetes 用户直接通过 AKS 使用 Azure NetApp 文件的生产就绪 CSI 驱动程序。 适用于 Kubernetes 的开源动态存储业务流程协调程序 Astra Trident 可满足此要求。 Astra Trident 是专为 Kubernetes 构建的企业级存储业务流程协调程序，其完全支持 NetApp。 其可通过自动执行存储预配，简化跨 Kubernetes 环境对存储的访问。 使用者可以利用 Astra Trident 适用于 Azure NetApp 文件的 CSI 驱动程序，抽象基础详细信息并按需创建/扩展卷，或拍摄卷的快照。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

> [!IMPORTANT]
> 此外，你的 AKS 群集必须[位于支持 Azure NetApp 文件的区域中][anf-regions]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

### <a name="prerequisites"></a>先决条件

使用 Azure NetApp 文件时需注意以下事项：

* Azure NetApp 文件只能在[选定的 Azure 区域][anf-regions]中使用。
* 初始部署 AKS 群集后，可以选择以静态或动态方式预配 Azure NetApp 文件。
* 要在 Azure NetApp 文件中使用动态预配，请安装并配置 [Astra Trident](https://netapp-trident.readthedocs.io/) 19.07 或更高版本。

## <a name="configure-azure-netapp-files"></a>配置 Azure NetApp 文件

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

可静态或动态预配卷。 以下详细介绍了这两个选项。

## <a name="provision-azure-netapp-files-volumes-statically"></a>静态预配 Azure NetApp 文件卷

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
UNIQUE_FILE_PATH="myfilepath2" # Note that file path needs to be unique within all ANF Accounts

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

### <a name="create-the-persistentvolume"></a>创建 PersistentVolume

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

### <a name="create-the-persistentvolumeclaim"></a>创建 PersistentVolumeClaim

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

### <a name="mount-with-a-pod"></a>装载到 Pod

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

## <a name="provision-azure-netapp-files-volumes-dynamically"></a>动态预配 Azure NetApp 文件卷

### <a name="install-and-configure-astra-trident"></a>安装并配置 Astra Trident

要动态预配卷，请安装 Astra Trident。 Astra Trident 是 NetApp 的动态存储预配程序，专为 Kubernetes 而构建。 使用 Astra Trident 的行业标准[容器存储接口 (CSI)](https://kubernetes-csi.github.io/docs/) 驱动程序可简化 Kubernetes 应用程序的存储消耗。 Astra Trident 在 Kubernetes 群集中部署为 Pod，并为 Kubernetes 工作负载提供动态存储业务流程服务。

参阅[文档](https://netapp-trident.readthedocs.io/en/latest/index.html)即可了解详细信息。

继续进行下一步之前，需执行以下操作：

1. 安装 Astra Trident。 可使用运算符/Helm 图表/`tridentctl` 安装 Trident。 如下所示的说明解释了可使用运算符安装 Astra Trident 的方法。 要了解其他安装方法的工作原理，请参阅[安装指南](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html)。

2. 创建后端。 要指示 Astra Trident Azure NetApp 文件订阅及其需要在何处创建卷，请创建后端。 此步骤需要上一步中创建的帐户详细信息。

#### <a name="install-astra-trident-using-the-operator"></a>使用运算符安装 Astra Trident

本部分将介绍如何使用运算符安装 Astra Trident。 还可以选择使用其他方法执行安装：

* [Helm 图表](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/operator-deploy.html#deploy-trident-operator-by-using-helm)。
* [tridentctl](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/tridentctl-deploy.html)。

请参阅[部署 Trident](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html)，了解每个选项的工作原理，并确定最适合的选项。

在 Astra Trident 的 [GitHub 存储库](https://github.com/NetApp/trident/releases)中下载 Astra Trident。 在所需版本中选择，并下载安装程序捆绑包。

```console
#Download Astra Trident

$  wget https://github.com/NetApp/trident/releases/download/v21.07.1/trident-installer-21.07.1.tar.gz
$  tar xzvf trident-installer-21.07.1.tar.gz
```
使用 `deploy/bundle.yaml` 部署运算符。

```console
$  kubectl create ns trident

namespace/trident created

$  kubectl apply -f trident-installer/deploy/bundle.yaml -n trident

serviceaccount/trident-operator created
clusterrole.rbac.authorization.k8s.io/trident-operator created
clusterrolebinding.rbac.authorization.k8s.io/trident-operator created
deployment.apps/trident-operator created
podsecuritypolicy.policy/tridentoperatorpods created
```

创建 `TridentOrchestrator` 以安装 Astra Trident。

```console
$ kubectl apply -f trident-installer/deploy/crds/tridentorchestrator_cr.yaml

tridentorchestrator.trident.netapp.io/trident created 
```

运算符通过使用 `TridentOrchestrator` 规范中提供的参数进行安装。可以从各种[安装](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html)和[后端指南](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/backends/index.html)中了解配置参数和示例后端。

确认已安装 Astra Trident。 

```console
$  kubectl describe torc trident
Name:         trident
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  trident.netapp.io/v1
Kind:         TridentOrchestrator
...
Spec:
  Debug:      true
  Namespace:  trident
Status:
  Current Installation Params:
    IPv6:                       false
    Autosupport Hostname:
    Autosupport Image:          netapp/trident-autosupport:21.01
    Autosupport Proxy:
    Autosupport Serial Number:
    Debug:                      true
    Enable Node Prep:           false
    Image Pull Secrets:
    Image Registry:
    k8sTimeout:           30
    Kubelet Dir:          /var/lib/kubelet
    Log Format:           text
    Silence Autosupport:  false
    Trident Image:        netapp/trident:21.07.1
  Message:                Trident installed
  Namespace:              trident
  Status:                 Installed
  Version:                v21.07.1
Events:
  Type    Reason      Age   From                        Message
  ----    ------      ----  ----                        -------
  Normal  Installing  74s   trident-operator.netapp.io  Installing Trident
  Normal  Installed   67s   trident-operator.netapp.io  Trident installed
```

### <a name="create-a-backend"></a>创建后端

安装 Astra Trident 后，创建指向 Azure NetApp 文件订阅的后端。

```console
$  kubectl apply -f trident-installer/sample-input/backends-samples/azure-netapp-files/backend-anf.yaml -n trident

secret/backend-tbc-anf-secret created
tridentbackendconfig.trident.netapp.io/backend-tbc-anf created
```

运行命令之前，需要更新 `backend-anf.yaml`，以使其包含有关 Azure NetApp 文件的详细信息，例如：

* Azure NetApp 文件内适用于 Azure 订阅的 `subscriptionID` 已开启。 The 
* Azure Active Directory (AD) [应用注册](../active-directory/develop/howto-create-service-principal-portal.md)中的 `tenantID`、`clientID` 和 `clientSecret`，且该应用注册对 Azure NetApp 文件服务具有足够权限。 应用注册必须拥有由 Azure 预定义的 `Owner` 和 `Contributor` 角色。
* 至少包含一个委派子网的 Azure 位置。

此外，可以选择提供不同的服务级别。 Azure NetApp 文件可提供三种[服务级别](../azure-netapp-files/azure-netapp-files-service-levels.md)：标准、高级和超级。

### <a name="create-a-storageclass"></a>创建 StorageClass

存储类用于定义使用永久性卷动态创建存储单位的方式。 要使用 Azure NetApp 文件卷，必须创建存储类。 创建名为 `anf-storageclass.yaml` 的文件，并将其复制到以下清单中。

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-netapp-files
provisioner: csi.trident.netapp.io
parameters:
  backendType: "azure-netapp-files"
  fsType: "nfs"
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类：

```console
$  kubectl apply -f anf-storageclass.yaml

storageclass/azure-netapp-files created

$  kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
azure-netapp-files   csi.trident.netapp.io   Delete          Immediate           false                  3s
```

### <a name="create-a-persistentvolumeclaim"></a>创建 PersistentVolumeClaim

PersistentVolumeClaim (PVC) 是指用户对存储空间的请求。 创建 PersistentVolumeClaim 后，Astra Trident 会自动创建 Azure NetApp 文件卷，使其可供 Kubernetes 工作负载使用。

创建名为 `anf-pvc.yaml` 的文件，并提供以下清单。 本示例创建一个名为“ReadWriteMany”的 1TiB 卷。

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: anf-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Ti
  storageClassName: azure-netapp-files
```

使用 [kubectl apply][kubectl-apply] 命令创建永久性卷声明：

```console
$  kubectl apply -f anf-pvc.yaml

persistentvolumeclaim/anf-pvc created

$  kubectl get pvc
kubectl get pvc -n trident
NAME      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS         AGE
anf-pvc   Bound    pvc-bffa315d-3f44-4770-86eb-c922f567a075   1Ti        RWO            azure-netapp-files   62s
```

### <a name="use-the-persistent-volume"></a>使用永久性卷

创建 PVC 后，可以启动 Pod 访问 Azure NetApp 文件卷。 以下清单可用于定义 NGINX pod，其可装载上一步中创建 Azure NetApp 文件卷。 在本示例中，卷将装载到 `/mnt/data`。

创建名为 `anf-nginx-pod.yaml` 的文件，其中包含以下内容：

```yml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: mcr.microsoft.com/oss/nginx/nginx:latest1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/data"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: anf-pvc
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod：

```console
$  kubectl apply -f anf-nginx-pod.yaml

pod/nginx-pod created
```

Kubernetes 现已创建装载卷的 Pod，并且可通过 `/mnt/data` 内的 `nginx` 容器访问。 使用 `kubectl describe` 检查 Pod 的事件日志以完成确认：

```console
$  kubectl describe pod nginx-pod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  anf-pvc
    ReadOnly:   false
  default-token-k7952:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-k7952
    Optional:    false
[...]
Events:
  Type    Reason                  Age   From                     Message
  ----    ------                  ----  ----                     -------
  Normal  Scheduled               15s   default-scheduler        Successfully assigned trident/nginx-pod to brameshb-non-root-test
  Normal  SuccessfulAttachVolume  15s   attachdetach-controller  AttachVolume.Attach succeeded for volume "pvc-bffa315d-3f44-4770-86eb-c922f567a075"
  Normal  Pulled                  12s   kubelet                  Container image "mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine" already present on machine
  Normal  Created                 11s   kubelet                  Created container nginx
  Normal  Started                 10s   kubelet                  Started container nginx
```

Astra Trident 支持 Azure NetApp 文件中的众多功能，例如：

* [扩展卷](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/vol-expansion.html)
* [按需卷快照](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/snapshots.html)
* [导入卷](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/import.html)

## <a name="next-steps"></a>后续步骤

* 有关 Azure NetApp 文件的详细信息，请参阅[什么是 Azure NetApp 文件][anf]。

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netapp-files-dynamic]: azure-netapp-files-dynamic.md
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
