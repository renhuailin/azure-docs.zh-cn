---
title: Azure Functions 事件网格 Blob 触发器
description: 了解如何设置和调试事件网格 Blob 触发器
author: cachai2
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: cachai
ms.openlocfilehash: 259928cbfc046930d82bc3462db6d8aad4cd6b06
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124988"
---
# <a name="azure-function-event-grid-blob-trigger"></a>Azure Functions 事件网格 Blob 触发器

本文演示了如何调试和部署本地事件网格 Blob 触发器函数，该函数处理由存储帐户引发的事件。

> [!NOTE]
> 事件网格 Blob 触发器以预览版提供。

## <a name="prerequisites"></a>先决条件

- 创建或使用现有的函数应用
- 创建或使用现有的存储帐户
- 安装了 5.0 及更高版本的 [Microsoft.Azure.WebJobs.Extensions.Storage 扩展](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)
- 下载 [ngrok](https://ngrok.com/)，使 Azure 能够调用本地函数

## <a name="create-a-new-function"></a>创建新函数

1. 在 Visual Studio Code 中打开函数应用。

1. 按 F1 创建新的 Blob 触发器函数。 请确保使用存储帐户的连接字符串。

1. 事件网格 Blob 触发器的默认 URL 为：

    # <a name="c"></a>[C#](#tab/csharp)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName={functionname}
    ```

    # <a name="python"></a>[Python](#tab/python)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    # <a name="java"></a>[Java](#tab/java)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    ---

    请注意函数应用的名称，并且触发器类型是 Blob 触发器，由 URL 中的 `blobs` 指示。 稍后在操作指南中设置终结点时，将需要使用此项。

1. 创建函数后，添加事件网格源参数。

    # <a name="c"></a>[C#](#tab/csharp)
    将 Source = BlobTriggerSource.EventGrid 添加到函数参数。
    
    ```csharp
    [FunctionName("BlobTriggerCSharp")]
    public static void Run([BlobTrigger("samples-workitems/{name}", Source = BlobTriggerSource.EventGrid, Connection = "connection")]Stream myBlob, string name, ILogger log)
    {
        log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
    }
    ```

    # <a name="python"></a>[Python](#tab/python)
    将 "source": "EventGrid" 添加到 function.json 绑定数据。
    
    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "name": "myblob",
          "type": "blobTrigger",
          "direction": "in",
          "path": "samples-workitems/{name}",
          "source": "EventGrid",
          "connection": "MyStorageAccountConnectionString"
        }
      ]
    }
    ```

    # <a name="java"></a>[Java](#tab/java)
    按 F5 生成函数。 生成完成后，将 "source": "EventGrid" 添加到 function.json 绑定数据 。
    
    ```json
    {
      "scriptFile" : "../java-1.0-SNAPSHOT.jar",
      "entryPoint" : "com.function.{MyFunctionName}.run",
      "bindings" : [ {
        "type" : "blobTrigger",
        "direction" : "in",
        "name" : "content",
        "path" : "samples-workitems/{name}",
        "dataType" : "binary",
        "source": "EventGrid",
        "connection" : "MyStorageAccountConnectionString"
       } ]
    }
    ```

    ---

1. 在处理日志记录的行的函数中设置断点。

1. 启动调试会话。

    # <a name="c"></a>[C#](#tab/csharp)
    按 F5 启动调试会话。

    # <a name="python"></a>[Python](#tab/python)
    按 F5 启动调试会话。

    # <a name="java"></a>[Java](#tab/java)
    打开新终端并运行以下 mvn 命令以启动调试会话。

    ```bash
    mvn azure-functions:run
    ```

    ---

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>调试函数
Blob 触发器识别到已将新文件上传到存储容器之后，即会命中本地函数中的断点。

## <a name="deployment"></a>部署

将函数应用部署到 Azure 时，请将本地终结点的 Webhook 终结点更新为已部署的应用终结点。 若要更新终结点，请按照[添加存储事件](#add-a-storage-event)中的步骤操作，并使用下面的步骤 5 中的 Webhook URL。 可以在函数应用左侧菜单的“应用密钥”部分中找到 `<BLOB-EXTENSION-KEY>` 。

# <a name="c"></a>[C#](#tab/csharp)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="python"></a>[Python](#tab/python)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="java"></a>[Java](#tab/java)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

---

## <a name="clean-up-resources"></a>清理资源

若要清除本文中创建的资源，请删除在本教程中创建的事件网格订阅。

## <a name="next-steps"></a>后续步骤

- [使用事件网格自动调整已上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions 的事件网格触发器](./functions-bindings-event-grid.md)