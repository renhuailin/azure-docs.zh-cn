---
title: 使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署
description: 在本地和多云 Kubernetes 群集中使用 Azure Defender for Kubernetes
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/14/2021
ms.author: memildin
ms.openlocfilehash: 2fc8c04cbe86737d924a1e61e8a2b3808082b7c9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351717"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>保护本地和多云环境中运行的已启用 Azure Arc 的 Kubernetes 群集

Azure Defender for Kubernetes 群集扩展使用为 Azure Kubernetes 服务群集提供的相同威胁检测功能来保护本地群集。 在群集启用[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md)，并按照本页所述部署扩展。 

该扩展还可以保护其他云提供商上的 Kubernetes 群集（不过，这些群集不在其托管 Kubernetes 服务上）。

> [!TIP]
> 我们在 [GitHub 上的安装示例](https://aka.ms/kubernetes-extension-installation-examples)中提供了一些示例文件，以帮助你完成安装过程。

## <a name="availability"></a>可用性

| 方面 | 详细信息 |
|--------|---------|
| 发布状态 | **预览**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| 所需的角色和权限 | [安全管理员](../role-based-access-control/built-in-roles.md#security-admin)可以消除警报<br>[安全读取者](../role-based-access-control/built-in-roles.md#security-reader)可以查看结果 |
| 定价 | 免费（预览期间） |
| 支持的 Kubernetes 分布 | [Azure Stack HCI 上的 Azure Kubernetes 服务](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS 引擎](https://github.com/Azure/aks-engine)<br> [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/)（4.6 或更高版本）<br> [VMware Tanzu Kubernetes 网格](https://tanzu.vmware.com/kubernetes-grid)<br> [Rancher Kubernetes 引擎](https://rancher.com/docs/rke/latest/en/) |
| 限制 | 已启用 Azure Arc 的 Kubernetes 和 Azure Defender 扩展不支持托管 Kubernetes 产品/服务，例如 Google Kubernetes Engine 和 Elastic Kubernetes Service。 [Azure Defender 原生适用于 Azure Kubernetes 服务 (AKS)](defender-for-kubernetes-introduction.md)，且无需将群集连接到 Azure Arc。 |
| 环境和区域 | 此扩展的可用性与[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md) 相同|

## <a name="architecture-overview"></a>体系结构概述

对于除 AKS 以外的所有 Kubernetes 群集，需要将群集连接到 Azure Arc。连接后，可以在[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md) 资源上将 Azure Defender for Kubernetes 部署为[群集扩展](../azure-arc/kubernetes/extensions.md)。

扩展组件从群集中的所有控制平面节点收集 Kubernetes 审核日志数据，并将其发送到云中的 Azure Defender for Kubernetes 后端作进一步分析。 该扩展将注册到用作数据管道的 Log Analytics 工作区，但审核日志数据不会存储在 Log Analytics 工作区中。

此图显示了 Azure Defender for Kubernetes 与已启用 Azure Arc 的 Kubernetes 群集之间的交互：

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="描绘 Azure Defender for Kubernetes 与已启用 Azure Arc 的 Kubernetes 群集之间的交互的概要体系结构示意图。" lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>先决条件

部署扩展之前，请确保：
- [将 Kubernetes 群集连接到 Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- 完成[通用群集扩展文档中列出的先决条件](../azure-arc/kubernetes/extensions.md#prerequisites)。
- 具有 [Helm 3](https://helm.sh/docs/intro/install) 版本 3.7.0 或更高版本
- 在以下终结点上配置“端口 443”用于出站访问：
    - 对于 Azure 政府云上的群集：
        - *.ods.opinsights.azure.us
        - *.oms.opinsights.azure.us
        - :::no-loc text="login.microsoftonline.us":::
    - 对于其他 Azure 云部署中的群集：
        - *.ods.opinsights.azure.com
        - *.oms.opinsights.azure.com
        - :::no-loc text="login.microsoftonline.com":::

## <a name="deploy-the-azure-defender-extension"></a>部署 Azure Defender 扩展

可以使用各种方法部署 Azure Defender 扩展。 有关详细步骤，请选择相关的选项卡。

### <a name="azure-portal"></a>[**Azure 门户**](#tab/k8s-deploy-asc)

### <a name="use-the-fix-button-from-the-security-center-recommendation"></a>使用安全中心建议中的“修复”选项

Azure 安全中心的专业建议提供：

- 有关哪些群集上部署了 Defender for Kubernetes 扩展的直观信息
- “修复”选项，用于在没有扩展的情况下将此扩展部署到这些群集中

1. 在 Azure 安全中心的“建议”页中，打开“启用 Azure Defender”安全控制。

1. 使用筛选器找到名为“已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展”的建议。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="有关为已启用 Azure Arc 的 Kubernetes 群集部署 Azure Defender 扩展的 Azure 安全中心建议。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > 请注意“操作”列中的“修复”图标

1. 选择该扩展以查看正常和不正常资源的详细信息 - 具有和没有该扩展的群集。

1. 在“不正常的资源”列表中选择一个群集，然后选择“修正”打开包含修正选项的窗格。

1. 选择相关的 Log Analytics 工作区，然后选择“修正 x 资源”。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="使用安全中心的修复选项为 Azure Arc 部署 Azure Defender 扩展。":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>使用 Azure CLI 部署 Azure Defender 扩展

1. 登录到 Azure：

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > 确保为 ``<your-subscription-id>`` 使用的订阅 ID 与将群集连接到 Azure Arc 时所用的 ID 相同。

1. 运行以下命令，在已启用 Azure Arc 的 Kubernetes 群集上部署该扩展：

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    下面提供了 Azure Defender 扩展类型支持的所有配置设置的说明：

    | 属性 | 说明 |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | 可选。 你自己的 Log Analytics 工作区的完整资源 ID。<br>如果未提供，将使用区域的默认工作区。<br><br>若要获取完整资源 ID，请运行以下命令，以默认 JSON 格式显示订阅中的工作区列表：<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Log Analytics 工作区资源 ID 采用以下语法：<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>在 [Log Analytics 工作区](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)中了解详细信息 |
    | auditLogPath |可选。 审核日志文件的完整路径。<br>如果未提供，将使用默认路径 ``/var/log/kube-apiserver/audit.log``。<br>对于 AKS 引擎，标准路径为 ``/var/log/kubeaudit/audit.log`` |

    以下命令显示所有可选字段的示例用法：

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**资源管理器**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>使用 Azure 资源管理器部署 Azure Defender 扩展

若要使用 Azure 资源管理器部署 Azure Defender 扩展，你的订阅中需有一个 Log Analytics 工作区。 在 [Log Analytics 工作区](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)中了解详细信息。

可以使用安全中心[安装示例](https://aka.ms/kubernetes-extension-installation-examples)中的 azure-defender-extension-arm-template.json 资源管理器模板。

> [!TIP]
> 如果你不熟悉资源管理器模板，请先参阅以下文章：[什么是 Azure 资源管理器模板？](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>使用 REST API 部署 Azure Defender 扩展 

若要使用 REST API 部署 Azure Defender 扩展，你的订阅中需有一个 Log Analytics 工作区。 在 [Log Analytics 工作区](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)中了解详细信息。

> [!TIP]
> 使用 API 部署 Azure Defender 扩展的最简单方法是使用安全中心[安装示例](https://aka.ms/kubernetes-extension-installation-examples)中提供的“Postman 集合 JSON”示例。
- 若要修改 Postman 集合 JSON 或要使用 REST API 手动部署扩展，请运行以下 PUT 命令：

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    其中：

    | 名称            | 在   | 必需 | 类型   | 描述                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | 订阅 ID | path | True     | 字符串 | 已启用 Azure Arc 的 Kubernetes 资源的订阅 ID |
    | 资源组  | path | True     | 字符串 | 包含已启用 Azure Arc 的 Kubernetes 资源的资源组的名称 |
    | 群集名称    | path | True     | 字符串 | 已启用 Azure Arc 的 Kubernetes 资源的名称  |


    对于 Authentication，头中必须包含一个持有者令牌（与使用其他 Azure API 时一样）。 若要获取持有者令牌，请运行以下命令：

    `az account get-access-token --subscription <your-subscription-id>` 对消息正文使用以下结构：
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    下面提供了属性说明：

    | 属性 | 说明 | 
    | -------- | ----------- |
    | logAnalytics.workspaceId | Log Analytics 资源的工作区 ID |
    | logAnalytics.key         | Log Analytics 资源的密钥 | 
    | auditLogPath             | 可选。 审核日志文件的完整路径。 默认值为 ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>验证部署

若要验证群集上是否已安装 Azure Defender 扩展，请执行下列其中一个选项卡中的步骤：

### <a name="azure-portal---security-center"></a>[**Azure 门户 - 安全中心**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>使用安全中心建议验证扩展的状态

1. 在 Azure 安全中心的“建议”页中，打开“启用 Azure Defender”安全控制。

1. 选择名为“已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展”的建议。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="有关为已启用 Azure Arc 的 Kubernetes 群集部署 Azure Defender 扩展的 Azure 安全中心建议。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. 检查在其上部署了该扩展的群集是否以“正常”状态列出。


### <a name="azure-portal---azure-arc"></a>[**Azure 门户 - Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>使用 Azure Arc 页验证扩展的状态

1. 在 Azure 门户中打开“Azure Arc”。
1. 在基础结构列表中选择“Kubernetes 群集”，然后选择特定的群集。
1. 打开“扩展”页。 其中列出了群集上的扩展。 检查“安装状态”列，确认是否已正确安装 Azure Defender 扩展。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="用于检查 Kubernetes 群集上所有已安装扩展的状态的 Azure Arc 页。" lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. 如需更多详细信息，请选择扩展。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Kubernetes 群集上 Azure Arc 扩展的完整详细信息。":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>使用 Azure CLI 验证是否已部署扩展

1. 在 Azure CLI 中运行以下命令：

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. 在响应中，查看 "extensionType": "microsoft.azuredefender.kubernetes" 和 "installState": "Installed"。

    > [!NOTE]
    > 在前几分钟，响应中可能会显示 "installState": "Pending"。
    
1. 如果状态显示为 Installed，请在将 `kubeconfig` 文件指向你的群集的情况下，在计算机上运行以下命令，以检查名为“azuredefender-XXXXX”的 Pod 是否处于“Running”状态：
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>使用 REST API 验证是否已部署扩展

若要确认部署是否成功或随时验证扩展的状态，请执行以下操作：

1. 运行以下 GET 命令：

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. 在响应中的 "extensionType": "microsoft.azuredefender.kubernetes" 内查看 "installState": "Installed"。

    > [!TIP]
    > 在前几分钟，响应中可能会显示 "installState": "Pending"。
    
1. 如果状态显示为 Installed，请在将 `kubeconfig` 文件指向你的群集的情况下，在计算机上运行以下命令，以检查名为“azuredefender-XXXXX”的 Pod 是否处于“Running”状态：
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>模拟来自 Azure Defender for Kubernetes 的安全警报

[Azure 安全中心的所有安全警报参考表](alerts-reference.md#alerts-k8scluster)中提供了受支持警报的完整列表。

1. 若要模拟 Azure Defender 警报，请运行以下命令：

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    预期响应为“No resource found”。

    在 30 分钟内，Azure Defender 将检测到此活动并触发安全警报。

1. 在 Azure 门户中，打开 Azure 安全中心的“安全警报”页并查看针对相关资源的警报：

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="来自 Azure Defender for Kubernetes 的示例警报。" lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>删除 Azure Defender 扩展

可以按照以下选项卡中所述，使用 Azure 门户、Azure CLI 或 REST API 删除该扩展。

### <a name="azure-portal---arc"></a>[**Azure 门户 - Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>使用 Azure 门户删除扩展

1. 在 Azure 门户中打开“Azure Arc”。
1. 在基础结构列表中选择“Kubernetes 群集”，然后选择特定的群集。
1. 打开“扩展”页。 其中列出了群集上的扩展。
1. 选择群集，然后选择“卸载”。

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="从已启用 Arc 的 Kubernetes 群集中删除扩展。" lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>使用 Azure CLI 删除 Azure Defender 扩展

1. 使用以下命令删除 Azure Defender for Kubernetes Arc 扩展：

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    删除扩展可能需要几分钟时间。 建议先等待一段时间，然后再尝试验证删除操作是否成功。

1. 若要验证是否已成功删除扩展，请运行以下命令：

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    从 Azure 资源管理器中删除扩展资源不应会出现延迟。 删除后，在将 `kubeconfig` 文件指向你的群集的情况下运行以下命令，验证群集上是否不存在名为“azuredefender-XXXXX”的 Pod： 

    ```console
    kubectl get pods -n azuredefender
    ```

    删除 Pod 可能需要几分钟时间。

### <a name="rest-api"></a>[**REST API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>使用 REST API 删除 Azure Defender 扩展 

若要使用 REST API 删除扩展，请运行以下 DELETE 命令：

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| 名称            | 在   | 必需 | 类型   | 描述                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| 订阅 ID | path | True     | 字符串 | 已启用 Arc 的 Kubernetes 群集的订阅 ID |
| 资源组  | path | True     | 字符串 | 已启用 Arc 的 Kubernetes 群集的资源组  |
| 群集名称    | path | True     | 字符串 | 已启用 Arc 的 Kubernetes 群集的名称            |

对于 Authentication，头中必须包含一个持有者令牌（与使用其他 Azure API 时一样）。 若要获取持有者令牌，请运行以下命令：

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

可能需要几分钟时间才能完成该请求。

---

## <a name="next-steps"></a>后续步骤

本页面介绍了如何为已启用 Azure Arc 的 Kubernetes 群集部署 Azure Defender 扩展。 请在以下页面中详细了解 Azure Defender 和 Azure 安全中心的容器安全功能：

- [安全中心的容器安全性](container-security.md)
- [适用于 Kubernetes 的 Azure Defender 简介](defender-for-kubernetes-introduction.md)
- [保护 Kubernetes 工作负载](kubernetes-workload-protections.md)
