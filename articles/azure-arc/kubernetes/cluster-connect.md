---
title: 使用群集连接连接到已启用 Azure Arc 的 Kubernetes 群集
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 使用群集连接安全连接到已启用 Azure Arc 的 Kubernetes 群集
ms.openlocfilehash: 65070f8850bb18be5c142c658190ebe9da41f7a0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767903"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>使用群集连接连接到已启用 Azure Arc 的 Kubernetes 群集

使用群集连接可以安全连接到已启用 Azure Arc 的 Kubernetes 群集，而无需在防火墙上启用任何入站端口。 访问已启用 Arc 的 Kubernetes 群集的 `apiserver` 可实现以下方案：
* 启用交互式调试和故障排除。
* 为群集提供对位于[自定义位置](custom-locations.md)的 Azure 服务及其上创建的其他资源的访问权限。

[群集连接 - 已启用 Azure Arc 的 Kubernetes](conceptual-cluster-connect.md) 一文中提供了此功能的概念性概述。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>先决条件   

- [安装或升级 Azure CLI](/cli/azure/install-azure-cli)，使用 2.16.0 或更高版本

- 安装 1.1.0 或更高版本的 `connectedk8s` Azure CLI 扩展：

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    如果已安装 `connectedk8s` 扩展，请将扩展更新到最新版本：
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- 一个现有的已启用 Azure Arc 的 Kubernetes 连接的群集。
    - 如果你尚未连接群集，请使用我们的[快速入门](quickstart-connect-cluster.md)。
    - 将[代理升级](agent-upgrade.md#manually-upgrade-agents)到 1.1.0 或更高版本。

- 通过在 `kubeconfig` 文件指向所需群集的计算机上运行以下命令，在任何已启用 Azure Arc 的 Kubernetes 群集上启用群集连接：

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- 除了[将 Kubernetes 群集连接到 Azure Arc](quickstart-connect-cluster.md#meet-network-requirements) 中所述的终结点以外，还启用以下终结点用于出站访问：

    | 端点 | 端口 |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>使用情况

支持将两个身份验证选项与群集连接功能配合使用： 
* Azure Active Directory (Azure AD)租户 
* 服务帐户令牌

### <a name="option-1-azure-active-directory"></a>选项 1：Azure Active Directory

1. 在 `kubeconfig` 文件指向 Kubernetes 群集的 `apiserver` 的情况下，与需要访问权限的 Azure AD 实体（服务主体或用户）建立 ClusterRoleBinding 或 RoleBinding：

    **对于用户：**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **对于 Azure AD 应用程序：**

    1. 获取与你的 Azure AD 应用程序关联的 `objectId`：

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. 与需要访问此群集的 Azure AD 实体（服务主体或用户）建立 ClusterRoleBinding 或 RoleBinding：
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. 使用所需的 Azure AD 实体登录到 Azure CLI 后，从任何位置（甚至可以从群集周围的防火墙外部）获取与群集通信所需的群集连接 `kubeconfig`：

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. 使用 `kubectl` 将请求发送到群集：

    ```console
    kubectl get pods
    ```
    
    现在应会看到来自群集的响应，其中包含 `default` 命名空间下所有 Pod 的列表。

### <a name="option-2-service-account-bearer-token"></a>选项 2：服务帐户持有者令牌

1. 在 `kubeconfig` 文件指向 Kubernetes 群集的 `apiserver` 的情况下，在任一命名空间中创建一个服务帐户（以下命令在默认命名空间中创建服务帐户）：

    ```console
    kubectl create serviceaccount admin-user
    ```

1. 创建 ClusterRoleBinding 或 RoleBinding 以[向此服务帐户授予对群集的适当权限](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding)：

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. 使用以下命令获取服务帐户的令牌

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. 从任何位置（甚至可以从群集周围的防火墙外部）获取与群集通信所需的群集连接 `kubeconfig`：

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. 使用 `kubectl` 将请求发送到群集：

    ```console
    kubectl get pods
    ```

    现在应会看到来自群集的响应，其中包含 `default` 命名空间下所有 Pod 的列表。

## <a name="known-limitations"></a>已知的限制

对 Kubernetes 群集发出请求时，如果使用的 Azure AD 实体是超过 200 个组的成员，则会出现以下错误，因为这是一个已知的限制：

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

若要避免此错误，请执行以下操作：
1. 创建一个不太可能是超过 200 个组的成员的[服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)。
1. 运行命令 `az connectedk8s proxy` 之前，先使用服务主体[登录](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)到 Azure CLI。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> 在群集上设置 [Azure AD RBAC](azure-rbac.md)