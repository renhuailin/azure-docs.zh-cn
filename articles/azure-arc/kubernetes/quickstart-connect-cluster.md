---
title: 快速入门：将现有 Kubernetes 群集连接到 Azure Arc
description: 在本快速入门中，了解如何连接已启用 Azure Arc 的 Kubernetes 群集。
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 05/25/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, 群集
ms.openlocfilehash: a15a841e24d1464741c115684ed639609576a314
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796549"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>快速入门：将现有 Kubernetes 群集连接到 Azure Arc 

在本快速入门中，你将了解已启用 Azure Arc 的 Kubernetes 的优点，以及如何将现有的 Kubernetes 群集连接到 Azure Arc。有关将群集连接到 Azure Arc 的概念，请参阅[已启用 Azure Arc 的 Kubernetes 代理体系结构一文](./conceptual-agent-architecture.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* 已启动并正在运行的 Kubernetes 群集。 如果没有群集，你可以使用以下任意选项创建群集：
    * [Docker 中的 Kubernetes (KIND)](https://kind.sigs.k8s.io/)
    * 使用 [Docker for Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 或 [Docker for Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 创建 Kubernetes 群集
    * 使用[群集 API](https://cluster-api.sigs.k8s.io/user/quick-start.html) 的自托管 Kubernetes 群集
    * 如果要将 OpenShift 群集连接到 Azure Arc，只需在运行 `az connectedk8s connect` 之前在群集上执行以下命令：
        
        ```azurecli-interactive
        oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
        ```

    >[!NOTE]
    > 群集至少需要有一个操作系统和体系结构类型的节点：`linux/amd64`。 目前尚不支持只有 `linux/arm64` 节点的群集。
    
* `kubeconfig` 指向群集的文件和上下文。
* 启用 Azure Arc 的 Kuberneters 资源类型 (`Microsoft.Kubernetes/connectedClusters`) 的“读取”和“写入”权限。

* 安装[最新版本的 Helm 3](https://helm.sh/docs/intro/install)。

* [安装 Azure CLI 或将其升级](/cli/azure/install-azure-cli)到不低于 2.16.0 的版本
* 安装 `connectedk8s` Azure CLI 扩展版本，版本不得低于 1.0.0：
  
  ```azurecli-interactive
  az extension add --name connectedk8s
  ```
>[!NOTE]
> 对于群集上的[自定义位置](./custom-locations.md)，请使用美国东部或欧洲西部区域。 对于所有其他已启用 Azure Arc 的 Kubernetes 功能，请[从此列表中选择任何区域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)。

## <a name="meet-network-requirements"></a>满足网络要求

> [!IMPORTANT]
> Azure Arc 代理需要以下协议/端口/出站 URL 才能运行：
> * 端口 443 上的 TCP：`https://:443`
> * 端口 9418 上的 TCP：`git://:9418`
  
| 终结点 (DNS) | 说明 |  
| ----------------- | ------------- |  
| `https://management.azure.com`（针对 Azure 云），`https://management.usgovcloudapi.net`（针对 Azure US Government） | 代理需要该终结点才可连接到 Azure 并注册群集。 |  
| `https://<region>.dp.kubernetesconfiguration.azure.com`（针对 Azure 云），`https://<region>.dp.kubernetesconfiguration.azure.us`（针对 Azure US Government） | 代理的数据平面终结点，用于推送状态和提取配置信息。 |  
| `https://login.microsoftonline.com`（针对 Azure 云），`https://login.microsoftonline.us`（针对 Azure US Government） | 提取和更新 Azure 资源管理器令牌所需的终结点。 |  
| `https://mcr.microsoft.com` | 拉取 Azure Arc 代理的容器映像所需的终结点。                                                                  |  
| `https://<region-code>.his.arc.azure.com`（针对 Azure 云），`https://usgv.his.arc.azure.us`（针对 Azure US Government） |  拉取系统分配的托管服务标识 (MSI) 证书所需的终结点。 Azure 云区域的 `<region-code>` 映射：`eus`（美国东部）、`weu`（西欧）、`wcus`（美国中西部）、`scus`（美国中南部）、`sea`（东南亚）、`uks`（英国南部）、`wus2`（美国西部 2）、`ae`（澳大利亚东部）、`eus2`（美国东部 2）、`ne`（北欧）、`fc`（法国中部）。 |

## <a name="1-register-providers-for-azure-arc-enabled-kubernetes"></a>1. 为已启用 Azure Arc 的 Kubernetes 注册提供程序

1. 输入以下命令：
    ```azurecli-interactive
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. 监视注册过程。 注册可能最多需要 10 分钟。
    ```azurecli-interactive
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="2-create-a-resource-group"></a>2.创建资源组

运行以下命令：  

```azurecli-interactive
az group create --name AzureArcTest --location EastUS --output table
```

输出：
<pre>
Location    Name
----------  ------------
eastus      AzureArcTest
</pre>

## <a name="3-connect-an-existing-kubernetes-cluster"></a>3. 连接现有的 Kubernetes 群集

运行以下命令：
```azurecli-interactive
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
> 上面未指定位置参数的命令会在资源组所在位置创建启用了 Azure Arc 的 Kubernetes 资源。 若要在其他位置创建启用了 Azure Arc 的 Kubernetes 资源，请在运行 `az connectedk8s connect` 命令时指定 `--location <region>` 或 `-l <region>`。

> [!NOTE]
> 如果你使用服务主体登录到 Azure CLI，则需要设置一个[附加参数](troubleshooting.md#enable-custom-locations-using-service-principal)，用于在群集上启用自定义位置功能。

## <a name="4-verify-cluster-connection"></a>4. 验证群集连接

运行以下命令：  

```azurecli-interactive
az connectedk8s list --resource-group AzureArcTest --output table
```

输出：
<pre>
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
</pre>

> [!NOTE]
> 加入群集后，大约需要 5 到 10 分钟，群集元数据（群集版本、代理版本、节点数等）才会出现在 Azure 门户中已启用 Azure Arc 的 Kubernetes 资源的“概述”页上。

## <a name="5-connect-using-an-outbound-proxy-server"></a>5. 使用出站代理服务器进行连接

如果群集位于出站代理服务器后面，Azure CLI 和已启用 Azure Arc 的 Kubernetes 代理需要通过出站代理服务器路由其请求。 


1. 设置 Azure CLI 使用出站代理服务器所需的环境变量：

    * 如果使用的是 Bash，请使用适当的值运行以下命令：

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * 如果使用的是 PowerShell，请使用适当的值运行以下命令：

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. 使用指定的代理参数运行 connect 命令：

    ```azurecli-interactive
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * 在 `--proxy-skip-range` 下指定 `excludedCIDR` 以确保代理的群集内通信不会中断。
> * 大多数出站代理环境预期使用 `--proxy-http`、`--proxy-https` 和 `--proxy-skip-range`。 仅在需要将代理预期的受信任证书插入代理 Pod 的受信任证书存储中时，才需要 `--proxy-cert`。

## <a name="6-view-azure-arc-agents-for-kubernetes"></a>6. 查看适用于 Kubernetes 的 Azure Arc 代理

已启用 Azure Arc 的 Kubernetes 会将几个运算符部署到 `azure-arc` 命名空间中。 

1. 使用以下命令查看这些部署和 Pod：

    ```azurecli-interactive
    kubectl -name azure-arc get deployments,pods
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

可以使用以下命令删除已启用 Azure Arc 的 Kubernetes 资源、任何关联的配置资源以及使用 Azure CLI 在群集上运行的任何代理：

```azurecli-interactive
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>使用 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源将删除任何关联的配置资源，但不会删除群集上运行的任何代理。 最佳做法是使用 `az connectedk8s delete` 而不是 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源。

## <a name="next-steps"></a>后续步骤

继续阅读下一篇文章，了解如何使用 GitOps 将配置部署到连接的 Kubernetes 群集。
> [!div class="nextstepaction"]
> [使用 GitOps 部署配置](tutorial-use-gitops-connected-cluster.md)
