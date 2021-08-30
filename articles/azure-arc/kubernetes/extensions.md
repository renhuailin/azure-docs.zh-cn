---
title: 已启用 Azure Arc 的 Kubernetes 群集扩展
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 在已启用 Azure Arc 的 Kubernetes 上部署扩展并管理其生命周期
ms.openlocfilehash: 28f82471b13ec798f7c75f6b17d88321dd4f79a9
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112378063"
---
# <a name="deploy-and-manage-azure-arc-enabled-kubernetes-cluster-extensions"></a>部署并管理已启用 Azure Arc 的 Kubernetes 群集扩展

Kubernetes 扩展功能可在已启用 Azure Arc 的 Kubernetes 群集上实现以下目的：

* 基于 Azure 资源管理器的群集扩展部署。
* 扩展 Helm 图表的生命周期管理。

本文内容：
> [!div class="checklist"]
> * 当前可用的已启用 Azure Arc 的 Kubernetes 群集扩展。
> * 如何创建扩展实例。
> * 必需参数和可选参数。
> * 如何查看、列出、更新和删除扩展实例。 

[群集扩展 - 已启用 Azure Arc 的 Kubernetes](conceptual-extensions.md) 一文中提供了此功能的概念性概述。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>先决条件

- [安装或升级 Azure CLI](/cli/azure/install-azure-cli)，确保其版本 >= 2.16.0。
- `connectedk8s`（版本 >= 1.1.0）和 `k8s-extension`（版本 >= 0.2.0）Azure CLI 扩展。 运行以下命令安装这些 Azure CLI 扩展：
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    如果已安装 `connectedk8s` 和 `k8s-extension` 扩展，则可以使用以下命令将其更新到最新版本：

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- 一个现有的已启用 Azure Arc 的 Kubernetes 连接的群集。
    - 如果你尚未连接群集，请使用我们的[快速入门](quickstart-connect-cluster.md)。
    - 将[代理升级](agent-upgrade.md#manually-upgrade-agents)到 1.1.0 或更高版本。

## <a name="currently-available-extensions"></a>当前可用的扩展

| 分机 | 说明 |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | 提供 Kubernetes 群集上部署的工作负载的性能相关信息。 从控制器、节点和容器收集内存与 CPU 利用率指标。 |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | 从 Kubernetes 群集收集与安全相关的信息，如审核日志数据。 基于收集的数据提供建议和威胁警报。 |
| [启用了 Azure Arc 的开放式服务网格](tutorial-arc-enabled-open-service-mesh.md) | 在群集上部署开放式服务网格，并启用 mTLS 安全性、细化的访问控制、流量移动、使用 Azure Monitor 或 Prometheus 和 Grafana 的开源附加产品进行监视、使用 Jaeger 进行跟踪、与外部认证管理解决方案集成等功能。 |
| [已启用 Azure Arc 的数据服务](../../azure-arc/kubernetes/custom-locations.md#create-custom-location) | 可以使用 Kubernetes 和你选择的基础结构在本地、边缘和公有云环境中运行 Azure 数据服务。 |
| [Azure Arc 上的 Azure 应用服务](../../app-service/overview-arc-integration.md) | 允许在已启用 Azure Arc 的 Kubernetes 群集基础之上预配应用服务 Kubernetes 环境。 |
| [Kubernetes 上的事件网格](../../event-grid/kubernetes/overview.md) | 在已启用 Azure Arc 的 Kubernetes 群集基础之上创建并管理事件网格资源，如主题和事件订阅。 |
| [Azure Arc 上的 Azure API 管理](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md) | 在已启用 Azure Arc 的 Kubernetes 群集上部署并管理 API 管理网关。 |
| [已启用 Azure Arc 的机器学习](../../machine-learning/how-to-attach-arc-kubernetes.md) | 在已启用 Azure Arc 的 Kubernetes 群集上部署并运行 Azure 机器学习。 |

## <a name="usage-of-cluster-extensions"></a>群集扩展的用法

### <a name="create-extensions-instance"></a>创建扩展实例

使用 `k8s-extension create` 并传入必需参数的值来创建新的扩展实例。 以下命令在已启用 Azure Arc 的 Kubernetes 群集上创建用于容器的 Azure Monitor 扩展实例：

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**输出：**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * 服务无法将敏感信息保留 48 小时以上。 如果已启用 Azure Arc 的 Kubernetes 代理保持网络连接的时间不超过 48 小时，且无法确定是否要在群集上创建扩展，则扩展会转换为 `Failed` 状态。 一旦进入 `Failed` 状态，你就需要再次运行 `k8s-extension create` 以创建全新的扩展 Azure 资源。
> * 用于容器的 Azure Monitor 是单一实例扩展（在每个群集上只需要一个）。 需要清理所有以前的用于容器的 Azure Monitor（不带扩展）Helm 图表安装，才能通过扩展安装同一组件。 请按照[运行 `az k8s-extension create` 之前删除 Helm 图表](../../azure-monitor/containers/container-insights-optout-hybrid.md)中的说明操作。

**必需的参数**

| 参数名称 | 说明 |
|----------------|------------|
| `--name` | 扩展实例的名称 |
| `--extension-type` | 要在群集上安装的扩展类型。 例如：Microsoft.AzureMonitor.Containers、microsoft.azuredefender.kubernetes | 
| `--scope` | 扩展的安装范围 - `cluster` 或 `namespace` |
| `--cluster-name` | 必须要在其上创建扩展实例的已启用 Azure Arc 的 Kubernetes 资源名称 |
| `--resource-group` | 包含已启用 Azure Arc 的 Kubernetes 资源的资源组 |
| `--cluster-type` | 必须要在其上创建扩展实例的群集类型。 目前只有 `connectedClusters`（对应于已启用 Azure Arc 的 Kubernetes）是可接受的值 |

**可选参数**

| 参数名称 | 说明 |
|--------------|------------|
| `--auto-upgrade-minor-version` | 布尔值属性，指定是否要自动升级扩展的次要版本。 默认值：`true`。  如果此参数设置为 true，那么你不能设置 `version` 参数，因为版本将动态更新。 如果设置为 `false`，则不会自动升级扩展，即使对于补丁版本，也是如此。 |
| `--version` | 要安装的扩展版本（要将扩展实例固定到的特定版本）。 如果 auto-upgrade-minor-version 设置为 `true`，则不可提供此参数。 |
| `--configuration-settings` | 可传入到扩展以控制其功能的设置。 这些设置将在参数名称的后面以空格分隔的 `key=value` 对形式传入。 如果在命令中使用此参数，则不能在同一命令中使用 `--configuration-settings-file`。 |
| `--configuration-settings-file` | JSON 文件的路径，该文件包含用于将配置设置传入到扩展的键值对。 如果在命令中使用此参数，则不能在同一命令中使用 `--configuration-settings`。 |
| `--configuration-protected-settings` | 这些设置是无法使用 `GET` API 调用或 `az k8s-extension show` 命令来检索的，因此会用于传入敏感设置。 这些设置将在参数名称的后面以空格分隔的 `key=value` 对形式传入。 如果在命令中使用此参数，则不能在同一命令中使用 `--configuration-protected-settings-file`。 |
| `--configuration-protected-settings-file` | JSON 文件的路径，该文件包含用于将敏感设置传入到扩展的键值对。 如果在命令中使用此参数，则不能在同一命令中使用 `--configuration-protected-settings`。 |
| `--release-namespace` | 此参数指示要在其中创建版本的命名空间。 仅当 `scope` 参数设置为 `cluster` 时，此参数才有意义。 |
| `--release-train` |  扩展作者可以在不同的版本序列（例如 `Stable`、`Preview` 等）中发布版本。如果未显式设置此参数，则默认将使用 `Stable`。 当 `autoUpgradeMinorVersion` 参数设置为 `false` 时，不能使用此参数。 |
| `--target-namespace` | 此参数指示要在其中创建版本的命名空间。 为此扩展实例创建的系统帐户的权限将限于此命名空间。 仅当 `scope` 参数设置为 `namespace` 时，此参数才有意义。 |

### <a name="show-details-of-an-extension-instance"></a>显示扩展实例的详细信息

使用 `k8s-extension show` 并传入必需参数的值，查看当前已安装的扩展实例的详细信息：

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**输出：**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>列出群集上安装的所有扩展

使用 `k8s-extension list` 并传入必需参数的值，列出群集上安装的所有扩展。

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**输出：**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>删除扩展实例

使用 `k8s-extension delete` 并传入必需参数的值，删除群集上的扩展实例。

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> 代表此扩展的 Azure 资源将会立即删除。 仅当 Kubernetes 群集上运行的代理已建立网络连接，并且可以再次访问 Azure 服务以获取所需状态时，才会删除群集上与此扩展关联的 Helm 版本。

## <a name="next-steps"></a>后续步骤

详细了解当前可用于已启用 Azure Arc 的 Kubernetes 的群集扩展：

> [!div class="nextstepaction"]
> [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> [启用了 Azure Arc 的开放式服务网格](tutorial-arc-enabled-open-service-mesh.md)
> 
> [!div class="nextstepaction"]
> [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)
> 
> [!div class="nextstepaction"]
> [Azure Arc 上的 Azure 应用服务](../../app-service/overview-arc-integration.md)
> 
> [!div class="nextstepaction"]
> [Kubernetes 上的事件网格](../../event-grid/kubernetes/overview.md)
> 
> [!div class="nextstepaction"]
> [Azure Arc 上的 Azure API 管理](../../api-management/how-to-deploy-self-hosted-gateway-azure-arc.md)