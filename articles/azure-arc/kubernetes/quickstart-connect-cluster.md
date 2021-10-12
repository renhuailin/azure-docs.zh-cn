---
title: 快速入门：将现有 Kubernetes 群集连接到 Azure Arc
description: 在本快速入门中，了解如何连接已启用 Azure Arc 的 Kubernetes 群集。
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 09/09/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, Azure, 群集
ms.openlocfilehash: 10c97945a78867d92b9ed4887e9655d49b195e33
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273754"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>快速入门：将现有 Kubernetes 群集连接到 Azure Arc

在本快速入门中，了解已启用 Azure Arc 的 Kubernetes 的优点，以及如何将现有的 Kubernetes 群集连接到 Azure Arc。有关将群集连接到 Azure Arc 的概念，请参阅[已启用 Azure Arc 的 Kubernetes 代理体系结构一文](./conceptual-agent-architecture.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* [安装 Azure CLI 或将其升级](/cli/azure/install-azure-cli)到不低于 2.16.0 的版本

* 安装 1.0.0 或更高版本的 connectedk8s Azure CLI 扩展：

  ```console
  az extension add --name connectedk8s
  ```

* 已启动并正在运行的 Kubernetes 群集。 如果没有群集，你可以使用以下任意选项创建群集：
    * [Docker 中的 Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    * 使用 [Docker for Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 或 [Docker for Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 创建 Kubernetes 群集
    * 使用[群集 API](https://cluster-api.sigs.k8s.io/user/quick-start.html) 的自托管 Kubernetes 群集
    * 如果要将 OpenShift 群集连接到 Azure Arc，只需在运行 `az connectedk8s connect` 之前在群集上执行以下命令：

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 群集至少需要有一个操作系统和体系结构类型的节点：`linux/amd64`。 目前尚不支持只有 `linux/arm64` 节点的群集。

* `kubeconfig` 指向群集的文件和上下文。
* 已启用 Azure Arc 的 Kuberneters 资源类型 (`Microsoft.Kubernetes/connectedClusters`) 的“读取”和“写入”权限。

* 安装 [Helm 3](https://helm.sh/docs/intro/install)。 确保 Helm 3 版本为 &lt; 3.7.0。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

* [Azure PowerShell 5.9.0 版或更高版本](/powershell/azure/install-az-ps)

* 安装 **Az.ConnectedKubernetes** PowerShell 模块：

    ```azurepowershell-interactive
    Install-Module -Name Az.ConnectedKubernetes
    ```

    > [!IMPORTANT]
    > 在 Az.ConnectedKubernetes PowerShell 模块处于预览版阶段时，必须使用 `Install-Module` cmdlet 来单独安装该模块。

* 已启动并正在运行的 Kubernetes 群集。 如果没有群集，你可以使用以下任意选项创建群集：
    * [Docker 中的 Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    * 使用 [Docker for Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 或 [Docker for Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 创建 Kubernetes 群集
    * 使用[群集 API](https://cluster-api.sigs.k8s.io/user/quick-start.html) 的自托管 Kubernetes 群集
    * 如果要将 OpenShift 群集连接到 Azure Arc，只需在运行 `New-AzConnectedKubernetes` 之前在群集上执行以下命令：

        ```console
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 群集至少需要有一个操作系统和体系结构类型的节点：`linux/amd64`。 目前尚不支持只有 `linux/arm64` 节点的群集。

* `kubeconfig` 指向群集的文件和上下文。
* 已启用 Azure Arc 的 Kuberneters 资源类型 (`Microsoft.Kubernetes/connectedClusters`) 的“读取”和“写入”权限。

* 安装 [Helm 3](https://helm.sh/docs/intro/install)。 确保 Helm 3 版本为 &lt; 3.7.0。

---

## <a name="meet-network-requirements"></a>满足网络要求

> [!IMPORTANT]
> Azure Arc 代理需要以下协议/端口/出站 URL 才能运行：
> * 端口 443 上的 TCP：`https://:443`

| 终结点 (DNS) | 说明 |
| ----------------- | ------------- |
| `https://management.azure.com`（针对 Azure 云），`https://management.usgovcloudapi.net`（针对 Azure US Government） | 代理需要该终结点才可连接到 Azure 并注册群集。 |
| `https://<region>.dp.kubernetesconfiguration.azure.com`（针对 Azure 云），`https://<region>.dp.kubernetesconfiguration.azure.us`（针对 Azure US Government） | 代理的数据平面终结点，用于推送状态和提取配置信息。 |
| `https://login.microsoftonline.com`、`login.windows.net`（针对 Azure 云），`https://login.microsoftonline.us`（针对 Azure US Government） | 提取和更新 Azure 资源管理器令牌所需的终结点。 |
| `https://mcr.microsoft.com`, `https://*.data.mcr.microsoft.com` | 拉取 Azure Arc 代理的容器映像所需的终结点。                                                                  |
| `https://gbl.his.arc.azure.com`（针对 Azure 云），`https://gbl.his.arc.azure.us`（针对 Azure US Government） |  需要用于获取区域终结点，以便拉取系统分配的托管标识证书。 |
| `https://*.his.arc.azure.com`（针对 Azure 云），`https://usgv.his.arc.azure.us`（针对 Azure US Government） |  拉取系统分配的托管标识证书时必需。 |
|`*.servicebus.windows.net`, `guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com`, `sts.windows.net` | 针对基于[连接](cluster-connect.md)和[位置](custom-locations.md)的场景。 |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. 为已启用 Azure Arc 的 Kubernetes 注册提供程序

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 输入以下命令：
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. 监视注册过程。 注册可能最多需要 10 分钟。
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

    注册后，应会看到这些命名空间的 `RegistrationState` 状态更改为 `Registered`。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. 输入以下命令：
    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```
1. 监视注册过程。 注册可能最多需要 10 分钟。
    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ExtendedLocation
    ```

    注册后，应会看到这些命名空间的 `RegistrationState` 状态更改为 `Registered`。
---

## <a name="2-create-a-resource-group"></a>2.创建资源组

运行以下命令：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name AzureArcTest --location EastUS --output table
```

输出：
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name AzureArcTest -Location EastUS
```

输出：
<pre>
ResourceGroupName : AzureArcTest
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureArcTest
</pre>

---

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. 连接现有的 Kubernetes 群集

运行以下命令：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

输出：
<pre>
Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
</pre>

> [!TIP]
> 上面未指定位置参数的命令会在资源组所在位置创建已启用 Azure Arc 的 Kubernetes 资源。 若要在其他位置创建已启用 Azure Arc 的 Kubernetes 资源，请在运行 `az connectedk8s connect` 命令时指定 `--location <region>` 或 `-l <region>`。

> [!NOTE]
> 如果你使用服务主体登录到 Azure CLI，则需要设置一个[附加参数](troubleshooting.md#enable-custom-locations-using-service-principal)，用于在群集上启用自定义位置功能。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest -Location eastus
```

输出：
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

## <a name="4a-connect-using-an-outbound-proxy-server"></a>4a. 使用出站代理服务器进行连接

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果群集位于出站代理服务器后面，Azure CLI 和已启用 Azure Arc 的 Kubernetes 代理需要通过出站代理服务器路由其请求。

1. 设置 Azure CLI 使用出站代理服务器所需的环境变量：

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

2. 使用指定的代理参数运行 connect 命令：

    ```azurecli
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

    > [!NOTE]
    > * 某些网络请求（例如涉及群集内服务间通信的请求）需要与通过代理服务器路由进行出站通信的流量分开。 `--proxy-skip-range` 参数可用于以逗号分隔的方式指定 CIDR 范围和终结点，以便代理与这些终结点之间的任何通信不会通过出站代理进行。 群集中服务的 CIDR 范围至少应指定为此参数的值。 例如，假设 `kubectl get svc -A` 返回一个服务列表，其中所有服务在 `10.0.0.0/16` 范围内都有 ClusterIP 值。 则要为 `--proxy-skip-range` 指定的值为 `10.0.0.0/16,kubernetes.default.svc,.svc.cluster.local,.svc`。
    > * 大多数出站代理环境预期使用 `--proxy-http`、`--proxy-https` 和 `--proxy-skip-range`。 仅在需要将代理预期的受信任证书插入代理 Pod 的受信任证书存储中时，才需要 `--proxy-cert`。
    > * 必须将出站代理配置为允许 WebSocket 连接。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

如果群集位于出站代理服务器后面，Azure PowerShell 和已启用 Azure Arc 的 Kubernetes 代理就需要通过出站代理服务器来路由它们的请求。

1. 设置 Azure PowerShell 使用出站代理服务器所需的环境变量：

    ```powershell
    $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
    $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
    $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
    ```

2. 运行指定了代理参数的连接命令：

    ```azurepowershell
    New-AzConnectedKubernetes -ClusterName <cluster-name> -ResourceGroupName <resource-group> -Location eastus -Proxy 'https://<proxy-server-ip-address>:<port>'
    ```

---

## <a name="5-verify-cluster-connection"></a>5. 验证群集连接

运行以下命令：

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az connectedk8s list --resource-group AzureArcTest --output table
```

输出：
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzConnectedKubernetes -ResourceGroupName AzureArcTest
```

输出：
<pre>
Location Name          Type
-------- ----          ----
eastus   AzureArcTest1 microsoft.kubernetes/connectedclusters
</pre>

---

> [!NOTE]
> 加入群集后，大约需要 5 到 10 分钟，群集元数据（群集版本、代理版本、节点数等）才会出现在 Azure 门户中已启用 Azure Arc 的 Kubernetes 资源的“概述”页上。

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. 查看适用于 Kubernetes 的 Azure Arc 代理

启用了 Azure Arc 的 Kubernetes 会将几个 operator 部署到 `azure-arc` 命名空间中。

1. 使用以下命令查看这些部署和 Pod：

    ```console
    kubectl get deployments,pods -n azure-arc
    ```

1. 验证所有 Pod 是否处于 `Running` 状态。

    输出：
    <pre>

    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    </pre>

## <a name="7-clean-up-resources"></a>7.清理资源

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以使用以下命令删除已启用 Azure Arc 的 Kubernetes 资源、任何关联的配置资源以及使用 Azure CLI 在群集上运行的任何代理：

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
> 使用 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源将删除任何关联的配置资源，但不会删除群集上运行的任何代理。 最佳做法是使用 `az connectedk8s delete` 而不是 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

可以使用 Azure PowerShell 通过以下命令删除已启用 Azure Arc 的 Kubernetes 资源、任何关联的配置资源，以及群集上运行的任何代理：

```azurepowershell
Remove-AzConnectedKubernetes -ClusterName AzureArcTest1 -ResourceGroupName AzureArcTest
```

>[!NOTE]
> 使用 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源将删除任何关联的配置资源，但不会删除群集上运行的任何代理。 最佳做法是使用 `Remove-AzConnectedKubernetes` 而不是 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源。

---

## <a name="next-steps"></a>后续步骤

继续阅读下一篇文章，了解如何使用 GitOps 将配置部署到连接的 Kubernetes 群集。
> [!div class="nextstepaction"]
> [使用 GitOps 部署配置](tutorial-use-gitops-connected-cluster.md)
