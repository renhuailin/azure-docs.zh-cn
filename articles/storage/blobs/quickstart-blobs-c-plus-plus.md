---
title: 快速入门：Azure Blob 存储库 v12 - C++
description: 本快速入门介绍如何使用适用于 C++ 的 Azure Blob 存储客户端库版本 12 在 Blob（对象）存储中创建容器和 blob。 接下来，将介绍如何将 blob 下载到本地计算机，以及如何在容器中列出所有 blob。
author: kyle-patterson
ms.author: kylepa
ms.date: 06/21/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5b961145b065f2ce63497b32d4ba533dda04c68f
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894259"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>快速入门：适用于 C++ 的 Azure Blob 存储客户端库 v12

适用于 C++ 的 Azure Blob 存储客户端库 v12 入门。 Azure Blob 存储是 Microsoft 提供的适用于云的对象存储解决方案。 请按照步骤操作，安装程序包并试用基本任务的示例代码。 Blob 存储最适合存储巨量的非结构化数据。

使用适用于 C++ 的 Azure Blob 存储客户端库 v12 完成以下操作：

- 创建容器
- 将 blob 上传到 Azure 存储
- 列出容器中所有的 blob
- 将 blob 下载到本地计算机
- 删除容器

资源：

- [API 参考文档](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [库源代码](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [示例](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>先决条件

- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure 存储帐户](../common/storage-account-create.md)
- [C++ 编译器](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - C 和 C++ 包管理器](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)

## <a name="setting-up"></a>设置

本部分逐步指导如何准备一个项目，使其与适用于 C++ 的 Azure Blob 存储客户端库 v12 配合使用。

### <a name="install-the-packages"></a>安装包

`vcpkg install` 命令将安装适用于 C++ 的 Azure 存储 Blob SDK 和所需的依赖项：

```console
vcpkg.exe install azure-storage-blobs-cpp:x64-windows
```

有关详细信息，请访问 GitHub 以获取并生成[适用于 C++ 的 Azure SDK](https://github.com/Azure/azure-sdk-for-cpp/)。

### <a name="create-the-project"></a>创建项目

在 Visual Studio 中创建 C++ Windows 控制台应用程序“BlobQuickstartV12”。

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="用于配置新的 C++ Windows 控制台应用的 Visual Studio 对话框":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>对象模型

Azure Blob 存储最适合存储巨量的非结构化数据。 非结构化数据是不遵循特定数据模型或定义的数据（如文本或二进制数据）。 Blob 存储提供了三种类型的资源：

- 存储帐户
- 存储帐户中的容器
- 容器中的 blob

以下图示显示了这些资源之间的关系。

![Blob 存储体系结构的图示](./media/storage-blobs-introduction/blob1.png)

使用以下 C++ 类与这些资源进行交互：

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html)：`BlobServiceClient` 类可用于操纵 Azure 存储资源和 blob 容器。
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html)：`BlobContainerClient` 类可用于操纵 Azure 存储容器及其 blob。
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html)：`BlobClient` 类可用于操纵 Azure 存储 blob。 这是所有专用 blob 类的基类。
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html)：`BlockBlobClient` 类可用于操纵 Azure 存储块 blob。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示如何使用适用于 C++ 的 Azure Blob 存储客户端库执行以下任务：

- [添加包含文件](#add-include-files)
- [获取连接字符串](#get-the-connection-string)
- [创建容器](#create-a-container)
- [将 blob 上传到容器中](#upload-blobs-to-a-container)
- [列出容器中的 blob](#list-the-blobs-in-a-container)
- [下载 blob](#download-blobs)
- [删除容器](#delete-a-container)

### <a name="add-include-files"></a>添加包含文件

从项目目录中执行以下操作：

1. 在 Visual Studio 中打开 BlobQuickstartV12.sln 解决方案文件
1. 在 Visual Studio 中，打开 BlobQuickstartV12.cpp 源文件
1. 删除自动生成的 `main` 中的所有代码
1. 添加 `#include` 语句

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>获取连接字符串

下面的代码从[配置存储连接字符串](#configure-your-storage-connection-string)中创建的环境变量中检索存储帐户的连接字符串。

在 `main()` 中添加此代码：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-container"></a>创建容器

调用 [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a5d253aacb6e20578b7f5f233547be3e2) 函数，创建 [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) 类的实例。 然后调用 [CreateIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ab3ef187d2e30e1a19ebadf45d0fdf9c4)，在存储帐户中创建实际容器。

> [!IMPORTANT]
> 容器名称必须为小写。 有关命名容器和 Blob 的详细信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>将 blob 上传到容器中

以下代码片段：

1. 声明一个包含“Hello Azure!”的字符串。
1. 对在[创建容器](#create-a-container)部分创建的容器调用 [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f)，获取对 [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) 对象的引用。
1. 通过调用 [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) 函数，将字符串上传到 blob。 此函数将创建 Blob（如果该 Blob 尚不存在），或者更新 Blob（如果该 Blob 已存在）。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 blob

通过调用 [ListBlobs](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a8f7e6653ef69788cb4fb23ec85d4fd96) 函数，列出容器中的 blob。 只向容器添加了一个 blob，所以此操作只返回这一个 blob。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_ListBlobs":::

### <a name="download-blobs"></a>下载 Blob

获取上传的 Blob 的属性。 然后，使用上传的 blob 的属性，声明新的 `std::vector<uint8_t>` 对象并调整其大小。 通过在 [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) 基类中调用 [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa0bcbdbf6ebd17e4888cced4736420f7) 函数，将以前创建的 blob 下载到新的 `std::vector<uint8_t>` 对象。 最后，显示下载的 blob 数据。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>删除 Blob

以下代码通过调用 [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#ac121df9f5dfcdeb80f47b50543df0984) 函数，从 Azure Blob 存储容器中删除 blob。

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>删除容器

以下代码使用 [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ae7661b6d8f37213b409504c0e9a68d69) 来删除整个容器，从而清除该应用创建的资源。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>运行代码

此应用将创建一个容器，并将文本文件上传到 Azure Blob 存储。 示例随后列出容器中的 Blob，下载文件并显示文件内容。 最后，应用会删除 blob 和容器。

应用的输出类似于以下示例：

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 C++ 上传、下载和列出 Blob。 此外，还介绍了如何创建和删除 Azure Blob 存储容器。

若要查看 C++ Blob 存储示例，请继续参考以下内容：

> [!div class="nextstepaction"]
> [适用于 C++ 的 Azure Blob 存储 SDK v12 示例](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
