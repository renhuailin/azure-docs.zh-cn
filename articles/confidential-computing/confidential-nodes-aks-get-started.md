---
title: 快速入门：使用 Azure CLI 与机密计算节点一起部署 Azure Kubernetes 服务 (AKS) 群集
description: 在本快速入门中，你将了解如何使用机密节点创建 AKS 群集，以及如何使用 Azure CLI 部署 Hello World 应用。
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: 73770acefc8a153e4a2f2fde146f9afd4c319cd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933128"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>快速入门：使用 Azure CLI 通过机密计算节点 (DCsv2) 部署 Azure Kubernetes 服务 (AKS) 群集

本快速入门面向的是这样的开发人员或群集操作员，他们想要快速创建 AKS 群集并部署应用程序，从而使用 Azure 中的托管 Kubernetes 服务监视应用程序。 还可以从 Azure 门户预配群集并添加机密计算节点。

## <a name="overview"></a>概述

本快速入门介绍如何使用 Azure CLI 通过机密计算节点部署 Azure Kubernetes 服务 (AKS) 群集，并在 enclave 中运行简单的 Hello World 应用程序。 AKS 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 要了解详细信息，请参阅 [AKS 简介](../aks/intro-kubernetes.md)和 [AKS 机密节点概述](confidential-nodes-aks-overview.md)。

> [!NOTE]
> 机密计算 DCsv2 VM 使用定价较高、其可用性受区域限制的专业硬件。 有关详细信息，请查看虚拟机页面了解[可用的 SKU 和受支持的区域](virtual-machine-solutions.md)。

### <a name="confidential-computing-node-features-dcsv2"></a>机密计算节点功能 (DCsv2)

1. 支持 Linux 容器的 Linux 工作器节点。
1. 带有 Ubuntu 18.04 虚拟机节点的第 2 代 VM。
1. 基于 Intel SGX 的 CPU，其中具有加密页高速缓存 (EPC)。 在[此处](./faq.md)了解详细信息。
1. 支持 Kubernetes 版本 1.16+。
1. 在 AKS 节点上预安装的 Intel SGX DCAP 驱动程序。 在[此处](./faq.md)了解详细信息。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

1. 一个有效的 Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
1. 在部署计算机上安装并配置 Azure CLI 版本 2.0.64 或更高版本（运行 `az --version` 以查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)。
1. 订阅中最少有 6 个 **DCSv2** 内核可供使用。 默认情况下，每个 Azure 订阅的机密计算 VM 内核配额为 8 个内核。 如果你计划预配需要 8 个以上内核的群集，请按照[这些](../azure-portal/supportability/per-vm-quota-requests.md)说明创建配额增加票证。

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>创建具有机密计算节点和加载项的新 AKS 群集

遵循以下说明添加具有加载项的支持机密计算的节点。

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>创建包含系统节点池的 AKS 群集

如果已有满足上述要求的 AKS 群集，请[跳到现有群集部分](#existing-cluster)，添加新的机密计算节点池。

首先，使用 [az group create][az-group-create] 命令为群集创建资源组。 以下示例会在 westus2 区域创建一个名为 myResourceGroup 的资源组 ：

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

现在，使用 [az aks create][az-aks-create] 命令创建 AKS 群集：

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

上面创建了一个具有系统节点池的新 AKS 群集，并启用了加载项。 接下来，将具有机密计算功能的用户节点池添加到 AKS 群集。

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>将机密计算节点池添加到 AKS 群集 

运行以下命令以添加具有三个节点且大小为 `Standard_DC2s_v2` 的用户节点池。 可以从 [DCsv2 SKU 和区域](../virtual-machines/dcv2-series.md)的受支持列表中选择其他 SKU。

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

运行后，应该会出现一个带有 **DCsv2** 的新节点池，以及机密计算加载项守护程序集（[SGX 设备插件](confidential-nodes-aks-overview.md#sgx-plugin)）。

### <a name="verify-the-node-pool-and-add-on"></a>验证节点池和加载项

使用 [az aks get-credentials][az-aks-get-credentials] 命令获取 AKS 群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

使用 kubectl get pods & nodes 命令验证是否正确创建节点以及与 SGX 相关的守护程序集是否在 **DCsv2** 节点池上运行，如下所示：

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

如果输出与上述项匹配，则 AKS 群集现已准备好运行机密应用程序。

转到[来自 Enclave 的 Hello World](#hello-world) 部署部分以测试 enclave 中的应用。 或者，按照以下说明在 AKS 上添加其他节点池（AKS 支持将 SGX 节点池和非 SGX 节点池混搭使用）。

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>将机密计算节点池添加到现有 AKS 群集<a id="existing-cluster"></a>

本部分假定你已运行的 AKS 群集满足先决条件部分（适用于加载项）中列出的条件。

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>在现有群集上启用机密计算 AKS 加载项

运行以下命令以启用机密计算加载项：

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>将 **DCSv2** 节点池添加到群集中

> [!NOTE]
> 要使用机密计算功能，则现有 AKS 群集需要具有至少一个基于 **DCsv2** VM SKU 的节点池。 要了解有关机密计算 DCsv2 VM SKU 的详细信息，请参阅[可用的 SKU 和支持的区域](virtual-machine-solutions.md)。

运行以下命令来创建新节点池：

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

验证是否已创建名称为 confcompool1 的新节点池：

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>验证守护程序集是否在机密节点池上运行

登录到现有 AKS 群集执行以下验证。

```console
kubectl get nodes
```

输出应会显示 AKS 群集上新添加的 confcompool1。 你还可以看到其他守护程序集。

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

如果输出与上述项匹配，则 AKS 群集现已准备好运行机密应用程序。 遵照以下说明部署测试应用程序。

## <a name="hello-world-from-isolated-enclave-application"></a>来自独立 enclave 应用程序的 Hello World <a id="hello-world"></a>
创建一个名为 hello-world-enclave.yaml 的文件，并粘贴以下 YAML 清单。 可在 [Open Enclave 项目](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)中找到这个基于 Open Enclave 的示例应用程序代码。 以下部署假定你已部署了“confcom”加载项。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

现在，使用 kubectl apply 命令创建一个将在 secure enclave 中启动的示例作业，如以下示例输出所示：

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

可运行以下命令来确认工作负载已成功创建了受信任的执行环境 (Enclave)：

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>清理资源

要删除关联的节点池或删除 AKS 群集，请使用以下命令：

### <a name="remove-the-confidential-computing-node-pool"></a>删除机密计算节点池

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>删除 AKS 群集

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>后续步骤

* 通过机密容器以机密的方式运行 Python 和 Node 等应用程序，方式是通过访问[机密容器示例](https://github.com/Azure-Samples/confidential-container-samples)。

* 通过访问 [Enclave 感知 Azure 容器示例](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)来运行 Enclave 感知应用程序。

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials