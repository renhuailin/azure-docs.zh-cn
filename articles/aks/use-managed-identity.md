---
title: 在 Azure Kubernetes 服务中使用托管标识
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用托管标识
services: container-service
ms.topic: article
ms.date: 12/06/2020
ms.openlocfilehash: e2a80ea869e17665e8a6d4fbd6960c3ccc8c1042
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751268"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes 服务中使用托管标识

目前，Azure Kubernetes 服务 (AKS) 群集（特指 Kubernetes 云提供商）需要使用标识才能在 Azure 中创建其他资源，例如负载均衡器和托管磁盘。 此标识可以是托管标识或服务主体。 如果使用[服务主体](kubernetes-service-principal.md)，你必须提供一个服务主体，或由 AKS 代表你创建一个。 如果使用托管标识，AKS 会自动为你创建托管标识。 使用服务主体的群集最终会达到这样一种状态，即，必须续订服务主体才能让群集保持正常运行。 管理服务主体会增加复杂性，这也是托管标识使用起来更简单的原因。 服务主体和托管标识适用相同的权限要求。

托管标识本质上是服务主体的包装器，这使其更易于管理。 根据 Azure Active Directory 的默认设置，MI 的凭据轮换每 46 天自动发生一次。 AKS 使用系统分配和用户分配的托管标识类型。 这些标识目前是不可变的。 若要了解详细信息，请阅读 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="before-you-begin"></a>准备阶段

必须安装了以下资源：

- Azure CLI 2.15.1 或更高版本

## <a name="limitations"></a>限制

* 在群集升级操作期间，托管标识暂时不可用。
* 不支持启用了托管标识的群集的租户移动/迁移。
* 如果已启用群集 `aad-pod-identity` ，Node-Managed 标识 (NMI) pod 会修改节点的 iptables，以截获对 Azure 实例元数据终结点的调用。 此配置意味着对元数据终结点发出的任何请求都将被 NMI 拦截，即使 pod 不使用 `aad-pod-identity`。 可以将 AzurePodIdentityException CRD 配置为通知 `aad-pod-identity` 应在不使用 NMI 进行出任何处理的情况下，代理与 CRD 中定义的标签匹配的 pod 所发起的对元数据终结点的任何请求。 应通过配置 AzurePodIdentityException CRD 在 `aad-pod-identity` 中排除在 _kube-system_ 命名空间中具有 `kubernetes.azure.com/managedby: aks` 标签的系统 pod。 有关详细信息，请参阅[禁用特定 pod 或应用程序的 aad-pod-identity](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)。
  若要配置例外情况，请安装 [mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)。

## <a name="summary-of-managed-identities"></a>托管标识摘要

AKS 对内置服务和加载项使用多个托管标识。

| 标识                       | 名称    | 使用案例 | 默认权限 | 自带标识
|----------------------------|-----------|----------|
| 控制面板 | 不可见 | 由 AKS 控制平面组件用于管理群集资源，包括入口负载均衡器和 AKS 托管的公共 Ip，以及群集自动缩放程序操作 | 节点资源组的参与者角色 | 预览
| Kubelet | AKS Cluster Name-agentpool | 向 Azure 容器注册表 (ACR) 进行身份验证 | NA（对于 kubernetes v1.15+） | 目前不支持
| 加载项 | AzureNPM | 无需标识 | 不可用 | 否
| 加载项 | AzureCNI 网络监视 | 无需标识 | 不可用 | 否
| 加载项 | azure-策略 (看门程序)  | 无需标识 | 不可用 | 否
| 加载项 | azure-策略 | 无需标识 | 不可用 | 否
| 加载项 | Calico | 无需标识 | 不可用 | 否
| 加载项 | 仪表板 | 无需标识 | 不可用 | 否
| 加载项 | HTTPApplicationRouting | 管理所需的网络资源 | 节点资源组的读取者角色，DNS 区域的参与者角色 | 否
| 加载项 | 入口应用程序网关 | 管理所需的网络资源| 节点资源组的参与者角色 | 否
| 加载项 | omsagent | 用于将 AKS 指标发送到 Azure Monitor | “监视指标发布者”角色 | 否
| 加载项 | Virtual-Node (ACIConnector) | 管理 Azure 容器实例 (ACI) 所需的网络资源 | 节点资源组的参与者角色 | 否
| OSS 项目 | aad-pod-identity | 通过 Azure Active Directory (AAD) 使应用程序可安全访问云资源 | NA | 要授予权限的步骤 https://github.com/Azure/aad-pod-identity#role-assignment 。

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

使用托管标识成功创建群集的命令中包含以下服务主体配置文件信息：

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

使用以下命令查询控制平面托管标识的 objectid：

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

结果应如下所示：

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

创建群集后，你便可以将应用程序工作负荷部署到新群集中，并与之交互，就像与基于服务主体的 AKS 群集交互一样。

> [!NOTE]
> 若要创建并使用自己的 VNet、静态 IP 地址或附加的 Azure 磁盘（资源位于工作器节点资源组外部），请使用群集系统分配的托管标识的 PrincipalID 来执行角色分配。 有关角色分配的详细信息，请参阅[委托对其他 Azure 资源的访问权限](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。
>
> 向 Azure 云提供商使用的群集托管标识授予的权限可能需要 60 分钟才能填充完毕。

最后，获取用于访问群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a> (预览将 AKS 群集更新为托管标识) 

你现在可以通过使用以下 CLI 命令，更新当前使用服务主体的 AKS 群集以使用托管标识。

首先，为系统分配的标识注册功能标志：

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

更新系统分配的标识：

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

更新用户分配的标识：

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

更新用户分配的标识：

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> 系统分配的标识或用户分配的标识更新为托管标识后，请 `az nodepool upgrade --node-image-only` 在节点上执行，以完成对托管标识的更新。

## <a name="bring-your-own-control-plane-mi"></a>自带控制平面 MI
使用自定义控制平面标识，可以在创建群集之前将访问权限授予现有标识。 此功能启用了方案，例如，将自定义 VNET 或 outboundType UDR 与预先创建的托管标识结合使用。

必须安装 Azure CLI 版本2.15.1 或更高版本。

### <a name="limitations"></a>限制
* 当前不支持 Azure 政府版。
* 当前不支持 Azure 中国世纪互联。

如果还没有托管标识，应继续使用 [az IDENTITY CLI][az-identity-create]创建一个示例。

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

如果你的托管标识是你的订阅的一部分，你可以使用 [az IDENTITY CLI 命令][az-identity-list] 对其进行查询。  

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
    --assign-identity <identity-id> \
```

使用自己的托管标识成功创建群集包含此 userAssignedIdentities 配置文件信息：

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

## <a name="next-steps"></a>后续步骤
* 使用 [Azure 资源管理器 (ARM) 模板 ][aks-arm-template] 来创建启用了托管身份的群集。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
