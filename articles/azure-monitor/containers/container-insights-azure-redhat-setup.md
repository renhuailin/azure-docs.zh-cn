---
title: 配置 Azure Red Hat OpenShift v3.x 的容器见解 | Microsoft Docs
description: 本文介绍如何使用 Azure Red Hat OpenShift 版本 3 或更高版本上托管的 Azure Monitor 来配置对 Kubernetes 群集的监视。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a2910655601548f39983547e12460d949901954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784670"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>配置 Azure Red Hat OpenShift v3 的容器见解

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月停用。
>
> 截至 2020 年 10 月，你将无法再创建新的 3.11 群集。
> 现有的 3.11 群集将继续运行到 2022 年 6 月，但在此日期之后将不再受支持。
>
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](../../openshift/tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:aro-feedback@microsoft.com)。

容器见解针对 Azure Kubernetes 服务 (AKS) 和 AKS 引擎群集提供了丰富的监视体验。 本文介绍了如何启用对 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 版本 3 或版本 3 的最新支持版本上托管的 Kubernetes 群集的监视，并实现类似的监视体验。

>[!NOTE]
>目前，对 Azure Red Hat OpenShift 的支持是公共预览版中的一项功能。
>

可使用以下支持的方法为 Azure Red Hat OpenShift 的新部署，或是一个或多个现有部署启用容器见解：

- 对于现有群集，通过 Azure 门户或使用 Azure 资源管理器模板。
- 对于新群集，使用 Azure 资源管理器模板，或使用 [Azure CLI](/cli/azure/openshift#az_openshift_create) 创建新群集。

## <a name="supported-and-unsupported-features"></a>支持/不支持的功能

容器见解支持对 Azure Red Hat OpenShift 进行监视，如[概述](container-insights-overview.md)一文所述，但以下功能除外：

- 实时数据（预览版）
- 从群集节点和 Pod [收集指标](container-insights-update-metrics.md)并将其存储在 Azure Monitor 指标数据库中

## <a name="prerequisites"></a>必备条件

- [Log Analytics 工作区](../logs/design-logs-deployment.md)。

    容器见解支持在 Azure [产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)中列出的区域中的 Log Analytics 工作区。 若要创建你自己的工作区，可通过 [Azure 资源管理器](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或 [Azure 门户](../logs/quick-create-workspace.md)进行创建。

- 若要启用和访问容器见解中的功能，你至少需要是 Azure 订阅中的 Azure 参与者角色的成员，并且需要是配置了容器见解的 Log Analytics 工作区的 [Log Analytics 参与者](../logs/manage-access.md#manage-access-using-azure-permissions)角色的成员。

- 若要查看监视数据，你需要是配置了容器见解的 Log Analytics 工作区中 [Log Analytics 读者](../logs/manage-access.md#manage-access-using-azure-permissions)角色的成员。

## <a name="identify-your-log-analytics-workspace-id"></a>标识 Log Analytics 工作区 ID

 若要与现有 Log Analytics 工作区集成，请首先标识 Log Analytics 工作区的完整资源 ID。 当你使用 Azure 资源管理器模板方法启用监视时，参数 `workspaceResourceId` 需要工作区的资源 ID。

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板为新群集启用

执行以下步骤对已启用监视的 Azure Red Hat OpenShift 群集进行部署。 继续之前，请查看教程[创建 Azure Red Hat OpenShift 群集](../../openshift/tutorial-create-cluster.md)，了解需要配置的依赖项，以便正确设置环境。

此方法包含两个 JSON 模板。 一个模板指定用于部署群集（已启用监视）的配置，另一个模板包含参数值，通过配置这些参数值可指定以下内容：

- Azure Red Hat OpenShift 群集资源 ID。

- 在其中部署群集的资源组。

- 执行创建新 ID 步骤后记下的或已经创建的 [Azure Active Directory 租户 ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant)。

- 执行创建新 ID 步骤后记下的或已经创建的 [Azure Active Directory 客户端应用程序 ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration)。

- 执行创建新密码步骤后记下的或已经创建的 [Azure Active Directory 客户端密码](../../openshift/howto-aad-app-configuration.md#create-a-client-secret)。

- 执行创建新组步骤后记下的或已经创建的 [Azure AD 安全组](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group)。

- 现有 Log Analytics 工作区的资源 ID。 请参阅[标识你的 Log Analytics 工作区 ID](#identify-your-log-analytics-workspace-id)，了解如何获取此信息。

- 要在群集中创建的主节点的数目。

- 代理池配置文件中的计算节点数。

- 代理池配置文件中的基础结构节点数。

如果不熟悉使用模板部署资源的概念，请参阅：

- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.65 版或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 将 Azure 资源管理器模板和参数文件下载并保存到本地文件夹，以使用以下命令创建带有监视加载项的群集：

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. 登录到 Azure

    ```azurecli
    az login
    ```

    如果你有权访问多个订阅，请运行 `az account set -s {subscription ID}`（将 `{subscription ID}` 替换为要使用的订阅）。

3. 如果还没有资源组，请为群集创建一个。 有关支持 Azure 上的 OpenShift 的 Azure 区域列表，请参阅[支持的区域](../../openshift/supported-resources.md#azure-regions)。

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. 编辑 JSON 参数文件 newClusterWithMonitoringParam.json 并更新以下值：

    - *location*
    - *clusterName*
    - aadTenantId
    - aadClientId
    - aadClientSecret
    - aadCustomerAdminGroupId
    - *workspaceResourceId*
    - masterNodeCount
    - computeNodeCount
    - infraNodeCount

5. 以下步骤使用 Azure CLI 对已启用监视的群集进行部署。

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    输出如下所示：

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>针对现有群集启用

执行以下步骤启用对 Azure 中部署的 Azure Red Hat OpenShift 群集的监视。 可以通过 Azure 门户或使用所提供的模板来实现此目的。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户菜单上或在门户主页中，选择“Azure Monitor”。 在“见解”部分，选择“容器”。 

3. 在“监视 - 容器”页上，选择“非监视群集”。

4. 从未受监视的群集列表中找到群集，然后单击“启用”。 可以通过在“群集类型”列下查找值 ARO 来确定列表中的结果 。

5. 在“加入到容器见解”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区。  
    列表预先选择了群集在订阅中部署到的默认工作区和位置。

    ![对未受监视的群集启用监视](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >如果想要创建新的 Log Analytics 工作区用于存储来自群集的监视数据，请按照[创建 Log Analytics 工作区](../logs/quick-create-workspace.md)中的说明进行操作。 确保在部署 RedHat OpenShift 群集的同一订阅中创建工作区。

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="enable-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板来启用

此方法包含两个 JSON 模板。 一个模板指定用于启用监视的配置，另一个模板包含参数值，通过配置这些参数值可指定：

- Azure RedHat OpenShift 群集资源 ID。

- 在其中部署群集的资源组。

- Log Analytics 工作区。 请参阅[标识你的 Log Analytics 工作区 ID](#identify-your-log-analytics-workspace-id)，了解如何获取此信息。

如果不熟悉使用模板部署资源的概念，请参阅：

- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.65 版或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 下载模板及参数文件，以使用以下命令更新带有监视加载项的群集：

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. 登录到 Azure

    ```azurecli
    az login
    ```

    如果你有权访问多个订阅，请运行 `az account set -s {subscription ID}`（将 `{subscription ID}` 替换为要使用的订阅）。

3. 指定 Azure RedHat OpenShift 群集的订阅。

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. 运行以下命令以确定群集位置和资源 ID：

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. 编辑 JSON 参数文件 existingClusterParam.json 并更新值 aroResourceId 和 aroResourceLocation 。 **workspaceResourceId** 的值是 Log Analytics 工作区的完整资源 ID，其中包含工作区名称。

6. 若要使用 Azure CLI 进行部署，请运行下列命令：

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    输出如下所示：

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>后续步骤

- 有关启用监视来收集 RedHat OpenShift 群集及其中运行的工作负载的运行状况和资源利用率的信息，请参阅[如何使用](container-insights-analyze.md)容器见解。

- 默认情况下，容器化代理会收集在所有命名空间（kube-system 除外）中运行的所有容器的 stdout/stderr 容器日志。 若要配置特定于某个或某些命名空间的容器日志收集，请参阅[容器见解代理配置](container-insights-agent-config.md)，为 ConfigMap 配置文件配置所需的数据收集设置。

- 若要从群集中抓取和分析 Prometheus 指标，请参阅[配置 Prometheus 指标抓取](container-insights-prometheus-integration.md)

- 若要了解如何停止使用容器见解监视群集，请参阅[如何停止监视 Azure Red Hat OpenShift 群集](./container-insights-optout-openshift-v3.md)。