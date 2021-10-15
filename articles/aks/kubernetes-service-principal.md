---
title: 适用于 Azure Kubernetes 服务 (AKS) 的服务主体
description: 在 Azure Kubernetes 服务 (AKS) 中为群集创建和管理 Azure Active Directory 服务主体
services: container-service
ms.topic: conceptual
ms.date: 04/22/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c2ad9c6eae22db2d940942c0ec15c6b1116a54cd
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353011"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS) 的服务主体

AKS 群集需要 [Azure Active Directory (AD) 服务主体][aad-service-principal]或[托管标识](use-managed-identity.md)才能与 Azure API 交互。 需要服务主体或托管标识才能动态创建和管理其他 Azure 资源，例如 Azure 负载均衡器或容器注册表 (ACR)。

本文介绍如何创建和使用适用于 AKS 群集的服务主体。

## <a name="before-you-begin"></a>准备阶段

若要创建 Azure AD 服务主体，必须具有相应的权限，能够向 Azure AD 租户注册应用程序，并将应用程序分配到订阅中的角色。 如果没有必需的权限，可能需要请求 Azure AD 或订阅管理员来分配必需的权限，或者预先创建一个可以与 AKS 群集配合使用的服务主体。

如果使用来自另一 Azure AD 租户的服务主体，则还需围绕部署群集时可用的权限进行更多的考虑。 你可能没有读取和写入目录信息的适当权限。 有关详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？][azure-ad-permissions]

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

还需要安装 Azure PowerShell 5.0.0 或更高版本。 运行 `Get-InstalledModule -Name Az` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure Az PowerShell 模块][install-the-azure-az-powershell-module]。

---

## <a name="automatically-create-and-use-a-service-principal"></a>自动创建和使用服务主体

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

当你通过 Azure 门户或 [az aks create][az-aks-create] 命令创建 AKS 群集时，Azure 会创建托管标识。

在下述 Azure CLI 示例中，尚未指定服务主体。 在此方案中，Azure CLI 为 AKS 群集创建一个托管标识。 

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

通过 Azure 门户或 [New-AzAksCluster][new-azakscluster] 命令创建 AKS 群集时，Azure 可以生成新的托管标识。

下方 Azure PowerShell 示例尚未指定服务主体。 在此方案中，Azure PowerShell 为 AKS 群集创建一个托管标识。 

```azurepowershell-interactive
New-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

> [!NOTE]
> 有关错误“服务主体 clientID：00000000-0000-0000-0000-000000000000 在 Active Directory 租户 00000000-0000-0000-0000-000000000000 中未找到”的详细信息，[请参阅其他注意事项](#additional-considerations)以删除 `acsServicePrincipal.json` 文件。

---
## <a name="manually-create-a-service-principal"></a>手动创建服务主体

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过 Azure CLI 手动创建服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令。 在以下示例中，`--skip-assignment` 参数阻止系统分配更多的默认分配。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

输出类似于以下示例。 记下你自己的 `appId`和 `password`。 在下一部分创建 AKS 群集时，会使用这些值。

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

可以在 Azure PowerShell 中使用 [New-AzADServicePrincipal][new-azadserviceprincipal] 命令创建服务主体。 在以下示例中，`-SkipAssignment` 参数阻止系统分配更多的默认分配。

```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName myAKSClusterServicePrincipal -SkipAssignment -OutVariable sp
```

输出类似于以下示例。 值还会存储在下一节创建 AKS 群集时使用的变量中。

```Output
Secret                : System.Security.SecureString
ServicePrincipalNames : {559513bd-0c19-4c1a-87cd-851a26afd5fc, http://myAKSClusterServicePrincipal}
ApplicationId         : 559513bd-0c19-4c1a-87cd-851a26afd5fc
ObjectType            : ServicePrincipal
DisplayName           : myAKSClusterServicePrincipal
Id                    : 559513bd-0c19-4c1a-87cd-851a26afd5fc
Type                  :
```

若要解密存储在机密安全字符串中的值，请使用下方示例。

```azurepowershell-interactive
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
[System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

有关详细信息，请参阅[使用 Azure PowerShell 创建 Azure 服务主体][create-an-azure-service-principal-with-azure-powershell]

---

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>指定适用于 AKS 群集的服务主体

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要在通过 [az aks create][az-aks-create] 命令创建 AKS 群集时使用现有的服务主体，请使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令的输出中的 `--service-principal` 和 `--client-secret` 参数来指定 `appId` 和 `password`：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> 如果使用的是具有自定义机密的现有服务主体，请确保该机密不超过 190 字节。

如果使用 Azure 门户来部署 AKS 群集，请在“创建 Kubernetes 群集”对话框的“身份验证”页上选择“配置服务主体”。  选择“使用现有”并指定以下值：

- **服务主体客户端 ID** 是你的 *appId*
- **服务主体客户端机密** 是密码值

![浏览到 Azure Vote 的图像](media/kubernetes-service-principal/portal-configure-service-principal.png)

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要在创建 AKS 群集时使用现有的服务主体，需要将服务主体 `ApplicationId` 和 `Secret` 对象转换为 PSCredential 对象，如下方示例中所示。

```azurepowershell-interactive
$Cred = New-Object -TypeName System.Management.Automation.PSCredential ($sp.ApplicationId, $sp.Secret)
```

运行 `New-AzAksCluster` 命令时，请使用之前创建的 PSCredential 对象作为其值来指定 `ServicePrincipalIdAndSecret` 参数。

```azurepowershell-interactive
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -ServicePrincipalIdAndSecret $Cred
```

> [!NOTE]
> 如果使用的是具有自定义机密的现有服务主体，请确保该机密不超过 190 字节。

如果使用 Azure 门户来部署 AKS 群集，请在“创建 Kubernetes 群集”对话框的“身份验证”页上选择“配置服务主体”。  选择“使用现有”并指定以下值：

- 服务主体客户端 ID 是你的 ApplicationId
- 服务主体客户端机密是密码值

![浏览到 Azure Vote 的图像](media/kubernetes-service-principal/portal-configure-service-principal.png)

---

## <a name="delegate-access-to-other-azure-resources"></a>委托对其他 Azure 资源的访问权限

AKS 群集的服务主体可以用来访问其他资源。 例如，如果希望将 AKS 群集部署到现有 Azure 虚拟网络子网或连接到 Azure 容器注册表 (ACR)，则需要将对那些资源的访问权限委托给服务主体。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要委托权限，请使用 [az role assignment create][az-role-assignment-create] 命令创建一个角色分配。 将 `appId` 分配到特定的作用域，例如一个资源组或虚拟网络资源。 然后，通过角色定义服务主体对资源的具体权限，如以下示例所示：

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

资源的 `--scope` 需要是完整的资源 ID，例如 /subscriptions/\<guid\>/resourceGroups/myResourceGroup 或 /subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要委托权限，请使用[New-AzRoleAssignment][new-azroleassignment]命令创建角色分配。 将 `ApplicationId` 分配到特定的作用域，例如一个资源组或虚拟网络资源。 然后，通过角色定义服务主体对资源的具体权限，如以下示例所示：

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId <ApplicationId> -Scope <resourceScope> -RoleDefinitionName Contributor
```

资源的 `Scope` 需要是完整的资源 ID，例如 /subscriptions/\<guid\>/resourceGroups/myResourceGroup 或 /subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet

---

> [!NOTE]
> 如果从节点资源组删除了参与者角色分配，则以下操作可能会失败。
> 使用系统托管标识向群集授予的权限可能需要 60 分钟才能填充完毕。

以下各部分详述了可能需要使用的常见委托。

### <a name="azure-container-registry"></a>Azure 容器注册表

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果使用 Azure 容器注册表 (ACR) 作为容器映像存储，则需要向 AKS 群集的服务主体授予读取和拉取映像的权限。 目前，建议的配置是使用 [az aks create][az-aks-create] 或 [az aks update][az-aks-update] 命令与注册表集成，并为服务主体分配适当的角色。 有关详细步骤，请参阅[通过 Azure Kubernetes 服务向 Azure 容器注册表进行身份验证][aks-to-acr]。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

如果使用 Azure 容器注册表 (ACR) 作为容器映像存储，则需要向 AKS 群集的服务主体授予读取和拉取映像的权限。 目前，建议配置为使用[New-AzAksCluster][new-azakscluster]或[Set-AzAksCluster][set-azakscluster]命令与注册表集成，并为服务主体分配适当角色。 有关详细步骤，请参阅[通过 Azure Kubernetes 服务向 Azure 容器注册表进行身份验证][aks-to-acr]。

---

### <a name="networking"></a>网络

可以使用高级网络，在该网络中，虚拟网络和子网或公共 IP 地址位于另一资源组中。 在虚拟网络的子网上分配[网络参与者][rbac-network-contributor]内置角色。 或者，可以创建有权访问该资源组中网络资源的[自定义角色][rbac-custom-role]。 有关更多详细信息，请参阅 [AKS 服务权限][aks-permissions]。

### <a name="storage"></a>存储

可能需要访问另一资源组中的现有磁盘资源。 分配下列角色权限集之一：

- 创建一个[自定义角色][rbac-custom-role]，并定义以下角色权限：
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- 或者，在资源组中分配[存储帐户参与者][rbac-storage-contributor]内置角色

### <a name="azure-container-instances"></a>Azure 容器实例

如果使用虚拟 Kubelet 与 AKS 集成并选择在与 AKS 群集分开的资源组中运行 Azure 容器实例 (ACI)，则必须在 ACI 资源组上授予 AKS 服务主体“参与者”权限。

## <a name="additional-considerations"></a>其他注意事项

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 AKS 和 Azure AD 服务主体时，请牢记以下注意事项。

- Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。
- 默认情况下，服务主体凭据的有效期为一年。 可以随时[更新或轮换服务主体凭据][update-credentials]。
- 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 *https://www.contoso.org/example* ）相关联。 应用程序的 URL 不一定是实际的终结点。
- 指定服务主体 **客户端 ID** 时，请使用 `appId` 的值。
- 在 Kubernetes 群集的代理节点 VM 中，服务主体凭据存储在 `/etc/kubernetes/azure.json` 文件中
- 使用 [az aks create][az-aks-create] 命令自动生成服务主体时，会将服务主体凭据写入用于运行命令的计算机上的 `~/.azure/aksServicePrincipal.json` 文件中。
- 如果没有在其他 AKS CLI 命令中明确传递服务主体，则将使用位于 `~/.azure/aksServicePrincipal.json` 的默认服务主体。
- 也可以选择删除 aksServicePrincipal.json 文件，AKS 将创建新的服务主体。
- 删除通过 [az aks create][az-aks-create] 创建的 AKS 群集时，不会删除自动创建的服务主体。
    - 若要删除服务主体，请查询群集 servicePrincipalProfile.clientId，然后使用 [az ad app delete][az-ad-sp-delete] 进行删除。 将以下资源组和群集名称替换为你自己的值：

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

使用 AKS 和 Azure AD 服务主体时，请牢记以下注意事项。

- Kubernetes 的服务主体是群集配置的一部分。 但是，请勿使用标识来部署群集。
- 默认情况下，服务主体凭据的有效期为一年。 可以随时[更新或轮换服务主体凭据][update-credentials]。
- 每个服务主体都与一个 Azure AD 应用程序相关联。 Kubernetes 群集的服务主体可以与任何有效的 Azure AD 应用程序名称（例如 *https://www.contoso.org/example* ）相关联。 应用程序的 URL 不一定是实际的终结点。
- 指定服务主体 **客户端 ID** 时，请使用 `ApplicationId` 的值。
- 在 Kubernetes 群集的代理节点 VM 中，服务主体凭据存储在 `/etc/kubernetes/azure.json` 文件中
- 使用[New-AzAksCluster][new-azakscluster]命令自动生成服务主体时，系统会将服务主体凭据写入用于运行该命令的计算机上的 `~/.azure/acsServicePrincipal.json` 文件中。
- 如果未在其他 AKS PowerShell 命令中专门传递服务主体，则使用位于 `~/.azure/acsServicePrincipal.json` 的默认服务主体。
- 还可以选择删除 acsServicePrincipal.json 文件，AKS 将创建新的服务主体。
- 删除通过[New-AzAksCluster][new-azakscluster]创建的 AKS 群集时，不会删除自动创建的服务主体。
    - 若要删除服务主体，请查询群集 ServicePrincipalProfile.ClientId，然后使用 [Remove-AzADServicePrincipal][remove-azadserviceprincipal] 进行删除。 将以下资源组和群集名称替换为你自己的值：

        ```azurepowershell-interactive
        $ClientId = (Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster ).ServicePrincipalProfile.ClientId
        Remove-AzADServicePrincipal -ApplicationId $ClientId
        ```
---

## <a name="troubleshoot"></a>故障排除

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

AKS 群集的服务主体凭据由 Azure CLI 缓存。 如果这些凭据已过期，则会在部署 AKS 群集时遇到错误。 运行 [az aks create][az-aks-create] 时，如果出现以下错误消息，则可能表示缓存的服务主体凭据出现问题：

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

请使用以下命令检查凭据文件的存在时间：

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

服务主体凭据的默认过期时间为一年后。 如果 *aksServicePrincipal.json* 文件的存在时间已超出一年，请删除该文件，然后尝试再次部署 AKS 群集。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

AKS 群集的服务主体凭据由 Azure PowerShell 缓存。 如果这些凭据已过期，则会在部署 AKS 群集时遇到错误。 如果在运行[New-AzAksCluster][new-azakscluster]时出现以下错误消息，则可能表示缓存的服务主体凭据出现问题：

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

请使用以下命令检查凭据文件的存在时间：

```azurepowershell-interactive
Get-ChildItem -Path $HOME/.azure/aksServicePrincipal.json
```

服务主体凭据的默认过期时间为一年后。 如果 *aksServicePrincipal.json* 文件的存在时间已超出一年，请删除该文件，然后尝试再次部署 AKS 群集。

---

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Active Directory 服务主体，请参阅[应用程序和服务主体对象][service-principal]。

有关如何更新凭据的信息，请参阅[为 AKS 中的服务主体更新或轮换凭据][update-credentials]。

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az_ad_app_list
[az-ad-app-delete]: /cli/azure/ad/app#az_ad_app_delete
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
[install-the-azure-az-powershell-module]: /powershell/azure/install-az-ps
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[new-azadserviceprincipal]: /powershell/module/az.resources/new-azadserviceprincipal
[create-an-azure-service-principal-with-azure-powershell]: /powershell/azure/create-azure-service-principal-azureps
[new-azroleassignment]: /powershell/module/az.resources/new-azroleassignment
[set-azakscluster]: /powershell/module/az.aks/set-azakscluster
[remove-azadserviceprincipal]: /powershell/module/az.resources/remove-azadserviceprincipal
