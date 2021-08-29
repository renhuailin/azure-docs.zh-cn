---
title: 异常检测器多变量 Java 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: cbea7a93d80a0d8f68b23cbcfde92d34d5a0d1d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803178"
---
开始使用适用于 Java 的异常检测器多变量客户端库。 请按照以下步骤操作，以使用服务提供的算法安装软件包。 新的多变量异常情况检测 API 使开发人员能够轻松地集成高级 AI 来检测指标组中的异常，且无需机器学习知识或标记的数据。 不同信号之间的依赖关系和相互关联会自动计为关键因素。 这可以帮助你主动防范复杂系统发生故障。

使用适用于 Java 的异常检测器多变量客户端库，以便：

* 检测一组时序中的系统级异常。
* 当任何单独的时序都不能告知太多信息时，而你不得不查看所有信号来检测问题。
* 使用数十到数百种不同类型的传感器对昂贵的物理资产进行预测维护，以测量系统运行状况的各个方面。

[库参考文档](/java/api/com.azure.ai.anomalydetector) | [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [包 (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/) | [示例代码](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版本的 [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 拥有 Azure 订阅后，可在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="创建异常检测器资源"  target="_blank">创建异常检测器资源</a>来获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到异常检测器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

本快速入门使用 Gradle 依赖项管理器。 可在 [Maven 中央存储库](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)中找到有关客户端库的详细信息。

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

### <a name="install-the-client-library"></a>安装客户端库

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后将以下生成配置复制到其中。 请确保包含项目依赖项。

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>创建 Java 文件

为示例应用创建一个文件夹。 在工作目录中运行以下命令：

```console
mkdir -p src/main/java
```

导航到新文件夹，并创建名为 MetricsAdvisorQuickstarts.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

为资源的 Azure 终结点和密钥创建变量。 为示例数据文件创建另一个变量。

> [!NOTE]
> 始终可以在两个密钥之间选择一个使用。 这是为了实现安全的密钥轮换。 在本快速入门中，使用第一个密钥。 

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
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

这些代码片段展示如何使用适用于 Node.js 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [定型模型](#train-a-model)
* [检测异常](#detect-anomalies)
* [导出模型](#export-model)
* [删除模型](#delete-model)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和凭据实例化 `anomalyDetectorClient` 对象。

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>训练模型

### <a name="construct-a-model-result-and-train-model"></a>构造模型结果和训练模型

首先，我们需要构造模型请求。 请确保开始时间和结束时间与数据源一致。

若要使用异常检测器多变量 API，我们需要在使用检测之前先训练自己的模型。 用于训练的数据是一批时序，每个时序应采用 CSV 文件格式，其中仅包含两列，即“时间戳”和“值”（列名应完全相同） 。 每个 CSV 文件都应该以时序的每个变量命名。 所有时序都应压缩为一个 zip 文件并上传到 [Azure Blob 存储](../../../../storage/blobs/storage-blobs-introduction.md#blobs)，但对于 zip 文件名没有要求。 或者，如果你希望变量名称与 .zip 文件名不同，也可以在 zip 文件中包含一个额外的 meta.json 文件。 当我们生成 [blob SAS（共享访问签名）URL](../../../../storage/common/storage-sas-overview.md) 后，就可以使用 zip 文件的 URL 进行训练了。

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy()
                                .setAlignMode(alignMode)
                                .setFillNAMethod(fillNAMethod)
                                .setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo()
                        .setSlidingWindow(window)
                        .setAlignPolicy(alignPolicy)
                        .setSource(source)
                        .setStartTime(startTime)
                        .setEndTime(endTime)
                        .setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] substring = header.split("/");
UUID modelId = UUID.fromString(substring[substring.length - 1]);
System.out.println(modelId);

//Check model status until the model is ready
Response<Model> trainResponse;
while (true) {
    trainResponse = anomalyDetectorClient.getMultivariateModelWithResponse(modelId, Context.NONE);
    ModelStatus modelStatus = trainResponse.getValue().getModelInfo().getStatus();
    if (modelStatus == ModelStatus.READY || modelStatus == ModelStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (trainResponse.getValue().getModelInfo().getStatus() != ModelStatus.READY){
    System.out.println("Training failed.");
    List<ErrorResponse> errorMessages = trainResponse.getValue().getModelInfo().getErrors();
    for (ErrorResponse errorMessage : errorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="detect-anomalies"></a>检测异常

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(modelId, detectionRequest, Context.NONE);
String location = detectAnomalyResponse.getDeserializedHeaders().getLocation();
String[] substring = location.split("/");
UUID resultId = UUID.fromString(substring[substring.length - 1]);

DetectionResult detectionResult;
while (true) {
    detectionResult = anomalyDetectorClient.getDetectionResult(resultId);
    DetectionStatus detectionStatus = detectionResult.getSummary().getStatus();;
    if (detectionStatus == DetectionStatus.READY || detectionStatus == DetectionStatus.FAILED) {
        break;
    }
    TimeUnit.SECONDS.sleep(10);
}

if (detectionResult.getSummary().getStatus() != DetectionStatus.READY){
    System.out.println("Inference failed");
    List<ErrorResponse> detectErrorMessages = detectionResult.getSummary().getErrors();
    for (ErrorResponse errorMessage : detectErrorMessages) {
        System.out.println("Error code:  " + errorMessage.getCode());
        System.out.println("Error message:  " + errorMessage.getMessage());
    }
}
```

## <a name="export-model"></a>导出模型

> [!NOTE]
> Export 命令的作用在于使容器化环境中能够运行 Anomaly Detector 多变量模型。 目前不支持多变量，但将来会添加相关支持。

若要导出训练的模型，请使用 `exportModelWithResponse`。

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>删除模型

若要删除可用于当前资源的现有模型，请使用 `deleteMultivariateModelWithResponse` 函数。

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```
### <a name="run-the-application"></a>运行应用程序

在运行它之前，请针对[完整的示例代码](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)检查你的代码，这会很有帮助。

使用 `run` 目标运行应用程序：

```console
gradle run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与资源组相关联的任何其他资源。

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../../overview-multivariate.md)
* [使用异常检测器 API 时的最佳做法](../../concepts/best-practices-multivariate.md)。
