---
title: 示例：使用 PersonDirectory 结构 - 人脸
titleSuffix: Azure Cognitive Services
description: 了解如何使用 PersonDirectory 数据结构来通过其他新功能存储更大容量的人脸和人员数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/22/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 0cba5fc8bb499ef52f6a2b1d89f23ddd6d450442
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603164"
---
# <a name="use-the-persondirectory-structure"></a>使用 PersonDirectory 结构

若要执行人脸识别操作（如识别和查找相似对象），人脸 API 客户需要创建一份 Person 对象的分类列表。 新的 PersonDirectory 是一种数据结构，其中包含添加到目录的每个人员身份识别的唯一 ID、可选名称字符串和可选用户元数据字符串 。

目前，人脸 API 提供 LargePersonGroup 结构，该结构具有相似功能，但限制添加 100,0000 个身份识别。 PersonDirectory 结构最多可扩展到 750,00000 个标识。

PersonDirectory 和之前的数据结构之间还存在一个重大差异，即在将人脸添加到 Person 对象后，便无需再执行任何定型调用，因为更新进程会自动发生。

## <a name="prerequisites"></a>先决条件
* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)。
* 拥有 Azure 订阅后，在 Azure 门户中[创建人脸资源](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)，获取密钥和终结点。 部署后，单击“转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到人脸 API。 可以将密钥和终结点粘贴到下方代码中。
  * 可以使用免费定价层 (F0) 试用该服务，然后再升级到付费层进行生产。

## <a name="add-persons-to-the-persondirectory"></a>将人员添加到 PersonDirectory
人员是 PersonDirectory 中的基本注册单元。 在将人员添加到目录后，你可以按识别模型向该人员添加最多 248 张人脸图像。 然后，你可以使用不同的作用域来识别这些图像中的人脸。

### <a name="create-the-person"></a>创建人员
若要创建人员，需要调用 CreatePerson API 并提供 name 或 userData 属性值。

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var addPersonUri = "https:// {endpoint}/face/v1.0-preview/persons";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Person");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(addPersonUri, content); 
}
```

调用 CreatePerson 将为相关人员和操作位置返回生成的 ID。 人员数据将以异步方式进行处理，因此你可以使用操作位置提取结果。 

### <a name="wait-for-asynchronous-operation-completion"></a>等待异步操作完成
你需要使用返回的操作位置字符串查询异步操作的状态，以检查操作进度。 

首先，应定义如下所示的数据模型来处理状态响应。

```csharp
[Serializable]
public class AsyncStatus
{
    [DataMember(Name = "status")]
    public string Status { get; set; }

    [DataMember(Name = "createdTime")]
    public DateTime CreatedTime { get; set; }

    [DataMember(Name = "lastActionTime")]
    public DateTime? LastActionTime { get; set; }

    [DataMember(Name = "finishedTime", EmitDefaultValue = false)]
    public DateTime? FinishedTime { get; set; }

    [DataMember(Name = "resourceLocation", EmitDefaultValue = false)]
    public string ResourceLocation { get; set; }

    [DataMember(Name = "message", EmitDefaultValue = false)]
    public string Message { get; set; }
}
```

然后，可以使用上文的 HttpResponseMessage 轮询 URL 并等待结果。

```csharp
string operationLocation = response.Headers.GetValues("Operation-Location").FirstOrDefault();

Stopwatch s = Stopwatch.StartNew();
string status = "notstarted";
do
{
    if (status == "succeeded")
    {
        await Task.Delay(500);
    }

    var operationResponseMessage = await client.GetAsync(operationLocation);

    var asyncOperationObj = JsonConvert.DeserializeObject<AsyncStatus>(await operationResponseMessage.Content.ReadAsStringAsync());
    status = asyncOperationObj.Status;

} while ((status == "running" || status == "notstarted") && s.Elapsed < TimeSpan.FromSeconds(30));
```


一旦状态返回为“成功”，则可将 Person 对象视为已添加到目录中。

> [!NOTE]
> 来自创建人员调用的异步操作无需显示“成功”状态，你便可将人脸添加到其中，但你需要先完成此操作，然后才能将人员添加到 DynamicPersonGroup 中（参阅下方的创建与更新 DynamicPersonGroup），或者在识别调用期间进行比较。 验证在将人脸成功添加到人员后，调用是否立即生效。


### <a name="add-faces-to-persons"></a>将人脸添加到人员

通过创建人员调用获得人员 ID 后，便可为每个识别模型的人员添加最多 248 张人脸图像。 指定要在调用中使用的识别模型（并选择性指定检测模型），因为每个识别模型下的数据将会在 PersonDirectory 中单独处理。

当前支持的识别模型包括：
* `Recognition_02`
* `Recognition_03`
* `Recognition_04`

此外，如果图像包含多个人脸，则需要为作为预期目标的人脸指定矩形边界框。 下列代码可用于将人脸添加到 Person 对象。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var queryString = "userData={userDefinedData}&targetFace={left,top,width,height}&detectionModel={detectionModel}";
var uri = "https://{endpoint}/face/v1.0-preview/persons/{personId}/recognitionModels/{recognitionModel}/persistedFaces?" + queryString;

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("url", "{image url}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

添加人脸调用后，人脸数据将以异步方式进行处理，而你需要以与之前相同的方式等待操作成功。

人脸添加操作完成后，相关数据将会在身份识别调用中准备就绪。

## <a name="create-and-update-a-dynamicpersongroup"></a>创建并更新 **DynamicPersonGroup**

DynamicPersonGroups 是对 PersonDirectory 中 Person  对象的引用集合；它们将用于创建目录的子集。 一种常见的用途是，如果你想要在执行身份识别操作时减少误报并增加准确性，只需将作用域限制为你期望匹配的 Person 对象即可。 实际用例包括在较大校园或组织中访问特定建筑物的目录。 组织目录可能包含 500,0000 个人，但你只需通过搜索特定 800 个人来找到特定的建筑物，因此你可以创建一个包含这些特定个人的 DynamicPersonGroup。 

如果之前使用过 PersonGroup，请注意两个主要区别： 
* DynamicPersonGroup 中的每个人员都是对 PersonDirectory 中实际人员的引用，这意味着无需在每个组中重新创建人员    。
* 如先前章节中所述，你无需执行定型调用，因为人脸数据会自动在目录级别进行处理。

### <a name="create-the-group"></a>创建组

若要创建 DynamicPersonGroup，需要提供包含字母数字或破折号字符的组 ID。 此 ID 将用作组的所有用途的唯一标识符。

有两种方式可以初始化组集合。 你可以一开始创建一个空组，并于之后填充：

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);
}
```

此为即时过程，无需等待任何异步操作成功。

或者，你可以使用一组人员 ID 来创建组，并通过在 AddPersonIds 参数中提供集以从一开始便将这些引用包含在其中：

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

> [!NOTE]
> 一旦调用返回，已创建的 DynamicPersonGroup 便可在身份识别调用中使用，而且该过程会提供任何人员引用。 另一方面，返回的操作 ID 的完成状态可指示“人员对组”关系的更新状态。

### <a name="update-the-dynamicpersongroup"></a>更新 DynamicPersonGroup

初始创建之后，可以通过更新动态人员组 API 添加和删除来自 DynamicPersonGroup 的人员引用。 若要将 Person 对象添加到组，请在 addPersonsIds 参数中列出人员 ID。 若要删除 Person 对象，请在 removePersonIds 参数中列出这些对象。 添加和删除操作都可在单个调用中执行：

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Dynamic Person Group updated");
body.Add("userData", "User defined data updated");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("removePersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PatchAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

一旦调用返回，系统将会在查询组时反映集合的更新。 与创建 API 一样，返回的操作将指示与更新相关的任何人员的“人员对组”关系的更新状态。 在对组进一步执行更新调用之前，你不需要等待操作完成。

## <a name="identify-faces-in-a-persondirectory"></a>识别 PersonDirectory 中的人脸

在 PersonDirectory 中使用人脸数据最常见的方法是将已注册的 Person 对象与给定的人脸进行比较，并找出其最可能所属的候选对象。 请求中可提供多个人脸，每个人脸都将在响应中收到自己的一组比较结果。

在 PersonDirectory 中，每个人脸都可以通过三种类型的作用域来进行识别：

### <a name="scenario-1-identify-against-a-dynamicpersongroup"></a>方案 1：根据 DynamicPersonGroup 进行识别
 
通过在请求中指定 dynamicPersonGroupId 属性，便可将人脸与组中引用的每个人员 进行比较。 只能在调用中识别单个 DynamicPersonGroup。 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("dynamicPersonGroupId", "{dynamicPersonGroupIdToIdentifyIn}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-2-identify-against-a-specific-list-of-persons"></a>方案 2：根据特定人员列表进行识别

你还可以在 personIds 属性中指定人员 ID 列表，以将人脸与其中的每个人员进行比较。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-3-identify-against-the-entire-persondirectory"></a>方案 3：根据整个 PersonDirectory 进行识别

在请求的 personIds 属性中提供单个星号，便会将人脸与在 PersonDirectory 中注册的每个人员进行比较 。 
 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```
在这三种方案中，识别只会将输入的人脸与 AddPersonFace 调用以“成功”响应返回的人脸进行比较。

## <a name="verify-faces-against-persons-in-the-persondirectory"></a>验证 PersonDirectory 中人员的人脸

通过自检测调用返回的人脸 ID，你可以验证人脸是否属于在 PersonDirectory 中注册的特定人员 。 使用 personId 属性指定人员。

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/verify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceId", "{guid1}");
body.Add("personId", "{guid1}");
var jsSerializer = new JavaScriptSerializer();
byte[] byteData = Encoding.UTF8.GetBytes(jsSerializer.Serialize(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

响应将包含布尔值（指示服务是否将新的人脸视为属于同一人员），以及用于预测的可信度分数。

## <a name="next-steps"></a>后续步骤

在本指南中，你已经学习了如何使用 PersonDirectory 结构来存储人脸应用的人脸和人员数据。 接下来，请学习添加用户人脸数据的最佳做法。

* [有关添加用户的最佳做法](../enrollment-overview.md)
