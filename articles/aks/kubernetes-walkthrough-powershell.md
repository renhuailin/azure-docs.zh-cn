---
title: 快速入门：使用 PowerShell 部署 AKS 群集
description: 了解如何使用 PowerShell 快速创建 Kubernetes 群集、部署应用程序，以及监视 Azure Kubernetes 服务 (AKS) 中的性能。
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8121a3031de8fd7fdb8dc3db0ef1f4be1902e3c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728144"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>快速入门：使用 PowerShell 部署 Azure Kubernetes 服务群集

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 在本快速入门中，请执行以下操作：
* 使用 PowerShell 部署 AKS 群集。
* 在该群集中运行一个包含 Web 前端和 Redis 实例的多容器应用程序。
* 监视运行应用程序的群集和 Pod 的运行状况。

若要详细了解如何创建 Windows Server 节点池，请参阅[创建支持 Windows Server 容器的 AKS 群集][windows-container-powershell]。

![Azure Kubernetes 服务中部署的投票应用](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

本快速入门假设读者基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果你在本地运行 PowerShell，请安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell][install-azure-powershell]。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果你有多个 Azure 订阅，请使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择应在其中计收资源费用的相应订阅 ID。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

[Azure 资源组](../azure-resource-manager/management/overview.md)是用于部署和管理 Azure 资源的逻辑组。 创建资源组时，系统会提示你指定一个位置。 此位置是：
* 资源组元数据的存储位置。
* 在创建资源期间未指定另一个区域时，资源在 Azure 中的运行位置。

以下示例在“eastus”区域创建名为“myResourceGroup”的资源组 。

使用 [New-AzResourceGroup][new-azresourcegroup] cmdlet 创建资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

成功创建的资源组的输出：

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>创建 AKS 群集

1. 使用 `ssh-keygen` 命令行实用工具生成 SSH 密钥对。
    * 有关详细信息，请参阅[快速步骤：创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对](../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 使用 [New-AzAksCluster][new-azakscluster] cmdlet 创建 AKS 群集。 默认已启用用于容器的 Azure Monitor。

    以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

几分钟后，该命令将完成并返回有关群集的信息。

> [!NOTE]
> 创建 AKS 群集时，会自动创建另一个资源组来存储 AKS 资源。 有关详细信息，请参阅[为什么使用 AKS 创建两个资源组？](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。

1. 使用 `Install-AzAksKubectl` cmdlet 在本地安装 `kubectl`：

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. 使用 [Import-AzAksCredential][import-azakscredential] cmdlet 将 `kubectl` 配置为连接到你的 Kubernetes 群集。 以下 cmdlet 将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. 使用 [kubectl get][kubectl-get] 命令验证与群集之间的连接。 此命令将返回群集节点的列表。

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    输出显示在上一步骤中创建的单个节点。 确保节点状态为 *Ready*：

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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
    * 如果使用 Azure Cloud Shell，可以使用 `vi` 或 `nano` 来创建此文件，就像在虚拟或物理系统中操作一样
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    输出显示已成功创建的部署和服务：

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。

使用带有 `--watch` 参数的 [kubectl get service][kubectl-get] 命令来监视进度。

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

`azure-vote-front` 服务的 **EXTERNAL-IP** 输出最初显示为 *pending*。

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

在 **EXTERNAL-IP** 地址从 *pending* 更改为实际公共 IP 地址后，请使用 `CTRL-C` 来停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看 Azure Vote 应用的实际效果，请打开 Web 浏览器并转到服务的外部 IP 地址。

![Azure Kubernetes 服务中部署的投票应用](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

在 Azure 门户中查看用于容器的 Azure Monitor 捕获的群集节点和 Pod 运行状况指标。

## <a name="delete-the-cluster"></a>删除群集

为了避免产生 Azure 费用，请清理不需要的资源。 使用 [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet 删除资源组、容器服务及所有相关资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: /previous-versions/azure/dev-spaces/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md