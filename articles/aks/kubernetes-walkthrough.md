---
title: 快速入门：使用 Azure CLI 部署 AKS 群集
description: 了解如何使用 Azure CLI 快速创建 Kubernetes 群集、部署应用程序，以及监视 Azure Kubernetes 服务 (AKS) 中的性能。
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: cda15b8d0dfd5074fca75283589c3ea173310b4e
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769943"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>快速入门：使用 Azure CLI 部署 Azure Kubernetes 服务群集

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 在本快速入门中，请执行以下操作：
* 使用 Azure CLI 部署 AKS 群集。 
* 在该群集中运行一个包含 Web 前端和 Redis 实例的多容器应用程序。 
* 监视运行应用程序的群集和 Pod 的运行状况。

  ![Azure Kubernetes 服务中部署的投票应用](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

本快速入门假设读者基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

若要详细了解如何创建 Windows Server 节点池，请参阅[创建支持 Windows Server 容器的 AKS 群集][windows-container-cli]。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.0.64 或更高版本的 Azure CLI。 如果使用 Azure Cloud Shell，则最新版本已安装。
- 用于创建群集的标识具有相应的最小权限。 有关 AKS 访问和标识的详细信息，请参阅 [Azure Kubernetes Service (AKS) 的访问和标识选项](concepts-identity.md)。

> [!NOTE]
> 如果你打算在本地（而不是 Azure Cloud Shell 中）运行本快速入门中的命令，请以管理员身份运行命令。

## <a name="create-a-resource-group"></a>创建资源组

[Azure 资源组](../azure-resource-manager/management/overview.md)是用于部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会提示你指定一个位置。 此位置是： 
* 资源组元数据的存储位置。
* 在创建资源期间未指定另一个区域时，资源在 Azure 中的运行位置。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

使用 [az group create][az-group-create] 命令创建资源组。


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

成功创建的资源组的输出：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>启用群集监视

验证是否已在订阅中注册 *Microsoft.OperationsManagement* 和 *Microsoft.OperationalInsights*。 若要检查注册状态，请使用以下命令：

```azurecli
az provider show -n Microsoft.OperationsManagement -o table
az provider show -n Microsoft.OperationalInsights -o table
```
 
如果未注册，请使用以下命令注册 *Microsoft.OperationsManagement* 和 *Microsoft.OperationalInsights*：
 
```azurecli
az provider register --namespace Microsoft.OperationsManagement
az provider register --namespace Microsoft.OperationalInsights
```

## <a name="create-aks-cluster"></a>创建 AKS 群集

使用带有 --enable-addons monitoring 参数的 [az aks create][az-aks-create] 命令创建 AKS 群集，以启用[用于容器的 Azure Monitor][azure-monitor-containers]。 以下示例创建包含一个节点的名为 *myAKSCluster* 的群集。 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。

> [!NOTE]
> 创建 AKS 群集时，会自动创建另一个资源组来存储 AKS 资源。 有关详细信息，请参阅[为什么使用 AKS 创建两个资源组？](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 

1. 在本地使用 [az aks install-cli][az-aks-install-cli] 命令安装 `kubectl`：

    ```azurecli
    az aks install-cli
    ```

2. 使用 [az aks get-credentials][az-aks-get-credentials] 命令将 `kubectl` 配置为连接到你的 Kubernetes 群集。 以下命令：  
      * 下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。
      * 使用 `~/.kube/config`，即 [Kubernetes 配置文件][kubeconfig-file]的默认位置。 使用 *--file* 指定 Kubernetes 配置文件的其他位置。


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. 使用 [kubectl get][kubectl-get] 命令验证与群集之间的连接。 此命令将返回群集节点的列表。

    ```azurecli-interactive
    kubectl get nodes
    ```

    输出显示在上一步骤中创建的单个节点。 确保节点状态为 *Ready*：

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>运行应用程序

[Kubernetes 清单文件][kubernetes-deployment]定义群集的所需状态，例如，要运行哪些容器映像。 

在本快速入门中，你将使用清单来创建运行 [Azure Vote 应用程序][azure-vote-app]所需的所有对象。 此清单包含两个 [Kubernetes 部署][kubernetes-deployment]：
* 示例 Azure Vote Python 应用程序。
* 一个 Redis 实例。 

此外，还会创建两个 [Kubernetes 服务][kubernetes-service]：
* Redis 实例的内部服务。
* 用于通过 Internet 访问 Azure Vote 应用程序的外部服务。

1. 创建名为 `azure-vote.yaml` 的文件。
    * 如果使用 Azure Cloud Shell，则可以使用 `code`、`vi`或 `nano` 来创建此文件，就像在虚拟或物理系统中操作一样
1. 复制以下 YAML 定义：

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. 使用 [kubectl apply][kubectl-apply] 命令部署应用程序，并指定 YAML 清单的名称：

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    输出显示已成功创建的部署和服务：

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。

使用带有 `--watch` 参数的 [kubectl get service][kubectl-get] 命令来监视进度。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

`azure-vote-front` 服务的 **EXTERNAL-IP** 输出最初显示为 *pending*。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

在 **EXTERNAL-IP** 地址从 *pending* 更改为实际公共 IP 地址后，请使用 `CTRL-C` 来停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看 Azure Vote 应用的实际效果，请打开 Web 浏览器并转到服务的外部 IP 地址。

![Azure Kubernetes 服务中部署的投票应用](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

在 Azure 门户中查看[用于容器的 Azure Monitor][azure-monitor-containers] 捕获的群集节点和 Pod 运行状况指标。 

## <a name="delete-the-cluster"></a>删除群集

为了避免产生 Azure 费用，请清理不需要的资源。 可以使用 [az group delete][az-group-delete] 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。
> 
> 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="get-the-code"></a>获取代码

本快速入门使用现有的容器映像创建了 Kubernetes 部署。 [GitHub 上提供了][azure-vote-app]相关的应用程序代码、Dockerfile 和 Kubernetes 清单文件。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一个 Kubernetes 群集，然后在其中部署了多容器应用程序。 [访问 AKS 群集的 Kubernetes Web 仪表板][kubernetes-dashboard]。

若要详细了解 AKS 并演练部署示例的完整代码，请继续阅读“Kubernetes 群集”教程。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install_azure_cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
