---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure AD
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 78eed4086c04ceca677a96f03875481e56206e0c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723964"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS 托管的 Azure Active Directory 集成

AKS 托管的 Azure AD 集成设计用来简化 Azure AD 集成体验，用户以前需要创建客户端应用和服务器应用，而且还需要由 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为你管理客户端应用和服务器应用。

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

群集管理员可以根据用户的标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (Kubernetes RBAC)。 使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅 [Open ID Connect 文档][open-id-connect]。

若要详细了解 Azure AD 集成流程，请参阅 [Azure Active Directory 集成概念文档](concepts-identity.md#azure-active-directory-integration)。

## <a name="limitations"></a>限制 

* 无法禁用 AKS 托管的 Azure AD 集成
* AKS 托管的 Azure AD 集成不支持未启用 Kubernetes RBAC 的群集
* 不支持更改与 AKS 托管的 Azure AD 集成关联的 Azure AD 租户

## <a name="prerequisites"></a>必备条件

* Azure CLI 2.11.0 或更高版本
* 版本不低于 [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) 的 Kubectl，或者 [kubelogin](https://github.com/Azure/kubelogin)
* 如果使用的是 [helm](https://github.com/helm/helm)，则最低版本为 helm 3.3。

> [!Important]
> 你必须使用最低版本为 1.18.1 的 Kubectl，或者使用 kubelogin。 如果未使用正确的版本，你会遇到身份验证问题。

若要安装 kubectl 和 kubelogin，请使用以下命令：

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

对于其他操作系统，请遵循[这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

## <a name="before-you-begin"></a>开始之前

对于你的群集，你需要一个 Azure AD 组。 此组需要用作群集的管理员组，以便授予群集管理员权限。 你可以使用现有 Azure AD 组，也可以创建一个新组。 记录你的 Azure AD 组的对象 ID。

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

你需要具有 [Azure Kubernetes 服务群集用户](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role)内置角色才能执行以下步骤。

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

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>使用 Azure AD 和 AKS 的条件性访问

将 Azure AD 与 AKS 群集集成时，还可以使用 [条件性访问][aad-conditional-access] 来控制对群集的访问权限。

> [!NOTE]
> Azure AD 条件访问是 Azure AD Premium 功能。

若要创建用于 AKS 的示例条件访问策略，请完成以下步骤：

1. 在 Azure 门户顶部，搜索并选择 "Azure Active Directory"。
1. 在左侧 Azure Active Directory 的菜单中，选择 " *企业应用程序*"。
1. 在左侧的 "企业应用程序" 菜单中，选择 " *条件访问*"。
1. 在左侧的条件性访问菜单中，选择 " *策略* "，然后选择 " *新建策略*"。
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="添加条件访问策略":::
1. 输入策略的名称，例如 *aks*。
1. 依次选择 " *用户和组*"、" *包括* 选择 *用户和组*"。 选择要应用策略的用户和组。 对于此示例，请选择具有群集的管理访问权限的同一个 Azure AD 组。
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="选择要应用条件访问策略的用户或组":::
1. 依次选择 " *云应用" 或 "操作*"、" *包括* 选择 *应用*"。 搜索 " *Azure Kubernetes 服务* "，并选择 " *AZURE Kubernetes 服务 AAD 服务器*"。
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="选择用于应用条件性访问策略的 Azure Kubernetes Service AD 服务器":::
1. 在“访问控制”  下，选择“授予”  。 选择 " *授予访问权限* " *，然后将 "要求设备标记为符合*"。
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="选择此设置以仅允许符合条件的设备的条件访问策略":::
1. 在 " *启用策略*" 下，依次选择 *"打开" 和 "* *创建*"。
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="启用条件性访问策略":::

获取用户凭据以访问群集，例如：

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

按照说明进行登录。

使用 `kubectl get nodes` 命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes
```

请按照说明进行操作以重新登录。 请注意，会出现一条错误消息，指出已成功登录，但管理员要求设备请求访问 Azure AD 权限才能访问资源。

在 Azure 门户中，导航到 Azure Active Directory，选择 "*活动*" 下的 "*企业应用程序*"，选择 "*登录*"。请注意顶部有一个 *状态* 为 "*失败*" 的项，以及 "*成功*" 的 *条件性访问*。 选择该条目，然后选择 "*详细信息*" 中的 "*条件访问*"。 请注意，你的条件访问策略已列出。

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="由于条件访问策略，登录条目失败":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>使用 Azure AD 和 AKS 配置实时群集访问

群集访问控制的另一个选项是将 Privileged Identity Management (PIM) 用于实时请求。

>[!NOTE]
> PIM 是需要高级 P2 SKU 的 Azure AD Premium 功能。 有关 Azure AD Sku 的详细信息，请参阅 [定价指南][aad-pricing]。

若要使用 AKS 托管 Azure AD 集成将实时访问请求与 AKS 群集集成，请完成以下步骤：

1. 在 Azure 门户顶部，搜索并选择 "Azure Active Directory"。
1. 记下租户 ID （ `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="在 web 浏览器中称为的其他说明），将显示 Azure Active Directory 的 Azure 门户屏幕，其中突出显示了租户的 ID。":::
1. 在左侧 Azure Active Directory 的菜单中，在 " *管理* " 下选择 " *组* "，然后选择 " *新建组*"。
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="显示 &quot;新建组&quot; 选项突出显示的 &quot;Azure 门户 Active Directory 组&quot; 屏幕。":::
1. 请确保已选中 "组类型 *"，并* 输入组名称，如 " *myJITGroup*"。 在 " *Azure AD 角色可以分配给此组 (预览")* 中，选择 *"是"*。 最后，选择“创建”。
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="显示 Azure 门户的新组创建屏幕。":::
1. 你将返回到 " *组* " 页。 选择新创建的组，并记下对象 ID，将这些说明的其余部分称为 `<object-id>` 。
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="显示刚刚创建的组的 Azure 门户屏幕，突出显示对象 Id":::
1. 使用 `<tenant-id>` 前面的和值，通过 AKS 管理的 Azure AD 集成部署 AKS 群集 `<object-id>` ：
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. 返回 Azure 门户，在左侧 *活动* 的菜单中，选择 " *特权访问 (预览")* 并选择 " *启用特权访问*"。
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="显示 Azure 门户的特权访问 (预览 &quot;) 页面，突出显示&quot; 启用特权访问 &quot;":::
1. 选择 " *添加分配* " 开始授予访问权限。
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="显示启用后 Azure 门户的特权访问 (预览) 屏幕。&quot;添加分配&quot; 选项已突出显示。":::
1. 选择 *成员* 的角色，并选择要向其授予群集访问权限的用户和组。 组管理员可随时修改这些分配。准备好继续时，选择 " *下一步*"。
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="将显示 Azure 门户的 &quot;添加分配成员身份&quot; 屏幕，并选择要添加为成员的示例用户。选项 &quot;下一步&quot; 已突出显示。":::
1. 选择 *活动* 的分配类型、所需的持续时间，并提供理由。 准备好继续时，选择 " *分配*"。 有关分配类型的详细信息，请参阅 [Privileged Identity Management 中的为特权访问组 (预览) 分配资格][aad-assignments]。
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="将显示 Azure 门户的 &quot;添加分配&quot; 设置屏幕。选择了 &quot;活动&quot; 分配类型，并提供了一个示例理由。&quot;Assign&quot; 选项已突出显示。":::

完成分配后，请通过访问群集来验证实时访问是否正常工作。 例如：

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

按照以下步骤登录。

使用 `kubectl get nodes` 命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes
```

请注意身份验证要求，然后按照步骤进行身份验证。 如果成功，应会看到类似于下面的输出：

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

请确保安全组的管理员为你的帐户提供了一个 *活动* 分配。

## <a name="next-steps"></a>后续步骤

* 了解 [适用于 Kubernetes 授权的 AZURE RBAC 集成][azure-rbac-integration]
* 了解 [Azure AD 与 Kubernetes RBAC 的集成][azure-ad-rbac]。
* 使用 [kubelogin](https://github.com/Azure/kubelogin) 来访问 kubectl 中未提供的 Azure 身份验证功能。
* 详细了解 [AKS 和 Kubernetes 标识概念][aks-concepts-identity]。
* 使用 [Azure 资源管理器 (ARM) 模板 ][aks-arm-template] 来创建启用 AKS Azure AD 管理的群集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: /azure/pricing/details/active-directory

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
