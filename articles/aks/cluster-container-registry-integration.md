---
title: 将将容器注册表与 Azure Kubernetes 服务集成
description: 了解如何将 Azure Kubernetes 服务 (AKS) 与 Azure 容器注册表 (ACR) 集成
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62dd73e216b8b1ff1d9bb61210c90f8457b5a4fe
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105698"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS) 时，需要建立身份验证机制。 此操作通过向 ACR 授予所需权限的方式实现，是 CLI、PowerShell 和门户体验的一部分。 本文提供了在这两个 Azure 服务之间配置身份验证的示例。

可以使用 Azure CLI 或 Azure PowerShell 通过几个简单的命令设置 AKS 与 ACR 的集成。 此集成会将 AcrPull 角色分配给关联到 AKS 群集的托管标识。

> [!NOTE]
> 本文介绍了 AKS 和 ACR 之间的自动身份验证。 如果需要从专用外部注册表拉取映像，请使用[映像拉取机密][Image Pull Secret]。

## <a name="before-you-begin"></a>准备阶段

这些示例需要：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure 订阅上的“所有者”、“Azure 帐户管理员”或“Azure 共同管理员”角色
* Azure CLI 2.7.0 版或更高版本

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* Azure 订阅上的“所有者”、“Azure 帐户管理员”或“Azure 共同管理员”角色
* Azure PowerShell 5.9.0 版或更高版本

---

为避免需要“所有者”、“Azure 帐户管理员”或“Azure 共同管理员”角色，可以使用现有的托管标识从 AKS 对 ACR 进行身份验证。 有关详细信息，请参阅[使用 Azure 托管标识向 Azure 容器注册表验证身份](../container-registry/container-registry-authentication-managed-identity.md)。

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>通过 ACR 集成创建新的 AKS 群集

可以在一开始创建 AKS 群集时设置 AKS 与 ACR 的集成。  若要允许 AKS 群集与 ACR 交互，请使用 Azure Active Directory 托管标识。 使用以下命令可以在订阅中授权现有 ACR，并为托管标识配置适当的 ACRPull 角色。 为下面的参数提供有效值。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

或者，可以使用 ACR 资源 ID 指定 ACR 名称，其格式如下：

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> 如果所用 ACR 与 AKS 群集位于不同的订阅中，则在从 AKS 群集进行附加或分离时，请使用 ACR 资源 ID。

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR = 'myContainerRegistry'

# Run the following line to create an Azure Container Registry if you do not already have one
New-AzContainerRegistry -Name $MYACR -ResourceGroupName myContainerRegistryResourceGroup -Sku Basic

# Create an AKS cluster with ACR integration
New-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -GenerateSshKey -AcrNameToAttach $MYACR
```

---

此步骤可能需要几分钟才能完成。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>为现有的 AKS 群集配置 ACR 集成

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

通过为 **acr-name** 或 **acr-resource-id** 提供有效值，将现有 ACR 与现有 AKS 群集集成，如下所示。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

或者，

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

> [!NOTE]
> 运行 `az aks update --attach-acr` 使用运行该命令的用户的权限来创建角色 ACR 分配。 此角色分配给 kubelet 托管标识。 有关 AKS 托管标识的详细信息，请参阅[托管标识摘要][summary-msi]。

还可以使用以下命令删除 ACR 与 AKS 群集之间的集成

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

或

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

通过为 acr-name 提供有效值，将现有 ACR 与现有 AKS 群集集成，如下所示。

```azurepowershell
Set-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -AcrNameToAttach <acr-name>
```

> [!NOTE]
> 运行 `Set-AzAksCluster -AcrNameToAttach` 会使用运行此命令的用户的权限来创建“ACR 分配”角色。 此角色分配给 kubelet 托管标识。 有关 AKS 托管标识的详细信息，请参阅[托管标识摘要][summary-msi]。

还可以使用以下命令删除 ACR 与 AKS 群集之间的集成

```azurepowershell
Set-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup -AcrNameToDetach <acr-name>
```

---

## <a name="working-with-acr--aks"></a>使用 ACR 和 AKS

### <a name="import-an-image-into-your-acr"></a>将映像导入 ACR

通过运行以下命令，将映像从 Docker Hub 导入到 ACR：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzContainerRegistryImage -RegistryName <acr-name> -ResourceGroupName myResourceGroup -SourceRegistryUri docker.io -SourceImage library/nginx:latest
```

---

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>将示例映像从 ACR 部署到 AKS

确保你具有正确的 AKS 凭据

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

---

创建名为 acr-nginx.yaml 的文件，其中包含以下内容。 请将 acr-name 替换为注册表的资源名称。 示例：myContainerRegistry。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

接下来，在 AKS 群集中运行此部署：

```console
kubectl apply -f acr-nginx.yaml
```

可以通过运行以下命令来监视部署：

```console
kubectl get pods
```

应有两个正在运行的 pod。

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>故障排除
* 运行 [az aks check-acr](/cli/azure/aks#az_aks_check_acr) 命令，验证是否可以从 AKS 群集访问注册表。
* 详细了解 [Azure 监视](../container-registry/monitor-service.md)
* 详细了解 [ACR 运行状况](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

[summary-msi]: use-managed-identity.md#summary-of-managed-identities
