---
title: 使用容器见解配置启用了 Azure Arc 的 Kubernetes 群集 | Microsoft Docs
description: 本文介绍如何在启用了 Azure Arc 的 Kubernetes 群集上配置通过容器见解进行监视的功能。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 307f9d9928042410dc9b4443aba5c019c592980c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711291"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>对已启用 Azure Arc 的 Kubernetes 群集启用监视

容器见解针对 Azure Kubernetes 服务 (AKS) 和 AKS 引擎群集提供了丰富的监视体验。 本文介绍如何对承载在 Azure 外部且启用了 Azure Arc 的 Kubernetes 群集启用监视，以实现类似的监视体验。

可以使用 PowerShell 或 Bash 脚本为一个或多个现有 Kubernetes 部署启用容器见解。

## <a name="supported-configurations"></a>支持的配置

容器见解支持对启用了 Azure Arc 的 Kubernetes（预览版）进行监视，如[概述](container-insights-overview.md)一文所述，但以下功能除外：

- 实时数据（预览版）

容器见解正式支持以下功能：

- Kubernetes 和支持策略的版本与 [AKS 支持](../../aks/supported-kubernetes-versions.md)的版本相同。

- 支持以下容器运行时：Docker、Moby 和 CRI 兼容的运行时，例如 CRI-O 和 ContainerD。

- 支持的适用于主节点和工作器节点的 Linux OS 版本包括：Ubuntu（18.04 LTS 和 16.04 LTS）。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保做好以下准备：

- Log Analytics 工作区。

    容器见解支持在 Azure [产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)中列出的区域中的 Log Analytics 工作区。 若要创建你自己的工作区，可通过 [Azure 资源管理器](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或 [Azure 门户](../logs/quick-create-workspace.md)进行创建。

- 若要启用和访问容器见解中的功能，你至少需要是 Azure 订阅中的 Azure 参与者角色的成员，并且需要是配置了容器见解的 Log Analytics 工作区的 [Log Analytics 参与者](../logs/manage-access.md#manage-access-using-azure-permissions)角色的成员。

- 你是 Azure Arc 群集资源上的[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色的成员。

- 若要查看监视数据，你需要是配置了容器见解的 Log Analytics 工作区中 [Log Analytics 读者](../logs/manage-access.md#manage-access-using-azure-permissions)角色的成员。

- [HELM 客户端](https://helm.sh/docs/using_helm/)，用于为指定的 Kubernetes 群集载入容器见解 chart。

- 适用于 Linux 的 Log Analytics 代理的容器化版本与 Azure Monitor 进行通信需要以下代理和防火墙配置信息：

    |代理资源|端口 |
    |------|---------|
    |`*.ods.opinsights.azure.com` |端口 443 |
    |`*.oms.opinsights.azure.com` |端口 443 |
    |`*.dc.services.visualstudio.com` |端口 443 |

- 容器化代理要求在群集的所有节点上打开 Kubelet 的 `cAdvisor secure port: 10250` 或 `unsecure port :10255` 以收集性能指标。 建议你在 Kubelet 的 cAdvisor 上配置 `secure port: 10250`（如果尚未配置）。

- 容器化代理要求在容器上指定以下环境变量，以便与群集中的 Kubernetes API 服务通信以收集清单数据 - `KUBERNETES_SERVICE_HOST` 和 `KUBERNETES_PORT_443_TCP_PORT`。

    >[!IMPORTANT]
    >监视启用了 Arc 的 Kubernetes 群集时支持使用的最低代理版本是 ciprod04162020 或更高版本。

- 如果你使用 PowerShell 脚本方法启用监视，则需要 [Powershell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true)。

- 如果你使用 Bash 脚本方法启用监视，则需要 [Bash 版本 4](https://www.gnu.org/software/bash/)。

## <a name="identify-workspace-resource-id"></a>确定工作区资源 ID

若要使用之前下载的 PowerShell 或 bash 脚本来启用群集监视并与现有 Log Analytics 工作区集成，请执行以下步骤来首先确定 Log Analytics 工作区的完整资源 ID。 当你运行命令来对指定的工作区启用监视加载项时，这是 `workspaceResourceId` 参数所必需的。 如果你没有可指定的工作区，则可以跳过包括 `workspaceResourceId` 参数这一步，让脚本为你创建一个新的工作区。

1. 使用以下命令列出你有权访问的所有订阅：

    ```azurecli
    az account list --all -o table
    ```

    输出如下所示：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    复制 **SubscriptionId** 的值。

2. 使用以下命令切换到托管 Log Analytics 工作区的订阅：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 以下示例以默认 JSON 格式显示订阅中的工作区列表。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在输出中，找到工作区名称，然后在字段 **ID** 下复制该 Log Analytics 工作区的完整资源 ID。

## <a name="enable-monitoring-using-powershell"></a>使用 PowerShell 启用监视

1. 下载脚本并将其保存到本地文件夹中，该脚本使用以下命令为你的群集配置监视加载项：

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. 通过设置 `subscriptionId`、`resourceGroupName` 和 `clusterName`（表示已启用 Azure Arc 的 Kubernetes 群集资源的资源 ID）的对应值来配置 `$azureArcClusterResourceId` 变量。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 通过运行 `kubectl config get-contexts` 命令，使用群集的 kube-context 配置 `$kubeContext` 变量。 若要使用当前上下文，请将值设置为 `""`。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 如果要使用现有的 Azure Monitor Log Analytics 工作区，请将变量 `$logAnalyticsWorkspaceResourceId` 配置为表示工作区资源 ID 的相应值。 否则，请将该变量设置为 `""`，脚本将在群集订阅的默认资源组中创建一个默认工作区（如果区域中尚不存在工作区）。 创建的默认工作区类似于 *DefaultWorkspace-\<SubscriptionID>-\<Region>* 格式。

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. 如果启用了 Arc 的 Kubernetes 群集通过代理服务器进行通信，请将变量 `$proxyEndpoint` 配置为代理服务器的 URL。 如果群集不是通过代理服务器进行通信，则可以将该值设置为 `""`。  有关详细信息，请参阅本文稍后的[配置代理终结点](#configure-proxy-endpoint)。

6. 运行以下命令来启用监视。

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="using-service-principal"></a>使用服务主体
脚本 enable-monitoring.ps1 使用交互式设备登录。 如果你更喜欢非交互式登录，则可以使用现有服务主体，也可以创建一个具有所需权限的新服务主体，如[先决条件](#prerequisites)中所述。 若要使用服务主体，必须将 $servicePrincipalClientId、$servicePrincipalClientSecret 和 $tenantId 参数（包含要使用的服务主体的值）传递给 enable-monitoring.ps1 脚本。

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

仅当使用与 Arc K8s 连接群集资源不同的 Azure 订阅中的现有 Log Analytics 工作区时，以下角色分配才适用。

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

例如：

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>使用 bash 脚本进行启用

使用提供的 bash 脚本执行以下步骤来启用监视。

1. 下载脚本并将其保存到本地文件夹中，该脚本使用以下命令为你的群集配置监视加载项：

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. 通过设置 `subscriptionId`、`resourceGroupName` 和 `clusterName`（表示已启用 Azure Arc 的 Kubernetes 群集资源的资源 ID）的对应值来配置 `azureArcClusterResourceId` 变量。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 通过运行 `kubectl config get-contexts` 命令，使用群集的 kube-context 配置 `kubeContext` 变量。 若要使用当前上下文，请将值设置为 `""`。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 如果要使用现有的 Azure Monitor Log Analytics 工作区，请将变量 `logAnalyticsWorkspaceResourceId` 配置为表示工作区资源 ID 的相应值。 否则，请将该变量设置为 `""`，脚本将在群集订阅的默认资源组中创建一个默认工作区（如果区域中尚不存在工作区）。 创建的默认工作区类似于 *DefaultWorkspace-\<SubscriptionID>-\<Region>* 格式。

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. 如果启用了 Arc 的 Kubernetes 群集通过代理服务器进行通信，请将变量 `proxyEndpoint` 配置为代理服务器的 URL。 如果群集不是通过代理服务器进行通信，则可以将该值设置为 `""`。 有关详细信息，请参阅本文稍后的[配置代理终结点](#configure-proxy-endpoint)。

6. 若要在群集上启用监视，可以使用根据你的部署方案提供的各种命令。

    运行以下命令以使用默认选项（例如使用当前 kube-context）启用监视，创建默认 Log Analytics 工作区，而不指定代理服务器：

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    运行以下命令以创建默认 Log Analytics 工作区，而不指定代理服务器：

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    运行以下命令以使用现有 Log Analytics 工作区，而不指定代理服务器：

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    运行以下命令以使用现有 Log Analytics 工作区并指定代理服务器：

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="using-service-principal"></a>使用服务主体
bash 脚本 enable-monitoring.sh 使用交互式设备登录。 如果你更喜欢非交互式登录，则可以使用现有服务主体，也可以创建一个具有所需权限的新服务主体，如[先决条件](#prerequisites)中所述。 若要使用服务主体，你必须将要使用的服务主体的 --client-id、--client-secret 和 --tenant-id 值传递给 enable-monitoring.sh bash 脚本。

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

仅当使用与 Arc K8s 连接群集资源不同的 Azure 订阅中的现有 Log Analytics 工作区时，以下角色分配才适用。

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

例如：

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>配置代理终结点

借助容器见解的容器化代理，你可以配置代理终结点，使其能够通过代理服务器进行通信。 容器化代理与 Azure Monitor 之间的通信可以通过 HTTP 或 HTTPS 代理服务器进行，并且支持匿名身份验证和基本身份验证（用户名/密码）。

代理配置值具有以下语法：`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>如果代理服务器不需要身份验证，那么你仍需指定伪用户名/密码。 这可以是任何用户名或密码。

|属性| 说明 |
|--------|-------------|
|协议 | http 或 https |
|user | 用于代理身份验证的可选用户名 |
|password | 用于代理身份验证的可选密码 |
|proxyhost | 代理服务器的地址或 FQDN |
|port | 代理服务器的可选端口号 |

例如： `http://user01:password@proxy01.contoso.com:3128`

如果将协议指定为“http”，则使用 SSL/TLS 安全连接创建 HTTP 请求。 代理服务器必须支持 SSL/TLS 协议。

### <a name="configure-using-powershell"></a>使用 PowerShell 进行配置

指定代理服务器的用户名和密码、IP 地址或 FQDN，以及端口号。 例如：

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>使用 bash 进行配置

指定代理服务器的用户名和密码、IP 地址或 FQDN，以及端口号。 例如：

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>后续步骤

- 启用监视功能以收集启用了 Arc 的 Kubernetes 群集及其上运行的工作负荷的运行状况和资源利用率后，请了解[如何使用容器见解](container-insights-analyze.md)。

- 默认情况下，容器化代理会收集在所有命名空间（kube-system 除外）中运行的所有容器的 stdout/stderr 容器日志。 若要配置特定于某个或某些命名空间的容器日志收集，请参阅[容器见解代理配置](container-insights-agent-config.md)，为 ConfigMap 配置文件配置所需的数据收集设置。

- 若要从群集中抓取和分析 Prometheus 指标，请参阅[配置 Prometheus 指标抓取](container-insights-prometheus-integration.md)。

- 若要了解如何停止使用容器见解监视启用了 Arc 的 Kubernetes 群集，请参阅[如何停止监视混合群集](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)。