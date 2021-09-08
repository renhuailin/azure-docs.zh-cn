---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure AD
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 7a5bea7e555bf4f388a06668b2e349045692a941
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106621"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS 托管的 Azure Active Directory 集成

AKS 托管 Azure AD 集成简化了 Azure AD 集成过程。 用户以前需要创建客户端应用和服务器应用，而且还需要由 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为你管理客户端应用和服务器应用。

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

群集管理员可以根据用户的标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅 [Open ID Connect 文档][open-id-connect]。

若要详细了解 Azure AD 集成流程，请参阅 [Azure Active Directory 集成概念文档](concepts-identity.md#azure-ad-integration)。

## <a name="limitations"></a>限制 

* 无法禁用 AKS 托管的 Azure AD 集成
* 不支持将 AKS 托管的 Azure AD 集成式群集更改为旧版 AAD
* AKS 托管的 Azure AD 集成不支持未启用 Kubernetes RBAC 的群集
* 不支持更改与 AKS 托管的 Azure AD 集成关联的 Azure AD 租户

## <a name="prerequisites"></a>必备条件

* Azure CLI 2.11.0 或更高版本
* 版本不低于 [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) 的 Kubectl，或者 [kubelogin](https://github.com/Azure/kubelogin)
* 如果使用的是 [helm](https://github.com/helm/helm)，则最低版本为 helm 3.3。

> [!Important]
> 你必须使用最低版本为 1.18.1 的 Kubectl，或者使用 kubelogin。 Kubernetes 和 kubectl 的次版本之间的差不应超过 1 个版本。 如果未使用正确的版本，你会遇到身份验证问题。

若要安装 kubectl 和 kubelogin，请使用以下命令：

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

对于其他操作系统，请遵循[这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

## <a name="before-you-begin"></a>开始之前

对于你的群集，你需要一个 Azure AD 组。 此组将在群集上注册为管理员组，以授予群集管理员权限。 你可以使用现有 Azure AD 组，也可以创建一个新组。 记录你的 Azure AD 组的对象 ID。

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

若要为群集管理员创建新的 Azure AD 组，请使用以下命令：

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>创建已启用 Azure AD 的 AKS 群集

使用以下 CLI 命令创建 AKS 群集。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

创建一个 AKS 群集，并为你的 Azure AD 组启用管理访问权限

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功创建 AKS 托管的 Azure AD 群集后，响应正文中会包含以下部分
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

创建群集后，即可开始访问该群集。

## <a name="access-an-azure-ad-enabled-cluster"></a>访问已启用 Azure AD 的群集

使用 Azure AD 定义组访问群集之前，需要 [Azure Kubernetes 服务群集用户](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role)内置角色。

获取用于访问群集的用户凭据：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
按照说明进行登录。

使用 kubectl get nodes 命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
配置 [Azure 基于角色的访问控制 (Azure RBAC)](./azure-ad-rbac.md)，以便为群集配置其他安全组。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>排查 Azure AD 的访问权限问题

> [!Important]
> 下面所述的步骤会绕过正常的 Azure AD 组身份验证。 请仅在紧急情况下使用它们。

如果你被永久阻止，不能访问具有群集访问权限的有效 Azure AD 组，你仍可以获取管理员凭据以直接访问群集。

若要执行这些步骤，你需要有权访问 [Azure Kubernetes 服务群集管理员](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)内置角色。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>在现有群集上启用 AKS 托管的 Azure AD 集成

你可以在现有的启用了 Kubernetes RBAC 的群集上启用 AKS 托管的 Azure AD 集成。 确保将管理员组设置为在群集上保留访问权限。

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

成功激活 AKS 托管的 Azure AD 群集后，响应正文中会包含以下部分

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

按照[此处][access-cluster]的步骤操作，再次下载用户凭据以访问群集。

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>升级到 AKS 托管的 Azure AD 集成

如果你的群集使用旧式 Azure AD 集成，则可以升级到 AKS 托管的 Azure AD 集成。

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功迁移 AKS 托管的 Azure AD 群集后，响应正文中会包含以下部分

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

如果要访问群集，请按照[此处][access-cluster]的步骤进行操作。

## <a name="non-interactive-sign-in-with-kubelogin"></a>通过 kubelogin 进行非交互式登录

有一些当前无法通过 kubectl 执行的非交互式方案，例如持续集成管道。 你可以使用 [`kubelogin`](https://github.com/Azure/kubelogin) 通过非交互式服务主体登录来访问群集。

## <a name="disable-local-accounts-preview"></a>禁用本地帐户（预览版）

在部署 AKS 群集时，本地帐户在默认情况下处于启用状态。 即使是在启用 RBAC 或 Azure Active Directory 集成时，`--admin` 访问权限仍然存在，本质上是作为一个不可审核的后门选项。 考虑到这一点，AKS 为用户提供了通过 `disable-local-accounts` 标志来禁用本地帐户的功能。 另外还为托管群集 API 添加了 `properties.disableLocalAccounts` 字段，以指示该功能是否已在群集上启用。

> [!NOTE]
> 在启用了 Azure AD 集成的群集上，属于 `aad-admin-group-object-ids` 指定的组的用户仍将能够通过非管理员凭据获取访问权限。 在未启用 Azure AD 集成并且 `properties.disableLocalAccounts` 设置为 true 的群集上，获取用户凭据的操作和获取管理员凭据的操作都会失败。

### <a name="register-the-disablelocalaccountspreview-preview-feature"></a>注册 `DisableLocalAccountsPreview` 预览版功能

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

若要使用没有本地帐户的 AKS 群集，则必须在订阅上启用 `DisableLocalAccountsPreview` 功能标志。 请确保使用最新版本的 Azure CLI 和 `aks-preview` 扩展。

使用 [az feature register][az-feature-register] 命令注册 `DisableLocalAccountsPreview` 功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "DisableLocalAccountsPreview"
```

状态显示为“已注册”需要几分钟时间。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/DisableLocalAccountsPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="create-a-new-cluster-without-local-accounts"></a>创建没有本地帐户的新群集

若要新建没有任何本地帐户的 AKS 群集，请使用带有 `disable-local-accounts` 标志的 [az aks create][az-aks-create] 命令：

```azurecli-interactive
az aks create -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

在输出中，检查 `properties.disableLocalAccounts` 字段的设置是否为 true，以确认已禁用了本地帐户：

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

尝试获取管理员凭据的操作会失败，并且会显示错误消息，指示该功能阻止访问：

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="disable-local-accounts-on-an-existing-cluster"></a>在现有群集上禁用本地帐户

若要在现有 AKS 群集上禁用本地帐户，请使用带有 `disable-local-accounts` 标志的 [az aks update][az-aks-update] 命令：

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --disable-local-accounts
```

在输出中，检查 `properties.disableLocalAccounts` 字段的设置是否为 true，以确认已禁用了本地帐户：

```output
"properties": {
    ...
    "disableLocalAccounts": true,
    ...
}
```

尝试获取管理员凭据的操作会失败，并且会显示错误消息，指示该功能阻止访问：

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Operation failed with status: 'Bad Request'. Details: Getting static credential is not allowed because this cluster is set to disable local accounts.
```

### <a name="re-enable-local-accounts-on-an-existing-cluster"></a>在现有群集上重新启用本地帐户

AKS 还提供了使用 `enable-local` 标志在现有群集上重新启用本地帐户的功能：

```azurecli-interactive
az aks update -g <resource-group> -n <cluster-name> --enable-aad --aad-admin-group-object-ids <aad-group-id> --enable-local
```

在输出中，检查 `properties.disableLocalAccounts` 字段的设置是否为 false，以确认已重新启用了本地帐户：

```output
"properties": {
    ...
    "disableLocalAccounts": false,
    ...
}
```

尝试获取管理员凭据的操作会成功：

```azurecli-interactive
az aks get-credentials --resource-group <resource-group> --name <cluster-name> --admin

Merged "<cluster-name>-admin" as current context in C:\Users\<username>\.kube\config
```

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>通过 Azure AD 和 AKS 使用条件访问

将 Azure AD 与 AKS 群集集成后，还可以使用[条件访问][aad-conditional-access]来控制对群集的访问。

> [!NOTE]
> Azure AD 条件访问是一项 Azure AD 高级版功能。

若要创建用于 AKS 的示例条件访问策略，请完成以下步骤：

1. 从 Azure 门户的顶部，搜索并选择“Azure Active Directory”。
1. 在左侧的 Azure Active Directory 菜单中，选择“企业应用程序”。
1. 在左侧的“企业应用程序”菜单中，选择“条件访问”。
1. 在左侧的“条件访问”菜单中，选择“策略”，然后选择“新建策略”。
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="添加条件访问策略":::
1. 为策略输入一个名称，例如“aks-policy”。
1. 选择“用户和组”，然后在“包括”下选择“选择用户和组”。 选择要应用策略的用户和组。 对于此示例，请选择对你的群集具有管理访问权限的同一个 Azure AD 组。
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="选择要应用条件访问策略的用户或组":::
1. 选择“云应用或操作”，然后在“包括”下选择“选择应用”。 搜索“Azure Kubernetes 服务”，然后选择“Azure Kubernetes 服务 AAD 服务器”。
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="选择用于应用条件访问策略的 Azure Kubernetes 服务 AD 服务器":::
1. 在“访问控制”  下，选择“授予”  。 选择“授予访问权限”，然后选择“需要标记为兼容的设备”。
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="选择此设置将仅允许符合条件访问策略的设备":::
1. 在“启用策略”下，选择“开”，然后选择“创建”  。
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="启用条件访问策略":::

获取用于访问群集的用户凭据，例如：

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

按照说明进行登录。

使用 `kubectl get nodes` 命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes
```

再次按照说明进行登录。 请注意，一条错误消息会指明你已成功登录，但若要访问资源，你的管理员要求请求访问的设备受 Azure AD 管理。

在 Azure 门户中，导航到 Azure Active Directory，选择“企业应用程序”，然后在“活动”下选择“登录”。请注意，顶部有一个条目，其中的“状态”为“失败”，“条件访问”为“成功”。    选择该条目，然后在“详细信息”中选择“条件访问”。  请注意，你的条件访问策略已列出。

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="由于条件访问策略而失败的登录条目":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>使用 Azure AD 和 AKS 配置即时群集访问

用于群集访问控制的另一个选项是对即时请求使用 Privileged Identity Management (PIM)。

>[!NOTE]
> PIM 是一个需要 Premium P2 SKU 的 Azure AD Premium 功能。 有关 Azure AD SKU 的详细信息，请参阅[定价指南][aad-pricing]。

若要使用 AKS 托管的 Azure AD 集成将即时访问请求与 AKS 群集集成，请完成以下步骤：

1. 从 Azure 门户的顶部，搜索并选择“Azure Active Directory”。
1. 记下租户 ID（在本文余下的说明中称为 `<tenant-id>`）:::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="在 Web 浏览器中显示 Azure Active Directory 的 Azure 门户屏幕，其中突出显示了租户的 ID。":::
1. 在左侧的 Azure Active Directory 菜单中的“管理”下，依次选择“组”、“新建组”。  
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="显示 Azure 门户中的 Active Directory“组”屏幕，其中突出显示了“新建组”选项。":::
1. 确保已选择“安全性”组类型，输入组的名称，例如 *myJITGroup*。 在“可将 Azure AD 角色分配到此组(预览版)”下，选择“是”。  最后，选择“创建”。
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="显示 Azure 门户中创建新组的屏幕。":::
1. 你将返回到“组”页。 选择新建的组，并记下“对象 ID”（在本文余下的说明中称为 `<object-id>`）。
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="显示 Azure 门户中刚刚创建的组的屏幕，其中突出显示了“对象 ID”":::
1. 使用前面记下的 `<tenant-id>` 和 `<object-id>` 值通过 AKS 托管的 Azure AD 集成部署 AKS 群集：
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. 返回 Azure 门户，在左侧的“活动”菜单中，依次选择“特权访问(预览版)”、“启用特权访问”。  
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="显示 Azure 门户中的“特权访问（预览版）”页，其中突出显示了“启用特权访问”":::
1. 选择“添加分配”开始授予访问权限。
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="显示启用后 Azure 门户的特权访问（预览）屏幕。突出显示了“添加分配”选项。":::
1. 选择成员的角色，然后选择要向其授予群集访问权限的用户和组。 组管理员可随时修改这些分配。准备好继续时，选择“下一步”。
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="显示 Azure 门户的“添加分配成员身份”屏幕，其中选择了一个要添加为成员的示例用户。突出显示了“下一步”选项。":::
1. 选择“活动”分配类型和所需的持续时间，并提供理由。 准备好继续操作时，选择“分配”。 有关分配类型的详细信息，请参阅[在 Privileged Identity Management 中为特权访问组分配资格（预览版）][aad-assignments]。
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="显示 Azure 门户的“添加分配设置”屏幕。其中选择了“活动”分配类型，并提供了示例理由。突出显示了“分配”选项。":::

完成分配后，通过访问群集来验证是否可以正常进行即时访问。 例如：

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

按照以下步骤登录。

使用 `kubectl get nodes` 命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes
```

请注意身份验证要求，并按照步骤完成身份验证。 如果成功，应会看到类似于下面的输出：

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>疑难解答

如果 `kubectl get nodes` 返回类似于下面的错误：

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

请确保安全组的管理员为你的帐户提供了“活动”分配。

## <a name="next-steps"></a>后续步骤

* 了解[用于 Kubernetes 授权的 Azure RBAC 集成][azure-rbac-integration]
* 了解 [Azure AD 与 Kubernetes RBAC 的集成][azure-ad-rbac]。
* 使用 [kubelogin](https://github.com/Azure/kubelogin) 来访问 kubectl 中未提供的 Azure 身份验证功能。
* 详细了解 [AKS 和 Kubernetes 标识概念][aks-concepts-identity]。
* 使用 [Azure 资源管理器 (ARM) 模板][aks-arm-template]创建已启用 AKS 托管 Azure AD 的群集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-update]: /cli/azure/aks#az_aks_update
