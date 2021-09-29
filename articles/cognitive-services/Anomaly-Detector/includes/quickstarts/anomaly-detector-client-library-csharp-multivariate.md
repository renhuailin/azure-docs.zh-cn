---
title: 异常检测器 .NET 多变量客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: c4048c3795f2e7925f70783b5c5b763d4796b9a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909882"
---
开始使用适用于 C# 的异常检测器多变量客户端库。 请按照以下步骤安装软件包并开始使用服务提供的算法。 新的多变量异常情况检测 API 使开发人员能够轻松地集成高级 AI 来检测指标组中的异常，且无需机器学习知识或标记的数据。 不同信号之间的依赖关系和相互关联会自动计为关键因素。 这可以帮助你主动防范复杂系统发生故障。

使用适用于 C# 的异常检测器多变量客户端库，以便：

* 检测一组时序中的系统级异常。
* 当任何单独的时序都不能告知太多信息时，而你不得不查看所有信号来检测问题。
* 使用数十到数百种不同类型的传感器对昂贵的物理资产进行预测维护，以测量系统运行状况的各个方面。

[库参考文档](/dotnet/api/azure.ai.anomalydetector) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/anomalydetector) | [包 (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本
* 拥有 Azure 订阅后，可在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="创建异常检测器资源"  target="_blank">创建异常检测器资源</a>来获取密钥和终结点。 等待其部署并选择“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到异常检测器 API。 稍后在快速入门中将密钥和终结点粘贴到下方的代码中。
    可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-net-core-application"></a>创建新的 .NET Core 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `anomaly-detector-quickstart-multivariate` 的新控制台应用。 此命令将创建包含单个 C# 源文件的简单“Hello World”项目：*Program.cs*。

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```dotnetcli
dotnet build
```

生成输出不应包含警告或错误。

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的异常检测器客户端库：

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

从项目目录中，打开 *Program.cs* 文件，并添加以下 using `directives`：

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

在应用程序的 `main()` 方法中，为资源的 Azure 终结点、API 密钥和自定义数据源创建变量。

> [!NOTE]
> 始终可在两个密钥之间任选其一。 这是为了实现安全的密钥轮换。 在本快速入门中，使用第一个密钥。 

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
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

## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 .NET 的异常检测器多变量客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [模型](#train-the-model)
* [检测异常](#detect-anomalies)
* [导出模型](#export-model)
* [删除模型](#delete-model)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和密钥实例化异常探测器客户端。

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>定型模型

按如下所示创建新的私有异步任务来处理模型训练。 你将使用 `TrainMultivariateModel` 来训练模型并使用 `GetMultivariateModelAysnc` 检查训练何时完成。

```csharp
private async Task<Guid?> trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        while (get_response.Value.ModelInfo.Status != ModelStatus.Ready & get_response.Value.ModelInfo.Status != ModelStatus.Failed)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, get_response.Value.ModelInfo.Status));
        }

        if (get_response.Value.ModelInfo.Status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Trainig failed."));
            IReadOnlyList<ErrorResponse> errors = get_response.Value.ModelInfo.Errors;
            foreach (ErrorResponse error in errors)
            {
                Console.WriteLine(String.Format("Error code: {0}.", error.Code));
                Console.WriteLine(String.Format("Error message: {0}.", error.Message));
            }
            throw new Exception("Training failed.");
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>检测异常

若要使用新训练的模型来检测异常，请创建一个名为 `detectAsync` 的 `private async Task`。 你将创建一个新的 `DetectionRequest`，并将其作为参数传递给 `DetectAnomalyAsync`。

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id,DateTimeOffset start_time, DateTimeOffset end_time)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        while (result.Value.Summary.Status != DetectionStatus.Ready & result.Value.Summary.Status != DetectionStatus.Failed)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Inference failed."));
            IReadOnlyList<ErrorResponse> errors = result.Value.Summary.Errors;
            foreach (ErrorResponse error in errors)
            {
                Console.WriteLine(String.Format("Error code: {0}.", error.Code));
                Console.WriteLine(String.Format("Error message: {0}.", error.Message));
            }
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>导出模型

> [!NOTE]
> Export 命令的作用在于使容器化环境中能够运行 Anomaly Detector 多变量模型。 目前不支持多变量，但将来会添加相关支持。

若要导出先前训练的模型，请创建一个名为 `exportAysnc` 的 `private async Task`。 你将使用 `ExportModelAsync` 并传递要导出的模型的模型 ID。

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>删除模型

若要删除先前创建的模型，请使用 `DeleteMultivariateModelAsync` 并传递要删除的模型的模型 ID。 若要检索模型 ID，可以使用 `getModelNumberAsync`：

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Main 方法

现在，你已拥有所有组件部分，需要将其他代码添加到 main 方法，以调用新创建的任务。

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnetcli
dotnet run
```
## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与资源组相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../../overview-multivariate.md)
* [使用异常检测器 API 时的最佳做法](../../concepts/best-practices-multivariate.md)。