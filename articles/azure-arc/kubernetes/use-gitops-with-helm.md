---
title: 在已启用 Arc 的 Kubernetes 群集上使用 GitOps 部署 Helm 图表
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 将 GitOps 和 Helm 配合用于已启用 Azure Arc 的群集配置
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: 75e2fcb25680817fc3e2bddabbbdd9c52b7dd059
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121399"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>在已启用 Arc 的 Kubernetes 群集上使用 GitOps 部署 Helm 图表

Helm 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与诸如 APT 和 Yum 的 Linux 包管理器类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。

本文介绍了如何配置 Helm 并将其用于启用了 Azure Arc 的 Kubernetes。

## <a name="before-you-begin"></a>开始之前

验证是否具有现有的已启用 Azure Arc 的 Kubernetes 连接的群集。 如果需要连接的群集，请参阅[连接已启用 Azure Arc 的 Kubernetes 群集快速入门](./quickstart-connect-cluster.md)。

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>将 GitOps 与 Helm 配合用于已启用 Azure Arc 的 Kubernetes 概述

 Helm Operator 提供了 Flux 的一个扩展，用于自动完成 Helm 图表发布。 Helm 图表发布是通过名为 HelmRelease 的 Kubernetes 自定义资源描述的。 Flux 将这些资源从 Git 同步到群集，同时 Helm Operator 确保根据资源中指定的内容发布 Helm 图表。

 本文中使用的[示例存储库](https://github.com/Azure/arc-helm-demo)的结构如下所示：

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

Git 存储库中有两个目录，其中一个目录包含 Helm 图表，另一个目录包含发布配置。在 `releases` 目录中，`app.yaml` 包含如下所示的 HelmRelease 配置：

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Helm 发布配置包含以下字段：

| 字段 | 描述 |
| ------------- | ------------- | 
| `metadata.name` | 必需字段。 需要遵循 Kubernetes 命名约定。 |
| `metadata.namespace` | 可选字段。 确定创建发布的位置。 |
| `spec.releaseName` | 可选字段。 如果未提供，则发布名称将是 `$namespace-$name`。 |
| `spec.chart.path` | 包含图表的目录（相对于存储库根路径）。 |
| `spec.values` | 图表本身中默认参数值的用户自定义设置。 |

HelmRelease `spec.values` 中指定的选项将替代图表源中的 `values.yaml` 中指定的选项。

可以在官方 [Helm Operator 文档](https://docs.fluxcd.io/projects/helm-operator/en/stable/)中详细了解 HelmRelease。

## <a name="create-a-configuration"></a>创建配置

使用适用于 `k8s-configuration` 的 Azure CLI 扩展，将已连接的群集链接到示例 Git 存储库。 将此配置命名为 `azure-arc-sample`，并在 `arc-k8s-demo` 命名空间中部署 Flux Operator。

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>配置参数

若要自定义配置创建，请[了解其他参数](./tutorial-use-gitops-connected-cluster.md#additional-parameters)。

## <a name="validate-the-configuration"></a>验证配置

使用 Azure CLI 验证是否已成功创建配置。

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

将使用符合性状态、消息和调试信息更新配置资源。

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>验证应用程序

运行以下命令并在浏览器中导航到 `localhost:8080`，验证应用程序是否正在运行。

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>后续步骤

使用 [Azure Policy](./use-azure-policy.md) 大规模应用群集配置。
