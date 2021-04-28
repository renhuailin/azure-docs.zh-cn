---
title: 适用于启用了 Azure Arc 的 Kubernetes 群集的 Azure RBAC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 在启用了 Azure Arc 的 Kubernetes 群集上使用 Azure RBAC 进行授权检查
ms.openlocfilehash: f0275e1516e8487b5a00fb08c885b09b6df1684c
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145692"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>将 Azure Active Directory 与已启用 Azure Arc 的 Kubernetes 群集集成

Kubernetes [ClusterRoleBinding 和 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) 对象类型可以帮助你以本机方式在 Kubernetes 中定义授权。 借助此功能，可在 Azure 中使用 Azure Active Directory 和角色分配来控制群集上的授权检查。 这意味着现在可以使用 Azure 角色分配来精确控制谁可以读取、写入和删除部署、Pod 和服务之类的 Kubernetes 对象

有关此功能的概念性概述，请参阅 [Azure RBAC - 启用了 Azure Arc 的 Kubernetes](conceptual-azure-rbac.md) 一文。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>先决条件

- [安装或升级 Azure CLI](/cli/azure/install-azure-cli)，使用 2.16.0 或更高版本

- 安装 1.1.0 或更高版本的 `connectedk8s` Azure CLI 扩展：

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    如果已安装 `connectedk8s` 扩展，你可使用以下命令将其更新到最新版本： 

    ```azurecli
    az extension update --name connectedk8s
    ```

- 一个现有的已启用 Azure Arc 的 Kubernetes 连接的群集。
    - 如果你尚未连接群集，请使用我们的[快速入门](quickstart-connect-cluster.md)。
    - 将[代理升级](agent-upgrade.md#manually-upgrade-agents)到 1.1.0 或更高版本。

> [!NOTE]
> 对于云服务提供商的托管 Kubernetes 产品/服务（例如，Elastic Kubernetes Service 或 Google Kubernetes Engine），用户无权访问群集的 `apiserver`，因此无法设置此功能。 对于 Azure Kubernetes Service (AKS) 群集，此功能是[以本机方式提供](../../aks/manage-azure-rbac.md)，不要求 AKS 群集连接至 Azure Arc。

## <a name="set-up-azure-ad-applications"></a>设置 Azure AD 应用程序

### <a name="create-server-application"></a>创建服务器应用程序

1. 创建新的 Azure AD 应用程序并获取其 `appId` 值，后续的步骤将其用作 `serverApplicationId`：

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. 更新应用程序组成员身份声明：

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. 创建服务主体并获取其 `password` 字段值，稍后在群集上启用此功能时，需要将该字段值用作 `serverApplicationSecret`：

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. 授予应用程序 API 权限：

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * 此步骤必须由 Azure 租户管理员执行。
    > * 若要在生产中使用此功能，建议为每个群集创建一个不同的服务器应用程序。

### <a name="create-client-application"></a>创建客户端应用程序

1. 创建新的 Azure AD 应用程序并获取其“appId”值，后续的步骤将其用作 `clientApplicationId`：

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. 创建此客户端应用程序的服务主体：

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. 获取服务器应用程序的 `oAuthPermissionId`：

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. 授予客户端应用程序所需的权限：

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
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

2. 执行以下命令，创建新的自定义角色：

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. 在上述命令的输出中，存储 `id` 字段的值，后续的步骤将其用作 `roleId`。

4. 使用上文中创建的角色，在服务器应用程序上创建一个角色分配作为代理人：

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>在群集上启用 Azure RBAC

1. 在启用了 Arc 的 Kubernetes 群集上，通过运行以下命令启用 Azure RBAC：

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. 运行上述命令之前，请确保计算机上的 `kubeconfig` 文件指向要启用 Azure RBAC 功能的群集。
    > 2. 在上述命令中可以使用 `--skip-azure-rbac-list` 指定以逗号分隔的用户名/电子邮件地址/OID 列表，它们使用 Kubernetes 本机 ClusterRoleBinding 和 RoleBinding 对象（而非 Azure RBAC）进行授权检查。

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>对于没有按 apiserver 规范运行的协调器的通用群集：

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
    
        如果 Kubernetes 群集为 1.19.0 或更高版本，则还需要设置以下 `apiserver argument`：

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. 保存并退出编辑器以更新 `apiserver` Pod。


### <a name="for-a-cluster-created-using-cluster-api"></a>对于使用群集 API 创建的群集

1. 将包含身份验证和授权 Webhook 配置文件的防护密码`from the workload cluster`复制到你的计算机：

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. 将 `azure-arc-guard-manifests.yaml` 文件中的 `namespace` 字段更改为管理群集内的命名空间，你将在其中应用自定义资源以创建工作负荷群集。

1. 应用以下清单：

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. 通过执行 `kubectl edit kcp <clustername>-control-plane`，编辑 `KubeadmControlPlane` 对象：
    
    1. 将以下代码片段添加到 `files:` 下：
    
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

    1. 将以下代码片段添加到 `apiServer:` -> `extraVolumes:` 下：
    
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

    1. 将以下代码片段添加到 `apiServer:` -> `extraArgs:` 下：
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. 保存并退出以更新 `KubeadmControlPlane` 对象。 请等待这些更改在工作负荷群集上得以实现。


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>创建角色分配以便用户访问群集

启用了 Azure Arc 的 Kubernetes 资源的所有者可以使用内置角色或自定义角色，将 Kubernetes 群集的访问权授予其他用户。

### <a name="built-in-roles"></a>内置角色

| 角色 | 说明 |
|---|---|
| [Azure Arc Kubernetes 查看者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | 允许进行只读访问并查看命名空间中的大多数对象。 此角色不允许查看密码。 这是因为，密码上的 `read` 权限允许访问命名空间中的 `ServiceAccount` 凭据，进而允许使用该 `ServiceAccount` 进行 API 访问（这是一种特权提升形式）。 |
| [Azure Arc Kubernetes 写入者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | 允许对命名空间中的大多数对象进行读/写访问。 此角色不允许查看或修改角色或角色绑定。 但是，此角色允许以命名空间中任何 `ServiceAccount` 的身份访问密码和运行 Pod，因此可用于获取命名空间中任何 `ServiceAccount` 的 API 访问级别。 |
| [Azure Arc Kubernetes 管理员](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | 授予管理员访问权限。 旨在使用 RoleBinding 在命名空间内授予。 如果在 RoleBinding 中使用，则允许对命名空间中的大部分资源进行读/写访问，包括能够在该命名空间内创建角色和角色绑定。 此角色不允许对资源配额或命名空间本身进行写入访问。 |
| [Azure Arc Kubernetes 群集管理员](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | 授予超级用户访问权限（对任何资源执行任何操作）。 在 ClusterRoleBinding 中使用时，即授予对群集中以及所有命名空间中每个资源的完全控制权。 在 RoleBinding 中使用时，即授予对该角色绑定的命名空间中每个资源（包括该命名空间本身）的完全控制权。|

在 Azure 门户上，你可在群集资源的“`Access Control (IAM)`”边栏选项卡上创建角色分配，其作用域为启用了 Arc 的 Kubernetes 群集。 此外，还可以使用 Azure CLI 命令，如下所示：

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

其中，`AZURE-AD-ENTITY-ID` 可以是用户名（例如，testuser@mytenant.onmicrosoft.com），甚至是服务主体的 `appId`。

下面是另一个在群集中创建作用域为特定命名空间的角色分配的示例：

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> 尽管可以使用 Azure 门户或 CLI 创建作用域为群集的角色分配，作用域为命名空间的角色分配只能使用 CLI 创建。

### <a name="custom-roles"></a>自定义角色

你可以选择创建自己的角色定义，以便在角色分配中使用。

请演练以下角色定义示例，该角色定义仅允许用户读取部署。 有关详细信息，请参阅[可用于构造角色定义的数据操作完整列表](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)。

将下面的 JSON 对象复制到名为 custom-role.json 的文件中。 请将 `<subscription-id>` 占位符替换为实际订阅 ID。 以下自定义角色使用其中一项数据操作，并允许你查看创建角色分配所在作用域（群集/命名空间）内的所有部署。

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

1. 通过从 `custom-role.json` 所保存在的文件夹运行以下命令，创建角色定义：

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. 使用该自定义角色定义创建角色分配：

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>使用用户凭据来配置 kubectl

可以通过两种方法获取访问群集所需的 `kubeconfig` 文件：
1. 使用启用了 Azure Arc 的 Kubernetes 群集的 [Cluster Connect](cluster-connect.md) 功能 (`az connectedk8s proxy`)。
1. 群集管理员分享 `kubeconfig` 文件给每个其他用户。

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>如果使用 Cluster Connect 功能访问群集

执行以下命令以启动代理进程：

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

该代理进程运行后，你可以在控制台中打开另一个选项卡，[开始向群集发送请求](#sending-requests-to-cluster)。

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>如果群集管理员已分享 `kubeconfig` 文件给你 

1. 执行以下命令，以便为用户设置凭据：

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. 打开早先创建的 `kubeconfig` 文件。 在 `contexts` 下，确认与该群集相关联的上下文指向上一步中创建的用户凭据。

1. 在用户配置下，添加 config-mode 设置：
  
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

## <a name="sending-requests-to-cluster"></a>向群集发送请求

1. 运行任何 `kubectl` 命令。 例如：
  * `kubectl get nodes` 
  * `kubectl get pods`

1. 系统提示进行基于浏览器的身份验证后，请复制设备登录 URL `https://microsoft.com/devicelogin`，并在 Web 浏览器中打开。

1. 输入印制在控制台上的代码，将终端上的代码复制并粘贴到设备身份验证输入提示中。

1. 输入用户名 (testuser@mytenant.onmicrosoft.com) 和关联的密码。

1. 如果你看到类似如下的错误消息，则表示你无权访问所请求的资源：

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    管理员必须创建新的角色分配，授权该用户访问该资源。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> 使用[群集连接](cluster-connect.md)安全连接到群集