---
title: 使用 Azure Policy 启用 AKS 监视加载项
description: 介绍了如何使用 Azure 自定义策略启用 AKS 监视加载项。
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713892"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>使用 Azure Policy 启用 AKS 监视加载项
本文介绍了如何使用 Azure 自定义策略启用 AKS 监视加载项。 可以在订阅或资源组作用域内分配监视加载项自定义策略。 如果 Azure Log Analytics 工作区和 AKS 群集位于不同的订阅中，则策略分配使用的托管标识必须对两个订阅或至少对 Log Analytics 工作区的资源具有所需的角色权限。 类似地，如果策略的作用域限定为资源组，那么，如果 Log Analytics 工作区不在所选的资源组范围内，则托管标识应当在该工作区上具有所需的角色权限。

监视加载项需要在 Azure Policy 使用的托管标识上具有以下角色：

 - [azure-kubernetes-service-contributor-role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [log-analytics-contributor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>使用 Azure 门户创建并分配策略定义

### <a name="create-policy-definition"></a>创建策略定义

1. 下载 Azure 自定义策略定义以启用 AKS 监视加载项。
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. 导航到 https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions 并在“策略定义创建”对话框中使用以下详细信息创建策略定义。
 
    - **定义位置**：选择应当在其中存储策略定义的 Azure 订阅。
    - **名称**：(Preview)AKS-Monitoring-Addon
    - **说明**：*用于在 Azure Kubernetes 群集上在指定作用域中启用监视加载项的 Azure 自定义策略*
    - **类别**：选择“使用现有项”并从下拉列表中选择“Kubernetes”。
    - **策略规则**：删除现有的示例规则，并复制在上面的步骤 #1 中下载的 azurepolicy.json 的内容。

### <a name="assign-policy-definition-to-specified-scope"></a>将策略定义分配到指定的作用域

> [!NOTE]
>  将自动创建托管标识，并在策略定义中分配指定的角色。

1. 选择你刚才创建的策略定义“(Preview) AKS Monitoring Addon”。
4. 单击“分配”并指定应当在其中分配策略的作用域。 
5. 单击“下一步”，并提供 Azure Log Analytics 工作区的资源 ID。 资源 ID 应当采用以下格式：`/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>`。
6. 如果要将策略应用于所选作用域中的现有 AKS 群集，请创建一个修正任务。
7. 单击“查看 + 创建”选项来创建策略分配。
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>使用 Azure CLI 创建并分配策略定义

### <a name="create-policy-definition"></a>创建策略定义

1. 使用以下命令下载 Azure 自定义策略定义规则和参数文件：

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. 使用以下命令创建策略定义：

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>将策略定义分配到指定的作用域

- 使用以下命令创建策略分配：

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Policy](../../governance/policy/overview.md)。
- 了解[修正安全性的工作原理](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)。
- 详细了解[容器见解](./container-insights-overview.md)。
- 安装 [Azure CLI](/cli/azure/install-azure-cli)。