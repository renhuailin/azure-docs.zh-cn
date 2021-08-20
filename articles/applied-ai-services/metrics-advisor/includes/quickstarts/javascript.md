---
title: 指标顾问 JavaScript 快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 07/07/2021
ms.author: mbullwin
ms.openlocfilehash: 0c3a6825012344042b7888aed13cae6d54b28cad
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341426"
---
[参考文档](/java/api/overview/azure/ai-metricsadvisor-readme) | [库源代码](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md) | [包 (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/metricsadvisor/ai-metrics-advisor/samples/v1/javascript)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Node.js](https://nodejs.org/)
* 获得 Azure 订阅后，<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="“创建指标顾问”资源"  target="_blank">在 Azure 门户中创建“指标顾问”资源 </a>，以部署“指标顾问”实例。  
* 带有时序数据的 SQL 数据库。
  
> [!TIP]
> * 可以在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/metricsadvisor/ai-metrics-advisor/samples/v1/javascript) 上找到 JavaScript 指标顾问示例。
> * 指标顾问资源可能需要 10 到 30 分钟才能部署一个服务实例供你使用。 部署成功后，单击“转到资源”。 部署完成后，可以通过 Web 门户和 REST API 这两种方式开始使用指标顾问实例。 
> * 可以在 Azure 门户的资源“概述”部分中找到 REST API 的 URL。 它将如下所示：
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

### <a name="install-the-client-library"></a>安装客户端库

安装 `@azure/ai-metrics-advisor` NPM 包：

```console
npm install @azure/ai-metrics-advisor
```

应用的 `package.json` 文件将使用依赖项进行更新。

创建一个名为 `index.js` 的文件，并导入以下库：

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/v1/javascript) 上找到它，其中包含此快速入门中的代码示例。

为资源的 Azure 终结点和密钥创建变量。 

> [!IMPORTANT]
> 转到 Azure 门户。 如果在“先决条件”部分中创建的指标顾问资源已成功部署，请单击“后续步骤”下的“转到资源”按钮  。 在资源的“密钥和终结点”页的“资源管理”下可以找到订阅密钥和终结点 。 <br><br>若要检索 API 密钥，必须转到 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)。 为资源选择相应的“目录”、“订阅”和“工作区”，然后选择“入门”   。 然后，你就能够从 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) 检索 API 密钥。   
>
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../../cognitive-services/cognitive-services-security.md)文章。

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>对象模型

以下类和接口处理指标顾问 JavaScript SDK 的某些主要功能。

|名称|说明|
|---|---|
| MetricsAdvisorClient | 用途： <br> - 列出事件 <br> - 列出事件的根本原因 <br> - 检索原始时序数据和由服务丰富的时序数据。 <br> - 列出警报 <br> - 添加反馈以优化模型 |
| MetricsAdvisorAdministrationClient | 允许你： <br> - 管理数据馈送 <br> - 创建、配置、检索、列出和删除异常情况警报配置 <br> - 管理挂钩  |
| DataFeed | 指标顾问从数据源中引入的内容。`DataFeed` 包含以下行： <br> - 时间戳 <br> - 零维度或多个维度 <br> - 一个或多个度量值  |
| DataFeedMetric | `DataFeedMetric` 是用于监视和评估特定业务流程状态的可计量度量值。 它可以是划分为多个维度的多个时序值的组合。 例如，Web 运行状况指标可能包含用户数和 en-us 市场的维度  。 |

## <a name="code-examples"></a>代码示例

这些代码片段展示如何借助适用于 JavaScript 的指标顾问客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [添加数据馈送](#add-a-data-feed)
* [检查引入状态](#check-ingestion-status)
* [配置异常情况检测](#configure-anomaly-detection)
* [创建挂钩](#create-a-hook)
* [创建警报配置](#create-an-alert-configuration)
* [查询警报](#query-the-alert)

## <a name="authenticate-the-client"></a>验证客户端

拥有两个密钥和终结点地址后，可以使用 MetricsAdvisorKeyCredential 类对客户端进行身份验证，如下所示：

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>添加数据馈送

指标顾问支持连接不同类型的数据源。 下面是从 SQL Server 引入数据的示例。

将 `sql_server_connection_string` 替换为你自己的 SQL Server 连接字符串，并将 `query` 替换为在单个时间戳返回数据的查询。 你还需要根据自定义数据调整 `metric` 和 `dimension` 值。

> [!IMPORTANT]
> 对于每个维度组合，查询应在每个时间戳处最多返回一条记录。 查询返回的所有记录必须具有相同的时间戳。 指标顾问将针对每个时间戳运行此查询以引入数据。 有关详细信息和示例，请参阅[教程：编写有效的查询](../../tutorials/write-a-valid-query.md)。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  const apiKey ="<paste-your-metrics-advisor-api-key-here>";
  const endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    admins: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>检查引入状态

开始数据引入后，我们可以检查引入状态。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>配置异常情况检测

我们需要异常情况检测配置来确定时序中的某个点是否异常。 虽然默认检测配置会自动应用于每个指标，但你可以创建自定义的异常情况检测配置来调整对数据使用的检测模式。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>创建挂钩

我们使用挂钩来订阅实时警报。 在此示例中，我们为指标顾问服务创建一个 Webhook，以将警报 POST 到该服务。

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>创建警报配置

此示例将演示如何配置需要触发警报的条件，以及要使用哪些挂钩作为警报的目标。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>查询警报

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomaliesForAlert(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```
