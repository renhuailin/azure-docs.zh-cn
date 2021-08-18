---
title: 为 Azure Arc 设置应用服务、Functions 和逻辑应用
description: 对于启用了 Azure Arc 的 Kubernetes 群集，请了解如何启用应用服务应用、函数应用和逻辑应用。
ms.topic: article
ms.date: 08/17/2021
ms.openlocfilehash: f6d917a9bd18c16e283f8c61e6cb6d15fcd4882f
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396977"
---
# <a name="set-up-an-azure-arc-enabled-kubernetes-cluster-to-run-app-service-functions-and-logic-apps-preview"></a>设置启用了 Azure Arc 的 Kubernetes 群集，以便运行应用服务、函数和逻辑应用（预览）

如果有[启用了 Azure Arc 的 Kubernetes 群集](../azure-arc/kubernetes/overview.md)，可使用它创建[启用了应用服务的自定义位置](overview-arc-integration.md)，并部署 Web 应用、函数应用和逻辑应用。

启用了 Azure Arc 的 Kubernetes 可让本地或云 Kubernetes 群集对 Azure 中的应用服务、Functions 和逻辑应用可见。 你可像在另一个 Azure 区域中那样创建应用并进行部署。

## <a name="prerequisites"></a>先决条件

若没有 Azure 帐户，可[立即注册](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)免费帐户。

<!-- ## Prerequisites

- Create a Kubernetes cluster in a supported Kubernetes distribution and connect it to Azure Arc in a supported region. See [Public preview limitations](overview-arc-integration.md#public-preview-limitations).
- [Install Azure CLI](/cli/azure/install-azure-cli), or use the [Azure Cloud Shell](../cloud-shell/overview.md).
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/). It's also preinstalled in the Azure Cloud Shell.

## Obtain cluster information

Set the following environment variables based on your Kubernetes cluster deployment:

```bash
staticIp="<public-ip-address-of-the-kubernetes-cluster>"
aksClusterGroupName="<name-resource-group-with-aks-cluster>"
groupName="<name-of-resource-group-with-the-arc-connected-cluster>"
clusterName="<name-of-arc-connected-cluster>"
geomasterLocation="TODO: Why so many different locations for different resources? Shouldn't we just say create everything in the connected cluster's resource group and location?"
``` -->

## <a name="add-azure-cli-extensions"></a>添加 Azure CLI 扩展

在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中启动 Bash 环境。

[![在新窗口中启动 Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

由于这些 CLI 命令尚未成为核心 CLI 集的一部分，因此请通过以下命令添加它们。

```azurecli-interactive
az extension add --upgrade --yes --name connectedk8s
az extension add --upgrade --yes --name k8s-extension
az extension add --upgrade --yes --name customlocation
az provider register --namespace Microsoft.ExtendedLocation --wait
az provider register --namespace Microsoft.Web --wait
az provider register --namespace Microsoft.KubernetesConfiguration --wait
az extension remove --name appservice-kube
az extension add --yes --source "https://aka.ms/appsvc/appservice_kube-latest-py2.py3-none-any.whl"
```

## <a name="create-a-connected-cluster"></a>创建已连接的群集

> [!NOTE]
> 本教程使用 [Azure Kubernetes 服务 (AKS)](../aks/index.yml) 提供从头开始设置环境的具体说明。 但是，对于生产工作负载，你可能不希望在 AKS 群集上启用 Azure Arc，因为它已在 Azure 中进行管理。 以下步骤将帮助你开始了解服务，但对于生产部署，应将它们视为说明性的，而不是规范性的。 请参阅[快速入门：将现有 Kubernetes 群集连接到 Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md)，了解有关如何创建启用了 Azure Arc 的 Kubernetes 群集的常规说明。

1. 在 Azure Kubernetes 服务中使用公共 IP 创建群集。 将 `<group-name>` 替换为所需的资源组名称。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    az network public-ip create --resource-group $infra_rg --name MyPublicIP --sku STANDARD
    staticIp=$(az network public-ip show --resource-group $infra_rg --name MyPublicIP --output tsv --query ipAddress)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    $aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    $resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    $infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    az network public-ip create --resource-group $infra_rg --name MyPublicIP --sku STANDARD
    $staticIp=$(az network public-ip show --resource-group $infra_rg --name MyPublicIP --output tsv --query ipAddress)
    ```

    ---
    
2. 获取 [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) 文件并测试与群集的连接。 默认情况下，kubeconfig 文件保存到 `~/.kube/config`。

    ```azurecli-interactive
    az aks get-credentials --resource-group $aksClusterGroupName --name $aksName --admin
    
    kubectl get ns
    ```
    
3. 创建用于包含 Azure Arc 资源的资源组。 将 `<group-name>` 替换为所需的资源组名称。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    ---
    
4. 将创建的群集连接到 Azure Arc。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)


    ```powershell
    $clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```

    ---
    
5. 通过以下命令验证连接。 它应显示 `provisioningState` 属性处于 `Succeeded` 状态。 如果不是，请在一分钟后再次运行该命令。

    ```azurecli-interactive
    az connectedk8s show --resource-group $groupName --name $clusterName
    ```
    
## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

虽然不需要 [Log Analytic 工作区](../azure-monitor/logs/quick-create-workspace.md)即可在 Azure Arc 中运行应用服务，但开发人员可通过它来获得在启用了 Azure Arc 的 Kubernetes 群集中运行的应用的应用程序日志。 

1. 为简单起见，接下来请创建工作区。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    workspaceName="$groupName-workspace" # Name of the Log Analytics workspace
    
    az monitor log-analytics workspace create \
        --resource-group $groupName \
        --workspace-name $workspaceName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $workspaceName="$groupName-workspace"

    az monitor log-analytics workspace create `
        --resource-group $groupName `
        --workspace-name $workspaceName
    ```

    ---
    
2. 运行以下命令，获取现有 Log Analytics 工作区的编码工作区 ID 和共享密钥。 下一步需要用到它们。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query customerId \
        --output tsv)
    logAnalyticsWorkspaceIdEnc=$(printf %s $logAnalyticsWorkspaceId | base64) # Needed for the next step
    logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query primarySharedKey \
        --output tsv)
    logAnalyticsKeyEncWithSpace=$(printf %s $logAnalyticsKey | base64)
    logAnalyticsKeyEnc=$(echo -n "${logAnalyticsKeyEncWithSpace//[[:space:]]/}") # Needed for the next step
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query customerId `
        --output tsv)
    $logAnalyticsWorkspaceIdEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsWorkspaceId))# Needed for the next step
    $logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query primarySharedKey `
        --output tsv)
    $logAnalyticsKeyEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsKey))
    ```
    
    ---

## <a name="install-the-app-service-extension"></a>安装应用服务扩展

1. 为[应用服务扩展](overview-arc-integration.md)的所需名称、应在其中预配资源的群集命名空间以及应用服务 Kubernetes 环境的名称设置以下环境变量。 为 `<kube-environment-name>` 选择唯一名称，因为在应用服务 Kubernetes 环境中创建的应用中，它将成为其域名的一部分。

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    extensionName="appservice-ext" # Name of the App Service extension
    namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionName="appservice-ext" # Name of the App Service extension
    $namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    $kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    ---
    
2. 在启用 Log Analytics 的情况下将应用服务扩展安装到连接了 Azure Arc 的群集。 同样，虽然 Log Analytics 不是必需的，但之后无法把其添加到扩展中，所以现在添加更加容易。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az k8s-extension create \
        --resource-group $groupName \
        --name $extensionName \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --extension-type 'Microsoft.Web.Appservice' \
        --release-train stable \
        --auto-upgrade-minor-version true \
        --scope cluster \
        --release-namespace $namespace \
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" \
        --configuration-settings "appsNamespace=${namespace}" \
        --configuration-settings "clusterName=${kubeEnvironmentName}" \
        --configuration-settings "loadBalancerIp=${staticIp}" \
        --configuration-settings "keda.enabled=true" \
        --configuration-settings "buildService.storageClassName=default" \
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" \
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" \
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" \
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    az k8s-extension create `
        --resource-group $groupName `
        --name $extensionName `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --extension-type 'Microsoft.Web.Appservice' `
        --release-train stable `
        --auto-upgrade-minor-version true `
        --scope cluster `
        --release-namespace $namespace `
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" `
        --configuration-settings "appsNamespace=${namespace}" `
        --configuration-settings "clusterName=${kubeEnvironmentName}" `
        --configuration-settings "loadBalancerIp=${staticIp}" `
        --configuration-settings "keda.enabled=true" `
        --configuration-settings "buildService.storageClassName=default" `
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" `
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" `
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" `
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    ---

    > [!NOTE]
    > 若要在不集成 Log Analytics 的情况下安装扩展，请从命令中删除最后三个 `--configuration-settings` 参数。
    >

    下表描述了运行命令时的各种 `--configuration-settings` 参数：
    
    | 参数 | 说明 |
    | - | - |
    | `Microsoft.CustomLocation.ServiceAccount` | 应为将要创建的自定义位置创建的服务帐户。 建议将此值设置为值 `default`。 |
    | `appsNamespace` | 用于预配应用定义和 Pod 的命名空间。 必须与扩展版本命名空间匹配。 |
    | `clusterName` | 将针对此扩展创建的应用服务 Kubernetes 环境的名称。 |
    | `loadBalancerIp` | Kubernetes 群集的公共 IP。 应用服务应用会接收此 IP 地址上的流量。 还会通知默认 DNS 映射。 |
    | `keda.enabled` | 是否应在 Kubernetes 群集上安装 [KEDA](https://keda.sh/)。 接受“覆盖”或“跳过”`true``false`。 |
    | `buildService.storageClassName` | 生成服务用于存储项目的[存储类的名称](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class)。 像 `default` 这样的值指定了名为 `default` 的类，而不是[被标记为默认值的任何类](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)。 |
    | `buildService.storageAccessMode` | 与上面的命名存储类一起使用的[访问模式](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)。 接受“覆盖”或“跳过”`ReadWriteOnce``ReadWriteMany`。 |
    | `customConfigMap` | 将由应用服务 Kubernetes 环境设置的 ConfigMap 的名称。 目前，它必须是 `<namespace>/kube-environment-config`，其中 `<namespace>` 应替换为上面的 `appsNamespace` 的值。 |
    | `envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group` | Azure Kubernetes 服务群集所在的资源组的名称。 仅当基础群集是 Azure Kubernetes 服务时才有效并且需要。  |
    | `logProcessor.appLogs.destination` | 可选。 接受 `log-analytics`。 |
    | `logProcessor.appLogs.logAnalyticsConfig.customerId` | 仅当 `logProcessor.appLogs.destination` 设置为 `log-analytics` 时才需要。 base64 编码的 Log Analytics 工作区 ID。 此参数应配置为受保护的设置。 |
    | `logProcessor.appLogs.logAnalyticsConfig.sharedKey` | 仅当 `logProcessor.appLogs.destination` 设置为 `log-analytics` 时才需要。 base64 编码的 Log Analytics 工作区共享密钥。 此参数应配置为受保护的设置。 |
    | | |
        
3. 保存应用服务扩展的 `id` 属性以备后用。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    extensionId=$(az k8s-extension show \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --resource-group $groupName \
        --name $extensionName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionId=$(az k8s-extension show `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --resource-group $groupName `
        --name $extensionName `
        --query id `
        --output tsv)
    ```

    ---

4. 等待扩展完全安装后再继续。 你可运行以下命令，让终端会话等待安装过程完成：

    ```azurecli-interactive
    az resource wait --ids $extensionId --custom "properties.installState!='Pending'" --api-version "2020-07-01-preview"
    ```

使用 `kubectl`，可查看已在 Kubernetes 群集中创建的 Pod：

```bash
kubectl get pods -n $namespace
```

你可从[应用服务扩展创建的 Pod](overview-arc-integration.md#pods-created-by-the-app-service-extension) 中详细了解这些 Pod 及其在系统中的作用。

## <a name="create-a-custom-location"></a>创建自定义位置

Azure 中的[自定义位置](../azure-arc/kubernetes/custom-locations.md)用于分配应用服务 Kubernetes 环境。

<!-- https://github.com/MicrosoftDocs/azure-docs-pr/pull/156618 -->

1. 为自定义位置的预期名称和连接到 Azure Arc 群集的 ID 设置以下环境变量。

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    customLocationName="my-custom-location" # Name of the custom location
    
    connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $customLocationName="my-custom-location" # Name of the custom location
    
    $connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    ---
    
2. 创建自定义位置：

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az customlocation create \
        --resource-group $groupName \
        --name $customLocationName \
        --host-resource-id $connectedClusterId \
        --namespace $namespace \
        --cluster-extension-ids $extensionId
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az customlocation create `
        --resource-group $groupName `
        --name $customLocationName `
        --host-resource-id $connectedClusterId `
        --namespace $namespace `
        --cluster-extension-ids $extensionId
    ```

    ---
    
    <!-- --kubeconfig ~/.kube/config # needed for non-Azure -->

3. 通过以下命令验证自定义位置是否成功创建。 输出应显示 `provisioningState` 属性处于 `Succeeded` 状态。 如果不是，请在一分钟后再次运行该命令。

    ```azurecli-interactive
    az customlocation show --resource-group $groupName --name $customLocationName
    ```
    
4. 保存自定义位置 ID，供下一步使用。

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    customLocationId=$(az customlocation show \
        --resource-group $groupName \
        --name $customLocationName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    $customLocationId=$(az customlocation show `
        --resource-group $groupName `
        --name $customLocationName `
        --query id `
        --output tsv)
    ```

    ---
    
## <a name="create-the-app-service-kubernetes-environment"></a>创建应用服务 Kubernetes 环境

开始在自定义位置创建应用之前，需要[应用服务 Kubernetes 环境](overview-arc-integration.md#app-service-kubernetes-environment)。

1. 创建应用服务 Kubernetes 环境：
    
    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az appservice kube create \
        --resource-group $groupName \
        --name $kubeEnvironmentName \
        --custom-location $customLocationId \
        --static-ip $staticIp
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az appservice kube create `
        --resource-group $groupName `
        --name $kubeEnvironmentName `
        --custom-location $customLocationId `
        --static-ip $staticIp
    ```

    ---
    
2. 通过以下命令验证应用服务 Kubernetes 环境是否成功创建。 输出应显示 `provisioningState` 属性处于 `Succeeded` 状态。 如果不是，请在一分钟后再次运行该命令。

    ```azurecli-interactive
    az appservice kube show --resource-group $groupName --name $kubeEnvironmentName
    ```
    

## <a name="next-steps"></a>后续步骤

- [快速入门：在 Azure Arc 上创建 Web 应用](quickstart-arc.md)
- [在 Azure Arc 上创建第一个函数](../azure-functions/create-first-function-arc-cli.md)
- [在 Azure Arc 上创建第一个逻辑应用](../logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md)
