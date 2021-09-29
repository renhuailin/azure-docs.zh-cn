---
title: 教程 - 生成使用 Blob 存储的高可用性应用程序
titleSuffix: Azure Storage
description: 使用读取访问异地区域冗余 (RA-GZRS) 存储实现应用程序数据的高可用性。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: e8009e7b86ca151b6445ff3a5c165687641318d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590340"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>教程：使用 Blob 存储构建高度可用的应用程序

本教程是一个系列中的第一部分。 本教程介绍如何在 Azure 中实现应用程序数据的高可用性。

完成本教程后，将会生成一个控制台应用程序，用于上传 Blob 并从[读取访问异地区域冗余](../common/storage-redundancy.md) (RA-GZRS) 存储帐户检索它。

Azure 存储中的异地冗余会将事务从主要区域异步复制到数百英里外的次要区域。 此复制过程可确保次要区域中的数据最终一致。 控制台应用程序使用[断路器](/azure/architecture/patterns/circuit-breaker)模式确定要连接到的终结点，在模拟故障和恢复时在终结点之间自动切换。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> - 创建存储帐户
> - 设置连接字符串
> - 运行控制台应用程序

## <a name="prerequisites"></a>先决条件

完成本教程：

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

- 安装 [Visual Studio 2019](https://www.visualstudio.com/downloads/)（包含 **Azure 开发** 工作负荷）。

  ![Azure 开发（位于“Web 和云”下）](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

- [安装 Python](https://www.python.org/downloads/)
- 下载并安装[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)

# <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="nodejs-v11-sdk"></a>[Node.js v11 SDK](#tab/nodejs11)

- 安装 [Node.js](https://nodejs.org)。

---

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-storage-account"></a>创建存储帐户

存储帐户提供唯一的命名空间来存储和访问 Azure 存储数据对象。

请按照以下步骤操作，创建读取访问异地区域冗余 (RA-GZRS) 存储帐户：

1. 在 Azure 门户中选择“创建资源”按钮。
2. 从“新建”页中选择“存储帐户 - blob、文件、表、队列”。
4. 使用以下信息填充存储帐户窗体（如下图所示），然后选择“创建”：

   | 设置       | 示例值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **订阅** | 我的订阅 | 有关订阅的详细信息，请参阅[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 |
   | **ResourceGroup** | *myResourceGroup* | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming)（命名规则和限制）。 |
   | **名称** | mystorageaccount | 存储帐户的唯一名称。 |
   | **位置** | *美国东部* | 选择一个位置。 |
   | **“性能”** | *标准* | 对于示例方案，“标准”性能是一个不错的选项。 |
   | **帐户种类** | StorageV2 | 建议使用常规用途 v2 存储帐户。 有关 Azure 存储帐户类型的详细信息，请参阅[存储帐户概述](../common/storage-account-overview.md)。 |
   | **复制**| *读取访问异地区域冗余存储 (RA-GZRS)* | 主要区域是区域冗余的，将在启用了对次要区域的读取访问权限的情况下复制到次要区域。 |
   | **访问层**| *热访问层* | 将热访问层用于经常访问的数据。 |

    ![创建存储帐户](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>下载示例

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

[下载示例项目](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)并解压缩 storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个控制台应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

[下载示例项目](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)并解压缩 storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个基本的 Python 应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="nodejs-v11-sdk"></a>[Node.js v11 SDK](#tab/nodejs11)

[下载示例项目](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs)并解压缩文件。 也可使用 [git](https://git-scm.com/) 将应用程序的副本下载到开发环境。 示例项目包含一个基本的 Node.js 应用程序。

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>配置示例

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

在应用程序中，必须为存储帐户提供连接字符串。 可以将此连接字符串存储在运行应用程序的本地计算机的环境变量中。 根据你的操作系统，按照下面的某个示例创建环境变量。

在 Azure 门户中导航到存储帐户。 在存储帐户中选择“设置”下的“访问密钥”。  复制主密钥或辅助密钥中的 **连接字符串**。 根据操作系统运行以下命令之一（将 \<yourconnectionstring\> 替换为实际的连接字符串）。 此命令将一个环境变量保存到本地计算机。 在 Windows 中，必须重载正在使用的 **命令提示符** 或 shell，该环境变量才可用。

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

在应用程序中，必须提供存储帐户凭据。 可以将此信息存储在运行应用程序的本地计算机的环境变量中。 根据操作系统，按照下面的某个示例创建环境变量。

在 Azure 门户中导航到存储帐户。 在存储帐户中选择“设置”下的“访问密钥”。  将“存储帐户名称”和“密钥”值粘贴到以下命令中 （替换 \<youraccountname\> 和 \<youraccountkey\> 占位符）。 此命令将环境变量保存到本地计算机。 在 Windows 中，必须重载正在使用的 **命令提示符** 或 shell，该环境变量才可用。

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="nodejs-v11-sdk"></a>[Node.js v11 SDK](#tab/nodejs11)

若要运行此示例，必须将存储帐户凭据添加到 `.env.example` 文件，然后将其重命名为 `.env`。

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

导航到存储帐户并在“设置”部分选择“访问密钥”，即可找到 Azure 门户中的此信息。 

安装必需的依赖项。 为此，请打开命令提示符，导航到示例文件夹，然后输入 `npm install`。

---

## <a name="run-the-console-application"></a>运行控制台应用程序

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

在 Visual Studio 中，按 F5 或选择“启动”，开始调试应用程序。 Visual Studio 会自动还原缺少的 NuGet 包（若已配置）。若要了解详情，请参阅[通过包还原安装和重新安装包](/nuget/consume-packages/package-restore#package-restore-overview)。

此时会启动一个控制台窗口，应用程序开始运行。 应用程序将 HelloWorld.png 映像从解决方案上传到存储帐户。 应用程序会进行检查，以确保映像已复制到辅助 RA-GZRS 终结点。 然后，它开始下载映像，最多可下载 999 次。 每次读取由 **P** 或 **S** 表示。其中，P 表示主终结点，S 表示辅助终结点。

![正在运行的控制台应用](media/storage-create-geo-redundant-storage/figure3.png)

在示例代码中，`Program.cs` 文件中的 `RunCircuitBreakerAsync` 任务用于通过 [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) 方法下载存储帐户中的图像。 下载前，将会先定义 [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext)。 操作上下文定义事件处理程序，此类程序在下载成功完成时或者下载失败并重试时触发。

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

若要在终端或命令提示符处运行应用程序，请转到 **circuitbreaker.py** 目录，然后输入 `python circuitbreaker.py`。 应用程序将 HelloWorld.png 映像从解决方案上传到存储帐户。 应用程序会进行检查，以确保映像已复制到辅助 RA-GZRS 终结点。 然后，它开始下载映像，最多可下载 999 次。 每次读取由 **P** 或 **S** 表示。其中，P 表示主终结点，S 表示辅助终结点。

![正在运行的控制台应用](media/storage-create-geo-redundant-storage/figure3.png)

在示例代码中，使用了 `circuitbreaker.py` 文件中的 `run_circuit_breaker` 方法通过 [get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-) 方法来下载存储帐户中的映像。

存储对象重试函数设置为线性重试策略。 重试函数决定了是否重试某个请求，并指定了在重试该请求之前需要等待的秒数。 如果应该在针对主终结点的初始请求失败以后重试针对次要终结点的请求，请将 **retry\_to\_secondary** 值设置为 true。 在示例应用程序的存储对象的 `retry_callback` 函数中，定义了自定义重试策略。

在下载之前，定义了服务对象的 [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) 和 [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) 函数。 这些函数定义了在下载成功完成或下载失败并重试时触发的事件处理程序。

# <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="nodejs-v11-sdk"></a>[Node.js v11 SDK](#tab/nodejs11)

若要运行示例，请打开命令提示符，导航到示例文件夹，然后输入 `node index.js`。

此示例在 Blob 存储帐户中创建一个容器，将 **HelloWorld.png** 上传到容器中，然后反复检查容器和图像是否已复制到次要区域。 复制完以后，会提示你输入 **D** 或 **Q**（然后按 ENTER），以便下载相关内容或退出。 输出应类似于以下示例：

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>了解示例代码

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

### <a name="retry-event-handler"></a>Retry 事件处理程序

当映像下载失败并设置为重试时，将调用 `OperationContextRetrying` 事件处理程序。 如果达到应用程序中定义的重试次数上限，请求的 [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) 会变为 `SecondaryOnly`。 此设置强制应用程序从辅助终结点尝试下载该图像。 此配置可减少请求图像所花的时间，因为不会无限重试主终结点。

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>RequestCompleted 事件处理程序

当图像下载成功时，会调用 `OperationContextRequestCompleted` 事件处理程序。 如果应用程序使用的是辅助终结点，则应用程序会继续使用该终结点，最多 20 次。 20 次以后，应用程序会将 [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) 重新设置为 `PrimaryThenSecondary` 并重试主终结点。 如果请求成功，应用程序会继续从主终结点读取。

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

### <a name="retry-event-handler"></a>Retry 事件处理程序

当映像下载失败并设置为重试时，将调用 `retry_callback` 事件处理程序。 如果达到应用程序中定义的重试次数上限，请求的 [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) 会变为 `SECONDARY`。 此设置强制应用程序从辅助终结点尝试下载该图像。 此配置可减少请求图像所花的时间，因为不会无限重试主终结点。

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>RequestCompleted 事件处理程序

当图像下载成功时，会调用 `response_callback` 事件处理程序。 如果应用程序使用的是辅助终结点，则应用程序会继续使用该终结点，最多 20 次。 20 次以后，应用程序会将 [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) 重新设置为 `PRIMARY` 并重试主终结点。 如果请求成功，应用程序会继续从主终结点读取。

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="nodejs-v11-sdk"></a>[Node.js v11 SDK](#tab/nodejs11)

使用 Node.js V10 SDK 时，不需回调处理程序。 示例会创建一个配置了重试选项和辅助终结点的管道。 这样，如果应用程序无法通过主管道访问数据，则会自动切换到辅助管道。

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>后续步骤

本系列教程的第一部分介绍了如何使用 RA-GZRS 存储帐户实现应用程序的高可用性。

请继续学习本系列教程的第二部分，了解如何模拟故障和强制应用程序使用辅助 RA-GZRS 终结点。

> [!div class="nextstepaction"]
> [模拟从主要区域读取数据时出现的故障](simulate-primary-region-failure.md)
