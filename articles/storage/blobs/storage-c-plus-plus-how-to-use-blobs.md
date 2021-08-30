---
title: 如何通过 C++ 使用对象 (Blob) 存储 - Azure | Microsoft Docs
description: 了解如何使用 C++ 通过 Azure Blob（对象）存储将非结构化数据 (Blob) 存储在云中。
author: twooley
ms.author: twooley
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 6dd7899e804e70e7fe3ab669c0c184f6b3882438
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748018"
---
# <a name="how-to-use-blob-storage-from-c"></a>如何通过 C++ 使用 Blob 存储

本指南演示如何使用 Azure Blob 存储执行常见方案。 此示例显示如何上传、列出、下载和删除 Blob。 示例采用 C++ 编写，并使用了[适用于 C++ 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)。
若要了解有关 Blob 存储的详细信息，请参阅 [Azure Blob 存储简介](storage-blobs-introduction.md)。

> [!NOTE]
> 本指南主要面向适用于 C++ 的 Azure 存储客户端库 1.0.0 版及更高版本（最高版本为 12.x）。 Microsoft 建议使用最新版本的适用于 C++ 的存储客户端库（通过 [GitHub](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage) 提供）。 有关详细信息，请参阅最新[快速入门](quickstart-blobs-c-plus-plus.md)。

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>创建 C++ 应用程序
在本指南中，将使用存储功能，这些功能可以在 C++ 应用程序中运行。

为此，需要安装适用于 C++ 的 Azure 存储客户端库，并在 Azure 订阅中创建 Azure 存储帐户。

若要安装适用于 C++ 的 Azure 存储客户端库，可使用以下方法：

- **Linux：** 按照 [适用于 C++ 的 Azure 存储客户端库自述文件：在 Linux 上开始使用](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)页中提供的说明操作。
- **Windows:** 在 Windows 中，使用 [vcpkg](https://github.com/microsoft/vcpkg) 作为依赖项管理员。 按照[快速入门](https://github.com/microsoft/vcpkg#quick-start)初始化 vcpkg。 然后使用以下命令安装库：

```powershell
.\vcpkg.exe install azure-storage-cpp
```

可以在[自述](https://github.com/Azure/azure-storage-cpp#download--install)文件中找到有关如何生成源代码和导出到 NuGet 的指南。

## <a name="configure-your-application-to-access-blob-storage"></a>配置应用程序以访问 Blob 存储
将以下 include 语句添加到 C++ 文件的顶部，要在此使用 Azure 存储 API 来访问 blob：

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串
Azure 存储客户端使用存储连接字符串来存储用于访问数据管理服务的终结点和凭据。 在客户端应用程序中运行时，必须提供以下格式的存储连接字符串，并对 AccountName 和 AccountKey 值使用 [Azure 门户](https://portal.azure.com)中列出的存储帐户的名称和存储帐户的存储访问密钥。 有关存储帐户和访问密钥的信息，请参阅[关于 Azure 存储帐户](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 此示例演示如何声明一个静态字段以保存连接字符串：

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

若要在本地 Windows 计算机中测试应用程序，可以使用 [Azurite 存储模拟器](../common/storage-use-azurite.md)。 Azurite 是一种用于模拟本地开发计算机上 Azure 中可用的 Blob、队列和表服务的实用工具。 以下示例演示如何声明一个静态字段以将连接字符串保存到本地存储模拟器：

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

若要启动 Azurite，请参阅[使用 Azurite 模拟器进行本地 Azure 存储开发](../common/storage-use-azurite.md)。

下面的示例假定使用了这两个方法之一来获取存储连接字符串。

## <a name="retrieve-your-storage-account"></a>检索存储帐户
可使用 **cloud_storage_account** 类来表示存储帐户信息。 若要从存储连接字符串中检索存储帐户信息，可以使用 **parse** 方法。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

接下来，获取对 **cloud_blob_client** 类的引用，因为它允许用户检索表示存储在 Blob 存储中的容器和 Blob 的对象。 以下代码使用我们在上面检索到的存储帐户对象创建 **cloud_blob_client** 对象：

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>如何：创建容器
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

此示例演示如何创建一个容器（如果该容器不存在）：

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

默认情况下，新容器是专用容器，因此，必须指定存储访问密钥才能从该容器下载 Blob。 如果要使容器中的文件 (blob) 对任何人都可用，则可以使用以下代码将容器设置为公用：

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Internet 中的所有人都可以查看公共容器中的 Blob，但是，仅在具有相应的访问密钥时，才能修改或删除它们。

## <a name="how-to-upload-a-blob-into-a-container"></a>如何：将 Blob 上传到容器
Azure Blob 存储支持块 Blob 和页 Blob。 大多数情况下，推荐使用块 Blob。

要将文件上传到块 Blob，请获取容器引用，并使用它获取块 Blob 引用。 拥有 Blob 引用后，可以通过调用 **upload_from_stream** 方法将任何数据流上传到其中。 如果之前不存在 Blob，此操作将创建一个；如果存在 Blob，此操作将覆盖它。 下面的示例演示了如何将 Blob 上传到容器中，并假定已创建容器。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

还可以使用 **upload_from_file** 方法将文件上传到块 Blob。

## <a name="how-to-list-the-blobs-in-a-container"></a>如何：列出容器中的 Blob
若要列出容器中的 Blob，首先需要获取容器引用。 然后，可以使用容器的 list_blobs 方法来检索其中的 Blob 和/或目录。 若要针对一个返回的 **list_blob_item** 访问其丰富的属性和方法，必须调用 **list_blob_item.as_blob** 方法以获取一个 **cloud_blob** 对象，或调用 **list_blob.as_directory** 方法以获取 cloud_blob_directory 对象。 以下代码演示如何检索和输出 **my-sample-container** 容器中每一项的 URI：

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

有关列出操作的更多详细信息，请参阅[使用 C++ 列出 Azure 存储资源](../common/storage-c-plus-plus-enumeration.md)。

## <a name="how-to-download-blobs"></a>如何：下载 Blob
如果要下载 Blob，请首先检索 Blob 引用，然后调用 download_to_stream 方法。 以下示例使用 **download_to_stream** 方法将 Blob 内容传输到一个流对象，然后用户即可将该对象保存到本地文件。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

也可以使用 **download_to_file** 方法将 Blob 的内容下载到文件。
此外，还可以使用 **download_text** 方法以文本字符串形式下载 Blob 的内容。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>如何：删除 Blob
要删除 Blob，请先获取 Blob 引用，然后对其调用 **delete_blob** 方法。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>后续步骤
现在，已了解 blob 存储的基础知识，请打开以下链接了解有关 Azure 存储的详细信息。

- [如何通过 C++ 使用队列存储](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [如何通过 C++ 使用表存储](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [使用 C++ 列出 Azure 存储资源](../common/storage-c-plus-plus-enumeration.md)
- [适用于 C++ 的存储空间客户端库参考](https://azure.github.io/azure-storage-cpp)
- [Azure 存储文档](https://azure.microsoft.com/documentation/services/storage/)
- [使用 AzCopy 命令行实用程序传输数据](../common/storage-use-azcopy-v10.md)