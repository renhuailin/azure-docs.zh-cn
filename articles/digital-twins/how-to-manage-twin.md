---
title: 管理数字孪生
titleSuffix: Azure Digital Twins
description: 请参阅如何检索、更新和删除单个孪生和关系。
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ba444a497fa4fccab6b8dec1fadb3383420e4d49
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452967"
---
# <a name="manage-digital-twins"></a>管理数字孪生

环境中的实体由 [数字孪生](concepts-twins-graph.md)表示。 管理数字孪生可能包括创建、修改和删除。 若要执行这些操作，可以使用 [**DigitalTwins api**](/rest/api/digital-twins/dataplane/twins)、 [.Net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)或 [Azure 数字孪生 CLI](how-to-use-cli.md)。

本文重点介绍如何管理数字孪生;若要整体处理关系和整数 [关系图](concepts-twins-graph.md) ，请参阅 [*操作方法：管理包含关系的双子关系图*](how-to-manage-graph.md)。

> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>管理孪生的方式

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>创建数字输出

若要创建克隆，请在 `CreateOrReplaceDigitalTwinAsync()` 服务客户端上使用方法，如下所示：

```csharp
await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myTwinId", initData);
```

若要创建数字克隆，需要提供：
* 数字克隆所需的 ID
* 要使用的[模型](concepts-models.md)

（可选）可以为数字输出的所有属性提供初始值。 属性被视为可选的，并且可以在以后进行设置，但 **在设置之前，不会将它们显示为完全不包含。**

>[!NOTE]
>尽管不需要初始化克隆的属性，但在创建克隆时，**需要设置** 克隆上的所有 [组件](concepts-models.md#elements-of-a-model)。 它们可以是空对象，但组件本身必须存在。

通过参数提供模型和任何初始属性值，该 `initData` 参数是一个包含相关数据的 JSON 字符串。 有关构造此对象的详细信息，请转到下一节。

> [!TIP]
> 创建或更新克隆后，可能会有长达10秒的延迟，更改将反映在 [查询](how-to-query-graph.md)中。 `GetDigitalTwin`[本文后面](#get-data-for-a-digital-twin)所述的 API () 不会遇到此延迟，因此，如果需要即时响应，请使用 API 调用而不是查询来查看新创建的孪生。 

### <a name="initialize-model-and-properties"></a>初始化模型和属性

您可以在创建克隆时初始化一个静态属性。 

克隆创建 API 接受序列化为克隆属性的有效 JSON 说明的对象。 请参阅 " [*概念：数字孪生" 和 "克隆图形"*](concepts-twins-graph.md) ，以获取对一种对的 JSON 格式的说明。 

首先，您可以创建一个数据对象来表示克隆及其属性数据。 可以手动创建参数对象，也可以使用提供的帮助器类创建参数对象。 下面是每个的示例。

#### <a name="create-twins-using-manually-created-data"></a>使用手动创建的数据创建孪生

如果不使用任何自定义帮助器类，则可以在中表示克隆的属性 `Dictionary<string, object>` ，其中 `string` 是属性的名称， `object` 是表示属性及其值的对象。

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>用 helper 类创建孪生

使用的帮助程序类， `BasicDigitalTwin` 可以直接在 "克隆" 对象中存储属性字段。 你仍可以使用生成属性列表 `Dictionary<string, object>` ，然后可以将其直接添加到克隆的对象 `CustomProperties` 。

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myRoomId", twin);
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` 对象附带了一个 `Id` 字段。 可以将此字段保留为空，但如果添加了一个 ID 值，则它需要与传递给调用的 ID 参数匹配 `CreateOrReplaceDigitalTwinAsync()` 。 例如：
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>获取数字克隆的数据

可以通过调用方法来访问任何数字克隆的详细信息， `GetDigitalTwin()` 如下所示：

```csharp
object result = await client.GetDigitalTwin(id);
```
此调用返回作为强类型对象类型（如）的非整型数据 `BasicDigitalTwin` 。 `BasicDigitalTwin` 是 SDK 中包含的序列化帮助器类，它将返回以预分析形式返回的核心数据和属性。 下面的示例演示如何使用此方法来查看克隆的详细信息：

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin("myRoomId");
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
  if (twin.Contents.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
使用方法检索次克隆时，仅返回至少已设置一次的属性 `GetDigitalTwin()` 。

>[!TIP]
>"克隆" 的 `displayName` 是其模型元数据的一部分，因此，在为克隆的实例获取数据时，它将不会显示。 若要查看此值，可以 [从模型中检索它](how-to-manage-model.md#retrieve-models)。

若要使用单个 API 调用检索多个孪生，请参阅 [*如何：查询双子图*](how-to-query-graph.md)中的查询 API 示例。

请考虑以下模型 (以 [数字孪生定义语言编写， (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) 定义 *月球*：

```json
{
    "@id": "dtmi:example:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```
`object result = await client.GetDigitalTwinAsync("my-moon");`对 *月亮* 类型的硬类型调用的结果如下所示：

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

数字克隆的已定义属性在数字克隆上作为顶级属性返回。 不属于 DTDL 定义的元数据或系统信息将以 `$` 前缀返回。 元数据属性包括：
* 此 Azure 数字孪生实例中数字输出的 ID，如 `$dtId` 。
* `$etag`，由 web 服务器分配的标准 HTTP 字段。
* 节中的其他属性 `$metadata` 。 其中包括：
    - 数字克隆的模型的 DTMI。
    - 每个可写属性的同步状态。 这对于设备最为有用，在这种情况下，在设备处于) 脱机状态时，服务和设备可能会 (分叉状态。 目前，此属性仅适用于连接到 IoT 中心的物理设备。 使用元数据部分中的数据，可以了解属性的完整状态以及上次修改的时间戳。 有关同步状态的详细信息，请参阅有关同步设备状态的 [此 IoT 中心教程](../iot-hub/tutorial-device-twins.md) 。
    - 服务特定的元数据，如 IoT 中心或 Azure 数字孪生。 

可以阅读有关序列化帮助程序类的详细信息，如 `BasicDigitalTwin` [*操作方法：使用 Azure 数字孪生 Api 和 sdk*](how-to-use-apis-sdks.md)。

## <a name="view-all-digital-twins"></a>查看所有数字孪生

若要查看实例中的所有数字孪生，请使用 [查询](how-to-query-graph.md)。 您可以使用 [查询 api](/rest/api/digital-twins/dataplane/query) 或 [CLI 命令](how-to-use-cli.md)来运行查询。

下面是基本查询的正文，该查询将返回实例中所有数字孪生的列表：

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="update-a-digital-twin"></a>更新数字孪生

若要更新数字克隆的属性，请编写要替换为 [JSON 修补](http://jsonpatch.com/) 格式的信息。 通过这种方式，可以一次替换多个属性。 然后将 JSON 修补文档传递到方法中 `UpdateDigitalTwin()` ：

```csharp
await client.UpdateDigitalTwin(id, patch);
```

修补调用可以更新单个克隆上的多个属性， (甚至所有这些属性都) 。 如果需要跨多个孪生更新属性，则每个克隆都需要单独的更新调用。

> [!TIP]
> 创建或更新克隆后，可能会有长达10秒的延迟，更改将反映在 [查询](how-to-query-graph.md)中。 `GetDigitalTwin`[本文前面](#get-data-for-a-digital-twin)所述的 API () 不会遇到此延迟，因此，如果需要即时响应，请使用 api 调用而不是查询来查看新更新的孪生。 

下面是 JSON 修补程序代码的示例。 此文档替换其所应用到的数字输出的 *质量* 和 *半径* 属性值。

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```
您可以使用 SDK 中的来创建修补程序 `JsonPatchDocument` 。 [SDK](how-to-use-apis-sdks.md) 示例如下。

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
```

### <a name="update-properties-in-digital-twin-components"></a>更新数字克隆组件中的属性

请记住，模型可以包含组件，从而使其可由其他模型组成。 

若要在数字克隆的组件中修补属性，可以使用 JSON 修补程序中的路径语法：

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>更新数字输出的模型

`UpdateDigitalTwin()`函数还可用于将数字克隆迁移到其他模型。 

例如，请考虑以下 JSON 修补程序文档，该文档将替换数字克隆的元数据 `$model` 字段：

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

仅当修补程序修改的数字克隆符合新的模型时，此操作才会成功。 

请看下面的示例：
1. 假设有一个数字克隆，其型号为 *foo_old*。 *foo_old* 定义所需的属性 *质量*。
2. 新模型 *foo_new* 定义属性质量并添加新的必需属性 *温度*。
3. 修补后，数字克隆必须同时具有质量和温度属性。 

此情况的修补程序需要更新模型和克隆的温度属性，如下所示：

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>处理冲突的更新调用

Azure 数字孪生确保所有传入的请求经过一次处理。 这意味着即使多个函数尝试同时更新静态上的相同属性，也 **不需要** 编写显式锁定代码来处理冲突。

此行为以每个随机的方式进行。 

例如，假设这三个调用同时到达的情况如下： 
*   在 *Twin1* 上写入属性 A
*   在 *Twin1* 上写入属性 B
*   在 *Twin2* 上写入属性 A

修改 *Twin1* 的两个调用会再执行一次，并为每个更改生成更改消息。 修改 *Twin2* 的调用可能会在不冲突的情况下同时执行，而不会发生冲突。

## <a name="delete-a-digital-twin"></a>删除数字克隆

可以使用方法删除孪生 `DeleteDigitalTwin()` 。 但是，如果没有更多的关系，则只能删除一个克隆。 因此，请先删除克隆的传入和传出关系。

下面是用于删除孪生及其关系的代码示例：

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

        await foreach (BasicRelationship rel in rels)
        {
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>删除所有数字孪生

有关如何同时删除所有孪生的示例，请下载 _Tutorial 中使用的示例应用 [：使用示例客户端应用了解基础知识](tutorial-command-line-app.md)。 *CommandLoop.cs* 文件在函数中执行此 `CommandDeleteAllTwins()` 功能。

## <a name="runnable-digital-twin-code-sample"></a>可运行的数字克隆代码示例

您可以使用下面的可运行代码示例创建一个克隆，更新其详细信息，然后删除克隆。 

### <a name="set-up-the-runnable-sample"></a>设置可运行示例

此代码片段使用教程中模型定义的 [Room.js](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) [*：使用示例客户端应用浏览 Azure 数字孪生*](tutorial-command-line-app.md)。 你可以使用此链接直接前往文件，或将其作为完整的端到端 [示例项目的](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)一部分进行下载。

在运行该示例之前，请执行以下操作：
1. 下载模型文件，将其放在您的项目中，并替换 `<path-to>` 以下代码中的占位符，告诉您的程序在何处找到该文件。
2. 将占位符替换 `<your-instance-hostname>` 为你的 Azure 数字孪生实例的主机名。
3. 将这些包添加到你的项目：
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

如果要直接运行该示例，还需要设置本地凭据。 下一节将对此进行演练。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>运行示例

完成上述步骤后，可以直接运行以下示例代码。

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload a model");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            // Upload the model to the service
            await client.CreateModelsAsync(typeList);

            //Create new digital twin
            BasicDigitalTwin twin = new BasicDigitalTwin();
            string twin_Id = "myRoomId";
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.Contents = props;
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin_Id, twin);
            Console.WriteLine("Twin created successfully");
            Console.WriteLine();

            //Print twin
            Console.WriteLine("--- Printing twin details:");
            twin = FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Update twin data
            var updateTwinData = new JsonPatchDocument();
            updateTwinData.AppendAdd("/Temperature", 25.0);
            await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
            Console.WriteLine("Twin properties updated");
            Console.WriteLine();

            //Print twin again
            Console.WriteLine("--- Printing twin details (after update):");
            FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete twin
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_Id);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.Contents.Keys)
            {
                if (twin.Contents.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        private static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        private static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

                await foreach (BasicRelationship rel in rels)
                {
                    await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       private static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
下面是上述程序的控制台输出： 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="显示已创建、更新和删除克隆的控制台输出" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>后续步骤

请参阅如何创建和管理数字孪生之间的关系： _ 操作 [*方法：管理包含关系的克隆图形*](how-to-manage-graph.md)