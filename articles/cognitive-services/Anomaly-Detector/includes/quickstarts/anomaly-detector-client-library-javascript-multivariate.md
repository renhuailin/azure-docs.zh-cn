---
title: 异常检测器多变量 JavaScript 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 9bf2b62e59b8320135629cc9fe751d6e4ab0e437
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803117"
---
开始使用适用于 JavaScript 的异常检测器多变量客户端库。 请按照以下步骤安装软件包并开始使用服务提供的算法。 新的多变量异常情况检测 API 使开发人员能够轻松地集成高级 AI 来检测指标组中的异常，且无需机器学习知识或标记的数据。 不同信号之间的依赖关系和相互关联会自动计为关键因素。 这可以帮助你主动防范复杂系统发生故障。

使用适用于 JavaScript 的异常检测器多变量客户端库，以便：

* 检测一组时序中的系统级异常。
* 当任何单独的时序都不能告知太多信息时，而你不得不查看所有信号来检测问题。
* 使用数十到数百种不同类型的传感器对昂贵的物理资产进行预测维护，以测量系统运行状况的各个方面。

[库参考文档](/javascript/api/overview/azure/ai-anomaly-detector-readme) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector) | [包 (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector) | [示例代码](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版本的 [Node.js](https://nodejs.org/)
* 拥有 Azure 订阅后，可在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="创建异常检测器资源"  target="_blank">创建异常检测器资源</a>来获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到异常检测器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

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

创建一个名为 `index.js` 的文件，并导入以下库：`
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

为资源的 Azure 终结点和密钥创建变量。 为示例数据文件创建另一个变量。

> [!NOTE]
> 始终可以在两个密钥之间选择一个使用。 这是为了实现安全的密钥轮换。 在本快速入门中，使用第一个密钥。 
   

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

若要使用异常检测器多变量 API，需要先训练你自己的模型。 训练数据是一组满足以下要求的多个时间序列：

每个时间序列都应是一个 CSV 文件，该文件有 2 列（且仅有 2 列），“timestamp”和“value”（全部小写）作为标题行。 “timestamp”值应符合 ISO 8601 标准；“value”可为整数，也可为带有任意小数位的小数。 例如：

|timestamp | 值|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

每个 CSV 文件应根据要用于模型训练的不同变量进行命名。 例如，“temperature.csv”和“humidity.csv”。 所有 CSV 文件都应压缩到一个没有任何子文件夹的 zip 文件中。 zip 文件可采用任何所需名称。 zip 文件应上传到 Azure Blob 存储。 为 zip 文件生成 Blob SAS（共享访问签名）URL 后，可使用该 URL 进行训练。 请查看此文档，了解如何从 Azure Blob 存储生成 SAS URL。

### <a name="install-the-client-library"></a>安装客户端库

安装 `ms-rest-azure` 和 `azure-ai-anomalydetector` NPM 包。 此快速入门中也使用了 csv-parse 库：

```console
npm install @azure/ai-anomaly-detector csv-parse
```

应用的 `package.json` 文件将使用依赖项进行更新。

## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 Node.js 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [定型模型](#train-a-model)
* [检测异常](#detect-anomalies)
* [导出模型](#export-model)
* [删除模型](#delete-model)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和凭据实例化 `AnomalyDetectorClient` 对象。

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="train-a-model"></a>训练模型

### <a name="construct-a-model-result"></a>构造模型结果

首先，我们需要构造模型请求。 请确保开始时间和结束时间与数据源一致。

```javascript
const Modelrequest = {
  source: data_source,
  startTime: new Date(2021,0,1,0,0,0),
  endTime: new Date(2021,0,2,12,0,0),
  slidingWindow:200
};
```

### <a name="train-a-new-model"></a>训练新模型

需要将模型请求传递给异常探测器客户端 `trainMultivariateModel` 方法。

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

若要检查模型训练是否完成，可以跟踪模型的状态：

```javascript
let model_response = await client.getMultivariateModel(model_id);
let model_status = model_response.modelInfo.status;

while (model_status != 'READY' && model_status != 'FAILED'){
  await sleep(10000).then(() => {});
  model_response = await client.getMultivariateModel(model_id);
  model_status = model_response.modelInfo.status;
}

if (model_status == 'FAILED') {
  console.log("Training failed.\nErrors:");
  for (let error of model_response.modelInfo?.errors ?? []) {
    console.log("Error code: " + error.code + ". Message: " + error.message);
  }
}

console.log("TRAINING FINISHED.");
```

## <a name="detect-anomalies"></a>检测异常

使用 `detectAnomaly` 和 `getDectectionResult` 函数确定数据源中是否存在异常。

```javascript
console.log("Start detecting...");
const detect_request = {
  source: data_source,
  startTime: new Date(2021,0,2,12,0,0),
  endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request);
const result_id = result_header.location?.split("/").pop() ?? "";
let result = await client.getDetectionResult(result_id);
let result_status = result.summary.status;

while (result_status != 'READY' && result_status != 'FAILED'){
  await sleep(2000).then(() => {});
  result = await client.getDetectionResult(result_id);
  result_status = result.summary.status;
}

if (result_status == 'FAILED') {
  console.log("Detection failed.\nErrors:");
  for (let error of result.summary.errors ?? []) {
    console.log("Error code: " + error.code + ". Message: " + error.message)
  }
}
console.log("Result status: " + result_status);
console.log("Result Id: " + result.resultId);
```

## <a name="export-model"></a>导出模型

> [!NOTE]
> Export 命令的作用在于使容器化环境中能够运行 Anomaly Detector 多变量模型。 目前不支持多变量，但将来会添加相关支持。

若要导出训练的模型，请使用 `exportModel` 函数。

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>删除模型

若要删除可用于当前资源的现有模型，请使用 `deleteMultivariateModel` 函数。

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>运行应用程序

在运行应用程序之前，对照[完整示例代码](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)检查你的代码，这会很有帮助

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与资源组相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../../overview-multivariate.md)
* [使用异常检测器 API 时的最佳做法](../../concepts/best-practices-multivariate.md)。