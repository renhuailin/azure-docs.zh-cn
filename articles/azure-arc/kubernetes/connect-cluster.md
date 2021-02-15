---
title: '将启用了 Azure Arc 的 Kubernetes 群集连接 (预览版) '
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 使用 Azure Arc 连接启用了 Azure Arc 的 Kubernetes 群集
keywords: Kubernetes, Arc, Azure, K8s, 容器
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e68eccf998592aa7d1ebfea51e4ca66d577b3c7f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390549"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>将启用了 Azure Arc 的 Kubernetes 群集连接 (预览版) 

本文介绍了如何将任何云本机计算基础 (CNCF) 认证 Kubernetes 群集（例如，Azure 上的 AKS 引擎、Azure Stack 集线器上的 AKS 引擎、GKE、EKS 和 VMware vSphere 群集）连接到 Azure Arc。

## <a name="before-you-begin"></a>开始之前

验证是否已准备好以下先决条件：

* 向上和正在运行的 Kubernetes 群集。 如果你没有现有的 Kubernetes 群集，则可以使用以下指南之一来创建测试群集：
  * [在 Docker 中使用 Kubernetes 创建 Kubernetes 群集 (种类) ](https://kind.sigs.k8s.io/)。
  * 使用 Docker for [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 或 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)创建 Kubernetes 群集。
* 用于在群集上访问群集和群集管理角色的 kubeconfig 文件，用于部署启用了 Arc 的 Kubernetes 代理。
* 使用 `az login` 和 `az connectedk8s connect` 命令的用户或服务主体必须具有对“Microsoft.Kubernetes/connectedclusters”资源类型的“读取”和“写入”权限。 "Kubernetes Cluster-Azure Arc 加入" 角色具有这些权限，可用于用户或服务主体上的角色分配。
* Helm 3：使用 connectedk8s 扩展加入群集。 [安装最新版本的 Helm 3](https://helm.sh/docs/intro/install) 以满足此要求。
* Azure CLI 版本 2.15 +，用于安装启用了 Azure Arc 的 Kubernetes CLI 扩展。 [安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 或更新到最新版本。
* 安装启用 Arc 的 Kubernetes CLI 扩展：
  
  * 安装 `connectedk8s` 扩展，该扩展可帮助你将 Kubernetes 群集连接到 Azure：
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * 安装 `k8sconfiguration` 扩展：
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * 如果以后要更新这些扩展，请运行以下命令：
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>支持的区域

* 美国东部
* 西欧

## <a name="network-requirements"></a>网络要求

Azure Arc 代理需要以下协议/端口/出站 Url 才能工作：

* 端口443上的 TCP： `https://:443`
* 端口9418上的 TCP： `git://:9418`

| 终结点 (DNS)                                                                                               | 说明                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | 代理需要连接到 Azure 并注册群集。                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | 代理的数据平面终结点，用于推送状态和获取配置信息。                                      |
| `https://login.microsoftonline.com`                                                                            | 需要获取并更新 Azure 资源管理器令牌。                                                                                    |
| `https://mcr.microsoft.com`                                                                            | 需要请求 Azure Arc 代理的容器映像。                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  需要请求系统分配的托管标识证书。                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>为已启用 Azure Arc 的 Kubernetes 注册两个提供程序：

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

注册是一个异步过程，可能需要大约10分钟。 可以使用以下命令监视注册进程：

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>创建资源组

使用资源组来存储群集的元数据。

首先，创建资源组来保存连接的群集资源。

```console
az group create --name AzureArcTest -l EastUS -o table
```

**输出：**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>连接群集

接下来，我们将使用以下内容将 Kubernetes 群集连接到 Azure `az connectedk8s connect` ：

1. 通过以下方式之一验证到 Kubernetes 群集的连接：
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. 使用 Helm 3 将适用于 Kubernetes 的 Azure Arc 代理部署到 `azure-arc` 命名空间：

    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

**输出：**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>验证已连接的群集

使用以下命令列出连接的群集：

```console
az connectedk8s list -g AzureArcTest -o table
```

**输出：**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

你还可以在 [Azure 门户](https://portal.azure.com/)上查看此资源。 在浏览器中打开门户，并根据前面在命令中使用的资源名称和资源组名称输入，导航到 "资源组" 和 "启用了 Azure Arc 的 Kubernetes" 资源 `az connectedk8s connect` 。  
> [!NOTE]
> 载入群集后，在 Azure 门户中启用了 Azure Arc Kubernetes 资源的 "概述" 页上，大约需要5到10分钟的群集元数据 (群集版本、代理版本、节点数等 ) 。

## <a name="connect-using-an-outbound-proxy-server"></a>使用出站代理服务器进行连接

如果群集位于出站代理服务器后面，Azure CLI 和启用了 Arc 的 Kubernetes 代理需要通过出站代理服务器路由其请求：

1. 检查 `connectedk8s` 计算机上安装的扩展的版本：

    ```console
    az -v
    ```

    需要 `connectedk8s` 扩展版本 0.2.5 + 来设置代理和出站代理。 如果计算机上安装了版本0.2.3 或更低版本，请按照 [更新步骤](#before-you-begin) 在计算机上获取最新版本的扩展。

2. 将 Azure CLI 所需的环境变量设置为使用出站代理服务器：

    * 如果使用的是 bash，请使用适当的值运行以下命令：

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

3. 运行连接命令并指定代理参数：

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * 指定 `excludedCIDR` 下的很 `--proxy-skip-range` 重要的一点是，确保代理的群集内通信不会中断。
> * 尽管 `--proxy-http` 、 `--proxy-https` 和 `--proxy-skip-range` 对于大多数出站代理环境都是预期的，但 `--proxy-cert` 仅当需要将来自代理的可信证书注入到代理 pod 的受信任的证书存储中时，才需要。
> * 上述代理规范当前仅适用于 Arc 代理，不适用于 sourceControlConfiguration 中使用的 flux pod。 启用 Arc 的 Kubernetes 团队正在积极地处理此功能，不久将会提供该功能。

## <a name="azure-arc-agents-for-kubernetes"></a>适用于 Kubernetes 的 Azure Arc 代理

已启用 Azure Arc 的 Kubernetes 会将几个运算符部署到 `azure-arc` 命名空间中。 你可以使用查看以下部署和 pod：

```console
kubectl -n azure-arc get deployments,pods
```

**输出：**

```console
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

启用了 Azure Arc 的 Kubernetes 由几个在部署到 `azure-arc` 命名空间的群集中运行的代理（运算符）组成。

| 代理 (操作员)                                                                                                | 说明                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | 监视连接的群集，以了解在群集上应用的源代码管理配置资源并更新符合性状态。                                                        |
| `deployment.apps/controller-manager` | 协调 Azure Arc 组件之间的交互的运算符运算符。                                      |
| `deployment.apps/metrics-agent`                                                                            | 收集其他 Arc 代理的性能指标。                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | 收集分类元数据，例如群集版本、节点计数和 Azure Arc 代理版本。                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  将上面提到的群集元数据同步到 Azure。                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  启用 Azure Arc 的 Kubernetes 目前支持系统分配的标识。 `clusteridentityoperator` 维护其他代理用于与 Azure 进行通信的托管服务标识 (MSI) 证书。                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  从部署为源代码管理配置一部分的 flux 操作员收集日志。                                                                  |

## <a name="delete-a-connected-cluster"></a>删除已连接的群集

可以使用 Azure CLI 或 Azure 门户删除 `Microsoft.Kubernetes/connectedcluster` 资源。


* **使用 Azure CLI 删除**：使用以下 Azure CLI 命令启动删除启用了 Azure Arc 的 Kubernetes 资源。
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  此命令删除 `Microsoft.Kubernetes/connectedCluster` Azure 中的资源和任何关联的 `sourcecontrolconfiguration` 资源。 Azure CLI 使用 `helm uninstall` 删除群集上运行的代理。

* **删除 Azure 门户**：在 Azure 门户上删除启用了 azure Arc 的 Kubernetes 资源会删除 `Microsoft.Kubernetes/connectedcluster` azure 中的资源和任何相关 `sourcecontrolconfiguration` 资源，但不 *会* 删除群集上运行的代理。 

  若要删除群集上运行的代理，请运行以下命令：

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>后续步骤

* [在连接的群集中使用 GitOps](./use-gitops-connected-cluster.md)
* [使用 Azure Policy 来控制群集配置](./use-azure-policy.md)
