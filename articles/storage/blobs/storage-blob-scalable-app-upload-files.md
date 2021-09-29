---
title: 将大量随机数据并行上传到 Azure 存储
description: 了解如何使用 Azure 存储客户端库将大量随机数据以并行方式上传到 Azure 存储帐户
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 787cfde40013122c3827cddd4903ca15dfe51836
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624930"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>将大量随机数据以并行方式上传到 Azure 存储

本教程是一个系列中的第二部分。 本教程演示如何部署将大量随机数据上传到 Azure 存储帐户的应用程序。

本系列教程的第二部分将介绍如何：

> [!div class="checklist"]
> - 配置连接字符串
> - 构建应用程序
> - 运行应用程序
> - 验证连接数

Microsoft Azure Blob 存储提供可缩放的服务来存储数据。 为了尽可能提高应用程序的性能，建议了解 blob 存储的工作方式。 了解 Azure Blob 的限制非常重要，要深入了解这些限制，请访问：[Blob 存储的可伸缩性和性能目标](../blobs/scalability-targets.md)。

在使用 Blob 设计高性能应用程序时，[分区命名](../blobs/storage-performance-checklist.md#partitioning)是另一个潜在重要因素。 对于大于或等于4 MiB 的块大小，会使用[高吞吐量块 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)，并且分区命名不会影响性能。 对于小于4 MiB 的块大小，Azure 存储使用基于范围的分区方案来进行缩放和负载均衡。 此配置意味着具有相似命名约定或前缀的文件转到相同分区。 此逻辑还包括文件上传到的容器的名称。 本教程中使用名称为 GUID 的文件以及随机生成的内容。 然后将这些文件和内容上传到五个使用随机名称的不同容器。

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须先完成上一存储教程：[为可缩放的应用程序创建虚拟机和存储帐户][previous-tutorial]。

## <a name="remote-into-your-virtual-machine"></a>远程登录到虚拟机

在本地计算机上使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为虚拟机的 publicIPAddress。 出现提示时，输入创建虚拟机时使用的凭据。

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>配置连接字符串

在 Azure 门户中导航到存储帐户。 在存储帐户中选择“设置”下的“访问密钥”。  从主密钥或辅助密钥复制 **连接字符串**。 登录到上一教程中创建的虚拟机。 以管理员身份打开“命令提示符”，并使用 `/m` 开关运行 `setx` 命令，该命令可保存计算机设置环境变量  。 重载“命令提示符”后，环境变量才可用  。 替换以下示例中的“\<storageConnectionString\>”：

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

完成后，打开另一“命令提示符”，导航到 `D:\git\storage-dotnet-perf-scale-app` 并键入 `dotnet build` 以生成应用程序  。

## <a name="run-the-application"></a>运行应用程序

导航到 `D:\git\storage-dotnet-perf-scale-app`。

键入 `dotnet run` 运行应用程序。 首次运行 `dotnet` 时，它会填充本地程序包高速缓存，以加快恢复速度并实现脱机访问。 完成此命令需要最多一分钟，并且仅完成一次。

```console
dotnet run
```

应用程序创建五个随机命名的容器，并开始将暂存目录中的文件上传到存储帐户。

下例显示了 `UploadFilesAsync` 方法：

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

最小线程数和最大线程数设置为 100，以确保允许大量并发连接。

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

除设置线程和连接限制设置外，还需将 [UploadFromStreamAsync ](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) 方法的 [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) 配置为使用并行，并禁用 MD5 哈希验证。 文件以 100 mb 的块上传，此配置提高了性能，但如果网络性能不佳，可能成本高昂，因为如果出现失败，会重试整个 100 mb 的块。

|properties|值|说明|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| 上传时，此设置将 blob 分为多个块。 为获得最佳性能，此值应为内核数的 8 倍。 |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| 该属性禁用对上传内容的 MD5 哈希检查。 禁用 MD5 验证可加快传输速度。 但是不能确认传输文件的有效性或完整性。   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| 该属性确定是否计算和存储文件的 MD5 哈希。   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 2 秒回退，最多重试 10 次 |确定请求的重试策略。 重试连接失败，在此示例中，[ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) 策略配置为 2 秒回退，最多可重试 10 次。 当应用程序快要达到 Blob 存储的可伸缩性目标时，此设置非常重要。 有关详细信息，请参阅 [Blob 存储的可伸缩性和性能目标](../blobs/scalability-targets.md)。  |

---

以下示例是截断的应用程序输出，该应用程序在 Windows 系统上运行。

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>验证连接

在上载文件的同时，可以验证存储帐户的并发连接数。 打开控制台窗口，然后键入 `netstat -a | find /c "blob:https"`。 此命令显示当前打开的连接数。 如以下示例所示，上传随机文件到存储帐户时，打开了 800 个连接。 此值在整个上传过程中不断更改。 通过以并行块区块的形式进行上传，可显著减少传输内容所需的时间。

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>后续步骤

本系列的第二部分介绍了以并行方式将大量随机数据上传到存储帐户的方法，例如如何：

> [!div class="checklist"]
> - 配置连接字符串
> - 构建应用程序
> - 运行应用程序
> - 验证连接数

进入本系列的第三部分，从存储帐户下载大量数据。

> [!div class="nextstepaction"]
> [从 Azure 存储下载大量随机数据](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
