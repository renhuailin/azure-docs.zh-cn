---
title: 教程 - 通过 Prometheus 和 Grafana 监视 Apache Spark 应用程序级指标
description: 教程 - 了解如何将 Apache Spark 应用程序指标解决方案部署到 Azure Kubernetes 服务 (AKS) 群集，以及如何集成 Grafana 仪表板。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: b32ddb38395d95e1c262c05aef878a1beeddc38c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734712"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>教程：通过 Prometheus 和 Grafana 监视 Apache Spark 应用程序级指标

## <a name="overview"></a>概述

在本教程中，你将了解如何将 Apache Spark 应用程序指标解决方案部署到 Azure Kubernetes 服务 (AKS) 群集，以及如何集成 Grafana 仪表板。

可以使用此解决方案以准实时方式收集和查询 Apache Spark 指标数据。 集成的 Grafana 仪表板用于诊断和监视 Apache Spark 应用程序。 源代码和配置已在 GitHub 上开放。

## <a name="prerequisites"></a>先决条件

1.  [Azure CLI](/cli/azure/install-azure-cli)
2.  [Helm 客户端 3.30+](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/)

也可使用 [Azure Cloud Shell](https://shell.azure.com/)，其中已包含立即可用的 Azure CLI、Helm 客户端和 kubectl。

## <a name="log-in-to-azure"></a>登录 Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>创建 Azure Kubernetes 服务实例 (AKS)

使用 Azure CLI 命令在订阅中创建 Kubernetes 群集。

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

注意：如果已有 AKS 群集，则可跳过此步骤。

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>创建服务主体并授予其对 Synapse 工作区的权限

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

结果应如下所示：

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

记下 appId、密码和 tenantID。

[![通过 srbac 授予权限的屏幕截图](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. 以 Synapse 管理员身份登录到 [Azure Synapse Analytics 工作区](https://web.azuresynapse.net/)

2. 在 Synapse Studio 的左窗格中，选择“管理”>“访问控制”

3. 单击左上方的“添加”按钮以 **添加角色分配**

4. 对于“范围”，请选择“工作区”

5. 对于“角色”，请选择“Synapse 计算操作员”

6. 对于“选择用户”，请输入你的 **<service_principal_name>** 并单击你的服务主体

7. 单击“应用”（请等待 3 分钟，让权限生效。）

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>安装连接器、Prometheus 服务器、Grafana 仪表板

1. 将 synapse-charts 存储库添加到 Helm 客户端。

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  通过 Helm 客户端安装组件：

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name：Synapse 工作区名称。
 - subscription_id：Synapse 工作区订阅 ID。
 - workspace_resource_group_name：Synapse 工作区资源组名称。
 - tenant_id：Synapse 工作区租户 ID。
 - service_principal_app_id：服务主体“appId”
 - service_principal_password：创建的服务主体密码。

## <a name="log-in-to-grafana"></a>登录到 Grafana

获取 Grafana 的默认密码和地址。 可以在 Grafana 设置中更改密码。

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

获取服务 IP，将此外部 IP 复制并粘贴到浏览器，然后使用用户名“admin”和密码进行登录。

## <a name="use-grafana-dashboards"></a>使用 Grafana 仪表板

在 Grafana 页面的左上角找到 Synapse 仪表板（“主页”->“Synapse 工作区/Synapse 应用程序”），尝试在 Synapse Studio 中运行示例代码，等待几秒钟，以便进行指标拉取。

此外，还可以使用“Synapse 工作区/工作区”和“Synapse 工作区/Spark 池”仪表板获取工作区和 Apache Spark 池的概述。

## <a name="uninstall"></a>卸载

通过 Helm 命令删除组件，如下所示。

```bash
helm delete <release_name> -n <namespace>
```

删除 AKS 群集。

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>组件简介

Azure Synapse Analytics 提供基于 Prometheus Operator 和 Synapse Prometheus Connector 的 [Helm 图表](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm)。 Helm 图表包含适用于 Apache Spark 应用程序级指标的 Prometheus 服务器、Grafana 服务器和 Grafana 仪表板。 可以使用 [Prometheus](https://prometheus.io/)（一种常用的开源监视系统）以准实时的方式收集这些指标，并使用 [Grafana](https://github.com/grafana/grafana) 进行可视化。

### <a name="synapse-prometheus-connector"></a>Synapse Prometheus Connector

Synapse Prometheus Connector 用于连接 Azure Synapse Apache Spark 池和 Prometheus 服务器。 它实现以下功能：

1.  身份验证：它是基于 AAD 的身份验证，可以自动刷新服务主体的 AAD 令牌，以用于执行应用程序发现、指标引入和其他功能。
2.  Apache Spark 应用程序发现：当你在目标工作区中提交应用程序时，Synapse Prometheus Connector 可以自动发现这些应用程序。
3.  Apache Spark 应用程序元数据：它收集基本的应用程序信息，并将数据导出到 Prometheus。

Synapse Prometheus Connector 已作为在 [Microsoft 容器注册表](https://github.com/microsoft/containerregistry)中托管的 docker 映像发布。 它是开源的，位于 [Azure Synapse Spark 应用程序指标](https://github.com/microsoft/azure-synapse-spark-metrics)中。

### <a name="prometheus-server"></a>Prometheus 服务器

Prometheus 是一个开源的监视和警报工具包。 Prometheus 是从云原生计算基础 (CNCF) 升级的，已成为云原生监视的实际标准。 Prometheus 可以帮助我们收集、查询和存储大量时序数据，并且可以轻松地与 Grafana 集成。 在此解决方案中，我们部署基于 Helm 图表的 Prometheus 组件。

### <a name="grafana-and-dashboards"></a>Grafana 和仪表板

Grafana 是开源的可视化和分析软件。 它用于查询指标、可视化指标、针对指标发出警报以及浏览指标。 Azure Synapse Analytics 提供一组默认的 Grafana 仪表板，用于可视化 Apache Spark 应用程序级指标。

“Synapse 工作区/工作区”仪表板提供所有 Apache Spark 池、应用程序计数、CPU 核心等内容的工作区级视图。

[![仪表板工作区的屏幕截图](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

“Synapse 工作区/Spark 池”仪表板包含该时间段内所选 Apache Spark 池中运行的 Apache Spark 应用程序的指标。

[![仪表板 sparkpool 的屏幕截图](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

“Synapse 工作区/Spark 应用程序”仪表板包含所选的 Apache Spark 应用程序。

[![仪表板应用程序的屏幕截图](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

上述仪表板模板已在 [Azure Synapse Spark 应用程序指标](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards)中开放源代码。