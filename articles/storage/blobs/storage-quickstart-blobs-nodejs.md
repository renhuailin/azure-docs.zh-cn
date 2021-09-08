---
title: 快速入门：Azure Blob 存储库 v12 - JavaScript
description: 本快速入门介绍如何使用适用于 JavaScript 的 Azure Blob 存储客户端库版本 12 在 Blob（对象）存储中创建容器和 blob。 接下来，将介绍如何将 blob 下载到本地计算机，以及如何在容器中列出所有 blob。
author: twooley
ms.author: twooley
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 58c956b48c26e4614dd15cd8c4bca657409d2678
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123467622"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>快速入门：在 Node.js 中使用 JavaScript v12 SDK 管理 blob

本快速入门介绍如何使用 Node.js 管理 blob。 Blob 是可以保存大量文本或二进制数据（包括图像、文档、流媒体和存档数据）的对象。 你将上传、下载和列出 Blob，并创建和删除容器。

其他资源：

* [API 参考文档](/javascript/api/@azure/storage-blob)
* [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [包(节点包管理器)](https://www.npmjs.com/package/@azure/storage-blob)
* [示例](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个 Azure 存储帐户。 [创建存储帐户](../common/storage-account-create.md)。
- [Node.js](https://nodejs.org/en/download/)。

## <a name="setting-up"></a>设置

本部分逐步指导如何准备一个项目，使其与适用于 JavaScript 的 Azure Blob 存储客户端库 v12 配合使用。

### <a name="create-the-project"></a>创建项目

创建名为 blob-quickstart-v12 的 JavaScript 应用程序。

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为项目创建新目录。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 切换到新创建的 blob-quickstart-v12 目录。

    ```console
    cd blob-quickstart-v12
    ```

1. 创建名为 package.json 的新文本文件。 此文件定义 Node.js 项目。 将此文件保存到 blob-quickstart-v12 目录中。 下面是文件的内容：

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    如果需要，可以在 `author` 字段中输入自己的名字。

### <a name="install-the-package"></a>安装包

当仍在 blob-quickstart-v12 目录中时，使用 `npm install` 命令安装适用于 JavaScript 包的 Azure Blob 存储客户端库。 此命令读取 package.json 文件，并安装适用于 JavaScript 包的 Azure Blob 存储客户端库 v12 及其依赖的所有库。

```console
npm install
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在代码编辑器中打开另一个新文本文件
1. 添加 `require` 调用以加载 Azure 和 Node.js 模块
1. 为程序创建结构，包括基本的异常处理

    代码如下：

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. 将新文件在 blob-quickstart-v12 目录中另存为 blob-quickstart-v12.js 。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>对象模型

Azure Blob 存储最适合存储巨量的非结构化数据。 非结构化数据是不遵循特定数据模型或定义的数据（如文本或二进制数据）。 Blob 存储提供了三种类型的资源：

* 存储帐户
* 存储帐户中的容器
* 容器中的 blob

以下图示显示了这些资源之间的关系。

![Blob 存储体系结构的图示](./media/storage-blobs-introduction/blob1.png)

使用以下 JavaScript 类与这些资源进行交互：

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient)：`BlobServiceClient` 类可用于操纵 Azure 存储资源和 blob 容器。
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient)：`ContainerClient` 类可用于操纵 Azure 存储容器及其 blob。
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient)：`BlobClient` 类可用于操纵 Azure 存储 blob。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示如何使用适用于 JavaScript 的 Azure Blob 存储客户端库执行以下步骤：

* [获取连接字符串](#get-the-connection-string)
* [创建容器](#create-a-container)
* [将 blob 上传到容器中](#upload-blobs-to-a-container)
* [列出容器中的 blob](#list-the-blobs-in-a-container)
* [下载 blob](#download-blobs)
* [删除容器](#delete-a-container)

### <a name="get-the-connection-string"></a>获取连接字符串

下面的代码从[配置存储连接字符串](#configure-your-storage-connection-string)部分中创建的环境变量中检索存储帐户的连接字符串。

在 `main` 函数内添加此代码：

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>创建容器

确定新容器的名称。 以下代码将 UUID 值追加到容器名称，确保其是唯一的。

> [!IMPORTANT]
> 容器名称必须为小写。 有关命名容器和 Blob 的详细信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

调用 [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) 方法，创建 [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) 类的实例。 然后，调用 [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) 方法以获取对容器的引用。 最后，调用 [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) 方法在存储帐户中实际创建容器。

将此代码添加到 `main` 函数的末尾：

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>将 blob 上传到容器中

以下代码片段：

1. 创建要上传到 blob 的文本字符串。
1. 通过从 [Create a container](#create-a-container) 部分调用 [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) 上的 [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) 方法，获取对 [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) 对象的引用。
1. 通过调用 [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) 方法，将文本字符串数据上传到 blob。

将此代码添加到 `main` 函数的末尾：

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

通过调用 [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) 方法，列出容器中的 blob。 在这种情况下，只向容器添加了一个 blob，因此列表操作只返回那个 blob。

将此代码添加到 `main` 函数的末尾：

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>下载 Blob

通过调用 [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) 方法，下载以前创建的 blob。 示例代码包括名为 `streamToString` 的帮助程序函数，用于将 Node.js 可读流读入字符串。

将此代码添加到 `main` 函数的末尾：

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

在 `main` 函数后添加此帮助器函数：

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>删除容器

以下代码使用 [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) 方法删除整个容器，从而清除该应用所创建的资源。 也可根据需要删除本地文件。

将此代码添加到 `main` 函数的末尾：

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>运行代码

此应用创建文本字符串，并将其上传到 Blob 存储。 示例随后列出容器中的 Blob，下载 Blob 并显示下载的数据。

在控制台提示符下，导航到包含 blob-quickstart-v12.js 文件的目录，然后执行以下 `node` 命令来运行应用。

```console
node blob-quickstart-v12.js
```

应用的输出类似于以下示例：

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

在调试器中逐步执行代码，并在执行过程中反复检查 [Azure 门户](https://portal.azure.com)。 检查是否正在创建容器。 可以在容器中打开 blob 并查看内容。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 JavaScript 上传、下载和列出 blob。

有关教程、示例、快速入门和其他文档，请访问：

> [!div class="nextstepaction"]
> [Azure for JavaScript 开发人员中心](/azure/developer/javascript/)

* 若要了解如何部署使用 Azure Blob 存储的 Web 应用，请参阅[教程：使用 Azure 存储将映像数据上传到云中](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)
* 若要查看 Blob 存储示例应用，请继续学习 [Azure Blob 存储客户端库 v12 JavaScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)。
* 若要了解详细信息，请参阅[适用于 JavaScript 的 Azure Blob 存储客户端库](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)。
