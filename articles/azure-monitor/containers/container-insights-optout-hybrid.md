---
title: 如何停止监视混合 Kubernetes 群集 | Microsoft Docs
description: 本文介绍如何通过容器见解停止监视混合 Kubernetes 群集。
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b8f548b8e9440804ae6a7ff293c35e7107cfc4c8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708927"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>如何停止监视混合群集

启用对 Kubernetes 群集的监视后，如果决定不再监视该群集，则可以使用容器见解停止监视该群集。 本文介绍如何在以下环境中实现此目的：

- Azure 和 Azure Stack 的 AKS 引擎
- OpenShift 版本 4 及更高版本
- 已启用 Azure Arc 的 Kubernetes（预览版）

## <a name="how-to-stop-monitoring-using-helm"></a>如何使用 Helm 停止监视

以下步骤适用于以下环境：

- Azure 和 Azure Stack 的 AKS 引擎
- OpenShift 版本 4 及更高版本

1. 若要先确定群集上安装的容器见解的 Helm chart 版本，请运行以下 helm 命令。

    ```
    helm list
    ```

    输出如下所示：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* 表示用于“容器见解”的 helm 图表版本。

2. 若要删除图表版本，请运行以下 helm 命令。

    `helm delete <releaseName>`

    示例：

    `helm delete azmon-containers-release-1`

    允许该命令后，将从群集中删除发布。 用户可通过再次运行命令 `helm list` 来验证。

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

配置更改可能需要几分钟才能完成。 由于 Helm 会在用户删除发布后对其进行跟踪，因此可以审核群集的历史记录，甚至还可以使用 `helm rollback` 撤消删除发布。

## <a name="how-to-stop-monitoring-on-azure-arc-enabled-kubernetes"></a>如何停止监视已启用 Azure Arc 的 Kubernetes

### <a name="using-powershell"></a>使用 PowerShell

1. 下载脚本并将其保存到本地文件夹中，该脚本使用以下命令为你的群集配置监视加载项：

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. 通过设置 `subscriptionId`、`resourceGroupName` 和 `clusterName`（表示已启用 Azure Arc 的 Kubernetes 群集资源的资源 ID）的对应值来配置 `$azureArcClusterResourceId` 变量。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 通过运行 `kubectl config get-contexts` 命令，使用群集的 kube-context 配置 `$kubeContext` 变量。 若要使用当前上下文，请将值设置为 `""`。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 运行以下命令以停止监视群集。

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>使用服务主体
脚本 disable-monitoring.ps1 使用交互式设备登录。 如果你更喜欢非交互式登录，则可以使用现有服务主体，也可以创建一个具有所需权限的新服务主体，如[先决条件](container-insights-enable-arc-enabled-clusters.md#prerequisites)中所述。 若要使用服务主体，必须将 $servicePrincipalClientId、$servicePrincipalClientSecret 和 $tenantId 参数（包含要使用的服务主体的值）传递给 enable-monitoring.ps1 脚本。

```powershell
$subscriptionId = "<subscription Id of the Azure Arc-connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

例如：

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>使用 Bash

1. 下载脚本并将其保存到本地文件夹中，该脚本使用以下命令为你的群集配置监视加载项：

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. 通过设置 `subscriptionId`、`resourceGroupName` 和 `clusterName`（表示已启用 Azure Arc 的 Kubernetes 群集资源的资源 ID）的对应值来配置 `azureArcClusterResourceId` 变量。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 通过运行命令 `kubectl config get-contexts`，使用群集的 kube-context 配置 `kubeContext` 变量。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 若要停止监视群集，可以使用根据部署方案提供的不同命令。

    运行以下命令以使用当前上下文停止监视群集。

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    运行以下命令以通过指定上下文来停止监视群集

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>使用服务主体
bash 脚本 disable-monitoring.sh 使用交互式设备登录。 如果你更喜欢非交互式登录，则可以使用现有服务主体，也可以创建一个具有所需权限的新服务主体，如[先决条件](container-insights-enable-arc-enabled-clusters.md#prerequisites)中所述。 若要使用服务主体，你必须将要使用的服务主体的 --client-id、--client-secret 和 --tenant-id 值传递给 enable-monitoring.sh bash 脚本。

```bash
subscriptionId="<subscription Id of the Azure Arc-connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

例如：

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>后续步骤

如果创建的“日志分析工作区”仅用于支持监视群集，且不需要再使用，则需要手动删除它。 如果不熟悉如何删除工作区，请参阅[删除 Azure Log Analytics 工作区](../logs/delete-workspace.md)。
