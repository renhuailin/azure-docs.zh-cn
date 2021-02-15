---
title: '使用启用了 GitOps on Arc 的 Kubernetes 群集 (预览部署 Helm 图表) '
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: '将 GitOps 与 Helm 配合使用，以启用启用了 Azure Arc 的群集配置 (预览) '
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: 883eb9c152bdc8a7c0e60e999cf9decf47fb80ec
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377918"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>使用启用了 GitOps on Arc 的 Kubernetes 群集 (预览部署 Helm 图表) 

Helm 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与 APT 和 Yum 之类的 Linux 包管理器类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。

本文介绍了如何配置 Helm 并将其用于启用了 Azure Arc 的 Kubernetes。

## <a name="before-you-begin"></a>开始之前

验证是否已启用现有的 Azure Arc Kubernetes 连接群集。 如果需要连接的群集，请参阅 [连接启用 Azure Arc Kubernetes 群集快速入门](./connect-cluster.md)。

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>将 GitOps 和 Helm 与启用了 Azure Arc 的 Kubernetes 一起使用概述

 Helm Operator 提供了 Flux 的一个扩展，用于自动完成 Helm 图表发布。 Helm 图表版本通过名为 HelmRelease 的 Kubernetes 自定义资源进行描述。 Flux 会将这些资源从 Git 同步到群集，而 Helm 运算符可确保按资源中的指定释放 Helm 图表。

 本文中使用的 [示例存储库](https://github.com/Azure/arc-helm-demo) 的结构如下所示：

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

在 Git 存储库中，我们有两个目录：一个包含 Helm 图表，另一个包含版本配置。在 `releases` 目录中， `app.yaml` 包含 HelmRelease 配置，如下所示：

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

| 字段 | 说明 |
| ------------- | ------------- | 
| `metadata.name` | 必需字段。 需要遵循 Kubernetes 命名约定。 |
| `metadata.namespace` | 可选字段。 确定发布的创建位置。 |
| `spec.releaseName` | 可选字段。 如果未提供，则发布名称将为 `$namespace-$name` 。 |
| `spec.chart.path` | 包含图表的目录，该目录是相对于存储库根所给定的。 |
| `spec.values` | 用户自定义图表本身的默认参数值。 |

在 HelmRelease 中指定的选项 `spec.values` 将覆盖图表源中指定的选项 `values.yaml` 。

可以在官方 [Helm 操作员文档](https://docs.fluxcd.io/projects/helm-operator/en/stable/)中了解有关 HelmRelease 的详细信息。

## <a name="create-a-configuration"></a>创建配置

使用的 Azure CLI 扩展将 `k8sconfiguration` 连接的群集链接到示例 Git 存储库。 为此配置指定名称 `azure-arc-sample` 并在命名空间中部署 Flux 运算符 `arc-k8s-demo` 。

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>配置参数

若要自定义配置的创建，请 [了解你可以使用的其他参数](./use-gitops-connected-cluster.md#additional-parameters)。

## <a name="validate-the-configuration"></a>验证配置

使用 Azure CLI 验证是否已 `sourceControlConfiguration` 成功创建。

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`资源将更新为符合性状态、消息和调试信息。

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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
    "chartVersion": "0.6.0"
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

运行以下命令并 `localhost:8080` 在浏览器中导航到，验证应用程序是否正在运行。

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure Policy 控制群集配置](./use-azure-policy.md)
