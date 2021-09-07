---
title: 适用于启用了 Azure Arc 的 Kubernetes 群集的 Azure RBAC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 在启用了 Azure Arc 的 Kubernetes 群集上使用 Azure RBAC 进行授权检查。
ms.openlocfilehash: 2607f82e0935ead0b6013bae963e22616c340b80
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105034"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>将 Azure Active Directory 与已启用 Azure Arc 的 Kubernetes 群集集成

Kubernetes [ClusterRoleBinding 和 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) 对象类型可以帮助你以本机方式在 Kubernetes 中定义授权。 借助此功能，可在 Azure 中使用 Azure Active Directory (Azure AD) 和角色分配来控制群集上的授权检查。 这意味着现在可以使用 Azure 角色分配来精确控制谁可以读取、写入和删除部署、Pod 和服务等 Kubernetes 对象。

有关此功能的概念性概述，请参阅[启用了 Azure Arc 的 Kubernetes 上的 Azure RBAC](conceptual-azure-rbac.md) 一文。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>必备条件

- [安装 Azure CLI 或将其升级](/cli/azure/install-azure-cli)到版本 2.16.0 或更高版本。

- 安装 `connectedk8s` Azure CLI 扩展，版本 1.1.0 或更高版本：

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    如果已安装 `connectedk8s` 扩展，则可以使用以下命令将其更新到最新版本： 

    ```azurecli
    az extension update --name connectedk8s
    ```

- 连接现有已启用 Azure Arc 的 Kubernetes 群集：
    - 如果你尚未连接群集，请使用我们的[快速入门](quickstart-connect-cluster.md)。
    - 将[代理升级](agent-upgrade.md#manually-upgrade-agents)到版本 1.1.0 或更高版本。

> [!NOTE]
> 对于云服务提供商的托管 Kubernetes 产品/服务（例如 Elastic Kubernetes Service 或 Google Kubernetes Engine），用户无权访问群集的 API 服务器，因此无法设置此功能。 对于 Azure Kubernetes Service (AKS) 群集，此功能是[以本机方式提供](../../aks/manage-azure-rbac.md)，不要求 AKS 群集连接至 Azure Arc。

## <a name="set-up-azure-ad-applications"></a>设置 Azure AD 应用程序

### <a name="create-a-server-application"></a>创建服务器应用程序

1. 创建新的 Azure AD 应用程序并获取其 `appId` 值。 此值在后续步骤中用作 `serverApplicationId`。

    ```azurecli
    CLUSTERNAME="<clusterName>"
    SERVER_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Server" --identifier-uris "https://${CLUSTERNAME}Server" --query appId -o tsv)
    echo $SERVER_APP_ID
    ```

1. 更新应用程序的组成员身份声明：

    ```azurecli
    az ad app update --id "${SERVER_APP_ID}" --set groupMembershipClaims=All
    ```

1. 创建服务主体并获取其 `password` 字段值。 稍后在群集上启用此功能时，需要将该字段值用作 `serverApplicationSecret`。

    ```azurecli
    az ad sp create --id "${SERVER_APP_ID}"
    SERVER_APP_SECRET=$(az ad sp credential reset --name "${SERVER_APP_ID}" --credential-description "ArcSecret" --query password -o tsv)
    ```

1. 向应用程序授予“登录并读取用户配置文件”API 权限：

    ```azurecli
    az ad app permission add --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > Azure 租户管理员必须运行此步骤。
    > 
    > 若要在生产中使用此功能，建议为每个群集创建一个不同的服务器应用程序。

### <a name="create-a-client-application"></a>创建客户端应用程序

1. 创建新的 Azure AD 应用程序并获取其 `appId` 值。 此值在后续步骤中用作 `clientApplicationId`。

    ```azurecli
    CLIENT_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Client" --native-app --reply-urls "https://${CLUSTERNAME}Client" --query appId -o tsv)
    echo $CLIENT_APP_ID
    ```

2. 创建此客户端应用程序的服务主体：

    ```azurecli
    az ad sp create --id "${CLIENT_APP_ID}"
    ```

3. 获取服务器应用程序的 `oAuthPermissionId` 值：

    ```azurecli
    az ad app show --id "${SERVER_APP_ID}" --query "oauth2Permissions[0].id" -o tsv
    ```

4. 授予客户端应用程序所需的权限：

    ```azurecli
    az ad app permission add --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}" --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}"
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>为服务器应用程序创建角色分配

服务器应用程序需要 `Microsoft.Authorization/*/read` 权限，以便检查发出请求的用户是否已获授权，可以使用该请求中指定的 Kubernetes 对象。

1. 创建名为 accessCheck.json 的文件，其内容如下所示：

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    请将 `<subscription-id>` 替换为实际订阅 ID。

2. 运行以下命令，创建新的自定义角色：

    ```azurecli
    ROLE_ID=$(az role definition create --role-definition ./accessCheck.json --query id -o tsv)
    ```

3. 使用创建的角色，在服务器应用程序上创建一个角色分配作为 `assignee`：

    ```azurecli
    az role assignment create --role "${ROLE_ID}" --assignee "${SERVER_APP_ID}" --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-the-cluster"></a>在群集上启用 Azure RBAC

在启用了 Arc 的 Kubernetes 群集上，通过运行以下命令启用 Azure 基于角色的访问控制 (RBAC)：

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id "${SERVER_APP_ID}" --app-secret "${SERVER_APP_SECRET}"
```
    
> [!NOTE]
> 运行上述命令之前，请确保计算机上的 `kubeconfig` 文件指向要启用 Azure RBAC 功能的群集。
>
> 在上述命令中可以使用 `--skip-azure-rbac-list` 指定以逗号分隔的用户名、电子邮件地址和 OpenID 连接列表，它们使用 Kubernetes 原生 `ClusterRoleBinding` 和 `RoleBinding` 对象（而非 Azure RBAC）进行授权检查。

### <a name="generic-cluster-where-no-reconciler-is-running-on-the-apiserver-specification"></a>没有按 apiserver 规范上运行协调器的通用群集

1. 通过 SSH 连接到该群集的每个主节点，并执行下列步骤：

    1. 在编辑模式下打开 `apiserver` 清单：
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. 在 `volumes` 下添加以下规范：
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. 在 `volumeMounts` 下添加以下规范：

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. 添加下列 `apiserver` 参数：

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        如果 Kubernetes 群集是版本1.19.0 或更高版本，则还需要设置以下 `apiserver` 参数：

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. 保存并关闭编辑器以更新 `apiserver` Pod。


### <a name="cluster-created-by-using-cluster-api"></a>使用群集 API 创建的群集

1. 将包含身份验证和授权 Webhook 配置文件的保护机密从工作负载群集复制到计算机上：

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. 将 azure-arc-guard-manifests.yaml 文件中的 `namespace` 字段更改为管理群集内的命名空间，你要将在其中应用自定义资源以创建工作负载群集。

1. 应用以下清单：

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. 通过运行 `kubectl edit kcp <clustername>-control-plane`，编辑 `KubeadmControlPlane` 对象：
    
    1. 将以下代码片段添加到 `files` 下：
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. 将以下代码片段添加到 `apiServer` > `extraVolumes` 下：
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. 将以下代码片段添加到 `apiServer` > `extraArgs` 下：
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. 保存并关闭以更新 `KubeadmControlPlane` 对象。 请等待这些更改出现在工作负载群集上。


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>创建角色分配以便用户访问群集

启用了 Azure Arc 的 Kubernetes 资源的所有者可以使用内置角色或自定义角色，将 Kubernetes 群集的访问权授予其他用户。

### <a name="built-in-roles"></a>内置角色

| 角色 | 说明 |
|---|---|
| [Azure Arc Kubernetes 查看者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | 允许进行只读访问并查看命名空间中的大多数对象。 此角色不允许查看密码。 这是因为机密的 `read` 权限允许访问命名空间中的 `ServiceAccount` 凭据。 这些凭据进而会允许使用该 `ServiceAccount` 值进行 API 访问（一种特权提升形式）。 |
| [Azure Arc Kubernetes 写入者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | 允许对命名空间中的大多数对象进行读/写访问。 此角色不允许查看或修改角色或角色绑定。 但是，此角色允许以命名空间中任何 `ServiceAccount` 值的身份访问密码和运行 Pod。 因此可将其用于获取命名空间中任何 `ServiceAccount` 值的 API 访问级别。 |
| [Azure Arc Kubernetes 管理员](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | 授予管理员访问权限。 旨在通过 `RoleBinding` 在命名空间内授予。 如果在 `RoleBinding` 中使用，则允许对命名空间中的大部分资源进行读/写访问，包括能够在该命名空间内创建角色和角色绑定。 此角色不允许对资源配额或命名空间本身进行写入访问。 |
| [Azure Arc Kubernetes 群集管理员](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | 授予超级用户访问权限（对任何资源执行任何操作）。 在 `ClusterRoleBinding` 中使用时，它提供对群集和所有命名空间中每种资源的完全控制。 在 `RoleBinding` 中使用时，即授予对该角色绑定的命名空间中每种资源（包括该命名空间本身）的完全控制权。|

可以在 Azure 门户的群集资源的“访问控制(IAM)”窗格中创建角色分配，其作用域为启用 Arc 的 Kubernetes 群集。 也可使用以下 Azure CLI 命令：

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

其中，`AZURE-AD-ENTITY-ID` 可以是用户名（例如 `testuser@mytenant.onmicrosoft.com`），甚至可以是服务主体的 `appId` 值。

下面是另一个在群集中创建作用域为特定命名空间的角色分配的示例：

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> 可以通过 Azure 门户或 Azure CLI 创建范围确定为群集的角色分配，但只能使用 CLI 创建范围确定为命名空间的角色分配。

### <a name="custom-roles"></a>自定义角色

可以选择创建自己的角色定义，以便在角色分配中使用。

请演练以下角色定义示例，该角色定义仅允许用户读取部署。 有关详细信息，请参阅[可用于构造角色定义的数据操作完整列表](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)。

将以下 JSON 对象复制到名为“custom-role.json”的文件中。 请将 `<subscription-id>` 占位符替换为实际订阅 ID。 自定义角色使用其中一项数据操作，并让你可查看创建角色分配所在作用域（群集或命名空间）内的所有部署。

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. 通过从保存了“custom-role.json”的文件夹运行以下命令来创建角色定义：

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. 使用该自定义角色定义创建角色分配：

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>使用用户凭据来配置 kubectl

可通过两种方法获取访问群集所需的 kubeconfig 文件：

- 使用启用了 Azure Arc 的 Kubernetes 群集的[群集连接](cluster-connect.md)功能 (`az connectedk8s proxy`)。
- 群集管理员与其他所有用户共享 kubeconfig 文件。

### <a name="if-youre-accessing-the-cluster-by-using-the-cluster-connect-feature"></a>如果要使用群集连接功能访问群集

运行以下命令以启动代理进程：

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

该代理进程运行后，你可以在控制台中打开另一个选项卡，[开始向群集发送请求](#send-requests-to-the-cluster)。

### <a name="if-the-cluster-admin-shared-the-kubeconfig-file-with-you"></a>如果群集管理员与你共享了 kubeconfig 文件 

1. 运行以下命令，以便为用户设置凭据：

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. 打开先前创建的 kubeconfig 文件。 在 `contexts` 下，确认与该群集相关联的上下文指向上一步中创建的用户凭据。

1. 在 `user` > `config` 下，添加 config-mode 设置：
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="send-requests-to-the-cluster"></a>发送请求到群集

1. 运行任何 `kubectl` 命令。 例如：
   * `kubectl get nodes` 
   * `kubectl get pods`

1. 系统提示进行基于浏览器的身份验证后，请复制设备登录 URL (`https://microsoft.com/devicelogin`)，并在 Web 浏览器中打开。

1. 输入显示在控制台上的代码。 将终端上的代码复制并粘贴到设备身份验证输入提示中。

1. 输入用户名 (`testuser@mytenant.onmicrosoft.com`) 和关联的密码。

1. 如果你看到类似如下的错误消息，则表示你无权访问所请求的资源：

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    管理员必须创建新的角色分配，授权该用户访问该资源。

## <a name="use-conditional-access-with-azure-ad"></a>配合使用条件访问和 Azure AD

将 Azure AD 与已启用 Arc 的 Kubernetes 群集集成后，还可以使用[条件访问](../../active-directory/conditional-access/overview.md)来控制对群集的访问。

> [!NOTE]
> Azure AD 条件访问是一项 Azure AD 高级版功能。

若要创建用于群集的示例条件访问策略，请完成以下步骤：

1. 从 Azure 门户的顶部，搜索并选择“Azure Active Directory”。
1. 在左侧 Azure Active Directory 的菜单中，选择“企业应用程序”。
1. 在左侧企业应用程序的菜单中，选择“条件访问”。
1. 在左侧“条件访问”的菜单中，选择“策略” > “新建策略” 。
    
    [ ![显示用于添加条件访问策略的按钮的屏幕截图。](./media/azure-rbac/conditional-access-new-policy.png) ](./media/azure-rbac/conditional-access-new-policy.png#lightbox)

1. 为策略输入名称，例如“arc-k8s-policy”。
1. 选择“用户和组”  。 在“包括”下，选择“选择用户和组” 。 然后选择要应用策略的用户和组。 对于此示例，请选择对你的群集具有管理访问权限的同一个 Azure AD 组。

    [ ![显示选择要应用条件访问策略的用户或组的屏幕截图。](./media/azure-rbac/conditional-access-users-groups.png) ](./media/azure-rbac/conditional-access-users-groups.png#lightbox)

1. 选择“云应用或操作”。 在“包含”下，选择“选择应用” 。 然后，搜索并选择之前创建的服务器应用程序。

    [ ![选择要应用条件访问策略的服务器应用程序的屏幕截图。](./media/azure-rbac/conditional-access-apps.png) ](./media/azure-rbac/conditional-access-apps.png#lightbox)

1. 在“访问控制”  下，选择“授予”  。 选择“授予访问权限” > “要求设备标记为合规” 。

    [ ![显示选择此设置将仅允许符合条件访问策略的设备的屏幕截图。](./media/azure-rbac/conditional-access-grant-compliant.png) ](./media/azure-rbac/conditional-access-grant-compliant.png#lightbox)
    
1. 在“启用策略”下，选择“开” > “创建”  。

    [ ![显示启用条件访问策略的屏幕截图。](./media/azure-rbac/conditional-access-enable-policies.png) ](./media/azure-rbac/conditional-access-enable-policies.png#lightbox)

再次访问群集。 例如，运行 `kubectl get nodes` 命令来查看群集中的节点：

```console
kubectl get nodes
```

再次按照说明进行登录。 一条错误消息会指明你已成功登录，但若要访问资源，你的管理员要求请求访问的设备受 Azure AD 管理。 执行以下步骤：

1. 在 Azure 门户中，转到“Azure Active Directory”。
1. 选择“企业应用程序”。 然后在“活动”下，选择“登录” 。 
1. 顶部的条目显示“状态”为“失败”，而“条件访问”为“成功”   。 选择该条目，然后在“详细信息”中选择“条件访问” 。 请注意，你的条件访问策略已列出。

   [ ![显示由于条件访问策略而失败的登录条目的屏幕截图。 ](./media/azure-rbac/conditional-access-sign-in-activity.png) ](./media/azure-rbac/conditional-access-sign-in-activity.png#lightbox)

## <a name="configure-just-in-time-cluster-access-with-azure-ad"></a>使用 Azure AD 配置即时群集访问

用于群集访问控制的另一个选项是对即时请求使用 Privileged Identity Management (PIM)。

>[!NOTE]
> PIM 是一种需要 Premium P2 SKU 的 Azure AD Premium 功能。 有关 Azure AD SKU 的详细信息，请参阅[定价指南](https://azure.microsoft.com/pricing/details/active-directory/)。

若要为群集配置即时访问请求，请完成以下步骤：

1. 从 Azure 门户的顶部，搜索并选择“Azure Active Directory”。
1. 记下租户 ID。 对于这些说明的其余部分，我们将该 ID 引用为 `<tenant-id>`。

    [ ![显示 Azure Active Directory 租户详细信息的屏幕截图。](./media/azure-rbac/jit-get-tenant-id.png) ](./media/azure-rbac/jit-get-tenant-id.png#lightbox)

1. 在左侧 Azure Active Directory 的菜单上的“管理”下，选择“组” > “新建组”  。

    [ ![显示用于创建机密的选项的屏幕截图。](./media/azure-rbac/jit-create-new-group.png) ](./media/azure-rbac/jit-create-new-group.png#lightbox)

1. 确保为“组类型”选择了“安全性” 。  输入组名称，例如“myJITGroup”。 在“可将 Azure AD 角色分配到此组(预览版)”下，选择“是”。  最后，选择“创建”。

    [ ![显示新组详细信息的屏幕截图。](./media/azure-rbac/jit-new-group-created.png) ](./media/azure-rbac/jit-new-group-created.png#lightbox)

1. 你将返回到“组”页。 选择新创建的组并记下对象 ID。 对于这些说明的其余部分，我们将该 ID 引用为 `<object-id>`。

    [ ![显示所创建组的对象标识符的屏幕截图。](./media/azure-rbac/jit-get-object-id.png) ](./media/azure-rbac/jit-get-object-id.png#lightbox)

1. 返回 Azure 门户，在左侧“活动”的菜单中，选择“特权访问（预览版）” 。 然后选择“启用特权访问”。

    [ ![显示用于启用特权访问的选择的屏幕截图。](./media/azure-rbac/jit-enabling-priv-access.png) ](./media/azure-rbac/jit-enabling-priv-access.png#lightbox)

1. 选择“添加分配”开始授予访问权限。

    [ ![显示用于添加活动分配按钮的屏幕截图。](./media/azure-rbac/jit-add-active-assignment.png) ](./media/azure-rbac/jit-add-active-assignment.png#lightbox)

1. 选择“成员”的角色，然后选择要向其授予群集访问权限的用户和组。 组管理员可以随时修改这些分配。 准备好继续操作时，选择“下一步”。

    [ ![显示添加分配的屏幕截图。](./media/azure-rbac/jit-adding-assignment.png) ](./media/azure-rbac/jit-adding-assignment.png#lightbox)

1. 选择“活动”分配类型，选择所需的持续时间，并提供理由。 准备好继续操作时，选择“分配”。 有关分配类型的详细信息，请参阅[在 Privileged Identity Management 中为特权访问组分配资格（预览版）](../../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group)。

    [ ![显示为分配选择属性的屏幕截图。](./media/azure-rbac/jit-set-active-assignment.png) ](./media/azure-rbac/jit-set-active-assignment.png#lightbox)

完成分配后，通过访问群集来验证是否可以正常进行即时访问。 例如，使用 `kubectl get nodes` 命令来查看群集中的节点：

```console
kubectl get nodes
```

请注意身份验证要求，并按照步骤完成身份验证。 如果身份验证成功，应会看到类似于下面的输出：

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME      STATUS   ROLES    AGE      VERSION
node-1    Ready    agent    6m36s    v1.18.14
node-2    Ready    agent    6m42s    v1.18.14
node-3    Ready    agent    6m33s    v1.18.14
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> 使用[群集连接](cluster-connect.md)安全连接到群集。
