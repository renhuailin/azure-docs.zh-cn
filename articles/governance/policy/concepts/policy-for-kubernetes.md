---
title: 了解适用于 Kubernetes 的 Azure Policy
description: 了解 Azure Policy 如何使用 Rego 和 Open Policy Agent 来管理在 Azure 或本地运行 Kubernetes 的群集。
ms.date: 08/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20b3362823644ab478e2069fbc610079820302c3
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323078"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>了解用于 Kubernetes 群集的 Azure Policy

Azure Policy 将扩展 [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3，这是一个用于 [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) 的许可控制器 Webhook，它以集中、一致的方式对群集应用大规模操作和安全措施。 借助 Azure Policy，可以从一个位置管理和报告 Kubernetes 群集的符合性状态。 该加载项制定以下功能：

- 检查 Azure Policy 服务对群集的策略分配。
- 将策略定义作为[约束模板](https://github.com/open-policy-agent/gatekeeper#constraint-templates)部署到群集中，并[约束](https://github.com/open-policy-agent/gatekeeper#constraints)自定义资源。
- 向 Azure Policy 服务报告审核和符合性详细信息。

适用于 Kubernetes 的 Azure Policy 支持以下群集环境：

- [Azure Kubernetes 服务 (AKS)](../../../aks/intro-kubernetes.md)
- [已启用 Azure Arc 的 Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> 适用于 AKS 引擎和已启用 Arc 的 Kubernetes 的附加产品都为“预览版”状态。 适用于 Kubernetes 的 Azure Policy 仅支持 Linux 节点池和内置策略定义。 内置策略定义属于“Kubernetes”类别。 具有“EnforceOPAConstraint”和“EnforceRegoPolicy”效果和相关“Kubernetes 服务”类别的有限预览策略定义已被弃用。 改为配合使用“审核”、“拒绝”效果和资源提供程序模式 `Microsoft.Kubernetes.Data`。

## <a name="overview"></a>概述

若要启用 Azure Policy 并将其用于 Kubernetes 群集，请执行以下操作：

1. 配置 Kubernetes 群集并安装加载项：
   - [Azure Kubernetes 服务 (AKS)](#install-azure-policy-add-on-for-aks)
   - [已启用 Azure Arc 的 Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS 引擎](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > 有关安装的常见问题，请参阅[故障排除 - Azure Policy 加载项](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors)。

1. [了解适用于 Kubernetes 的 Azure Policy 语言](#policy-language)

1. [向 Kubernetes 群集分配内置定义](#assign-a-built-in-policy-definition)

1. [等待验证](#policy-evaluation)

## <a name="limitations"></a>限制

以下一般限制适用于 Kubernetes 群集的 Azure Policy 加载项：

- Kubernetes 版本 1.14 或更高版本支持适用于 Kubernetes 的 Azure Policy 加载项。
- 适用于 Kubernetes 的 Azure Policy 加载项只能部署到 Linux 节点池
- 仅支持内置策略定义
- 每个群集每个策略的最大不符合记录数：500
- 每个订阅的最大不符合记录数：1000000
- 不支持在 Azure Policy 加载项之外安装 Gatekeeper。 在启用 Azure Policy 加载项之前，卸载由以前的 Gatekeeper 安装的所有组件。
- [不符合性的原因](../how-to/determine-non-compliance.md#compliance-reasons)不适用于`Microsoft.Kubernetes.Data`
  [资源提供程序模式](./definition-structure.md#resource-provider-modes)。 使用[组件详细信息](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)。
- [资源提供程序模式](./definition-structure.md#resource-provider-modes)不支持[免除](./exemption-structure.md)。

以下限制仅适用于 AKS 的 Azure Policy 加载项：

- 不能同时启用 [AKS Pod 安全策略](../../../aks/use-pod-security-policies.md)和适用于 AKS 的 Azure Policy 附加产品。 有关详细信息，请参阅 [AKS Pod 安全限制](../../../aks/use-azure-policy.md)。
- 命名空间由 Azure Policy 加载项自动排除以进行评估：kube-system、gatekeeper-system 和 aks-periscope。

## <a name="recommendations"></a>建议

下面是有关如何使用 Azure Policy 加载项的常规建议：

- Azure Policy 附加产品需要三个 Gatekeeper 组件才能运行：一个审核 Pod 和两个 Webhook Pod 副本。 随着集群中 Kubernetes 资源和策略分配计数的增加，这些组件会消耗更多的资源，这就需要执行审核和强制操作。

  - 对于少于 500 个 Pod、最多 20 个约束的单个群集：每个组件 2 个 vCPU，350 MB 内存。
  - 对于超过 500 个 Pod、最多 40 个约束的单个群集：每个组件 3 个 vCPU，600 MB 内存。

- Windows Pod [不支持安全上下文](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods)。
  因此，某些 Azure Policy 定义（例如禁用根权限）不能在 Windows Pod 中升级，仅适用于 Linux Pod。

以下建议仅适用于 AKS 和 Azure Policy 加载项：

- 使用具有 `CriticalAddonsOnly` 排斥的系统节点池来计划 Gatekeeper Pod。 有关详细信息，请参阅[使用系统节点池](../../../aks/use-system-pools.md#system-and-user-node-pools)。
- 保护来自 AKS 群集的出站流量。 有关详细信息，请参阅[控制群集节点的出口流量](../../../aks/limit-egress-traffic.md)。
- 如果群集启用了 `aad-pod-identity`，节点托管标识 (NMI) pod 将修改节点的 iptable，以拦截对 Azure 实例元数据终结点的调用。 此配置意味着对元数据终结点发出的任何请求都将被 NMI 拦截，即使 pod 不使用 `aad-pod-identity`。 可以将 AzurePodIdentityException CRD 配置为通知 `aad-pod-identity` 应在不使用 NMI 进行出任何处理的情况下，代理与 CRD 中定义的标签匹配的 pod 所发起的对元数据终结点的任何请求。 应通过配置 AzurePodIdentityException CRD 在 `aad-pod-identity` 中排除在 _kube-system_ 命名空间中具有 `kubernetes.azure.com/managedby: aks` 标签的系统 pod。 有关详细信息，请参阅[禁用特定 pod 或应用程序的 aad-pod-identity](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)。
  若要配置例外情况，请安装 [mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)。

## <a name="install-azure-policy-add-on-for-aks"></a>为 AKS 安装 Azure Policy 加载项

在安装 Azure Policy 附加产品或启用任何服务功能之前，订阅必须启用“Microsoft.PolicyInsights”资源提供程序。

1. 需要安装和配置 Azure CLI 版本 2.12.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 注册资源提供程序和预览功能。

   - Azure 门户：

     注册“Microsoft.PolicyInsights”资源提供程序。 有关步骤，请参阅[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

   - Azure CLI：

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. 如果安装了有限预览策略定义，请在“策略”页下删除 AKS 群集中带有“禁用”按钮的加载项。

1. AKS 群集的版本必须是 1.14 或更高版本。 使用以下脚本验证 AKS 群集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安装 Azure CLI 2.12.0 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

完成上述先决条件步骤后，请在要管理的 AKS 群集中安装 Azure Policy 加载项。

- Azure 门户

  1. 在 Azure 门户中，选择“所有服务”，然后搜索并选择“Kubernetes 服务”，以启动 AKS 服务。

  1. 选择 AKS 群集之一。

  1. 选择“Kubernetes 服务”页左侧的“策略”。

  1. 在主页中，选择“启用加载项”按钮。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

若要验证加载项安装是否成功以及 azure-policy 和 gatekeeper Pod 是否正在运行，请运行以下命令 ：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

最后，通过运行此 Azure CLI 命令，并将 `<rg>` 替换为资源组名称，将 `<cluster-name>` 替换为 AKS 群集名称 `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>`，来验证是否已安装最新的加载项。 结果应类似于以下输出：

```output
{
        "config": null,
        "enabled": true,
        "identity": null
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>为已启用 Azure Arc 的 Kubernetes 安装 Azure Policy 附加产品（预览版）

在安装 Azure Policy 加载项或启用任何服务功能之前，订阅必须启用 Microsoft.PolicyInsights 资源提供程序并为群集服务主体创建角色分配。

1. 需要安装和配置 Azure CLI 版本 2.12.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 若要启用资源提供程序，请按照[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步骤操作，或运行 Azure CLI 或 Azure PowerShell 命令：

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Kubernetes 群集的版本必须是 1.14 或更高版本。

1. 安装 [Helm 3](https://v3.helm.sh/docs/intro/install/)。

1. 已为 Azure Arc 启用 Kubernetes 群集。有关详细信息，请参阅[将 Kubernetes 群集载入 Azure Arc](../../../azure-arc/kubernetes/quickstart-connect-cluster.md)。

1. 拥有已启用 Azure Arc 的 Kubernetes 群集的完全限定 Azure 资源 ID。

1. 打开加载项的端口。 Azure Policy 加载项使用这些域和端口提取策略定义和分配，并将群集的符合性报告回 Azure Policy。

   |域 |端口 |
   |---|---|
   |`data.policy.core.windows.net` |`443` |
   |`store.policy.core.windows.net` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. 将“策略见解数据编写器(预览版)”角色分配给已启用 Azure Arc 的 Kubernetes 群集。 将 `<subscriptionId>` 替换为你的订阅 ID，将 `<rg>` 替换为已启用 Azure Arc 的 Kubernetes 群集的资源组，并将 `<clusterName>` 替换为已启用 Azure Arc 的 Kubernetes 群集的名称。 对于安装步骤，请跟踪“AppId”、“密码”和“租户”的返回值。

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   上述命令的示例输出如下：

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

完成上述先决条件步骤后，请在已启用 Azure Arc 的 Kubernetes 群集中安装 Azure Policy 加载项：

1. 将 Azure Policy 加载项存储库添加到 Helm：

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. 使用 Helm 图表安装 Azure Policy 加载项：

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   有关加载项 Helm 图表安装内容的详细信息，请参阅 GitHub 上的 [Azure Policy 加载项 Helm 图表定义](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters)。

若要验证加载项安装是否成功以及 azure-policy 和 gatekeeper Pod 是否正在运行，请运行以下命令 ：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>为 AKS 引擎安装 Azure Policy 附加产品（预览版）

在安装 Azure Policy 加载项或启用任何服务功能之前，订阅必须启用 Microsoft.PolicyInsights 资源提供程序并为群集服务主体创建角色分配。

1. 需要安装并配置 Azure CLI 2.0.62 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 若要启用资源提供程序，请按照[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步骤操作，或运行 Azure CLI 或 Azure PowerShell 命令：

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. 为群集服务主体创建角色分配。

   - 如果你不知道群集服务主体应用 ID，请使用以下命令查找。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - 使用 Azure CLI 将“策略见解数据编写器(预览版)”角色分配到群集服务主体应用 ID（上一步中的 aadClientID 值）。 将 `<subscriptionId>` 替换为订阅 ID，将 `<aks engine cluster resource group>` 替换为 AKS 引擎自托管 Kubernetes 群集所在的资源组。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

完成上述先决条件步骤后，安装 Azure Policy 加载项。 可在 AKS 引擎的创建或更新周期中进行安装，也可以作为对现有群集的独立操作进行安装。

- 在创建或更新周期期间安装

  若要在创建新的自托管群集或更新现有群集时启用 Azure Policy 加载项，请包含 AKS 引擎的[加载项](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy)属性群集定义。

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  有关详细信息，请参阅外部指南 [AKS 引擎群集定义](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。

- 在现有群集中使用 Helm 图表进行安装

  使用以下步骤来准备群集并安装加载项：

  1. 安装 [Helm 3](https://v3.helm.sh/docs/intro/install/)。

  1. 将 Azure Policy 存储库添加到 Helm。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     有关详细信息，请参阅 [Helm 图表 - 快速入门指南](https://helm.sh/docs/using_helm/#quickstart-guide)。

  1. 使用 Helm 图表安装加载项。 将 `<subscriptionId>` 替换为订阅 ID，将 `<aks engine cluster resource group>` 替换为 AKS 引擎自托管 Kubernetes 群集所在的资源组。

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     有关加载项 Helm 图表安装内容的详细信息，请参阅 GitHub 上的 [Azure Policy 加载项 Helm 图表定义](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine)。

     > [!NOTE]
     > 由于 Azure Policy 加载项与资源组 ID 之间的关系，Azure Policy 对每个资源组仅支持一个 AKS 引擎群集。

若要验证加载项安装是否成功以及 azure-policy 和 gatekeeper Pod 是否正在运行，请运行以下命令 ：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Policy 语言

用于管理 Kubernetes 的 Azure Policy 语言结构遵循现有策略定义。 使用 `Microsoft.Kubernetes.Data` 的[资源提供程序模式](./definition-structure.md#resource-provider-modes)，会使用效果[审核](./effects.md#audit)和[拒绝](./effects.md#deny)来管理你的 Kubernetes 群集。 “审核”和“拒绝”必须提供特定于使用 [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) 和 Gatekeeper v3 的详细信息属性。

作为策略定义中 details.constraintTemplate 和 details.constraint 属性的一部分，Azure Policy 将这些 [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) 的 URI 传递给加载项 。 Rego 是 OPA 和 Gatekeeper 支持的语言，用于验证对 Kubernetes 群集的请求。 通过支持 Kubernetes 管理的现有标准，Azure Policy 可重用现有规则并将其与 Azure Policy 配对以获得统一的云符合性报告体验。 有关详细信息，请参阅[什么是 Rego？](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)。

## <a name="assign-a-built-in-policy-definition"></a>分配内置策略定义

若要为 Kubernetes 群集分配策略定义，系统必须为你分配适当的 Azure 基于角色的访问控制 (Azure RBAC) 策略分配操作。 Azure 内置角色“资源策略参与者”和“所有者”可进行这些操作。 若要了解详细信息，请参阅 [Azure Policy 中的 Azure RBAC 权限](../overview.md#azure-rbac-permissions-in-azure-policy)。

通过以下步骤，使用 Azure 门户查找用于管理群集的内置策略定义：

1. 在 Azure 门户中启动 Azure Policy 服务。 在左窗格中选择“所有服务”，然后搜索并选择“策略” 。

1. 在“Azure Policy”页面的左侧窗格中，选择“定义”。

1. 从“类别”下拉列表框中，使用“全选”清除筛选器，然后选择“Kubernetes” 。

1. 选择策略定义，然后选择“分配”按钮。

1. 将“范围”设置为将应用策略分配的 Kubernetes 群集的管理组、订阅或资源组。

   > [!NOTE]
   > 为 Kubernetes 定义分配 Azure Policy 时，“范围”必须包括群集资源。 对于 AKS 引擎群集，“范围”必须是群集的资源组。

1. 为策略分配提供可以用于轻松识别它的“名称”和“说明”。

1. 将[策略实施](./assignment-structure.md#enforcement-mode)设置为下面的一个值。

   - 已启用 - 在群集上强制实施策略。 拒绝带有冲突的 Kubernetes 许可请求。

   - 已禁用 - 不在群集上强制实施策略。 不拒绝带有冲突的 Kubernetes 许可请求。 符合性评估结果仍可用。 向运行群集推出新策略定义时，“已禁用”选项可用于测试策略定义，因为不拒绝带有冲突的许可请求。

1. 选择“**下一页**”。

1. 设置参数值

   - 若要从策略评估中排除 Kubernetes 命名空间，请在参数“命名空间排除”中指定命名空间的列表。 建议排除以下内容：kube-system、gatekeeper-system 和 azure-arc。

1. 选择“查看 + 创建”。

或者，使用[分配策略 - 门户](../assign-policy-portal.md)快速入门来查找和分配 Kubernetes 策略。 搜索 Kubernetes 策略定义，而不是示例“audit vms”。

> [!IMPORTANT]
> 内置策略定义适用于 Kubernetes 类别的 Kubernetes 群集。 有关内置策略定义的列表，请参阅 [Kubernetes 示例](../samples/built-in-policies.md#kubernetes)。

## <a name="policy-evaluation"></a>策略评估

加载项每 15 分钟使用 Azure Policy 服务签入一次，查看策略分配中的更改。
在此刷新周期内，加载项将检查更改。 这些更改将触发约束模板和约束的创建、更新或删除。

在 Kubernetes 群集中，如果命名空间具有以下任意一种标签，则不拒绝带有冲突的许可请求。 符合性评估结果仍可用。

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> 虽然群集管理员可能有权创建和更新 Azure Policy 加载项安装的约束模板和约束资源，但这些情况不受支持，因为手动更新会被覆盖。 Gatekeeper 会继续评估在安装加载项和分配 Azure Policy 策略定义之前已存在的策略。

每隔 15 分钟，加载项就会调用对群集的完全扫描。 在收集完全扫描的详细信息和 Gatekeeper 对群集尝试更改的所有实时评估后，加载项将结果报告回 Azure Policy，以便像所有 Azure Policy 分配一样包含在[符合性详细信息](../how-to/get-compliance-data.md)中。 在审核周期中，仅返回活动策略分配的结果。 审核结果也可以视为已失败约束的“状态”字段中列出的[冲突](https://github.com/open-policy-agent/gatekeeper#audit)。 有关不符合资源的详细信息，请参阅[资源提供程序模式的组件详细信息](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)。

> [!NOTE]
> 适用于 Kubernetes 群集的 Azure Policy 中的每个符合性报告都包含过去 45 分钟内的所有冲突。 时间戳指示发生冲突的时间。

一些其他注意事项：

- 如果未向 Azure 安全中心注册群集订阅，则 Azure 安全中心 Kubernetes 策略会自动应用于群集。

- 在包含现有 Kubernetes 资源的群集上应用拒绝策略时，不符合新策略的任何预先存在的资源将继续运行。 如果在其他节点上重新计划了不符合的资源，则 Gatekeeper 会阻止资源创建。

- 如果群集具有用于验证资源的拒绝策略，则在创建部署时，用户将看不到拒绝消息。 例如，考虑包含副本集和 Pod 的 Kubernetes 部署。 用户执行 `kubectl describe deployment $MY_DEPLOYMENT` 时，不会返回拒绝消息作为事件的一部分。 但是，`kubectl describe replicasets.apps $MY_DEPLOYMENT` 会返回与拒绝关联的事件。

## <a name="logging"></a>日志记录

作为 Kubernetes 控制器/容器，azure-policy 和 gatekeeper Pod 在 Kubernetes 群集中保留日志。 日志可以在 Kubernetes 群集的“见解”页中公开。 有关详细信息，请参阅[使用适用于容器的 Azure Monitor 监视 Kubernetes 群集性能](../../../azure-monitor/containers/container-insights-analyze.md)。

若要查看加载项日志，请使用 `kubectl`：

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

有关详细信息，请参阅 Gatekeeper 文档中的[调试 Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging)。

## <a name="troubleshooting-the-add-on"></a>对加载项进行故障排除

有关如何对适用于 Kubernetes 的加载项进行故障排除的详细信息，请参阅 Azure Policy 故障排除一文的 [Kubernetes 部分](../troubleshoot/general.md#add-on-for-kubernetes-general-errors)。

## <a name="remove-the-add-on"></a>删除加载项

### <a name="remove-the-add-on-from-aks"></a>从 AKS 删除加载项

若要从 AKS 群集中删除 Azure Policy 加载项，请使用 Azure 门户或 Azure CLI：

- Azure 门户

  1. 在 Azure 门户中，选择“所有服务”，然后搜索并选择“Kubernetes 服务”，以启动 AKS 服务。

  1. 选择要在其中禁用 Azure Policy 加载项的 AKS 群集。

  1. 选择“Kubernetes 服务”页左侧的“策略”。

  1. 在主页中，选择“禁用加载项”按钮。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>从已启用 Azure Arc 的 Kubernetes 中删除加载项

若要从已启用 Azure Arc 的 Kubernetes 群集中删除 Azure Policy 加载项和 Gatekeeper，请运行以下 Helm 命令：

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>从 AKS 引擎删除加载项

若要从 AKS 引擎群集中删除 Azure Policy 加载项和 Gatekeeper，请使用与加载项的安装方式一致的方法：

- 如果设置 AKS 引擎群集定义中的“加载项”属性进行安装：

  将 azure-policy 的“加载项”属性更改为 false 后，将群集定义重新部署到 AKS 引擎：

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  有关详细信息，请参阅 [AKS 引擎 - 禁用 Azure Policy 加载项](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)。

- 如果安装有 Helm 图表，请运行以下 Helm 命令：

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy 加载项收集的诊断数据

适用于 Kubernetes 的 Azure Policy 加载项收集有限的群集诊断数据。 该诊断数据是与软件和性能相关的重要技术数据。 可通过以下方式使用该数据：

- 使 Azure Policy 加载项保持最新
- 使 Azure Policy 加载项保持安全、可靠和高性能
- 改进 Azure Policy 加载项 - 通过对加载项使用的聚合分析

加载项收集的信息不是个人数据。 当前正在收集以下详细信息：

- Azure Policy 加载项代理版本
- 群集类型
- 群集区域
- 群集资源组
- 群集资源 ID
- 群集订阅 ID
- 群集 OS（示例：Linux）
- 群集城市（示例：西雅图）
- 群集州或省/自治区/直辖市（示例：华盛顿州）
- 群集国家或地区（示例：美国）
- 在策略评估的代理安装期间，Azure Policy 加载项遇到异常/错误
- Azure Policy 加载项未安装的 Gatekeeper 策略数

## <a name="next-steps"></a>后续步骤

- 查看 [Azure Policy 示例](../samples/index.md)中的示例。
- 查看[策略定义结构](definition-structure.md)。
- 查看[了解策略效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
