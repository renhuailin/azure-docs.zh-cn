---
title: 使用 Visual Studio Code 将 Azure Functions 连接到 Azure Cosmos DB
description: 了解如何通过将输出绑定添加到 Visual Studio Code 项目，从而将 Azure Functions 连接到 Azure Cosmos DB 帐户。
author: ThomasWeiss
ms.date: 08/17/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 9fbc67f14d6d67393b62129f2b4e9269fb6c6f52
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446234"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>使用 Visual Studio Code 将 Azure Functions 连接到 Azure Cosmos DB

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

本文介绍如何使用 Visual Studio Code 将 [Azure Cosmos DB](../cosmos-db/introduction.md) 连接到在前一篇快速入门文章中创建的函数。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到存储在 Azure Cosmos DB 容器中的 JSON 文档。 

::: zone pivot="programming-language-csharp"
在开始之前，必须先完成[快速入门：使用 Visual Studio Code 在 Azure 中创建 C# 函数](create-first-function-vs-code-csharp.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。
::: zone-end
::: zone pivot="programming-language-javascript"  
在开始之前，必须先完成[快速入门：使用 Visual Studio Code 在 Azure 中创建 JavaScript 函数](create-first-function-vs-code-node.md)。 如果在该文章结束时清理了资源，请再次执行相应的步骤，以在 Azure 中重新创建函数应用和相关资源。  
::: zone-end

## <a name="configure-your-environment"></a>配置环境

在开始之前，请确保安装适用于 Visual Studio Code 的 [Azure 数据库扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)。

## <a name="create-your-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

> [!IMPORTANT]
> [Azure Cosmos DB 无服务器](../cosmos-db/serverless.md)现已正式发布。 这种基于消耗的模式让 Azure Cosmos DB 成为了无服务器工作负载的强有力选项。 要在无服务器模式下使用 Azure Cosmos DB，请在创建帐户时选择“无服务器”作为“容量模式”。

1. 在 Visual Studio Code 中，右键单击在[前面文章](./create-first-function-vs-code-csharp.md)中创建了函数应用的 Azure 订阅，然后选择“创建服务器...”。

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-account.png" alt-text="从 Visual Studio Code 创建新 Azure Cosmos DB 帐户" border="true":::

1. 根据提示提供以下信息：

    + 选择 Azure 数据库服务器：选择 `Core (SQL)` 以创建一个文档数据库，然后可以使用 SQL 语法来查询。 [了解有关 Azure Cosmos DB SQL API 的详细信息](../cosmos-db/introduction.md)。

    + 帐户名称：输入标识此 Azure Cosmos DB 帐户的唯一名称。 帐户名称只能使用小写字母、数字及连字符 (-)，必须为 3 到 31 个字符长。

    + 选择容量模型：选择“无服务器”以在[无服务器](../cosmos-db/serverless.md)模式下创建帐户。 选择“预配吞吐量”以在[预配吞吐量](../cosmos-db/set-throughput.md)模式下创建帐户。 如果要开始使用 Azure Cosmos DB，建议选择“无服务器”。

    + 选择新资源的资源组：选择在[前面文章](./create-first-function-vs-code-csharp.md)中创建函数应用的资源组。

    + 选择新资源的位置：选择用于托管 Azure Cosmos DB 帐户的地理位置。 使用离你或你的用户最近的位置，以便能够以最快的速度访问数据。

## <a name="create-an-azure-cosmos-db-database-and-container"></a>创建 Azure Cosmos DB 数据库和容器

1. 创建新 Azure Cosmos DB 帐户后，右键单击其名称，然后选择“创建数据库...”。

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-database.png" alt-text="从 Visual Studio Code 创建新 Azure Cosmos DB 数据库" border="true":::

1. 出现提示时，请输入 `my-database` 作为“数据库名称”。

1. 创建数据库后，右键单击其名称，然后选择“创建集合...”

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="从 Visual Studio Code 创建新 Azure Cosmos DB 容器" border="true":::

1. 根据提示提供以下信息：

    + 输入集合的 id：`my-container`

    + 输入集合的[分区键](../cosmos-db/partitioning-overview.md)：`id`

## <a name="update-your-function-app-settings"></a>更新函数应用设置

在[前一篇快速入门文章](./create-first-function-vs-code-csharp.md)中，你已在 Azure 中创建了一个函数应用。 在本文中，你将学习如何更新函数应用，以在上面创建的 Azure Cosmos DB 容器中写入 JSON 文档。 要连接到 Azure Cosmos DB 帐户，必须将其连接字符串添加到应用设置。 然后，将新设置下载到 local.settings.json 文件，这样就可以在本地运行时可以连接到 Azure Cosmos DB 帐户。

1. 在 Visual Studio Code 中，右键单击 Azure Cosmos DB 帐户，然后选择“复制连接字符串”。

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="复制 Azure Cosmos DB 连接字符串" border="true":::

1. 按 <kbd>F1</kbd> 键打开命令面板，然后搜索并运行命令 `Azure Functions: Add New Setting...`。

1. 选择你在前一篇文章中创建的函数应用。 根据提示提供以下信息：

    + **输入新应用设置名称**：键入 `CosmosDbConnectionString`。

    + **输入“CosmosDbConnectionString”的值**：粘贴前面复制的 Azure Cosmos DB 帐户的连接字符串。

1. 再次按 <kbd>F1</kbd> 键打开命令面板，然后搜索并运行命令 `Azure Functions: Download Remote Settings...`。 

1. 选择你在前一篇文章中创建的函数应用。 选择“全是”覆盖现有本地设置。 

## <a name="register-binding-extensions"></a>注册绑定扩展

由于你使用 Azure Cosmos DB 输出绑定，因此在运行项目之前，必须安装对应的绑定扩展。 

::: zone pivot="programming-language-csharp"

除了 HTTP 和计时器触发器，绑定将实现为扩展包。 在终端窗口中运行以下 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令，将存储扩展包添加到项目中。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

项目已配置为使用[扩展捆绑包](functions-bindings-register.md#extension-bundles)，因此会自动安装一组预定义的扩展包。 

在项目根目录下的 host.json 文件中启用扩展捆绑包，如下所示：

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

现在，你可以将 Azure Cosmos DB 输出绑定添加到项目。

## <a name="add-an-output-binding"></a>添加输出绑定

在 Functions 中，每种类型的绑定都需要一个 `direction`、`type`，以及要在 function.json 文件中定义的唯一 `name`。 定义这些属性的方式取决于函数应用的语言。

::: zone pivot="programming-language-csharp"

在 C# 类库项目中，绑定被定义为函数方法上的绑定属性。 然后，基于这些属性自动生成 Functions 所需的 function.json 文件。

打开 HttpExample.cs 项目文件，并将以下参数添加到 `Run` 方法定义中：

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

`documentsOut` 参数是一个 `IAsyncCollector<T>` 类型，它表示在函数完成时将写入到 Azure Cosmos DB 容器的 JSON 文档的集合。 特定属性指定容器的名称及其父数据库的名称。 Azure Cosmos DB 帐户的连接字符串是由 `ConnectionStringSettingAttribute` 设置。

Run 方法定义如下所示：  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

直接在 function.json 文件中定义绑定属性。 根据绑定类型，可能还需要其他属性。 [Azure Cosmos DB 输出配置](./functions-bindings-cosmosdb-v2-output.md#configuration)描述了 Azure Cosmos DB 输出绑定必需的字段。 扩展可以轻松地将绑定添加到 function.json 文件。 

要创建绑定，请右键单击（在 macOS 系统上，请按住 Ctrl 并单击）HttpTrigger 文件夹中的 `function.json` 文件，并选择“添加绑定...”。请按照提示为新绑定定义以下绑定属性：

| Prompt | 值 | 说明 |
| -------- | ----- | ----------- |
| **选择绑定方向** | `out` | 该绑定是输出绑定。 |
| **选择方向为“out”的绑定** | `Azure Cosmos DB` | 绑定是 Azure Cosmos DB 绑定。 |
| **用于在代码中标识此绑定的名称** | `outputDocument` | 用于标识代码中引用的绑定参数的名称。 |
| **要在其中写入数据的 Cosmos DB 数据库** | `my-database` | 包含目标容器的 Azure Cosmos DB 数据库的名称。 |
| **要在其中写入数据的数据库集合** | `my-container` | 将在其中写入 JSON 文档的 Azure Cosmos DB 容器的名称。 |
| 如果为 true，则创建 Cosmos DB 数据库和集合 | `false` | 目标数据库和容器已存在。 |
| **从 local.setting.json 中选择设置** | `CosmosDbConnectionString` | 包含 Azure Cosmos DB 帐户连接字符串的应用程序设置的名称。 |
| **分区键（可选）** | 留空 | 仅当输出绑定创建容器时才是必需的。 |
| **集合吞吐量（可选）** | 留空 | 仅当输出绑定创建容器时才是必需的。 |

绑定将添加到 function.json 中的 `bindings` 数组，应如下所示：

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

::: zone pivot="programming-language-csharp"  

添加使用 `documentsOut` 输出绑定对象来创建 JSON 文档的代码。 请在方法返回之前添加此代码。

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

此时，你的函数应如下所示：

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

添加在 `context.bindings` 上使用 `outputDocument` 输出绑定对象来创建 JSON 文档的代码。 请在 `context.res` 语句之前添加此代码。

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

此时，你的函数应如下所示：

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>在本地运行函数

1. 与上一篇文章中所述，按 <kbd>F5</kbd> 启动函数应用项目和 Core Tools。 

1. 运行 Core Tools 后，转到“Azure:Functions”区域。 在“Functions”下，展开“本地项目” > “Functions”  。 右键单击 `HttpExample` 函数（在 Mac 按 Ctrl-单击），然后选择“立即执行函数...”。

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="立即在 Visual Studio Code 中执行函数":::

1. 在“输入请求正文”中，你将看到请求消息正文值 `{ "name": "Azure" }`。 按 Enter 将此请求消息发送给函数。  
 
1. 返回响应后，按 <kbd>Ctrl + C</kbd> 停用 Core Tools。

### <a name="verify-that-a-json-document-has-been-created"></a>验证是否已创建 JSON 文档

1. 在 Azure 门户上，返回到 Azure Cosmos DB 帐户并选择“数据资源管理器”。

1. 展开数据库和容器，然后选择“项”以出容器中已经创建的文档。

1. 验证输出绑定是否已创建新的 JSON 文档。

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="验证是否已在 Azure Cosmos DB 容器中创建了新文档" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>重新部署并验证更新的应用

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令面板中，搜索并选择 `Azure Functions: Deploy to function app...`。

1. 选择你在第一篇文章中创建的函数应用。 由于你要将项目重新部署到同一个应用，因此请选择“部署”以关闭关于覆盖文件的警告。

1. 部署完成后，可再次使用“立即执行函数...”功能在 Azure 中触发该函数。

1. 再次[检查在 Azure Cosmos DB 容器中创建的文档](#verify-that-a-json-document-has-been-created)，确认输出绑定再次生成新的 JSON 文档。

## <a name="clean-up-resources"></a>清理资源

在 Azure 中，“资源”是指函数应用、函数、存储帐户等。 这些资源可以组合到资源组中，删除该组即可删除组中的所有内容。

已创建完成这些快速入门所需的资源。 这些资源可能需要付费，具体取决于[帐户状态](https://azure.microsoft.com/account/)和[服务定价](https://azure.microsoft.com/pricing/)。 如果不再需要这些资源，请参阅下面介绍的资源删除方法：

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>后续步骤

已更新 HTTP 触发的函数，可将 JSON 文档写入 Azure Cosmos DB 容器。 现在，可以详细了解如何使用 Visual Studio Code 开发 Functions：

+ [使用 Visual Studio Code 开发 Azure Functions](functions-develop-vs-code.md)

+ [Azure Functions 触发器和绑定](functions-triggers-bindings.md)。
::: zone pivot="programming-language-csharp"  
+ [C# 中完整 Function 项目的示例](/samples/browse/?products=azure-functions&languages=csharp)。

+ [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 中完整函数项目的示例](/samples/browse/?products=azure-functions&languages=javascript)。

+ [Azure Functions JavaScript 开发人员指南](functions-reference-node.md)  
::: zone-end  