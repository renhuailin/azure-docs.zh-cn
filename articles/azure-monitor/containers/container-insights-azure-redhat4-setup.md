---
title: 配置 Azure Red Hat OpenShift v4.x 的容器见解 | Microsoft Docs
description: 本文介绍如何使用 Azure Red Hat OpenShift 版本 4 或更高版本上托管的 Azure Monitor 来配置对 Kubernetes 群集的监视。
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 02cb794463b965ebafef0b6861477dbf69227511
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506406"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>配置 Azure Red Hat OpenShift v4.x 的容器见解

容器见解提供了对 Azure Kubernetes 服务 (AKS) 和 AKS 引擎群集的丰富的监视体验。 本文介绍如何通过启用对 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 版本 4.x 上托管的 Kubernetes 群集的监视来实现类似的监视体验。

>[!NOTE]
>目前，对 Azure Red Hat OpenShift 的支持是公共预览版中的一项功能。
>

你可以使用本文中所述的受支持方法，为 Azure Red Hat OpenShift v4.x 的一个或多个现有部署启用容器见解。

对于现有群集，请[在 Azure CLI 中运行此 Bash 脚本](/cli/azure/openshift#az-openshift-create&preserve-view=true)。

## <a name="supported-and-unsupported-features"></a>支持/不支持的功能

如[容器见解概述](container-insights-overview.md)中所述，容器见解支持监视 Azure Red Hat OpenShift v4.x，但以下功能除外：

- 实时数据（预览版）
- 从群集节点和 Pod [收集指标](container-insights-update-metrics.md)并将其存储在 Azure Monitor 指标数据库中

## <a name="prerequisites"></a>先决条件

- Azure CLI 版本 2.0.72 或更高版本  

- [Helm 3](https://helm.sh/docs/intro/install/) CLI 工具

- [Bash 版本 4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 命令行工具

- [Log Analytics 工作区](../logs/design-logs-deployment.md)。

    容器见解支持在 Azure [产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)中列出的区域中的 Log Analytics 工作区。 若要创建你自己的工作区，可通过 [Azure 资源管理器](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或 [Azure 门户](../logs/quick-create-workspace.md)进行创建。

- 要启用和访问容器见解中的功能，需要至少在 Azure 订阅中拥有 Azure 参与者角色，且在 Log Analytics 工作区中拥有 [Log Analytics 参与者](../logs/manage-access.md#manage-access-using-azure-permissions)角色，并配置了容器见解。

- 要查看监视数据，需要在 Log Analytics 工作区中拥有 [*Log Analytics 读者*](../logs/manage-access.md#manage-access-using-azure-permissions)角色，并配置了容器见解。

## <a name="enable-monitoring-for-an-existing-cluster"></a>启用对现有群集的监视

要使用提供的 Bash 脚本启用对 Azure 中部署的 Azure Red Hat OpenShift 版本 4 或更高版本群集的监视，请执行以下操作：

1. 通过运行以下命令登录到 Azure：

    ```azurecli
    az login
    ```

1. 下载脚本并将其保存到本地文件夹，通过运行以下命令，为群集配置监视加载项：

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. 通过[教程：连接到 Azure Red Hat OpenShift 4 群集](../../openshift/tutorial-connect-cluster.md)中的说明连接到 ARO v4 群集。


### <a name="integrate-with-an-existing-workspace"></a>与现有工作区集成

在本节中，你将使用先前下载的 Bash 脚本启用群集监视。 若要与现有 Log Analytics 工作区集成，请首先标识 `logAnalyticsWorkspaceResourceId` 参数所需的 Log Analytics 工作区的完整资源 ID，然后运行命令，针对指定的工作区启用监视加载项。

如果你没有可指定的工作区，可以跳到[与默认工作区集成](#integrate-with-the-default-workspace)部分，让脚本为你创建一个新的工作区。

1. 通过运行以下命令列出你有权访问的所有订阅：

    ```azurecli
    az account list --all -o table
    ```

    输出将如下所示：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. 复制 **SubscriptionId** 的值。

1. 通过运行以下命令切换到托管 Log Analytics 工作区的订阅：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. 运行以下命令，以默认 JSON 格式显示订阅中的工作区列表：

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. 在输出中，找到工作区名称，然后在字段 **ID** 下复制该 Log Analytics 工作区的完整资源 ID。

1. 若要启用监视，请运行以下命令。 替换 `azureAroV4ClusterResourceId` 和 `logAnalyticsWorkspaceResourceId` 参数的值。

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    使用导出命令填充完 3 个变量后，必须运行以下命令：

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

启用监视后，可能需要大约 15 分钟才能查看群集的运行状况指标。

### <a name="integrate-with-the-default-workspace"></a>与默认工作区集成

在本节中，你将使用已下载的 Bash 脚本启用对 Azure Red Hat OpenShift v4.x 群集的监视。

在此示例中，不需要预先创建工作区或指定现有工作区。 此命令简化了这一过程，如果区域中尚不存在默认的工作区，它会在群集订阅的默认资源组中创建默认工作区。

创建的默认工作区的格式为 DefaultWorkspace-\<GUID>-\<Region>。  

替换 `azureAroV4ClusterResourceId` 参数的值。

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

例如：

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId 

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="enable-monitoring-from-the-azure-portal"></a>在 Azure 门户中启用监视

容器见解中的多群集视图在“未受监视的群集”选项卡下突出显示未启用监视功能的 Azure Red Hat OpenShift 群集。群集旁边的“启用”选项不会启动从门户载入监视。 你将被重定向到本文，以按照本文前面所述的步骤手动启用监视。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从左侧窗格或主页中选择“Azure Monitor”。

1. 在“见解”部分，选择“容器” 。

1. 在“监视 - 容器”页上，选择“未监视的群集” 。

1. 在未受监视的群集列表中，选择群集，然后选择“启用”。

    可以通过在“群集类型”列中查找 ARO 值来确定列表中的结果 。 选择“启用”后，你将被重定向到本文。

## <a name="next-steps"></a>后续步骤

- 现在你已启用监视，用来收集 RedHat OpenShift 版本 4.x 群集的运行状况和资源利用率，以及在群集上运行的工作负载，了解[如何使用](container-insights-analyze.md)容器见解。

- 默认情况下，容器化代理会收集在所有命名空间（kube-system 除外）中运行的所有容器的 stdout 和 stderr  容器日志。 若要配置特定于某个或某些命名空间的容器日志收集，请参阅[容器见解代理配置](container-insights-agent-config.md)，为 ConfigMap 配置文件配置所需的数据收集设置。

- 若要从群集中抓取和分析 Prometheus 指标，请参阅[配置 Prometheus 指标抓取](container-insights-prometheus-integration.md)。

- 若要了解如何停止使用容器见解监视群集，请参阅[如何停止监视 Azure Red Hat OpenShift 群集](./container-insights-optout-openshift-v3.md)。
