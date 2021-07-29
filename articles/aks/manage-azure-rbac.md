---
title: 通过 Azure 管理 Kubernetes 中的 Azure RBAC
titleSuffix: Azure Kubernetes Service
description: 了解如何通过 Azure Kubernetes 服务 (AKS) 使用 Azure RBAC 进行 Kubernetes 授权。
services: container-service
ms.topic: article
ms.date: 02/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: 57aae03e18f938ca89da5081a2076698ea3341f8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110091569"
---
# <a name="use-azure-rbac-for-kubernetes-authorization"></a>使用 Azure RBAC 进行 Kubernetes 授权

目前，你已经可以利用 [Azure Active Directory (Azure AD) 与 AKS 之间的集成身份验证](managed-aad.md)。 启用后，此集成允许客户将 Azure AD 用户、组或服务主体用作 Kubernetes RBAC 中的主题，详见[此文](azure-ad-rbac.md)。
使用此功能，你无需分别管理 Kubernetes 的用户标识和凭据。 但是，你仍需分别设置和管理 Azure RBAC 和 Kubernetes RBAC。 若要详细了解如何在 AKS 上通过 RBAC 进行身份验证和授权，请参阅[此文](concepts-identity.md)。

本文档介绍了一种新方法，该方法允许你跨 Azure 资源、AKS 和 Kubernetes 资源实现统一的管理和访问控制。

## <a name="before-you-begin"></a>准备阶段

有了通过 Azure 管理 Kubernetes 资源的 RBAC 这一功能，你就可以选择使用 Azure 或原生 Kubernetes 机制管理群集资源的 RBAC。 启用后，将由 Azure RBAC 以独占方式验证 Azure AD 主体，由 Kubernetes RBAC 以独占方式验证常规 Kubernetes 用户和服务帐户。 若要详细了解如何在 AKS 上通过 RBAC 进行身份验证和授权，请参阅[此文](concepts-identity.md#azure-rbac-for-kubernetes-authorization)。

### <a name="prerequisites"></a>先决条件

- 确保已有 Azure CLI 2.24.0 或更高版本
- 确保已安装 [kubectl v1.18.3+][az-aks-install-cli]。

### <a name="limitations"></a>限制

- 需要[托管 Azure AD 集成](managed-aad.md)。
- 使用 [kubectl v1.18.3+][az-aks-install-cli]。
- 目前，如果你有 CRD 并且要创建自定义角色定义，则涵盖 CRD 的唯一方法是提供 `Microsoft.ContainerService/managedClusters/*/read`。 AKS 将会为 CRD 提供更精细的权限。 对于剩余的对象，你可以使用特定的 API 组，例如 `Microsoft.ContainerService/apps/deployments/read`。
- 授权服务器可能需要长达 5 分钟的时间来传播并更新新的角色分配。
- 为身份验证配置的 Azure AD 租户需要与包含 AKS 群集的订阅的租户相同。 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>使用 Azure RBAC 和托管 Azure AD 集成创建新群集

使用以下 CLI 命令创建 AKS 群集。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

创建具有托管 Azure AD 集成并使用 Azure RBAC 进行 Kubernetes 授权的 AKS 群集。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

成功创建具有 Azure AD 集成且使用 Azure RBAC 进行 Kubernetes 授权的群集后，响应正文中会出现以下部分：

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="integrate-azure-rbac-into-an-existing-cluster"></a>将 Azure RBAC 集成到现有群集中

> [!NOTE]
> 若要使用 Azure RBAC 进行 Kubernetes 授权，必须在群集上启用 Azure Active Directory 集成。 有关详细信息，请参阅 [Azure Active Directory 集成][managed-aad]。

若要将用于 Kubernetes 授权的 Azure RBAC 添加到现有 AKS 群集中，请使用带 `enable-azure-rbac` 标志的 [az aks update][az-aks-update] 命令。

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster --enable-azure-rbac
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>为用户创建角色分配以便访问群集

AKS 提供以下四个内置角色：


| 角色                                | 说明  |
|-------------------------------------|--------------|
| Azure Kubernetes 服务 RBAC 读取者  | 允许进行只读访问并查看命名空间中的大多数对象。 不允许查看角色或角色绑定。 此角色不允许查看 `Secrets`，因为读取 Secrets（机密）的内容就可以访问命名空间中的 ServiceAccount 凭据，这样就会允许以命名空间中任何 ServiceAccount 的身份进行 API 访问（一种特权提升形式）  |
| Azure Kubernetes 服务 RBAC 写入者 | 允许对命名空间中的大多数对象进行读/写访问。 此角色不允许查看或修改角色或角色绑定。 但是，此角色允许以命名空间中任何 ServiceAccount 的身份访问 `Secrets` 和运行 Pod，因此可用于获取命名空间中任何 ServiceAccount 的 API 访问级别。 |
| Azure Kubernetes 服务 RBAC 管理员  | 允许要在命名空间内授予的管理员访问权限。 允许对命名空间（或群集范围）中的大多数资源进行读/写访问，包括在命名空间内创建角色和角色绑定。 此角色不允许对资源配额或命名空间本身进行写入访问。 |
| Azure Kubernetes 服务 RBAC 群集管理员  | 允许超级用户访问权限（对任何资源执行任何操作）。 它提供对群集中每个资源和所有命名空间的完全控制。 |


以整个 AKS 群集为作用域的角色分配可以在 Azure 门户中群集资源的“访问控制(IAM)”边栏选项卡上执行，也可以通过 Azure CLI 命令执行，如下所示：

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

其中，`<AAD-ENTITY-ID>` 可以是用户名（例如 user@contoso.com），甚至可以是服务主体的 ClientID。

你还可以创建以群集中的特定命名空间为作用域的角色分配：

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

目前，以命名空间为作用域的角色分配需要通过 Azure CLI 进行配置。


### <a name="create-custom-roles-definitions"></a>创建自定义角色定义

（可选）你可以选择创建自己的角色定义，然后进行分配，如上所述。

下面是一个角色定义示例，仅允许用户读取部署，不允许读取任何其他内容。 你可以在[此处](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)查看可能的操作的完整列表。


将下面的 json 复制到名为 `deploy-view.json` 的文件中。

```json
{
    "Name": "AKS Deployment Reader",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

将 `<YOUR SUBSCRIPTION ID>` 替换为你的订阅的 ID，此 ID 可以通过运行以下命令来获取：

```azurecli-interactive
az account show --query id -o tsv
```

现在，我们可以通过从保存了 `deploy-view.json` 的文件夹运行以下命令来创建角色定义：

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

现在，你已有了角色定义，可以通过运行以下命令将其分配给用户或其他标识了：

```azurecli-interactive
az role assignment create --role "AKS Deployment Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>通过 `kubectl` 使用 Azure RBAC 进行 Kubernetes 授权

> [!NOTE]
> 通过运行以下命令，确保你具有最新的 kubectl：
>
> ```azurecli-interactive
> az aks install-cli
> ```
>
> 你可能需要通过 `sudo` 特权来运行它。

现在，你已分配了所需的角色和权限。 你可以开始调用 Kubernetes API，例如，通过 `kubectl` 调用它。

为此，我们首先使用以下命令获取群集的 kubeconfig：

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> 你需要具有 [Azure Kubernetes 服务群集用户](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role)内置角色才能执行上一步骤。

现在，你可以使用 kubectl 来执行所需操作，例如列出群集中的节点。 首次运行它时，你需要登录。后续命令将使用相应的访问令牌。

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>通过 `kubelogin` 使用 Azure RBAC 进行 Kubernetes 授权

为了取消阻止其他方案，例如非交互式登录、较旧的 `kubectl` 版本，或跨多个群集利用 SSO 而无需登录到新群集（假定你的令牌仍然有效），AKS 创建了一个名为 [`kubelogin`](https://github.com/Azure/kubelogin) 的 exec 插件。

你可以通过运行以下命令使用它：

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

第一次，你必须像使用普通 kubectl 那样以交互方式登录，但之后，只要你的令牌仍然有效，即使是新的 Azure AD 群集，你也不再需要登录。

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```

## <a name="clean-up"></a>清理

### <a name="clean-role-assignment"></a>清理角色分配

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```

从你执行的所有分配中复制一个或多个 ID，然后执行以下命令。

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>清除角色定义

```azurecli-interactive
az role definition delete -n "AKS Deployment Reader"
```

### <a name="delete-cluster-and-resource-group"></a>删除群集和资源组

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

- [在此处](concepts-identity.md)详细了解 AKS 身份验证、授权、Kubernetes RBAC 和 Azure RBAC。
- [在此处](../role-based-access-control/overview.md)详细了解 Azure RBAC。
- [在此处](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)详细了解可用来精细定义自定义 Azure 角色（用于 Kubernetes 授权）的所有操作。


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-update]: /cli/azure/aks#az_aks_update
[managed-aad]: ./managed-aad.md