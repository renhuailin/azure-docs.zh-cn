---
title: Kubernetes on Azure 教程 - 缩放应用程序
description: 此 Azure Kubernetes 服务 (AKS) 教程介绍如何缩放 Kubernetes 中的节点和 Pod，以及如何实施水平 Pod 自动缩放。
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ab994a49ed81a13eeb018d39d2f1d8be7eaee733
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741610"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>教程：在 Azure Kubernetes 服务 (AKS) 中缩放应用程序

如果已按照教程执行，则在 AKS 中已有可正常工作的 Kubernetes 群集，并且已部署了示例 Azure 投票应用。 在本教程第 5 部分（共 7 部分）中，会在应用中扩大 Pod 并尝试 Pod 自动缩放。 还会了解如何缩放 Azure VM 节点数以更改群集用于托管工作负荷的容量。 学习如何：

> [!div class="checklist"]
> * 缩放 Kubernetes 节点
> * 手动缩放运行应用程序的 Kubernetes Pod
> * 配置运行应用前端的自动缩放 Pod

在后面的教程中，Azure 投票应用程序将更新为新版本。

## <a name="before-you-begin"></a>开始之前

上一教程中，应用程序已打包到容器映像中。 该映像已上传到 Azure容器注册表，同时，你创建了 AKS 群集。 然后，应用程序部署到了 AKS 群集。 如果尚未完成这些步骤，并且想要逐一完成，请先阅读[教程 1 – 创建容器映像][aks-tutorial-prepare-app]。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

此教程需要运行 Azure CLI 2.0.53 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

此教程需要运行 Azure PowerShell 5.9.0 版或更高版本。 运行 `Get-InstalledModule -Name Az` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell][azure-powershell-install]。

---

## <a name="manually-scale-pods"></a>手动缩放 Pod

在前述教程中部署 Azure 投票前端和 Redis 实例时，创建了单个副本。 若要查看群集中 Pod 的数目和状态，请使用 [kubectl get][kubectl-get] 命令，如下所示：

```console
kubectl get pods
```

以下示例输出显示一个前端 Pod 和一个后端 Pod：

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

若要手动更改 *azure-vote-front* 部署中的 Pod 数，请使用 [kubectl scale][kubectl-scale] 命令。 以下示例将前端 Pod 数增加到 *5*：

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

再次运行 [kubectl get pods][kubectl-get]，验证 AKS 是否成功创建其他 Pod。 大约一分钟后，即可在群集中使用 Pod：

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>自动缩放 Pod

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kubernetes 支持[水平 Pod 自动缩放][kubernetes-hpa]以根据 CPU 利用率或其他选择指标调整部署中的 Pod 数。 [指标服务器][metrics-server]用来将资源利用率提供给 Kubernetes，可自动部署在 AKS 群集 1.10 及更高版本中。 若要查看 AKS 群集的版本，请使用 [az aks show][az-aks-show] 命令，如以下示例所示：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Kubernetes 支持[水平 Pod 自动缩放][kubernetes-hpa]以根据 CPU 利用率或其他选择指标调整部署中的 Pod 数。 [指标服务器][metrics-server]用来将资源利用率提供给 Kubernetes，可自动部署在 AKS 群集 1.10 及更高版本中。 若要查看 AKS 群集的版本，请使用 [Get-AzAksCluster][get-azakscluster] cmdlet，如以下示例所示：

```azurepowershell
(Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster).KubernetesVersion
```

---

> [!NOTE]
> 如果 AKS 群集小于 *1.10*，则不会自动安装指标服务器。 指标服务器安装清单在指标服务器发行版中以 `components.yaml` 资产的形式提供，这意味着你可以通过 URL 安装这些清单。 若要详细了解这些 YAML 定义，请参阅自述文件的[部署][metrics-server-github]部分。
>
> 示例安装：
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

若要使用自动缩放程序，你的 Pod 中的所有容器和你的 Pod 必须定义了 CPU 请求和限制。 在 `azure-vote-front` 部署中，前端容器已请求了 0.25 个 CPU，限制为 0.5 个 CPU。

这些资源请求和限制是为每个容器定义的，如以下示例代码片段所示：

```yaml
  containers:
  - name: azure-vote-front
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    ports:
    - containerPort: 80
    resources:
      requests:
        cpu: 250m
      limits:
        cpu: 500m
```

下面的示例使用 [kubectl autoscale][kubectl-autoscale] 命令自动缩放 *azure-vote-front* 部署中的 Pod 数。 如果所有 Pod 的平均 CPU 利用率超过其请求使用率的 50%，则自动缩放程序会将 Pod 增加到最多 *10* 个实例。 为部署定义的最小实例数为 *3*：

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

或者，可以创建一个清单文件来定义自动缩放程序的行为和资源限制。 下面名为 `azure-vote-hpa.yaml` 的清单文件的示例。

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

使用 `kubectl apply` 应用 `azure-vote-hpa.yaml` 清单文件中定义的自动缩放程序。

```console
kubectl apply -f azure-vote-hpa.yaml
```

若要查看自动缩放程序的状态，请使用 `kubectl get hpa` 命令，如下所示：

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

在 Azure 投票应用处于最小负荷状态几分钟之后，Pod 副本数会自动减少到 3 个。 也可再次使用 `kubectl get pods` 来查看不需要的 Pod 是否已删除。

## <a name="manually-scale-aks-nodes"></a>手动缩放 AKS 节点

如果在前面的教程中使用命令创建了 Kubernetes 群集，则它具有两个节点。 如果计划在群集上有更多或更少的容器工作负荷，则可以手动调整节点数。

下面的示例将名为 myAKSCluster 的 Kubernetes 群集中的节点数增加到 3 个。 该命令需要几分钟时间完成。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

当群集成功缩放时，输出类似于以下示例：

```output
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster | Set-AzAksCluster -NodeCount 3
```

当群集成功缩放时，输出类似于以下示例：

```output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.19.9
DnsPrefix               : myAKSCluster
Fqdn                    : myakscluster-000a0aa0.hcp.eastus.azmk8s.io
PrivateFQDN             :
AgentPoolProfiles       : {default}
WindowsProfile          : Microsoft.Azure.Commands.Aks.Models.PSManagedClusterWindowsProfile
AddonProfiles           : {}
NodeResourceGroup       : MC_myresourcegroup_myAKSCluster_eastus
EnableRBAC              : True
EnablePodSecurityPolicy :
NetworkProfile          : Microsoft.Azure.Commands.Aks.Models.PSContainerServiceNetworkProfile
AadProfile              :
ApiServerAccessProfile  :
Identity                :
LinuxProfile            : Microsoft.Azure.Commands.Aks.Models.PSContainerServiceLinuxProfile
ServicePrincipalProfile : Microsoft.Azure.Commands.Aks.Models.PSContainerServiceServicePrincipalProfile
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myresourcegroup/providers/Micros
                          oft.ContainerService/managedClusters/myAKSCluster
Name                    : myAKSCluster
Type                    : Microsoft.ContainerService/ManagedClusters
Location                : eastus
Tags                    : {}
```

---

## <a name="next-steps"></a>后续步骤

在本教程中，在 Kubernetes 群集中使用了不同的缩放功能。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 手动缩放运行应用程序的 Kubernetes Pod
> * 配置运行应用前端的自动缩放 Pod
> * 手动缩放 Kubernetes 节点

继续学习下一教程，了解如何在 Kubernetes 中更新应用程序。

> [!div class="nextstepaction"]
> [在 Kubernetes 中更新应用程序][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[azure-powershell-install]: /powershell/azure/install-az-ps
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
