---
title: 排查常见的启用 Azure Arc 的 Kubernetes 问题
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 排查已启用 Arc 的 Kubernetes 群集的常见问题。
keywords: Kubernetes, Arc, Azure, 容器
ms.openlocfilehash: e1f4e84f16c6b584f1ffbd918a86c251f47efcca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653994"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>启用 Azure Arc Kubernetes 故障排除

本文档提供有关连接、权限和代理问题的疑难解答指南。

## <a name="general-troubleshooting"></a>常规故障排除

### <a name="azure-cli"></a>Azure CLI

使用 `az connectedk8s` 或 `az k8s-configuration` CLI 命令之前，请检查是否已将 Azure CLI 设置为适用于正确的 Azure 订阅。

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc 代理

已启用 Azure Arc 的 Kubernetes 的所有代理在 `azure-arc` 命名空间中部署为 Pod。 所有盒都应该运行并传递其运行状况检查。

首先，请验证 Azure Arc helm 版本：

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

如果找不到或缺少 Helm 版本，请尝试再次将 [群集连接到 Azure Arc](./connect-cluster.md) 。

如果与一起存在 Helm 版本 `STATUS: deployed` ，请使用以下方式检查代理的状态 `kubectl` ：

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

所有 pod 都应 `STATUS` 在 `Running` `3/3` `2/2` 列中显示为或 `READY` 。 提取日志，并描述返回或的 `Error` pod `CrashLoopBackOff` 。 如果任何 pod 卡住 `Pending` 状态，则群集节点上的资源可能不足。 [向上缩放群集](https://kubernetes.io/docs/tasks/administer-cluster/) 可以使这些 pod 转换为 `Running` 状态。

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>将 Kubernetes 群集连接到 Azure Arc

若要将群集连接到 Azure，需要对 Azure 订阅的访问权限以及对 `cluster-admin` 目标群集的访问权限。 如果你无法访问群集或权限不足，则将群集连接到 Azure Arc 将会失败。

### <a name="insufficient-cluster-permissions"></a>群集权限不足

如果提供的 kubeconfig 文件没有足够的权限来安装 Azure Arc 代理，则 Azure CLI 命令将返回错误。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

将群集连接到 Azure Arc 的用户应 `cluster-admin` 在群集上向其分配角色。

### <a name="installation-timeouts"></a>安装超时

若要将 Kubernetes 群集连接到启用了 Azure Arc 的 Kubernetes，需要在群集上安装 Azure Arc 代理。 如果群集在慢速 internet 连接上运行，则代理的容器映像请求可能需要更长的时间才能 Azure CLI 超时。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm 问题

Helm `v3.3.0-rc.1` 版本存在一个 [问题](https://github.com/helm/helm/pull/8527) ，即 CLI 扩展使用的 Helm 安装/升级 (`connectedk8s`) 导致所有挂钩都出现以下错误：

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

若要从此问题恢复，请执行以下步骤：

1. 删除 Azure 门户中启用了 Azure Arc 的 Kubernetes 资源。
2. 在计算机上运行以下命令：
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. 在计算机上[安装稳定版本](https://helm.sh/docs/intro/install/)的 Helm 3，而不是预发行版。
4. 运行 `az connectedk8s connect` 具有适当值的命令，将群集连接到 Azure Arc。

## <a name="configuration-management"></a>配置管理

### <a name="general"></a>常规
若要帮助解决配置资源的问题，请运行指定了参数的 az 命令 `--debug` 。

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>创建配置

启用了 Azure Arc Kubernetes 资源 () 的写入权限 `Microsoft.Kubernetes/connectedClusters/Write` 是必要的，并且足以在该群集上创建配置。

### <a name="configuration-remains-pending"></a>配置保留 `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>监视

容器 Azure Monitor 要求在特权模式下运行 DaemonSet。 若要成功设置用于监视的规范 Charmed Kubernetes 群集，请运行以下命令：

```console
juju config kubernetes-worker allow-privileged=true
```