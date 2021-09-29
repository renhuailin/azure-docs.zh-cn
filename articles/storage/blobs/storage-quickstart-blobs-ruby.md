---
title: 快速入门：Azure Blob 存储客户端库 - Ruby
description: 在Azure Blob 存储中创建存储帐户和容器。 使用适用于 Ruby 的存储客户端库创建、下载 Blob 并列出容器中的 Blob。
author: normesta
ms.author: normesta
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: c34c042598e94cb2a86d1a068caca40bd3240b29
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587473"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>快速入门：适用于 Ruby 的 Azure Blob 存储客户端库

了解如何使用 Ruby 创建、下载和列出 Microsoft Azure Blob 存储的容器中的 Blob。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

请确保已安装下述额外的必备组件：

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- 使用 [RubyGem 包](https://rubygems.org/gems/azure-storage-blob)安装[用于 Ruby 的 Azure 存储库](https://github.com/azure/azure-storage-ruby)：

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中使用的[示例应用程序](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git)是基本的 Ruby 应用程序。

使用 [Git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。 此命令会将存储库克隆到本地计算机：

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

导航到 storage-blobs-ruby-quickstart 文件夹，然后在代码编辑器中打开 example.rb 文件 。

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

提供存储帐户名称和帐户密钥，以创建应用程序的 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 实例。

example.rb 文件中的以下代码将新的 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 对象实例化。 将 accountname  和 accountkey  值分别替换为帐户名称和密钥。

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>运行示例

此示例在 Blob 存储中创建一个容器，在容器中创建新的 Blob、列出容器中的 Blob 并将 Blob 下载到本地文件。

运行该示例。 下面是运行应用程序的输出示例：

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

按 Enter 继续时，示例程序会删除存储容器和本地文件。 继续前，请在“文档”文件夹中检查下载的文件。

还可以使用 [Azure 存储资源管理器](https://storageexplorer.com)查看存储帐户中的文件。 Azure 存储资源管理器是免费的跨平台工具，可用于访问存储帐户信息。

验证文件后，按 Enter 键删除测试文件并结束演示。 打开 example.rb 文件查看代码。

## <a name="understand-the-sample-code"></a>了解示例代码

接下来逐步介绍示例代码，以便你可以了解其工作方式。

### <a name="get-references-to-the-storage-objects"></a>获取对存储对象的引用

首先创建用于访问和管理 Blob 存储的对象的实例。 这些对象相互关联， 每个对象被列表中的下一个对象使用。

- 创建 Azure 存储 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 对象的实例，用于设置连接凭据。
- 创建[容器](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container)对象，该对象代表要访问的容器。 容器用于组织 blob，就像使用计算机上的文件夹组织文件一样。

获得容器对象后，可以创建[块](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) Blob 对象，使之指向你感兴趣的特定 Blob。 使用[块](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block)对象创建、下载和复制 Blob。

> [!IMPORTANT]
> 容器名称必须为小写。 有关容器名称和 Blob 名称的详细信息，请参阅 [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)（命名和引用容器、Blob 和元数据）。

下面的示例代码：

- 创建新容器
- 设置容器的权限，使 Blob 成为公共 Blob。 容器名为 quickstartblobs，追加了唯一 ID。

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>在容器中创建 Blob

Blob 存储支持块 Blob、追加 Blob 和页 Blob。 若要创建 Blob，请调用 [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) 方法传入 Blob 的数据。

以下示例在前面创建的容器中创建一个名为 QuickStart_ 的 Blob，其具有唯一 ID 和 .txt 文件扩展名 。

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

块 Blob 最大可以为 4.7 TB，并且可以是从电子表格到大视频文件的任何内容。 页 Blob 主要用于支持 IaaS 虚拟机的 VHD 文件。 追加 Blob 通常用于日志记录，例如有时需要写入到文件，再继续添加更多信息。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 blob

使用 list_blobs[](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method) 方法获取容器中的文件的列表。 下面的代码检索 Blob 列表，然后显示它们的名称。

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>下载 blob

使用 [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) 方法将 Blob 下载到本地磁盘。 以下代码将下载前面部分所创建的 Blob。

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>清理资源

如果不再需要 Blob，请使用 [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) 将其删除。 使用 [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) 方法删除整个容器。 删除容器还会删除容器中存储的所有 Blob。

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>用于开发包含 Blob 的 Ruby 应用程序的资源

查看以下附加资源，了解如何进行 Ruby 开发：

- 在 GitHub 上查看并下载 Azure 存储的 [Ruby 客户端库源代码](https://github.com/Azure/azure-storage-ruby)。
- 浏览使用 Ruby 客户端库编写的 [ 示例](/samples/browse/?products=azure&languages=ruby)。
- [示例：Ruby 中的 Azure 存储入门](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Ruby 在 Azure Blob 存储和本地磁盘之间传输文件。 若要了解有关如何使用 Blob 存储的详细信息，请继续阅读存储帐户概述。

> [!div class="nextstepaction"]
> [存储帐户概述](../common/storage-account-overview.md)

若要详细了解存储资源管理器和 Blob，请参阅[使用存储资源管理器管理 Azure Blob 存储资源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
