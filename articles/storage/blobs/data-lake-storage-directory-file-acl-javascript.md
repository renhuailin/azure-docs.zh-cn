---
title: 使用 JavaScript (Node.js) 管理 Azure Data Lake Storage Gen2 中的数据
description: 使用适用于 JavaScript 的 Azure 存储数据湖客户端库在启用了分层命名空间的存储帐户中管理目录和文件。
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: b7e753f248e9de1c2812cabed5c1cf432e726f59
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593204"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>使用 Node.js 中的 JavaScript SDK 管理 Azure Data Lake Storage Gen2 中的目录和文件

本文介绍如何使用 Node.js 在具有分层命名空间的存储帐户中创建和管理目录与文件。

若要了解如何获取、设置和更新目录与文件的访问控制列表 (ACL)，请参阅[使用 Node.js 中的 JavaScript SDK 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-javascript.md)。

“[包(节点包管理器)](https://www.npmjs.com/package/@azure/storage-file-datalake)” | ”[示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)” | ”[提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)”

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 有关详细信息，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 一个已启用分层命名空间的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- 如果在 Node.js 应用程序中使用此包，则需要 Node.js 8.0.0 或更高版本。

## <a name="set-up-your-project"></a>设置项目

打开一个终端窗口，然后键入以下命令来安装适用于 JavaScript 的 Data Lake 客户端库。

```javascript
npm install @azure/storage-file-datalake
```

将此语句放置在代码文件的顶部，以导入 `storage-file-datalake` 包。

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential =
     new StorageSharedKeyCredential(accountName, accountKey);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;
}

```

> [!NOTE]
> 此授权方法仅适用于 Node.js 应用程序。 如果打算在浏览器中运行代码，则可以使用 Azure Active Directory (Azure AD) 进行授权。

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD) 进行连接

可以使用[适用于 JS 的 Azure 标识客户端库](https://www.npmjs.com/package/@azure/identity)，通过 Azure AD 对应用程序进行身份验证。

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。 若要获取这些值，请参阅[从 Azure AD 获取用于请求客户端应用程序授权的令牌](../common/storage-auth-aad-app.md)。

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;
}
```

> [!NOTE]
> 有关更多示例，请参阅[适用于 JS 的 Azure 标识客户端库](https://www.npmjs.com/package/@azure/identity)文档。

## <a name="create-a-container"></a>创建容器

容器充当文件的文件系统。 可以通过获取 **FileSystemClient** 实例，然后调用 **FileSystemClient.Create** 方法来创建一个。

此示例创建一个名为 `my-file-system` 的容器。

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";

  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();

}
```

## <a name="create-a-directory"></a>创建目录

可以通过获取 **DirectoryClient** 实例，然后调用 **DirectoryClient.create** 方法来创建目录引用。

此示例将名为 `my-directory` 的目录添加到容器中。

```javascript
async function CreateDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");

  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

可以通过调用 **DirectoryClient.rename** 方法来重命名或移动目录。 以参数形式传递所需目录的路径。

此示例将某个子目录重命名为名称 `my-directory-renamed`。

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  await directoryClient.move("my-directory-renamed");

}
```

此示例将名为 `my-directory-renamed` 的目录移到名为 `my-directory-2` 的目录的子目录中。

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed");
  await directoryClient.move("my-directory-2/my-directory-renamed");

}
```

## <a name="delete-a-directory"></a>删除目录

可以通过调用 **DirectoryClient.delete** 方法来删除目录。

此示例删除名为 `my-directory` 的目录。

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

首先，读取文件。 此示例使用 Node.js `fs` 模块。 然后，通过创建一个 **FileClient** 实例并调用 **FileClient.create** 方法，在目标目录中创建文件引用。 通过调用 **FileClient.append** 方法来上传文件。 请确保通过调用 **FileClient.flush** 方法完成上传。

此示例将文本文件上传到名为 `my-directory` 的目录。

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs')

  var content = "";

  fs.readFile('mytestfile.txt', (err, data) => {
      if (err) throw err;

      content = data.toString();

  })

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>从目录下载

首先，创建表示要下载的文件的一个 **FileSystemClient** 实例。 使用 **FileSystemClient.read** 方法读取该文件。 然后，写入该文件。 此示例使用 Node.js `fs` 模块来执行该操作。

> [!NOTE]
> 此文件下载方法仅适用于 Node.js 应用程序。 如果打算在浏览器中运行代码，请参阅[适用于 JavaScript 的 Azure Storage File Data Lake 客户端库](https://www.npmjs.com/package/@azure/storage-file-datalake)自述文件来查看有关如何在浏览器中执行此操作的示例。

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);

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

  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>列出目录内容

此示例输出名为 `my-directory` 的目录中的每个目录和文件的名称。

```javascript
async function ListFilesInDirectory(fileSystemClient) {

  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {

    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>另请参阅

- [包(节点包管理器)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)
