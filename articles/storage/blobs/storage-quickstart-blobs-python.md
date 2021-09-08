---
title: 快速入门：Azure Blob 存储库 v12 - Python
description: 本快速入门介绍如何使用适用于 Python 的 Azure Blob 存储客户端库版本 12 在 Blob（对象）存储中创建容器和 Blob。 接下来，将介绍如何将 blob 下载到本地计算机，以及如何在容器中列出所有 blob。
author: twooley
ms.author: twooley
ms.date: 01/28/2021
ms.topic: quickstart
ms.service: storage
ms.subservice: blobs
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: d560dfc8d8621e94315d8603a9d6e8b3b8cbf09a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468107"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>快速入门：使用 Python v12 SDK 管理 blob

本快速入门介绍如何使用 Python 管理 blob。 Blob 是可以保存大量文本或二进制数据（包括图像、文档、流媒体和存档数据）的对象。 你将上传、下载和列出 Blob，并创建和删除容器。

更多资源：

* [API 参考文档](/python/api/azure-storage-blob)
* [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [包（Python 包索引）](https://pypi.org/project/azure-storage-blob/)
* [示例](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个 Azure 存储帐户。 [创建存储帐户](../common/storage-account-create.md)。
- [Python](https://www.python.org/downloads/) 2.7 或 3.6+。

## <a name="setting-up"></a>设置

本部分逐步指导如何准备一个项目，使其与适用于 Python 的 Azure Blob 存储客户端库 v12 配合使用。

### <a name="create-the-project"></a>创建项目

创建名为 blob-quickstart-v12 的 Python 应用程序。

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为项目创建新目录。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 切换到新创建的 blob-quickstart-v12 目录。

    ```console
    cd blob-quickstart-v12
    ```

1. 在 blob-quickstart-v12 目录中，创建名为 data 的另一个目录 。 将在此目录中创建和存储 Blob 数据文件。

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>安装包

还是在应用程序目录中，使用 `pip install` 命令安装适用于 Python 包的 Azure Blob 存储客户端库。

```console
pip install azure-storage-blob
```

此命令会安装适用于 Python 包的 Azure Blob 存储客户端库及其依赖的所有库。 在本例中，即适用于 Python 的 Azure Core 库。

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在代码编辑器中打开新文本文件
1. 添加 `import` 语句
1. 为程序创建结构，包括基本的异常处理

    代码如下：

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. 在 blob-quickstart-v12 目录中，将新文件另存为 blob-quickstart-v12.py 。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>对象模型

Azure Blob 存储最适合存储巨量的非结构化数据。 非结构化数据是不遵循特定数据模型或定义的数据（如文本或二进制数据）。 Blob 存储提供了三种类型的资源：

* 存储帐户
* 存储帐户中的容器
* 容器中的 blob

以下图示显示了这些资源之间的关系。

![Blob 存储体系结构的图示](./media/storage-blobs-introduction/blob1.png)

使用以下 Python 类与这些资源进行交互：

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient)：`BlobServiceClient` 类可用于操纵 Azure 存储资源和 blob 容器。
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient)：`ContainerClient` 类可用于操纵 Azure 存储容器及其 blob。
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient)：`BlobClient` 类可用于操纵 Azure 存储 blob。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示了如何使用适用于 Python 的 Azure Blob 存储客户端库执行以下任务：

* [获取连接字符串](#get-the-connection-string)
* [创建容器](#create-a-container)
* [将 blob 上传到容器中](#upload-blobs-to-a-container)
* [列出容器中的 blob](#list-the-blobs-in-a-container)
* [下载 blob](#download-blobs)
* [删除容器](#delete-a-container)

### <a name="get-the-connection-string"></a>获取连接字符串

下面的代码从[配置存储连接字符串](#configure-your-storage-connection-string)部分中创建的环境变量中检索存储帐户的连接字符串。

在 `try` 块内添加此代码：

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>创建容器

确定新容器的名称。 以下代码将 UUID 值追加到容器名称，确保其是唯一的。

> [!IMPORTANT]
> 容器名称必须为小写。 有关命名容器和 Blob 的详细信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

调用 [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) 方法，创建 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) 类的实例。 然后，调用 [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) 方法在存储帐户中实际创建容器。

将此代码添加到 `try` 块的末尾：

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>将 blob 上传到容器中

以下代码片段：

1. 创建用于保存数据文件的本地目录。
1. 在本地目录中创建文本文件。
1. 通过从[创建容器](#create-a-container)部分中调用 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) 上的 [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) 方法，获取对 [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) 对象的引用。
1. 通过调用 [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) 方法将本地文本文件上传到 blob。

将此代码添加到 `try` 块的末尾：

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

通过调用 [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) 方法，列出容器中的 blob。 在这种情况下，只向容器添加了一个 blob，因此列表操作只返回那个 blob。

将此代码添加到 `try` 块的末尾：

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>下载 Blob

通过调用 [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) 方法，下载以前创建的 blob。 示例代码将向文件名添加后缀“DOWNLOAD”，这样你就可以在本地文件系统中看到这两个文件。

将此代码添加到 `try` 块的末尾：

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>删除容器

以下代码使用 [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) 方法删除整个容器，从而清除该应用所创建的资源。 也可根据需要删除本地文件。

在删除 blob、容器和本地文件之前，应用会调用 `input()` 以暂停并等待用户输入。 在资源被删除之前，请验证是否已正确创建这些资源。

将此代码添加到 `try` 块的末尾：

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>运行代码

此应用会在本地文件夹中创建测试文件，并将其上传到 Azure Blob 存储。 示例随后会列出容器中的 Blob，并下载具有新名称的文件。 可将旧文件和新文件进行比较。

导航到包含 blob-quickstart-v12.py 文件的目录，然后执行以下 `python` 命令来运行应用。

```console
python blob-quickstart-v12.py
```

应用的输出类似于以下示例：

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

在开始清理过程之前，请在“data”文件夹中查看这两个文件。 可打开它们，然后就会看到它们完全相同。

验证文件后，按 Enter 键以删除测试文件并完成演示。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Python 上传、下载和列出 blob。

若要查看 Blob 存储示例应用，请继续执行以下操作：

> [!div class="nextstepaction"]
> [Azure Blob 存储 SDK v12 Python 示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* 有关详细信息，请参阅[适用于 Python 的 Azure 存储客户端库](/azure/developer/python/sdk/storage/overview)。
* 有关教程、示例、快速入门和其他文档，请访问[面向 Python 开发人员的 Azure](/azure/python/)。
