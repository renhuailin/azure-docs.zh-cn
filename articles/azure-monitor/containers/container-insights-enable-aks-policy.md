---
title: 使用 Azure 策略启用 AKS 监视加载项
description: 介绍如何使用 Azure 自定义策略启用 AKS 监视加载项。
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713892"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>使用 Azure 策略启用 AKS 监视加载项
本文介绍如何使用 Azure 自定义策略启用 AKS 监视加载项。 可以在订阅或资源组作用域上为监视加载项自定义策略分配。 如果 Azure Log Analytics 工作区和 AKS 群集位于不同的订阅中，则策略分配使用的托管标识必须对 Log Analytics 工作区的资源具有所需的角色权限。 同样，如果策略的范围限定为资源组，那么，如果工作区不在所选的资源组范围内，则托管标识应拥有 Log Analytics 工作区所需的角色权限。

监视加载项需要 Azure 策略使用的托管标识上的以下角色：

 - [kubernetes--参与者-role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [日志分析-参与者](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>使用 Azure 门户创建和分配策略定义

### <a name="create-policy-definition"></a>创建策略定义

1. 下载 Azure 自定义策略定义以启用 AKS 监视加载项。
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions在 "策略定义创建" 对话框中，导航到并创建策略定义，并提供以下详细信息。
 
    - **定义位置**：选择要在其中存储策略定义的 Azure 订阅。
    - **名称**： *(预览版) AKS* -加载项
    - **说明**： *Azure 自定义策略，用于启用在指定范围内的 azure Kubernetes 群集 (s) 上监视加载项*
    - "**类别**"：选择 "*使用现有*" 并从下拉 *Kubernetes* 选择 "。
    - **策略规则**：删除现有的示例规则，并复制上面 #1 步骤中下载 *azurepolicy.js* 的内容。

### <a name="assign-policy-definition-to-specified-scope"></a>将策略定义分配到指定的作用域

> [!NOTE]
>  将自动创建托管标识，并在策略定义中分配指定的角色。

1.  (预览中选择要创建的策略定义 *) AKS 监视加载* 项。
4. 单击 " *分配*"，并指定应将策略分配到的 **范围** 。 
5. 单击 " **下一步** "，提供 Azure Log Analytics 工作区的资源 ID。 资源 ID 应采用以下格式 `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` 。
6. 如果要将策略应用于所选作用域中的现有 AKS 群集，请创建一项补救任务。
7. 单击 " **查看 + 创建** " 选项以创建策略分配。
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>使用 Azure CLI 创建和分配策略定义

### <a name="create-policy-definition"></a>创建策略定义

1. 下载 Azure 自定义策略定义规则和参数文件，并提供以下命令：

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. 在以下命令中创建策略定义：

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>将策略定义分配到指定的作用域

- 用以下命令创建策略分配：

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure 策略](../../governance/policy/overview.md)的详细信息。
- 了解 [修正安全的工作](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)方式。
- 详细了解 [容器见解](./container-insights-overview.md)。
- 安装 [Azure CLI](/cli/azure/install-azure-cli)。