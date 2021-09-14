---
title: 使用 API 收集 Apache Spark 应用程序指标
description: 教程 - 了解如何使用 Synapse Prometheus Connector 将现有的本地 Prometheus 服务器与 Azure Synapse 工作区集成，以获取准实时的 Azure Spark 应用程序指标。
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: b2810d97651c6819996c79ce554fa2feee2a6c65
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539634"
---
#  <a name="collect-apache-spark-applications-metrics-using-apis"></a>使用 API 收集 Apache Spark 应用程序指标

## <a name="overview"></a>概述

在本教程中，你将了解如何使用 Synapse Prometheus Connector 将现有的本地 Prometheus 服务器与 Azure Synapse 工作区集成，以获取准实时的 Apache Spark 应用程序指标。 

本教程还介绍了 Azure Synapse REST 指标 API。 你可以通过 REST API 提取 Apache Spark 应用程序指标数据，以构建你自己的监视和诊断工具包，或将其与你的监视系统集成。

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>对本地 Prometheus 服务器使用 Azure Synapse Prometheus Connector

[Azure Synapse Prometheus Connector](https://github.com/microsoft/azure-synapse-spark-metrics) 是一个开源项目。 Synapse Prometheus Connector 使用基于文件的服务发现方法，可用于执行以下操作：
 - 通过 AAD 服务主体向 Synapse 工作区进行身份验证。
 - 提取工作区 Apache Spark 应用程序列表。 
 - 通过基于 Prometheus 文件的配置拉取 Apache Spark 应用程序指标。 

### <a name="1-prerequisite"></a>1.先决条件

你需要在 Linux VM 上部署 Prometheus 服务器。

### <a name="2-create-a-service-principal"></a>2.创建服务主体

若要在本地 Prometheus 服务器中使用 Azure Synapse Prometheus Connector，你应按照以下步骤创建服务主体。

#### <a name="21-create-a-service-principal"></a>2.1 创建服务主体：

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

记下 appId、密码和租户 ID。

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2.2 将相应的权限添加到在上面的步骤中创建的服务主体。

![通过 srbac 授予权限的屏幕截图](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. 以 Synapse 管理员身份登录到 [Azure Synapse Analytics 工作区](https://web.azuresynapse.net/)

2. 在 Synapse Studio 的左窗格中，选择“管理”>“访问控制”

3. 单击左上方的“添加”按钮以 **添加角色分配**

4. 对于“范围”，请选择“工作区”

5. 对于“角色”，请选择“Synapse 计算操作员”

6. 对于“选择用户”，请输入你的 **<service_principal_name>** 并单击你的服务主体

7. 单击“应用”（请等待 3 分钟，让权限生效。）


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3.下载 Azure Synapse Prometheus Connector

使用命令安装 Azure Synapse Prometheus Connector。

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4.为 Azure Synapse 工作区创建 config 文件

在 config 文件夹中创建 config.yaml 文件，并填充以下字段：workspace_name、tenant_id、service_principal_name 和 service_principal_password。
可以在 yaml config 中添加多个工作区。

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5.更新 Prometheus config

在 Prometheus scrape_config 中添加以下 config 节，将 <your_workspace_name> 替换为你的工作区名称，并将 <path_to_synapse_connector> 替换为克隆的 synapse-prometheus-connector 文件夹

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6.在 Prometheus 服务器 VM 中启动 Connector

在 Prometheus 服务器 VM 中启动 Connector 服务器，如下所示。

```
python main.py
```

等待几秒钟，Connector 就会开始工作。 可以在 Prometheus 服务发现页中看到“synapse-prometheus-connector”。

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>使用 Azure Synapse Prometheus 或 REST 指标 API 收集指标数据

### <a name="1-authentication"></a>1.身份验证
可以使用客户端凭据流获取访问令牌。 若要访问指标 API，你应该为服务主体获取 Azure AD 访问令牌，该令牌具有访问 API 所需的适当权限。

| 参数     | 必需 | 说明                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | True     | 你的 Azure 服务主体（应用程序）租户 ID                                                          |
| grant_type    | True     | 指定请求的授权类型。 在客户端凭据授权流中，该值必须是 client_credentials。 |
| client_id     | True     | 在 Azure 门户或 Azure CLI 中注册的应用程序的应用程序（服务主体）ID。        |
| client_secret | True     | 为应用程序（服务主体）生成的机密                                                  |
| resource      | True     | Synapse 资源 URI，应为“https://dev.azuresynapse.net”                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

响应如下所示：

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2.列出 Azure Synapse 工作区中正在运行的应用程序

若要获取 Synapse 工作区的 Apache Spark 应用程序的列表，可以按此文档中的说明操作：[监视 - 获取 Apache Spark 作业列表](/rest/api/synapse/data-plane/monitoring/getsparkjoblist)。


### <a name="3-collect-apache-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. 通过 Prometheus 或 REST API 收集 Apache Spark 应用程序指标


#### <a name="collect-apache-spark-application-metrics-with-the-prometheus-api"></a>通过 Prometheus API 收集 Apache Spark 应用程序指标

通过 Prometheus API 获取指定的 Apache Spark 应用程序的最新指标

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| 参数          | 必需 | 说明                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| endpoint           | True     | 工作区开发终结点，例如 `https://myworkspace.dev.azuresynapse.net.` |
| livyApiVersion     | True     | 请求的有效 api-version。 目前为 2019-11-01-preview                      |
| sparkPoolName      | True     | Spark 池的名称。                                                                     |
| sessionID          | True     | 会话的标识符。                                                                 |
| sparkApplicationId | True     | Spark 应用程序 ID                                                                        |

示例请求： 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

示例响应：

状态代码：200 响应如下所示：

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-apache-spark-application-metrics-with-the-rest-api"></a>通过 REST API 收集 Apache Spark 应用程序指标

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| 参数          | 必需 | 说明                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| endpoint           | True     | 工作区开发终结点，例如 `https://myworkspace.dev.azuresynapse.net.` |
| livyApiVersion     | True     | 请求的有效 api-version。 目前为 2019-11-01-preview                      |
| sparkPoolName      | True     | Spark 池的名称。                                                                     |
| sessionID          | True     | 会话的标识符。                                                                 |
| sparkApplicationId | True     | Spark 应用程序 ID                                                                        |

示例请求

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

示例响应状态代码：200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4.构建你自己的诊断和监视工具

Prometheus API 和 REST API 提供有关 Apache Spark 应用程序运行信息的丰富指标数据。你可以通过 Prometheus API 和 REST API 收集与应用程序相关的指标数据。 你还可以构建更符合自身需求的、你自己的诊断和监视工具。
