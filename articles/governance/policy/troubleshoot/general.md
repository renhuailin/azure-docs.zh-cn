---
title: 排查常见错误
description: 了解如何排查为 Kubernetes 创建策略定义、各种 Sdk 和外接程序时遇到的问题。
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592611"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>使用 Azure 策略排查错误

创建策略定义、使用 Sdk 或设置 [Azure policy For Kubernetes](../concepts/policy-for-kubernetes.md) 外接程序时，可能会遇到错误。 本文介绍了可能发生的各种常见错误，并建议解决这些错误的方法。

## <a name="find-error-details"></a>查找错误详细信息

错误详细信息的位置取决于所使用的 Azure 策略的方面。

- 如果你使用的是自定义策略，请转到 Azure 门户获取有关该架构的 linting 反馈，或查看生成的 [符合性数据](../how-to/get-compliance-data.md) 以了解资源的评估方式。
- 如果使用的是各种 Sdk，SDK 会提供有关函数失败原因的详细信息。
- 如果要使用 Kubernetes 的外接程序，请从群集中的 [日志记录](../concepts/policy-for-kubernetes.md#logging) 开始。

## <a name="general-errors"></a>常规错误

### <a name="scenario-alias-not-found"></a>方案：找不到别名

#### <a name="issue"></a>问题

在策略定义中使用的别名不正确或不存在。 Azure Policy 使用[别名](../concepts/definition-structure.md#aliases)映射到 Azure 资源管理器属性。

#### <a name="cause"></a>原因

在策略定义中使用的别名不正确或不存在。

#### <a name="resolution"></a>解决方法

首先，验证资源管理器属性是否有别名。 若要查找可用别名，请访问 Visual Studio Code 或 SDK 的 [Azure 策略扩展](../how-to/extension-for-vscode.md) 。
如果资源管理器属性没有别名，请创建支持票证。

### <a name="scenario-evaluation-details-arent-up-to-date"></a>方案：评估详细信息不是最新的

#### <a name="issue"></a>问题

资源处于 " _未启动_ " 状态，或符合性详细信息不是最新的。

#### <a name="cause"></a>原因

应用新策略或计划分配需要大约30分钟。 在现有分配范围内的新资源或更新的资源将在大约15分钟内可用。 标准符合性扫描每24小时进行一次。 有关详细信息，请参阅[评估触发器](../how-to/get-compliance-data.md#evaluation-triggers)。

#### <a name="resolution"></a>解决方法

首先，请等待一段适当的时间来完成评估，并在 Azure 门户或 SDK 中提供符合性结果。 若要使用 Azure PowerShell 或 REST API 开始新的评估扫描，请参阅按 [需评估扫描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。

### <a name="scenario-compliance-isnt-as-expected"></a>方案：符合性与预期不符

#### <a name="issue"></a>问题

资源不是预期的资源的 _符合_ 或 _不符合_ 的评估状态。

#### <a name="cause"></a>原因

资源不在策略分配的正确范围内，或者策略定义不按预期操作。

#### <a name="resolution"></a>解决方法

若要解决策略定义问题，请执行以下操作：

1. 首先，请等待适当的时间来完成评估，并在 Azure 门户或 SDK 中提供符合性结果。 

1. 若要使用 Azure PowerShell 或 REST API 开始新的评估扫描，请参阅按 [需评估扫描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 确保正确设置了分配参数和分配范围。
1. 检查[策略定义模式](../concepts/definition-structure.md#mode)：
   - 此模式应该 `all` 适用于所有资源类型。
   - `indexed`如果策略定义检查标记或位置，则模式应该为。
1. 确保资源的作用域未 [排除](../concepts/assignment-structure.md#excluded-scopes) 或不 [例外](../concepts/exemption-structure.md)。
1. 如果策略分配的符合性显示 `0/0` 资源，表示没有确定在分配范围内适用的资源。 检查策略定义和分配范围。
1. 有关应符合的不符合要求的资源，请参阅 [确定](../how-to/determine-non-compliance.md)不符合的原因。 定义与计算属性值的比较指示资源不相容的原因。
   - 如果“目标值”错误，请修改策略定义。
   - 如果“当前值”错误，请通过 `resources.azure.com` 验证资源有效负载。
1. 有关其他常见问题和解决方案，请参阅 [故障排除：不按预期执行](#scenario-enforcement-not-as-expected)。

如果复制的和自定义的内置策略定义或自定义定义仍存在问题，请在“创作策略”下创建支持票证，以正确提交问题。

### <a name="scenario-enforcement-not-as-expected"></a>方案：未按预期执行

#### <a name="issue"></a>问题

预期 Azure 策略要对其执行操作的资源未进行操作， [Azure 活动日志](../../../azure-monitor/essentials/platform-logs-overview.md)中没有条目。

#### <a name="cause"></a>原因

已为 _禁用_ 的 [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode)设置配置策略分配。
禁用 **enforcementMode** 时，不会强制实施策略效果，活动日志中没有条目。

#### <a name="resolution"></a>解决方法

通过执行以下操作来解决策略分配的强制问题：

1. 首先，请等待适当的时间来完成评估，并在 Azure 门户或 SDK 中提供符合性结果。 

1. 若要使用 Azure PowerShell 或 REST API 开始新的评估扫描，请参阅按 [需评估扫描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 请确保正确设置了分配参数和分配范围并 _启用_ 了 **enforcementMode** 。
1. 检查[策略定义模式](../concepts/definition-structure.md#mode)：
   - 此模式应该 `all` 适用于所有资源类型。
   - `indexed`如果策略定义检查标记或位置，则模式应该为。
1. 确保资源的作用域未 [排除](../concepts/assignment-structure.md#excluded-scopes) 或不 [例外](../concepts/exemption-structure.md)。
1. 验证资源负载是否与策略逻辑匹配。 可以通过 [捕获 HTTP 存档 (HAR) trace](../../../azure-portal/capture-browser-trace.md) 或查看 Azure 资源管理器模板 (ARM 模板) 属性来完成此操作。
1. 有关其他常见问题和解决方案，请参阅 [故障排除：符合预期的符合性](#scenario-compliance-isnt-as-expected)。

如果复制的和自定义的内置策略定义或自定义定义仍存在问题，请在“创作策略”下创建支持票证，以正确提交问题。

### <a name="scenario-denied-by-azure-policy"></a>方案：被 Azure Policy 拒绝

#### <a name="issue"></a>问题

拒绝创建或更新资源。

#### <a name="cause"></a>原因

新资源或更新资源的作用域的策略分配符合策略定义的条件， [拒绝](../concepts/effects.md#deny) 生效。 无法创建或更新满足这些定义的资源。

#### <a name="resolution"></a>解决方法

拒绝策略分配中的错误消息包括策略定义和策略分配 ID。 如果消息中的错误信息丢失，还可在[活动日志](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)中找到。 使用此信息可获取更多详细信息，以了解资源限制和调整请求中的资源属性以使其匹配允许的值。

## <a name="template-errors"></a>模板错误

### <a name="scenario-policy-supported-functions-processed-by-template"></a>方案：策略支持的、由模板处理的函数

#### <a name="issue"></a>问题

Azure 策略支持一些仅在策略定义中可用的 ARM 模板功能和函数。 资源管理器将这些函数作为部署的一部分而不是作为策略定义的一部分进行处理。

#### <a name="cause"></a>原因

使用支持的函数（如 `parameter()` 或 `resourceGroup()` ）会在部署时导致函数的处理结果，而不是允许策略定义和 Azure 策略引擎处理。

#### <a name="resolution"></a>解决方法

若要在策略定义中传递函数，请将整个字符串转义，使 `[` 属性类似于 `[[resourceGroup().tags.myTag]` 。 转义符会导致资源管理器在处理模板时将值视为字符串。 然后，Azure 策略将该函数放在策略定义中，这样它就可以按预期方式动态进行。 有关详细信息，请参阅 [Azure 资源管理器模板中的语法和表达式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="add-on-for-kubernetes-installation-errors"></a>Kubernetes 加载项安装错误

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>方案：使用 Helm 图表安装因密码错误而失败

#### <a name="issue"></a>问题

`helm install azure-policy-addon`命令失败，并返回以下错误之一：

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

生成的密码包括 `,` Helm 图表要拆分的)  (逗号。

#### <a name="resolution"></a>解决方法

当你运行时 `helm install azure-policy-addon` ，请 `,` 使用反斜杠 () 来转义 password 值中的逗号 () `\` 。

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>方案：使用 Helm 图安装失败，因为该名称已存在

#### <a name="issue"></a>问题

`helm install azure-policy-addon`命令失败，并返回以下错误：

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

已安装或部分安装了名称的 Helm 图表 `azure-policy-addon` 。

#### <a name="resolution"></a>解决方法

按照说明 [删除 Azure Policy For Kubernetes 外接程序](../concepts/policy-for-kubernetes.md#remove-the-add-on)，然后重新运行该 `helm install azure-policy-addon` 命令。

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>方案：使用系统分配的托管标识替换 Azure 虚拟机用户分配的标识

#### <a name="issue"></a>问题

将来宾配置策略计划分配给计算机内部的审核设置后，分配给该计算机的用户分配的托管标识将不再分配。 仅分配系统分配的托管标识。

#### <a name="cause"></a>原因

以前在来宾配置 DeployIfNotExists 定义中使用的策略定义确保将系统分配的标识分配给计算机，但它们也会删除用户分配的标识分配。

#### <a name="resolution"></a>解决方法

先前导致此问题的定义将显示为 "已 _\[ 弃用 \]_"，并由管理先决条件的策略定义替换，而不删除用户分配的托管标识。 需要手动操作。 删除标记为 "已 _\[ 弃用 \]_" 的任何现有策略分配，并将其替换为与原始名称相同的已更新先决条件策略计划和策略定义。

有关详细叙述，请参阅博客文章 [针对来宾配置审核策略发布的重要更改](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)。

## <a name="add-on-for-kubernetes-general-errors"></a>Kubernetes 常规错误的外接程序

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>方案：外接程序无法到达 Azure 策略服务终结点，因为有出口限制

#### <a name="issue"></a>问题

加载项无法访问 Azure 策略服务终结点，并返回以下错误之一：

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>原因

锁定群集传出后会出现此问题。

#### <a name="resolution"></a>解决方法

确保以下文章中提到的域和端口处于打开状态：

- [AKS 群集 (Fqdn) 所需的出站网络规则和完全限定的域名](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [安装适用于 Azure Arc 的 Azure 策略外接程序 Kubernetes (预览) ](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>方案：外接程序无法访问 Azure 策略服务终结点，因为有 aad 标识配置

#### <a name="issue"></a>问题

加载项无法访问 Azure 策略服务终结点，并返回以下错误之一：

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>原因

当在群集上安装了外接程序 _标识_ 并且 _kube_ 在 _aad-identity_ 中未排除时，会出现此错误。

Aad (NMI) 盒中的 _aad 标识_ 组件节点托管标识修改节点的 iptables，以截获对 Azure 实例元数据终结点的调用。 此设置意味着对元数据终结点所做的任何请求都将被 NMI 截获，即使该 pod 不使用 _aad-pod 标识_。
可以将 _AzurePodIdentityException_ CUSTOMRESOURCEDEFINITION (.crd) 配置为通知 _aad_ -a 来自与在 .crd 中定义的标签相匹配的 pod 的元数据终结点的任何请求都应该在无 NMI 处理的情况下代理。

#### <a name="resolution"></a>解决方法

`kubernetes.azure.com/managedby: aks`通过配置 _AzurePodIdentityException_ .crd，排除 _aad_ - _kube_ 命名空间中具有标签的系统 pod。

有关详细信息，请参阅 [禁用 Azure Active Directory (Azure AD 特定 pod/应用程序) pod 标识](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)。

若要配置异常，请按以下示例操作：

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>方案：资源提供程序未注册

#### <a name="issue"></a>问题

外接程序可以访问 Azure 策略服务终结点，但外接程序日志显示以下错误之一：

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>原因

"PolicyInsights" 资源提供程序未注册。 必须注册该外接程序，才能获取策略定义并返回符合性数据。

#### <a name="resolution"></a>解决方法

在群集订阅中注册 "PolicyInsights" 资源提供程序。 有关说明，请参阅 [注册资源提供程序](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。

### <a name="scenario-the-subscription-is-disabled"></a>方案：订阅已禁用

#### <a name="issue"></a>问题

外接程序可以访问 Azure 策略服务终结点，但会显示以下错误：

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>原因

此错误表示订阅已确定有问题，并且添加了功能标志 `Microsoft.PolicyInsights/DataPlaneBlocked` 来阻止订阅。

#### <a name="resolution"></a>解决方法

若要调查并解决此问题，请 [联系功能团队](mailto:azuredg@microsoft.com)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出或者无法解决，请访问以下通道之一获得支持：

- 通过 [Microsoft Q&A](/answers/topics/azure-policy.html) 获得专家提供的答案。
- 联系 [@AzureSupport](https://twitter.com/azuresupport)。 此官方 Microsoft Azure Twitter 上的资源通过将 Azure 社区连接到正确的答案、支持和专家来帮助改善客户体验。
- 如果仍需要帮助，请参阅 [Azure 支持站点](https://azure.microsoft.com/support/options/) 并选择 " **提交支持请求**"。
