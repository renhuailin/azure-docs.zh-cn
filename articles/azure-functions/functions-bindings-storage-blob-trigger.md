---
title: 适用于 Azure Functions 的 Azure Blob 存储触发器
description: 了解如何在 Azure Blob 存储数据更改时运行 Azure 函数。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b1be9681246711cfd722bdc4d4806d75875f3429
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179291"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>适用于 Azure Functions 的 Azure Blob 存储触发器

检测到新的或更新的 Blob 时，Blob 存储触发器会启动某个函数。 Blob 内容以[函数输入](./functions-bindings-storage-blob-input.md)的形式提供。

Azure Blob 存储触发器需要使用常规用途存储帐户。 还支持具有[分层命名空间](../storage/blobs/data-lake-storage-namespace.md)的存储 V2 帐户。 若要使用仅限 Blob 的帐户，或者，如果应用程序有特殊需求，请查看使用此触发器的替代方法。

若要了解设置和配置详细信息，请参阅[概述](./functions-bindings-storage-blob.md)。

## <a name="polling"></a>轮询

轮询在检查日志和运行定期容器扫描之间起到混合作用。 每次以 10,000 个为一组扫描 Blob，并在间隔之间使用继续标记。

> [!WARNING]
> 此外，[将“尽力”创建存储日志](/rest/api/storageservices/About-Storage-Analytics-Logging)。 不保证捕获所有事件。 在某些情况下可能会遗漏某些日志。
> 
> 如果需要更快或更可靠的 blob 处理，在创建 blob 时，请考虑创建[队列消息](../storage/queues/storage-dotnet-how-to-use-queues.md)。 然后，使用[队列触发器](functions-bindings-storage-queue.md)而不是 Blob 触发器来处理 Blob。 另一个选项是使用事件网格；请参阅教程[使用事件网格自动调整上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)。
>

## <a name="alternatives"></a>备选方法

### <a name="event-grid-trigger"></a>事件网格触发器

> [!NOTE]
> 使用 Storage Extension 5.x 及更高版本时，Blob 触发器提供对基于事件网格的 Blob 触发器的内置支持。 有关详细信息，请参阅下面的[存储扩展 5.x 及更高版本](#storage-extension-5x-and-higher)部分。

[事件网格触发器](functions-bindings-event-grid.md)还为 [blob 事件](../storage/blobs/storage-blob-event-overview.md)提供内置支持。 以下方案请使用事件网格而不是 Blob 存储触发器：

- **仅限 Blob 的存储帐户**：[仅限 Blob 的存储帐户](../storage/common/storage-account-overview.md#types-of-storage-accounts)适用于 Blob 输入和输出绑定，但不适用于 Blob 触发器。

- **大规模**：大规模可以宽松地定义为包含 100,000 个以上的 Blob 的容器，或者定义为每秒进行 100 个以上 Blob 更新的存储帐户。

- **现有 blob**：设置触发器时，blob 触发器将处理容器中的所有现有 blob。 如果容器包含多个现有 blob，并且只想要对新 blob 触发，请使用事件网格触发器。

- **最大程度地降低延迟**：如果函数应用基于消耗计划，则当函数应用处于空闲状态时，处理新 Blob 会出现长达 10 分钟的延迟。 若要避免此延迟，可以切换到启用了 Always On 的应用服务计划。 还可以为 Blob 存储帐户使用[事件网格触发器](functions-bindings-event-grid.md)。 有关示例，请参阅[事件网格教程](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)。

请参阅事件网格示例的[使用事件网格重试图像大小](../event-grid/resize-images-on-storage-blob-upload-event.md)教程。

#### <a name="storage-extension-5x-and-higher"></a>存储扩展 5.x 及更高版本

使用预览版存储扩展时，Blob 触发器中提供对事件网格的内置支持，需要你在现有 Blob 触发器中将 `source` 参数设置为事件网格。 

要详细了解如何使用基于事件网格的 Blob 触发器，请参阅[事件网格 Blob 触发器指南](./functions-event-grid-blob-trigger.md)。

### <a name="queue-storage-trigger"></a>队列存储触发器

处理 blob 的另一种方法是编写对应于正在创建或修改的 blob 的队列消息，然后使用[队列存储触发器](./functions-bindings-storage-queue.md)开始处理。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示在 `samples-workitems` 容器中添加或更新 blob 时写入日志的 [C# 函数](functions-dotnet-class-library.md)。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

blob 触发器路径 `samples-workitems/{name}` 中的字符串 `{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 `BlobTrigger` 属性的详细信息，请参阅[属性和注释](#attributes-and-annotations)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例显示了 *function.json* 文件中的一个 blob 触发器绑定以及使用该绑定的代码。 在 `samples-workitems` [容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)中添加或更新 Blob 时，该函数会写入一条日志。

下面是 function.json 文件中的绑定数据：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `samples-workitems/{name}` 中的字符串 `{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的 [配置](#configuration)部分。

下面是绑定到 `Stream` 的 C# 脚本代码：

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

下面是绑定到 `CloudBlockBlob` 的 C# 脚本代码：

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="java"></a>[Java](#tab/java)

在 `myblob` 容器中添加或更新 Blob 时，该函数会写入一条日志。

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例显示了 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [JavaScript 代码](functions-reference-node.md)。 在 `samples-workitems` 容器中添加或更新 Blob 时，该函数会写入日志。

function.json 文件如下所示：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `samples-workitems/{name}` 中的字符串 `{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的 [配置](#configuration)部分。

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

下面的示例演示如何创建在将文件添加到 `source` blob 存储容器时运行的函数。

函数配置文件 (_function.json_) 包含一个绑定，其 `type` 为 `blobTrigger` 且 `direction` 设置为 `in`。

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "MyStorageAccountConnectionString"
    }
  ]
}
```

下面是 run.ps1 文件的关联代码。

```powershell
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

以下示例显示了 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [Python 代码](functions-reference-python.md)。 在 `samples-workitems` [容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)中添加或更新 Blob 时，该函数会写入一条日志。

function.json 文件如下所示：

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `samples-workitems/{name}` 中的字符串 `{name}` 会创建一个[绑定表达式](./functions-bindings-expressions-patterns.md)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的 [配置](#configuration)部分。

下面是 Python 代码：

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

对于 [C# 类库](functions-dotnet-class-library.md)，请使用以下属性来配置 blob 触发器：

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  该特性的构造函数采用一个表示要监视的容器的路径字符串，并选择性地采用某种 [Blob 名称模式](#blob-name-patterns)。 下面是一个示例：

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  有关完整示例，请参阅[触发器示例](#example)。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

要使用的存储帐户按以下顺序确定：

* `BlobTrigger` 特性的 `Connection` 属性。
* 作为 `BlobTrigger` 特性应用到同一参数的 `StorageAccount` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* 函数应用的默认存储帐户（“AzureWebJobsStorage”应用设置）。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger` 特性用于授予对触发函数的 blob 的访问权限。 有关详细信息，请参阅[触发器示例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `BlobTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `blobTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 [用法](#usage)部分中已阐述异常。 |
|**name** | 不适用 | 表示函数代码中的 Blob 的变量的名称。 |
|**路径** | **BlobPath** |要监视的[容器](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)。  可以是某种 [Blob 名称模式](#blob-name-patterns)。 |
|连接 | **Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>连接字符串必须属于某个常规用途存储帐户，而不能属于[Blob 存储帐户](../storage/common/storage-account-overview.md#types-of-storage-accounts)。<br><br>如果使用 [5.x 版或更高版本的扩展](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)，而不是使用连接字符串，则可以提供对用于定义连接的配置节的引用。 请参阅[连接](./functions-reference.md#connections)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger` 特性用于授予对触发函数的 blob 的访问权限。 有关详细信息，请参阅[触发器示例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<NAME>` 访问 blob 数据，其中 `<NAME>` 与 function.json 中定义的值匹配。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

通过与 function.json 文件中绑定名称参数指定的名称匹配的字符串参数访问 Blob 数据。

# <a name="python"></a>[Python](#tab/python)

通过类型为 [InputStream](/python/api/azure-functions/azure.functions.inputstream) 的参数访问 blob 数据。 有关详细信息，请参阅[触发器示例](#example)。

---

## <a name="blob-name-patterns"></a>Blob 名称模式

可以在 *function.json* 的 `path` 属性中或者在 `BlobTrigger` 特性构造函数中指定 Blob 名称模式。 名称模式可以是[筛选器或绑定表达式](./functions-bindings-expressions-patterns.md)。 以下部分提供了有关示例。

> [!TIP]
> 容器名称不能在名称模式中包含解析程序。

### <a name="get-file-name-and-extension"></a>获取文件名和扩展名

以下示例演示如何分别绑定到 Blob 文件名和扩展名：

```json
"path": "input/{blobname}.{blobextension}",
```

如果 Blob 名为 *original-Blob1.txt*，则函数代码中 `blobname` 和 `blobextension` 变量的值为 *original-Blob1* 和 *txt*。

### <a name="filter-on-blob-name"></a>按 Blob 名称筛选

以下示例仅根据 `input` 容器中以字符串“original-”开头的 Blob 触发：

```json
"path": "input/original-{name}",
```

如果 Blob 名称为 *original-Blob1.txt*，则函数代码中 `name` 变量的值为 `Blob1.txt`。

### <a name="filter-on-file-type"></a>按文件类型筛选

以下示例仅根据 *.png* 文件触发：

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>按文件名中的大括号筛选

若要查找文件名中的大括号，请使用两个大括号将大括号转义。 以下示例筛选名称中包含大括号的 Blob：

```json
"path": "images/{{20140101}}-{name}",
```

如果 Blob 名为 *{20140101}-soundfile.mp3*，则函数代码中的 `name` 变量值为 *soundfile.mp3*。

## <a name="metadata"></a>Metadata

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="java"></a>[Java](#tab/java)

元数据在 Java 中不可用。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

可以通过 `$TriggerMetadata` 参数获取元数据。

# <a name="python"></a>[Python](#tab/python)

元数据在 Python 中不可用。

---

## <a name="blob-receipts"></a>Blob 回执

Azure Functions 运行时确保没有为相同的新 blob 或更新 blob 多次调用 blob 触发器函数。 为了确定是否已处理给定的 blob 版本，它会维护 *blob 回执*。

Azure Functions 将 Blob 回执存储在函数应用的 Azure 存储帐户中名为 azure-webjobs-hosts 的容器中（由 `AzureWebJobsStorage` 应用设置定义）。 Blob 回执包含以下信息：

* 触发的函数（`<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>`，例如：`MyFunctionApp.Functions.CopyBlob`）
* 容器名称
* Blob 类型（`BlockBlob` 或 `PageBlob`）
* Blob 名称
* ETag（blob 版本标识符，例如：`0x8D1DC6E70A277EF`）

若要强制重新处理某个 blob，可从 azure-webjobs-hosts 容器中手动删除该 blob 的 blob 回执。 虽然重新处理可能不会立即发生，但它肯定会在稍后的时间点发生。 若要立即重新处理，可以更新 azure-webjobs-hosts/blobscaninfo 中的 scaninfo Blob 。 将再次扫描 `LatestScan` 属性后具有上次修改时间戳的任何 Blob。

## <a name="poison-blobs"></a>有害 Blob

当给定 blob 的 blob 触发函数失败时，Azure Functions 将默认重试该函数共计 5 次。

如果 5 次尝试全部失败，Azure Functions 会将消息添加到名为 webjobs-blobtrigger-poison 的存储队列。 最大尝试次数可配置。 使用相同的 MaxDequeueCount 设置处理有害 Blob 和有害队列消息。 有害 Blob 的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>`）
* BlobType（`BlockBlob` 或 `PageBlob`）
* ContainerName
* BlobName
* ETag（blob 版本标识符，例如：`0x8D1DC6E70A277EF`）

## <a name="concurrency-and-memory-usage"></a>并发和内存使用情况

Blob 触发器可在内部使用队列，因此并发函数调用的最大数量受 [host.json 中的队列配置](functions-host-json.md#queues)控制。 默认设置会将并发限制到 24 个调用。 此限制分别应用于使用 blob 触发器的函数。

> [!NOTE]
> 对于使用 [5.0.0 或更高版本的存储扩展](functions-bindings-storage-blob.md#storage-extension-5x-and-higher)的应用，host.json 中的队列配置仅适用于队列触发器。 而 blob 触发器并发由 [host.json 中的 blob 配置](functions-host-json.md#blobs)控制。

[消耗计划](event-driven-scaling.md)将虚拟机 (VM) 上的函数应用限制为 1.5 GB 内存。 内存由每个并发执行函数实例和函数运行时本身使用。 如果 blob 触发的函数将整个 blob 加载到内存中，该函数使用的仅用于 blob 的最大内存为 24 * 最大 blob 大小。 例如，包含 3 个由 blob 触发的函数的函数应用和默认设置，其每 VM 最大并发为 3*24 = 72 个函数调用。

JavaScript 和 Java 函数会将整个 blob 加载到内存中，并且如果绑定到 `string` 或 `Byte[]`，则 C# 函数也会如此。

## <a name="hostjson-properties"></a>host.json 属性

[host.json](functions-host-json.md#blobs) 文件包含控制 blob 触发器行为的设置。 有关可用设置的详细信息，请参阅 [host.json 设置](functions-bindings-storage-blob.md#hostjson-settings)部分。

## <a name="next-steps"></a>后续步骤

- [函数运行时读取 blob 存储数据](./functions-bindings-storage-blob-input.md)
- [通过函数写入 blob 存储数据](./functions-bindings-storage-blob-output.md)
