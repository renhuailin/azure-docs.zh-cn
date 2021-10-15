---
title: 运行 Azure 函数以响应 Blob 解除冻结事件
titleSuffix: Azure Storage
description: 了解如何使用 .NET 开发 Azure 函数，然后将 Azure 事件网格配置为运行函数，以响应解除冻结存档层中的 Blob 时触发的事件。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/11/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 06239708293be94f13c62cab7004c7a57d00eea1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275499"
---
# <a name="run-an-azure-function-in-response-to-a-blob-rehydration-event"></a>运行 Azure 函数以响应 Blob 解除冻结事件

若要读取存档层中的 Blob，必须先将 Blob 解除冻结到热或冷存储层。 解除冻结过程可能需要几小时才能完成。 可以将 [Azure 事件网格](../../event-grid/overview.md)配置为在 Blob 解除冻结操作完成时触发事件，然后在应用程序中处理此事件，而无需反复轮询解除冻结操作的状态。

当有事件发生时，事件网格通过终结点将事件发送到事件处理程序。 许多 Azure 服务都可以用作事件处理程序，包括 [Azure Functions](../../azure-functions/functions-overview.md)。 Azure 函数是在响应事件时可执行的代码块。 本操作指南将引导你完成此过程：开发 Azure 函数并将事件网格配置为运行该函数，以响应解除冻结 Blob 时发生的事件。

本文介绍如何通过 Visual Studio 使用 .NET 创建和测试 Azure 函数。 可以在各种本地开发环境使用各种不同编程语言来构建 Azure Functions。 有关 Azure Functions 支持的语言的详细信息，请参阅 [Azure Functions 支持的语言](../../azure-functions/supported-languages.md)。 有关 Azure Functions 的开发选项的详细信息，请参阅[在本地对 Azure Functions 进行编码和测试](../../azure-functions/functions-develop-local.md)。

有关解除冻结存档层中的 Blob 的详细信息，请参阅[存档层中的 Blob 解除冻结概述](archive-rehydrate-overview.md)。

## <a name="prerequisites"></a>先决条件

本文介绍如何通过 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 使用 .NET 来开发 Azure 函数。 你可以免费安装 Visual Studio Community。 请确保[将 Visual Studio 配置为使用 .NET 进行 Azure 开发](/dotnet/azure/configure-visual-studio)。

若要在本地调试 Azure 函数，需要使用可发送 HTTP 请求的工具，例如 Postman。

需要一个 [Azure 订阅](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 如果还没有帐户，请在开始之前[创建免费帐户](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-an-azure-function-app"></a>创建 Azure Function App

函数应用是一种 Azure 资源，用作 Azure Functions 的容器。 可以使用新的或现有的函数应用来完成本文中所述的步骤。

若要在 Azure 门户中创建新的函数应用，请执行以下步骤：

1. 在 Azure 门户中，搜索“函数应用”。 选择“函数应用”图标以导航到订阅中的函数应用列表。
1. 选择“创建”按钮以创建新的函数应用。
1. 在“基本信息”选项卡上，指定资源组，并为新的函数应用提供唯一名称。
1. 确保将“发布”选项设置为“代码”。
1. 从“运行时堆栈”下拉列表中选择“.NET”。 将自动填充“版本”字段，以使用最新版本的 .NET core。
1. 为新的函数应用选择区域。

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-basics-tab.png" alt-text="显示如何在 Azure 中创建新的函数应用 -“基本信息”选项卡的屏幕截图":::

1. 完成“基本信息”选项卡后，导航到“托管”选项卡 。
1. 在“托管”选项卡上，选择将在其中存储 Azure 函数的存储帐户。 可以选择现有存储帐户，也可以创建新的存储帐户。
1. 确保将“操作系统”字段设置为“Windows”。
1. 在“计划类型”字段中，选择“消耗(无服务器)”。 有关此计划的详细信息，请参阅 [Azure Functions 消耗计划托管](../../azure-functions/consumption-plan.md)。

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-hosting-tab.png" alt-text="显示如何在 Azure 中创建新的函数应用 -“托管”选项卡的屏幕截图":::

1. 选择“查看 + 创建”以创建新的函数应用。

若要了解有关配置函数应用的详细信息，请参阅 Azure Functions 文档中的[管理函数应用](../../azure-functions/functions-how-to-use-azure-function-app-settings.md)。

## <a name="create-an-azure-function-as-an-event-grid-trigger"></a>将 Azure 函数创建为事件网格触发器

接下来，创建在特定存储帐户中解除冻结 Blob 时将运行的 Azure 函数。 请按照以下步骤在 Visual Studio 中使用 C# 和 .NET Core 创建 Azure 函数：

1. 启动 Visual Studio 2019 并创建新的 Azure Functions 项目。 有关详细信息，请按照[创建函数应用项目](../../azure-functions/functions-create-your-first-function-visual-studio.md#create-a-function-app-project)中所述的说明进行操作。
1. 在“新建 Azure Functions 应用程序”步骤中，选择以下值：
    - 默认情况下，Azure Functions 运行时设置为“Azure Functions v3 (.NET Core)”。 Microsoft 建议使用此版本的 Azure Functions 运行时。
    - 从可能的触发器列表中，选择“事件网格触发器”。 有关为何将事件网格触发器用作使用 Azure 函数处理 Blob 存储事件的建议的触发器类型的详细信息，请参阅[将函数作为事件网格事件的事件处理程序](../../event-grid/handler-functions.md)。
    - “存储帐户”设置指示将存储 Azure 函数的位置。 可选择现有存储帐户，也可以创建一个新存储帐户。
1. 单击“创建”以在 Visual Studio 中创建新的项目。
1. 接下来，如[重命名函数](../../azure-functions/functions-create-your-first-function-visual-studio.md#rename-the-function)中所述重命名类和 Azure 函数。 为方案选择适合的名称。
1. 在 Visual Studio 中，选择“工具” | “NuGet 包管理器” | “包管理器控制台”，然后从控制台安装以下包  ：

    ```powershell
    Install-Package Azure.Storage.Blobs
    Install-Package Microsoft.ApplicationInsights.WorkerService
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights
    ```

1. 在 Azure 函数的类文件中，粘贴以下 using 语句：

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    ```

1. 在类文件中找到“Run”方法。 这是在事件发生时运行的方法。 将以下代码粘贴到“Run”方法的主体中。 请务必将尖括号中的占位符值替换为你自己的值：

    ```csharp
    // When either Microsoft.Storage.BlobCreated or Microsoft.Storage.BlobTierChanged
    // event occurs, write the event details to a log blob in the same container
    // as the event subject (the blob for which the event occurred).

    // Create a unique name for the log blob.
    string logBlobName = string.Format("function-log-{0}.txt", DateTime.UtcNow.Ticks);

    // Populate connection string with your Shared Key credentials.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net";

    // Get data from the event.
    dynamic data = eventGridEvent.Data;
    string eventBlobUrl = Convert.ToString(data.url);
    string eventApi = Convert.ToString(data.api);

    // Build string containing log information.
    StringBuilder eventInfo = new StringBuilder();
    eventInfo.AppendLine(string.Format("{0} operation occurred.", eventApi));
    eventInfo.AppendLine(string.Format("Blob URL: {0}", eventBlobUrl));
    eventInfo.AppendLine($@"Additional event details:
        Id=[{eventGridEvent.Id}]
        EventType=[{eventGridEvent.EventType}]
        EventTime=[{eventGridEvent.EventTime}]
        Subject=[{eventGridEvent.Subject}]
        Topic=[{eventGridEvent.Topic}]");

    // If event was BlobCreated and API call was CopyBlob, respond to the event.
    bool copyBlobEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated") &&
                                 (eventApi == "CopyBlob");

    // If event was BlobTierChanged and API call was SetBlobTier, respond to the event.
    bool setTierEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobTierChanged") &&
                                (eventApi == "SetBlobTier");

    // If one of these two events occurred, write event info to a log blob.
    if (copyBlobEventOccurred | setTierEventOccurred)
    {
        // Create log blob in same account and container.
        BlobUriBuilder logBlobUriBuilder = new BlobUriBuilder(new Uri(eventBlobUrl))
        {
            BlobName = logBlobName
        };

        BlobClient logBlobClient = new BlobClient(ConnectionString,
                                                  logBlobUriBuilder.BlobContainerName,
                                                  logBlobName);

        byte[] byteArray = Encoding.ASCII.GetBytes(eventInfo.ToString());

        try
        {
            // Write the log info to the blob.
            // Overwrite if the blob already exists.
            using (MemoryStream memoryStream = new MemoryStream(byteArray))
            {
                BlobContentInfo blobContentInfo =
                    logBlobClient.Upload(memoryStream, overwrite: true);
            }
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine(e.Message);
            throw;
        }
    }
    ```

有关开发 Azure Functions 的详细信息，请参阅 [Azure Functions 开发指南](../../azure-functions/functions-reference.md)。

若要详细了解在将 Blob 存储事件发布到事件处理程序时所包括的信息，请参阅[将 Azure Blob 存储用作事件网格源](../../event-grid/event-schema-blob-storage.md)。

## <a name="run-the-azure-function-locally-in-the-debugger"></a>使用调试器在本地运行 Azure 函数

若要在本地测试 Azure 函数代码，需手动发送触发事件的 HTTP 请求。 可以使用 Postman 之类的工具来发布请求。

Azure 函数类文件的顶部是一个 URL 终结点，可用于在本地环境中进行测试。 发布包含此 URL 的请求会触发本地环境中的事件，以便调试代码。 此 URL 的格式如下：

```http
http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
```

发送到此终结点的请求为模拟请求。 它不会发送或接收 Azure 存储帐户中的数据。

请按照以下步骤构造请求并将其发送到此终结点。 此示例介绍如何使用 Postman 发送请求。

1. 在 Postman 中，创建新请求。
1. 将上面显示的 URL 粘贴到请求 URL 的字段中，然后将函数名称替换为 `{functionname}` 并删除大括号。 请确保将请求谓词设置为“GET”。

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-url.png" alt-text="显示如何在 Postman 中为事件触发器指定本地 URL 的屏幕截图":::

1. 添加“Content-Type”标头并将其设置为“application/json”。
1. 添加“aeg-event-type”标头并将其设置为“通知”。

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-headers.png" alt-text="显示用于触发事件的本地请求的标头配置的屏幕截图":::

1. 在 Postman 中，指定请求正文，并将正文类型设置为 JSON，将格式设置为原始 。 以下示例模拟“复制 Blob”请求。 将尖括号中的占位符值替换为你自己的值。 请注意，无需更改日期/时间或标识符值，因为这是模拟请求：

    ```json
    [{
      "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
      "subject": "/blobServices/default/containers/<container-name>/blobs/<blob-name>",
      "eventType": "Microsoft.Storage.BlobCreated",
      "id": "2bfb587b-501e-0094-2746-8b2884065d32",
      "data": {
        "api": "CopyBlob",
        "clientRequestId": "3d4dedc7-6c27-4816-9405-fdbfa806b00c",
        "requestId": "2bfb587b-501e-0094-2746-8b2884000000",
        "eTag": "0x8D9595DCA505BDF",
        "contentType": "text/plain",
        "contentLength": 48,
        "blobType": "BlockBlob",
        "url": "https://<storage-account>.blob.core.windows.net/<container-name>/<blob-name>",
        "sequencer": "0000000000000000000000000000201B00000000004092a5",
        "storageDiagnostics": {
          "batchId": "8a92736a-6006-0026-0046-8bd7f5000000"
        }
      },
      "dataVersion": "",
      "metadataVersion": "1",
      "eventTime": "2021-08-07T04:42:41.0730463Z"
    }]
    ```

1. 在 Visual Studio 中，将任何所需断点放入代码，并按 F5 运行调试程序。
1. 在 Postman 中，选择“发送”按钮以将请求发送到终结点。

发送请求时，事件网格会调用 Azure 函数，则可以正常进行调试。 有关其他信息和示例，请参阅 Azure Functions 文档中的[手动发送请求](../../azure-functions/functions-bindings-event-grid-trigger.md#manually-post-the-request)。

触发事件的请求是模拟的，但事件触发时运行的 Azure 函数会将日志信息写入存储帐户中的新 Blob。 可在 Azure 门户中验证此 Blob 的内容并查看其上次修改时间，如下图所示：

:::image type="content" source="media/archive-rehydrate-handle-event/log-blob-contents-portal.png" alt-text="显示 Azure 门户中的日志 Blob 内容的屏幕截图":::

## <a name="publish-the-azure-function"></a>发布 Azure 函数

在本地测试 Azure 函数后，下一步是将 Azure 函数发布到之前创建的 Azure 函数应用。 必须发布函数，以便将事件网格配置为将存储帐户中发生的事件发送到函数终结点。

按以下步骤发布函数：

1. 在“解决方案资源管理器”中，选择并按住（或右键单击）Azure Functions 项目并选择“发布”。
1. 在“发布”窗口中，选择“Azure”作为目标，然后选择“下一步”  。
1. 选择“Azure 函数应用 (Windows)”作为特定目标，然后选择“下一步” 。
1. 在“函数实例”选项卡的下拉菜单中选择你的订阅，然后在可用函数应用列表中找到你的 Azure 函数应用。
1. 确保已选中“从包文件运行”复选框。
1. 选择“完成”以准备发布函数。
1. 在“发布”页中，验证配置是否正确。 如果看到一条警告，显示未配置 Application Insights 的服务依赖项，可以通过此页对其进行配置。
1. 选择“发布”按钮，开始将 Azure 函数发布到之前创建的 Azure 函数应用。

    :::image type="content" source="media/archive-rehydrate-handle-event/visual-studio-publish-azure-function.png" alt-text="显示从 Visual Studio 发布 Azure 函数的页面的屏幕截图":::

每当对 Azure 函数中的代码进行更改时，都必须将更新的函数发布到 Azure。

## <a name="subscribe-to-blob-rehydration-events-from-a-storage-account"></a>从存储帐户订阅 Blob 解除冻结事件

现在，你拥有一个函数应用，其中包含可在响应事件时运行的 Azure 函数。 下一步是从存储帐户创建事件订阅。 事件订阅将存储帐户配置为通过事件网格发布事件，以响应存储帐户中对 Blob 的操作。 然后，事件网格会将事件发送到指定的事件处理程序终结点。 在本例中，事件处理程序即为在上一部分中创建的 Azure 函数。

创建事件订阅时，可以筛选发送到事件处理程序的事件。 解除冻结存档层中的 Blob 时要捕获的事件为“Microsoft.Storage.BlobTierChanged”，它是对[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作的响应，以及“Microsoft.Storage.BlobCreated”，它是对[复制 Blob](/rest/api/storageservices/copy-blob) 或[从 URL 复制 Blob](/rest/api/storageservices/copy-blob-from-url) 操作的响应 。 你可能只需要处理以下事件之一，具体取决于你的方案。

若要创建事件订阅，请执行以下步骤：

1. 在 Azure 门户中，导航到包含要从存档层解除冻结的 Blob 的存储帐户。
1. 选择左侧导航窗格中的“事件”设置。
1. 在“事件”页中，选择“更多选项” 。
1. 选择“创建事件订阅”。
1. 在“创建事件订阅”页的“事件订阅详细信息”部分中，提供事件订阅的名称 。
1. 在“主题详细信息”部分中，提供系统主题的名称。 系统主题表示 Azure 存储发布的一个或多个事件。 有关系统主题的详细信息，请参阅 [Azure 事件网格中的系统主题](../../event-grid/system-topics.md)。
1. 在“事件类型”部分中，选择“已创建 Blob”和“已更改 Blob 层”事件  。 根据所选的从存档层中解除冻结 Blob 的方式，将触发这两个事件之一。

    :::image type="content" source="media/archive-rehydrate-handle-event/select-event-types-portal.png" alt-text="显示如何在 Azure 门户中为 Blob 解除冻结事件选择事件类型的屏幕截图":::

1. 在“终结点详细信息”部分的下拉菜单中选择“Azure 函数”。
1. 选择“选择终结点”以指定在上一部分中创建的函数。 在“选择 Azure 函数”对话框中，为 Azure 函数选择订阅、资源组和函数应用。 最后，从下拉列表中选择函数名称，然后选择“确认选择”。

    :::image type="content" source="media/archive-rehydrate-handle-event/select-azure-function-endpoint-portal.png" alt-text="显示如何选择 Azure 函数作为事件网格订阅的终结点的屏幕截图":::

1. 选择“创建”按钮创建事件订阅，并开始将事件发送到 Azure 函数事件处理程序。

若要详细了解事件订阅，请参阅 [Azure 事件网格概念](../../event-grid/concepts.md#event-subscriptions)。

## <a name="test-the-azure-function-event-handler"></a>测试 Azure 函数事件处理程序

若要测试 Azure 函数，可以在包含事件订阅的存储帐户中触发事件。 之前创建的事件订阅正在对两个事件进行筛选，分别为“Microsoft.Storage.BlobCreated”和“Microsoft.Storage.BlobTierChanged” 。 当其中任一事件触发时，它将触发 Azure 函数。

本文中所显示的 Azure 函数在两个方案中会写入日志 Blob：

- 当事件为“Microsoft.Storage.BlobCreated”且 API 操作为“复制 Blob” 。
- 当事件为“Microsoft.Storage.BlobTierChanged”且 API 操作为“设置 Blob 层” 。

若要了解如何通过解除冻结 Blob 来测试函数，请参阅以下两个过程之一：

- [使用复制操作解除冻结 Blob](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)
- [通过更改 Blob 层解除冻结 Blob](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)

解除冻结完成后，会将日志 Blob 写入与已解除冻结的 Blob 相同的容器。 例如，使用复制操作解除冻结 Blob 后，可以在 Azure 门户中看到原始源 Blob 保留在存档层中，完全解除冻结的目标 Blob 显示在目标联机层中，Azure 函数创建的日志 Blob 也显示在列表中。

:::image type="content" source="media/archive-rehydrate-handle-event/copy-blob-archive-tier-rehydrated-with-log-blob.png" alt-text="显示存档层中的原始 Blob、热存储层中的解除冻结的 Blob 以及事件处理程序写入的日志 Blob 的屏幕截图":::

请记住，解除冻结 Blob 最多需要 15 小时，具体取决于解除冻结优先级设置。 如果将解除冻结优先级设置为“高”，则对于小于 10 GB 大小的 Blob，解除冻结可能会在一小时内完成。 但是，高优先级解除冻结的成本更高。 有关详细信息，请参阅[存档层中的 Blob 解除冻结概述](archive-rehydrate-overview.md)。

> [!TIP]
> 尽管本操作指南的目标是在 Blob 解除冻结上下文中处理这些事件，但出于测试目的，观察上传 Blob 或更改联机 Blob 层（例如从热存储层更改为冷存储层）时的这些响应事件也颇有帮助，因为事件是立即触发的。

有关如何筛选事件网格中的事件的详细信息，请参阅[如何筛选 Azure 事件网格的事件](../../event-grid/how-to-filter-events.md)。

## <a name="see-also"></a>另请参阅

- [Blob 数据的热访问层、冷访问层和存档访问层](access-tiers-overview.md)
- [存档层中的 Blob 解除冻结概述](archive-rehydrate-overview.md)
- [将存档的 Blob 解冻到联机层](archive-rehydrate-to-online-tier.md)
- [响应 Blob 存储事件](storage-blob-event-overview.md)
