---
title: 快速入门：将现有 Kubernetes 群集连接到 Azure Arc
description: 在本快速入门中，了解如何连接已启用 Azure Arc 的 Kubernetes 群集。
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, Azure, 群集
ms.openlocfilehash: 3fc522c4bdda9eb1047d5258bcc431d0268990b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121637"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>快速入门：将现有 Kubernetes 群集连接到 Azure Arc 

在本快速入门中，我们将获得已启用 Azure Arc 的 Kubernetes 的好处，并将现有 Kubernetes 群集连接到 Azure Arc。有关将群集连接到 Azure Arc 的概念，请参阅[已启用 Azure Arc 的 Kubernetes 代理体系结构一文](./conceptual-agent-architecture.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* 验证是否具有：
    * 已启动并正在运行的 Kubernetes 群集。
    * 一个 `kubeconfig` 文件，指向要连接到 Azure Arc 的群集。
    * 用户或服务主体连接和创建已启用 Azure Arc 的 Kubernetes 资源类型 (`Microsoft.Kubernetes/connectedClusters`) 的“读取”和“写入”权限。
* 安装[最新版本的 Helm 3](https://helm.sh/docs/intro/install)。
* 安装以下版本 >= 1.0.0 的已启用 Azure Arc 的 Kubernetes CLI 扩展：
  
  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * 若要将这些扩展更新到最新版本，请运行以下命令：
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8s-configuration
  ```

>[!NOTE]
>支持的区域：
>* 美国东部
>* 西欧
>* 美国中西部
>* 美国中南部
>* 东南亚
>* 英国南部
>* 美国西部 2
>* 澳大利亚东部
>* 美国东部 2
>* 北欧

## <a name="meet-network-requirements"></a>满足网络要求

>[!IMPORTANT]
>Azure Arc 代理需要以下协议/端口/出站 URL 才能正常运行：
>* 端口 443 上的 TCP：`https://:443`
>* 端口 9418 上的 TCP：`git://:9418`
  
| 终结点 (DNS) | 说明 |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | 代理需要该终结点才可连接到 Azure 并注册群集。                                                        |  
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com`, `https://westcentralus.dp.kubernetesconfiguration.azure.com`, `https://southcentralus.dp.kubernetesconfiguration.azure.com`, `https://southeastasia.dp.kubernetesconfiguration.azure.com`, `https://uksouth.dp.kubernetesconfiguration.azure.com`, `https://westus2.dp.kubernetesconfiguration.azure.com`, `https://australiaeast.dp.kubernetesconfiguration.azure.com`, `https://eastus2.dp.kubernetesconfiguration.azure.com`, `https://northeurope.dp.kubernetesconfiguration.azure.com` | 代理的数据平面终结点，用于推送状态和提取配置信息。                                      |  
| `https://login.microsoftonline.com`                                                                            | 提取和更新 Azure 资源管理器令牌所需的终结点。                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | 拉取 Azure Arc 代理的容器映像所需的终结点。                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  拉取系统分配的托管服务标识 (MSI) 证书所需的终结点。                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>为已启用 Azure Arc 的 Kubernetes 注册两个提供程序

1. 输入以下命令：
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    ```
2. 监视注册过程。 注册可能最多需要 10 分钟。
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table    
    ```

## <a name="create-a-resource-group"></a>创建资源组

创建资源组：  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>连接现有的 Kubernetes 群集

1. 使用以下命令将 Kubernetes 群集连接到 Azure Arc：
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
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
    ```

> [!TIP]
> 上面未指定位置参数的命令会在资源组所在位置创建启用了 Azure Arc 的 Kubernetes 资源。 若要在其他位置创建启用了 Azure Arc 的 Kubernetes 资源，请在运行 `az connectedk8s connect` 命令时指定 `--location <region>` 或 `-l <region>`。

## <a name="verify-cluster-connection"></a>验证群集连接

使用以下命令查看已连接群集的列表：  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> 加入群集后，大约需要 5 到 10 分钟，群集元数据（群集版本、代理版本、节点数等）才会出现在 Azure 门户中已启用 Azure Arc 的 Kubernetes 资源的“概述”页上。

## <a name="connect-using-an-outbound-proxy-server"></a>使用出站代理服务器进行连接

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

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * 在 `--proxy-skip-range` 下指定 `excludedCIDR` 以确保代理的群集内通信不会中断。
> * 大多数出站代理环境预期使用 `--proxy-http`、`--proxy-https` 和 `--proxy-skip-range`。 仅在需要将代理预期的受信任证书插入代理 Pod 的受信任证书存储中时，才需要 `--proxy-cert`。

## <a name="view-azure-arc-agents-for-kubernetes"></a>查看适用于 Kubernetes 的 Azure Arc 代理

已启用 Azure Arc 的 Kubernetes 会将几个运算符部署到 `azure-arc` 命名空间中。 

1. 使用以下命令查看这些部署和 Pod：

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. 验证所有 Pod 是否处于 `Running` 状态。

    ```output
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
    ```

## <a name="clean-up-resources"></a>清理资源

可以使用以下命令删除已启用 Azure Arc 的 Kubernetes 资源、任何关联的配置资源以及使用 Azure CLI 在群集上运行的任何代理：

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>使用 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源将删除任何关联的配置资源，但不会删除群集上运行的任何代理。 最佳做法是使用 `az connectedk8s delete` 而不是 Azure 门户删除已启用 Azure Arc 的 Kubernetes 资源。

## <a name="next-steps"></a>后续步骤

继续阅读下一篇文章，了解如何使用 GitOps 将配置部署到连接的 Kubernetes 群集。
> [!div class="nextstepaction"]
> [使用 GitOps 部署配置](tutorial-use-gitops-connected-cluster.md)
