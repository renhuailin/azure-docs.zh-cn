---
title: 监视已启用 Azure Arc 的 Kubernetes 群集
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: 使用 Azure Monitor 收集已启用 Azure Arc 的 Kubernetes 群集的指标和日志
ms.openlocfilehash: 1ece606aa3967d9fddaa5f964c43e24350610817
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709718"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>已启用 Azure Arc 的 Kubernetes 群集的 Azure Monitor 容器见解

[Azure Monitor 容器见解](container-insights-overview.md)为已启用 Azure Arc 的 Kubernetes 群集提供了丰富的监视体验。

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>支持的配置

- Azure Monitor 容器见解支持监视已启用 Azure Arc 的 Kubernetes（预览），如[概述](container-insights-overview.md)一文中所述，但实时数据（预览）功能除外。 此外，用户无需[所有者](../../role-based-access-control/built-in-roles.md#owner)权限也可[启用指标](container-insights-update-metrics.md)
- `Docker`、`Moby` 和 CRI 兼容容器运行时（如 `CRI-O` 和 `containerd`）。
- 支持无身份验证的出站代理和有基本身份验证的出站代理。 目前不支持需要受信任证书的出站代理。

>[!NOTE]
> Azure Monitor 容器见解目前不支持 Kubernetes v1.22 或更高版本

## <a name="prerequisites"></a>必备条件

- 满足[通用群集扩展文档](../../azure-arc/kubernetes/extensions.md#prerequisites)中列出的先决条件。
- Log Analytics 工作区：Azure Monitor 容器见解支持在 Azure [产品（按区域）页](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)上列出的区域中的 Log Analytics 工作区。 你可以通过 [Azure 资源管理器](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md) 或 [Azure 门户](../logs/quick-create-workspace.md)创建自己的工作区。
- 对于包含已启用 Azure Arc 的 Kubernetes 资源的 Azure 订阅，需要具有[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色分配。 如果 Log Analytics 工作区位于不同的订阅中，在 Log Analytics 工作区上需要拥有 [Log Analytics 参与者](../logs/manage-access.md#manage-access-using-azure-permissions)角色。
- 要查看监视数据，在 Log Analytics 工作区上需要拥有 [Log Analytics 读者](../logs/manage-access.md#manage-access-using-azure-permissions)角色。
- 除了[将 Kubernetes 群集连接到 Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements) 中所述的终结点以外，还需要为出站访问启用以下终结点。

    | 端点 | 端口 |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    如果已启用 Azure Arc 的 Kubernetes 资源位于 Azure 美国政府环境中，则需要启用以下终结点以进行出站访问：

    | 端点 | 端口 |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- 如果以前已使用没有群集扩展的脚本在此群集上部署了 Azure Monitor 容器见解，请按[此处](container-insights-optout-hybrid.md)列出的说明删除此 Helm 图表。 然后，你可以继续为 Azure Monitor 容器见解创建群集扩展实例。

    >[!NOTE]
    > 基于脚本部署 Azure Monitor 容器见解（预览）这种部署方式将被[群集扩展](../../azure-arc/kubernetes/extensions.md)形式替代。 仅在 2021 年 6 月前提供对通过脚本部署 Azure Monitor 的支持，建议尽早迁移到群集扩展这种部署形式。

### <a name="identify-workspace-resource-id"></a>确定工作区资源 ID

运行以下命令来查找 Log Analytics 工作区的完整 Azure 资源管理器标识符。 

1. 使用以下命令列出你有权访问的所有订阅：

    ```azurecli
    az account list --all -o table
    ```

2. 使用以下命令切换到托管 Log Analytics 工作区的订阅：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 以下示例以默认 JSON 格式显示订阅中的工作区列表。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在输出中，找到相关的工作区名。 其中的 `id` 字段表示 Log Analytics 工作区的 Azure 资源管理器标识符。

    >[!TIP]
    > 还可以通过 Azure 门户在 Log Analytics 工作区的“概述”边栏选项卡中找到此 `id`。

## <a name="create-extension-instance-using-azure-cli"></a>使用 Azure CLI 创建扩展实例

### <a name="option-1---with-default-values"></a>选择 1 - 使用默认值

此选择使用以下默认设置：

- 创建或使用与群集区域相对应的现有默认 Log Analytics 工作区
- 已为 Azure Monitor 群集扩展启用自动升级

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>选择 2 - 使用现有 Azure Log Analytics 工作区

你可以在具有“参与者”角色或更大权限的角色的任何订阅中使用现有 Azure Log Analytics 工作区。

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>选择 3 - 使用高级配置

如果要调整默认资源请求和限制，可以使用高级配置设置：

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

查看 [Helm 图表的资源请求和限制部分](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml)，了解可用的配置设置。

### <a name="option-4---on-azure-stack-edge"></a>选择 4 - 在 Azure Stack Edge 上

如果已启用 Azure Arc 的 Kubernetes 群集位于 Azure Stack Edge 上，则需要使用自定义装载路径 `/home/data/docker`。

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> 如果要在 create 命令中显式指定要安装的扩展的版本，请确保指定的版本不低于 2.8.2。

## <a name="create-extension-instance-using-azure-portal"></a>使用 Azure 门户创建扩展实例

>[!IMPORTANT]
>  如果要在运行在 Azure Stack Edge 上的 Kubernetes 群集上部署 Azure Monitor，需要使用 Azure CLI 选项，而不是 Azure 门户选项，因为需要为这些群集设置自定义装载路径。    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>从已启用 Azure Arc 的 Kubernetes 资源边栏选项卡加入

1. 在 Azure 门户中，选择要监视的已启用 Azure Arc 的 Kubernetes 群集。

2. 在资源边栏选项卡的“监视”部分下，选择“见解(预览)”项。

3. 在加入页上，选择“配置 Azure Monitor”按钮

4. 你现在可以选择要将指标和日志数据发送到的 [Log Analytics 工作区](../logs/quick-create-workspace.md)。

5. 选择“配置”按钮以部署 Azure Monitor 容器见解群集扩展。

### <a name="onboarding-from-azure-monitor-blade"></a>从 Azure Monitor 边栏选项卡加入

1. 在 Azure 门户中，导航到“Monitor”边栏选项卡，然后选择“见解”菜单下的“容器”选项。

2. 选择“未受监视的群集”选项卡，查看可为其启用监视的已启用 Azure Arc 的 Kubernetes 群集。

3. 单击要启用监视的群集旁的“启用”链接。

4. 选择 Log Analytics 工作区，然后选择“配置”按钮以继续操作。

## <a name="create-extension-instance-using-azure-resource-manager"></a>使用 Azure 资源管理器创建扩展实例

1. 下载 Azure 资源管理器模板和参数：

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. 更新 arc-k8s-azmon-extension-arm-template-params.json 文件中的参数值。对于 Azure 公有云，需要使用 `opinsights.azure.com` 作为 workspaceDomain 的值。

3. 部署模板以创建 Azure Monitor 容器见解扩展 

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>删除扩展实例

以下命令仅删除扩展实例，但不删除 Log Analytics 工作区。 Log Analytics 资源中的数据保持不变。

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>断开连接的群集
如果群集已从 Azure 断开连接超过 48 小时，则 Azure Resource Graph 不会包含有关群集的信息。 因此，“见解”边栏选项卡显示的群集状态相关信息可能不正确。

## <a name="next-steps"></a>后续步骤

- 启用监视功能以收集启用了 Azure Arc 的 Kubernetes 群集及其上运行的工作负载的运行状况和资源利用率后，请了解[如何使用](container-insights-analyze.md)容器见解。

- 默认情况下，容器化代理会收集在所有命名空间（kube-system 除外）中运行的所有容器的 stdout/stderr 容器日志。 若要配置特定于某个或某些命名空间的容器日志收集，请参阅[容器见解代理配置](container-insights-agent-config.md)，为 ConfigMap 配置文件配置所需的数据收集设置。

- 若要从群集中抓取和分析 Prometheus 指标，请参阅[配置 Prometheus 指标抓取](container-insights-prometheus-integration.md)。
