---
title: 快速入门：Azure 存储文件共享库 v12 - C++
description: 本快速入门介绍如何使用适用于 C++ 的 Azure 存储文件共享客户端库版本 12 创建文件共享和文件。 接下来，介绍如何设置和检索元数据，然后将文件下载到本地计算机。
author: kyle-patterson
ms.author: kylepa
ms.date: 06/22/2021
ms.service: storage
ms.subservice: files
ms.topic: quickstart
ms.openlocfilehash: 4da02f46ef793ae03a11fa4895471488f78d0db1
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894268"
---
# <a name="develop-for-azure-files-with-c"></a>使用 C++ 针对 Azure 文件进行开发

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="about-this-tutorial"></a>关于本教程

本教程将介绍如何使用 C++ 针对 Azure 文件存储执行基本操作。 如果你不熟悉 Azure 文件存储，请先了解后续部分中的概念，以便理解相关示例。 涵盖的部分示例包括：

* 创建和删除 Azure 文件共享
* 创建和删除目录
* 上传、下载和删除文件
* 设置和列出文件的元数据

> [!Note]  
> 由于 Azure 文件可以通过 SMB 进行访问，因此可以编写简单的应用程序，通过标准的 C++ I/O 类和函数来访问 Azure 文件共享。 本文将介绍如何编写使用 Azure 存储 C++ SDK 的应用程序，该 SDK 使用 [文件 REST API](/rest/api/storageservices/file-service-rest-api) 与 Azure 文件通信。


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
vcpkg.exe install azure-storage-files-shares-cpp:x64-windows
```

有关详细信息，请访问 GitHub 以获取并生成[适用于 C++ 的 Azure SDK](https://github.com/Azure/azure-sdk-for-cpp/)。

### <a name="create-the-project"></a>创建项目

在 Visual Studio 中，新建名为 FilesShareQuickstartV12 的适用于 Windows 的 C++ 控制台应用程序。

:::image type="content" source="./media/quickstart-files-c-plus-plus/visual-studio-create-project.png" alt-text="用于配置新的 C++ Windows 控制台应用的 Visual Studio 对话框":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="code-examples"></a>代码示例

这些示例代码片段演示如何使用适用于 C++ 的 Azure 文件共享客户端库执行以下任务：

- [添加包含文件](#add-include-files)
- [获取连接字符串](#get-the-connection-string)
- [创建文件共享](#create-a-files-share)
- [将文件上传到文件共享](#upload-files-to-a-files-share)
- [设置文件的元数据](#set-the-metadata-of-a-file)
- [列出文件的元数据](#list-the-metadata-of-a-file)
- [下载文件](#download-files)
- [删除文件](#delete-a-file)
- [删除文件共享](#delete-a-files-share)

### <a name="add-include-files"></a>添加包含文件

从项目目录中执行以下操作：

1. 在 Visual Studio 中打开 FilesShareQuickstartV12.sln 解决方案文件。
1. 在 Visual Studio 中打开 FilesShareQuickstartV12.cpp 源文件。
1. 删除 `main` 中自动生成的所有代码。
1. 添加 `#include` 语句。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>获取连接字符串

下面的代码从[配置存储连接字符串](#configure-your-storage-connection-string)中创建的环境变量中检索存储帐户的连接字符串。

在 `main()` 中添加此代码：

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-files-share"></a>创建文件共享

通过调用 [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a7462bcad8a9144f84b0acc70735240e2) 函数来创建 [ShareClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html) 类的实例。 然后调用 [CreateIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a6432b4cc677ac03257c7bf234129ec5b)，在存储帐户中创建实际的文件共享。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_CreateFilesShare":::

### <a name="upload-files-to-a-files-share"></a>将文件上传到文件共享

以下代码片段：

1. 声明一个包含“Hello Azure!”的字符串。
1. 获取对 [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) 对象的引用，方法是获取根 [ShareDirectoryClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html)，然后对[创建文件共享](#create-a-files-share)部分中创建的文件共享调用 [GetFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html#a93545b8d82ee94f9de183c4d277059d8)。
1. 调用 [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#aa80c1f0da6e6784aa0a67cff03d128ba) 函数，将字符串上传到文件。 如果该文件尚不存在，此函数将创建该文件；如果该文件存在，则更新该文件。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_UploadFile":::

### <a name="set-the-metadata-of-a-file"></a>设置文件的元数据

通过调用 [ShareFileClient.SetMetadata](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a2f5a40b71040003e41ba8495101f67bb) 函数来设置文件的元数据属性。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_SetFileMetadata":::

### <a name="list-the-metadata-of-a-file"></a>列出文件的元数据

通过调用 [ShareFileClient.GetProperties](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a220017815847877cfe8f244b482fb45c) 函数来获取文件的元数据属性。 元数据位于返回的 `Value` 的 `Metadata` 字段下。 元数据是一个键值对，类似于[设置文件的元数据](#set-the-metadata-of-a-file)中的示例。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_GetFileMetadata":::

### <a name="download-files"></a>下载文件

在[列出文件的元数据](#list-the-metadata-of-a-file)中检索文件的属性后，使用上传的文件的属性创建一个新的 `std::vector<uint8_t>` 对象。 通过调用 [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) 基类中的 [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a256e7b317fbf762c4f8f5023a2cd8cb9) 函数，将前面创建的文件下载到新的 `std::vector<uint8_t>` 对象。 最后，显示下载的文件数据。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DownloadFile":::

### <a name="delete-a-file"></a>删除文件

以下代码通过调用 [ShareFileClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a86036b445dce67a96f7bf6c45f163f59) 函数，从 Azure 存储文件共享中删除 Blob。

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFile":::

### <a name="delete-a-files-share"></a>删除文件共享

以下代码使用 [ShareClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a9915630503f1675b5f49eb9c01ca2601) 删除整个文件共享，从而清理应用创建的资源。

将此代码添加到 `main()` 的末尾：

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFilesShare":::

## <a name="run-the-code"></a>运行代码

此应用将创建一个容器，并将文本文件上传到 Azure Blob 存储。 示例随后列出容器中的 Blob，下载文件并显示文件内容。 最后，应用会删除 blob 和容器。

应用的输出类似于以下示例：

```output
Azure Files Shares storage v12 - C++ quickstart sample
Creating files share: sample-share
Uploading file: sample-file
Listing file metadata...
key1:value1
key2:value2
Downloaded file contents: Hello Azure!
Deleting file: sample-file
Deleting files share: sample-share
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何使用 C++ 上传、下载和列出文件。 此外，你还了解了如何创建和删除 Azure 存储文件共享。

若要查看 C++ Blob 存储示例，请继续参考以下内容：

> [!div class="nextstepaction"]
> [适用于 C++ 的 Azure 存储文件共享 SDK v12 示例](https://github.com/Azure/azure-sdk-for-cpp/tree/main/sdk/storage/azure-storage-files-shares/sample)
