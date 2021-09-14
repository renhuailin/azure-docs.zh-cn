---
title: 在 Azure Kubernetes 服务中使用托管标识
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用托管标识
services: container-service
ms.topic: article
ms.date: 05/12/2021
ms.openlocfilehash: d3d479730b88c80c627c3e6dad2ab8f80eb3aee6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431723"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes 服务中使用托管标识

目前，Azure Kubernetes 服务 (AKS) 群集（特指 Kubernetes 云提供商）需要使用标识才能在 Azure 中创建其他资源，例如负载均衡器和托管磁盘。 此标识可以是托管标识或服务主体。 如果使用[服务主体](kubernetes-service-principal.md)，你必须提供一个服务主体，或由 AKS 代表你创建一个。 如果使用托管标识，AKS 会自动为你创建托管标识。 使用服务主体的群集最终会达到这样一种状态，即，必须续订服务主体才能让群集保持正常运行。 管理服务主体会增加复杂性，这也是托管标识使用起来更简单的原因。 服务主体和托管标识适用相同的权限要求。

托管标识本质上是服务主体的包装器，这使其更易于管理。 根据 Azure Active Directory 的默认设置，MI 的凭据轮换每 46 天自动发生一次。 AKS 使用系统分配和用户分配的托管标识类型。 这些标识目前是不可变的。 若要了解详细信息，请阅读 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="before-you-begin"></a>准备阶段

必须安装了以下资源：

- Azure CLI 2.23.0 或更高版本

## <a name="limitations"></a>限制

* 不支持启用了托管标识的群集的租户移动/迁移。
* 如果群集启用了 `aad-pod-identity`，节点托管标识 (NMI) pod 将修改节点的 iptable，以拦截对 Azure 实例元数据终结点的调用。 此配置意味着对元数据终结点发出的任何请求都将被 NMI 拦截，即使 pod 不使用 `aad-pod-identity`。 可以将 AzurePodIdentityException CRD 配置为通知 `aad-pod-identity` 应在不使用 NMI 进行出任何处理的情况下，代理与 CRD 中定义的标签匹配的 pod 所发起的对元数据终结点的任何请求。 应通过配置 AzurePodIdentityException CRD 在 `aad-pod-identity` 中排除在 _kube-system_ 命名空间中具有 `kubernetes.azure.com/managedby: aks` 标签的系统 pod。 有关详细信息，请参阅[禁用特定 pod 或应用程序的 aad-pod-identity](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)。
  若要配置例外情况，请安装 [mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)。

## <a name="summary-of-managed-identities"></a>托管标识摘要

AKS 对内置服务和加载项使用多个托管标识。

| 标识                       | 名称    | 使用案例 | 默认权限 | 自带标识
|----------------------------|-----------|----------|
| 控制面板 | AKS 群集名称 | 由 AKS 控制平面组件用于管理群集资源，包括入口负载均衡器和 AKS 管理的公共 IP、群集自动缩放程序、Azure 磁盘和文件 CSI 驱动程序 | 节点资源组的参与者角色 | 支持
| Kubelet | AKS Cluster Name-agentpool | 向 Azure 容器注册表 (ACR) 进行身份验证 | NA（对于 kubernetes v1.15+） | 支持
| 加载项 | AzureNPM | 无需标识 | 不可用 | 否
| 加载项 | AzureCNI 网络监视 | 无需标识 | 不可用 | 否
| 加载项 | azure-policy (gatekeeper) | 无需标识 | 不可用 | 否
| 加载项 | azure-policy | 无需标识 | 不可用 | 否
| 加载项 | Calico | 无需标识 | 不可用 | 否
| 加载项 | 仪表板 | 无需标识 | 不可用 | 否
| 加载项 | HTTPApplicationRouting | 管理所需的网络资源 | 节点资源组的读取者角色，DNS 区域的参与者角色 | 否
| 加载项 | 入口应用程序网关 | 管理所需的网络资源| 节点资源组的参与者角色 | 否
| 加载项 | omsagent | 用于将 AKS 指标发送到 Azure Monitor | “监视指标发布者”角色 | 否
| 加载项 | Virtual-Node (ACIConnector) | 管理 Azure 容器实例 (ACI) 所需的网络资源 | 节点资源组的参与者角色 | 否
| OSS 项目 | aad-pod-identity | 通过 Azure Active Directory (AAD) 使应用程序可安全访问云资源 | NA | https://github.com/Azure/aad-pod-identity#role-assignment 处提供了授予权限的步骤。

## <a name="create-an-aks-cluster-with-managed-identities"></a>创建具有托管标识的 AKS 群集

现在，可以使用以下 CLI 命令创建具有托管标识的 AKS 群集。

首先，创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

创建群集后，你便可以将应用程序工作负荷部署到新群集中，并与之交互，就像与基于服务主体的 AKS 群集交互一样。

最后，获取用于访问群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities"></a>将 ASK 群集更新至托管标识

现在可以使用以下 CLI 命令更新当前使用服务主体的 AKS 集群以使用托管标识。

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```
> [!NOTE]
> 更新后，群集的控制平面和加载项 Pod 会切换为使用托管标识，但 kubelet 会保持使用服务主体，直到升级代理池。 请在节点上执行 `az aks nodepool upgrade --node-image-only` 以完成对托管标识的更新。 


> 如果群集使用 --attach-acr 从 ACR 拉取映像，那么，在你将群集更新为托管标识后，需要重新运行“az aks update --attach-acr <ACR Resource ID>”，使新创建的用于托管标识的 kubelet 获得从 ACR 进行拉取的权限。 否则，升级后将无法从 ACR 拉取。


## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>获取系统分配的托管标识并将其用于 AKS 群集

使用以下 CLI 命令确认 AKS 群集是否正在使用托管标识：

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

如果群集正在使用托管标识，你将看到 `clientId` 值为“msi”。 改用服务主体的群集将改为显示对象 ID。 例如： 

```output
{
  "clientId": "msi"
}
```

验证群集是否正在使用托管标识后，可以使用以下命令来查找控制平面系统分配的标识的对象 ID：

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> 若要创建并使用自己的 VNet、静态 IP 地址或附加的 Azure 磁盘（资源位于工作器节点资源组外部），请使用群集系统分配的托管标识的 PrincipalID 来执行角色分配。 有关角色分配的详细信息，请参阅[委托对其他 Azure 资源的访问权限](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。
>
> 向 Azure 云提供商使用的群集托管标识授予的权限可能需要 60 分钟才能填充完毕。


## <a name="bring-your-own-control-plane-mi"></a>自带控制平面 MI
凭借自定义控制平面标识，即可在创建群集之前将访问权限授予现有标识。 此功能适用于多种场景，例如将自定义 VNET 或 outboundType UDR 与预先创建的托管标识结合使用。

必须安装 Azure CLI 2.15.1 或更高版本。

### <a name="limitations"></a>限制
* 目前不支持 Azure 政府中的 USDOD 中部、USDOD 东部、USGov 爱荷华州。

如果还没有托管标识，应创建一个，例如使用 [az IDENTITY CLI][az-identity-create] 来创建。

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
结果应如下所示：

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

如果托管标识属于订阅，可以使用 [az IDENTITY CLI 命令][az-identity-list]对其进行查询。  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

现在，可以使用以下命令创建具有现有标识的群集：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id>
```

若成功使用自己的托管标识创建了群集，则其中包含 userAssignedIdentities 配置文件信息：

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="bring-your-own-kubelet-mi"></a>自带 kubelet MI

凭借 Kubelet 标识，即可在创建群集之前将访问权限授予现有标识。 此功能可以实现使用预先创建的托管标识连接到 ACR 等方案。

### <a name="prerequisites"></a>先决条件

- 必须安装 Azure CLI 2.26.0 或更高版本。

### <a name="limitations"></a>限制

- 仅适用于用户分配的托管群集。
- 目前不支持 Azure 中国世纪互联中的中国东部、中国北部。

### <a name="create-or-obtain-managed-identities"></a>创建或获取托管标识

如果还没有控制平面托管标识，应先创建一个。 下方示例使用 [az identity create][az-identity-create] 命令创建：

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```

结果应如下所示：

```output
{                                  
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

如果还没有 kubelet 托管标识，应先创建一个。 下方示例使用 [az identity create][az-identity-create] 命令创建：

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

结果应如下所示：

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

如果现有托管标识属于订阅，可以使用 [az identity list][az-identity-list] 命令对其进行查询：

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>使用 kubelet 标识创建群集

现在，可以使用以下命令创建具有现有标识的群集。 通过 `assign-identity` 提供控制平面标识，通过 `assign-kublet-identity` 提供 kubelet 托管标识：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
    --assign-kubelet-identity <kubelet-identity-id>
```

使用自己的 kubelet 托管标识成功创建的群集应包含以下输出：

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>后续步骤
* 使用 [Azure 资源管理器模板][aks-arm-template]创建已启用托管标识的群集。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
