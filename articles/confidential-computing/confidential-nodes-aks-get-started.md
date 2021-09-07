---
title: 快速入门：使用 Azure CLI 部署具有机密计算节点的 AKS 群集
description: 了解如何创建具有机密节点的 Azure Kubernetes 服务 (AKS) 群集，并使用 Azure CLI 部署 Hello World 应用。
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: db2eb3bf906ffebe67489ab83cb7e13595b601d5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112747"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>快速入门：使用 Azure CLI 部署具有机密计算节点的 AKS 群集

在本快速入门中，你将使用 Azure CLI 部署具有机密计算 (DCsv2) 节点的 Azure Kubernetes 服务 (AKS) 群集。 然后，在 enclave 中运行一个简单的 Hello World 应用程序。 你还可通过 Azure 门户预配群集并添加机密计算节点，但本快速入门重点介绍 Azure CLI。

AKS 是一种使开发人员或群集操作员能够快速部署和管理群集的托管 Kubernetes 服务。 要了解详细信息，请参阅 [AKS 简介](../aks/intro-kubernetes.md)和 [AKS 机密节点概述](confidential-nodes-aks-overview.md)。

机密计算节点的功能包括：

- 支持 Linux 容器的 Linux 工作器节点。
- 带有 Ubuntu 18.04 虚拟机 (VM) 节点的第 2 代 VM。
- 支持 Intel SGX 的 CPU，它利用加密页高速缓存 (EPC) 来帮助在受机密性保护的 enclave 中运行容器。 有关详细信息，请参阅 [Azure 机密计算常见问题解答](./faq.yml)。
- 在机密计算节点上预装了 Intel SGX DCAP 驱动程序。 有关详细信息，请参阅 [Azure 机密计算常见问题解答](./faq.yml)。

> [!NOTE]
> DCsv2 VM 使用定价较高、可用性受区域限制的专用硬件。 有关详细信息，请参阅[可用的 SKU 和受支持的区域](virtual-machine-solutions.md)。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

- 一个有效的 Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在部署计算机上安装并配置 Azure CLI 版本 2.0.64 或更高版本。 

  运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)。
- 订阅中最少有六个 DCsv2 核心可供使用。 

  默认情况下，每个 Azure 订阅的机密计算配额为 8 个 VM 核心。 如果你计划预配需要 8 个以上核心的群集，请按照[这些说明](../azure-portal/supportability/per-vm-quota-requests.md)创建关于增加配额的工单。

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>创建具有机密计算节点和加载项的 AKS 群集

根据以下说明创建启用了机密计算加载项的 AKS 群集，将节点池添加到群集，并验证所创建的内容。

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>创建包含系统节点池的 AKS 群集

> [!NOTE]
> 如果已有满足前面所列先决条件的 AKS 群集，请[跳至下一部分](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster)，添加一个机密计算节点池。

首先，使用 [az group create][az-group-create] 命令为群集创建资源组。 以下示例会在 westus2 区域创建一个名为 myResourceGroup 的资源组 ：

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

现在，使用 [az aks create][az-aks-create] 命令创建一个启用了机密计算加载项的 AKS 群集：

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addons confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>将具有机密计算功能的用户节点池添加到 AKS 群集 

运行以下命令，在 AKS 群集中添加大小为 `Standard_DC2s_v2` 且具有三个节点的用户节点池。 你可从[受支持的 DCsv2 SKU 和区域列表](../virtual-machines/dcv2-series.md)中选择其他 SKU。

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

运行命令后，应该会显示具有 DCsv2 的新节点池，以及机密计算加载项 DaemonSet（[SGX 设备插件](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)）。

### <a name="verify-the-node-pool-and-add-on"></a>验证节点池和加载项

使用 [az aks get-credentials][az-aks-get-credentials] 命令获取 AKS 群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

使用 `kubectl get pods` 命令验证是否正确创建了节点，以及与 SGX 相关的 DaemonSet 是否在 DCsv2 节点池上运行：

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

如果输出与前面的代码匹配，那 AKS 群集现在就可以运行机密应用程序了。

你可以转到本快速入门的[通过独立的 enclave 应用程序部署 Hello World](#hello-world) 部分，在 enclave 中测试应用。 或根据以下说明在 AKS 上添加更多节点池。 （AKS 支持 SGX 节点池和非 SGX 节点池混合。）

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>将机密计算节点池添加到现有 AKS 群集<a id="existing-cluster"></a>

本部分假设你已在运行满足本快速入门前面所列先决条件的 AKS 群集。

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>在现有群集上启用机密计算 AKS 加载项

运行以下命令以启用机密计算加载项：

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>将 DCSv2 节点池添加到群集中

> [!NOTE]
> 要使用机密计算功能，现有 AKS 群集至少需要有一个基于 DCsv2 VM SKU 的节点池。 要详细了解用于机密计算的 DCs-v2 VM SKU，请参阅[可用的 SKU 和受支持的区域](virtual-machine-solutions.md)。

运行以下命令，创建节点池：

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

验证是否已创建名为 confcompool1 的新节点池：

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>验证 DaemonSet 是否在机密节点池上运行

登录到现有 AKS 群集，执行以下验证：

```console
kubectl get nodes
```

输出应会显示 AKS 群集上新添加的 confcompool1 池。 你还可以看到其他 DaemonSet。

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

如果输出与前面的代码匹配，那 AKS 群集现在就可以运行机密应用程序了。 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>通过独立的 enclave 应用程序部署 Hello World <a id="hello-world"></a>
现在可以开始部署测试应用程序了。 

创建一个名为 hello-world-enclave.yaml 的文件，并粘贴以下 YAML 清单。 你可在 [Open Enclave 项目](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)中找到此示例应用程序代码。 此部署假设你已部署 confcom 加载项。

> [!NOTE]
> 以下示例从 Docker Hub 拉取公共容器映像。 建议设置一个拉取密钥，以使用 Docker Hub 帐户进行身份验证，而不是发出匿名拉取请求。 若要在使用公共内容时提高可靠性，请在专用 Azure 容器注册表中导入和管理映像。 [详细了解如何使用公共映像](../container-registry/buffer-gate-public-content.md)。

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
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

现在，使用 `kubectl apply` 命令创建一个将在安全 enclave 中打开的示例作业，如以下示例输出所示：

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

可运行以下命令来确认工作负载是否已成功创建受信任执行环境 (enclave)：

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

要删除在本快速入门中创建的机密计算节点池，请使用以下命令： 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

要删除 AKS 群集，请使用以下命令： 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>后续步骤

* 使用 [GitHub 中的机密容器示例](https://github.com/Azure-Samples/confidential-container-samples)，通过机密容器运行 Python、Node 或其他应用程序。

* 使用 [GitHub 中的 enclave 感知 Azure 容器示例](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)运行 enclave 感知应用程序。

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
