---
title: 使用 Java 针对 Azure 文件进行开发 | Microsoft Docs
description: 了解如何开发使用 Azure 文件来存储文件数据的 Java 应用程序和服务。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/26/2021
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76bbc39cc1bb53d51c35b4afde13de15842801e2
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576000"
---
# <a name="develop-for-azure-files-with-java"></a>使用 Java 针对 Azure 文件进行开发

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

了解开发使用 Azure 文件存储来存储数据的 Java 应用程序的基础知识。 使用 Azure 文件存储 API 创建控制台应用程序并了解基本操作：

- 创建和删除 Azure 文件共享
- 创建和删除目录
- 枚举 Azure 文件共享中的文件和目录
- 上传、下载和删除文件

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>创建 Java 应用程序

若要生成示例，需要 Java 开发工具包 (JDK) 和[适用于 Java 的 Azure 存储 SDK](https://github.com/azure/azure-sdk-for-java)。 此外，应该已经创建了一个 Azure 存储帐户。

## <a name="set-up-your-application-to-use-azure-files"></a>设置应用程序以使用 Azure 文件

若要使用 Azure 文件存储 API，请将以下代码添加到要通过其访问 Azure 文件存储的 Java 文件的顶部。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串

要使用 Azure 文件，需要连接到 Azure 存储帐户。 配置连接字符串并使用它连接到存储帐户。 定义用于保存连接字符串的静态变量。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

将 *\<storage_account_name\>* 和 *\<storage_account_key\>* 替换为存储帐户的实际值。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

将 your_storage_account_name 和 your_storage_account_key 替换为存储帐户的实际值。

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-an-azure-file-share"></a>访问 Azure 文件共享

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

若要访问 Azure 文件存储，请创建一个 [ShareClient](/java/api/com.azure.storage.file.share.shareclient) 对象。 使用 [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) 类生成新的 **ShareClient** 对象。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

若要访问存储帐户，请使用 CloudStorageAccount 对象，将连接字符串传递到其 parse 方法。

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** 会引发 InvalidKeyException，因此需将其置于 try/catch 块内。

---

## <a name="create-a-file-share"></a>创建文件共享

Azure 文件存储中的所有文件和目录都存储在名为共享的容器内。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

如果共享已经存在，则 [ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) 方法将引发异常。 将对 create 的调用置于 `try/catch` 块中并处理异常。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

若要获取对共享及其内容的访问权限，请创建 Azure 文件存储客户端。

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

使用 Azure 文件客户端可以获取对共享的引用。

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

实际创建共享时，请使用 CloudFileShare 对象的 createIfNotExists 方法。

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

而在目前，share 保留对名为 sample share 的共享的引用。

---

## <a name="delete-a-file-share"></a>删除文件共享

以下示例代码将删除文件共享。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

通过调用 [ShareClient.delete](/java/api/com.azure.storage.file.share.shareclient.delete) 方法删除共享。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

通过对 CloudFileShare 对象调用 deleteIfExists 方法来删除共享。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>创建目录

将文件置于子目录中，而不是将其全部置于根目录中，以便对存储进行整理。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

以下代码通过调用 [ShareDirectoryClient.create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create) 创建目录。 示例方法返回一个 `Boolean` 值，该值指示它是否成功创建了目录。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

以下代码会在根目录下创建名为 sampledir 的子目录。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>删除目录

删除目录是一项简单的任务。 你无法删除仍包含文件或子目录的目录。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

如果目录不存在或不为空，则 [ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) 方法将引发异常。 将对 delete 的调用置于 `try/catch` 块中并处理异常。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>枚举 Azure 文件共享中的文件和目录

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

通过调用 [ShareDirectoryClient.listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories) 获取文件和目录的列表。 该方法会返回可循环访问的 [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) 对象的列表。 以下代码将列出 dirName 参数指定的目录内的文件和目录。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

通过对 CloudFileDirectory 引用调用 listFilesAndDirectories，获取文件和目录的列表。 该方法会返回可循环访问的 ListFileItem 对象的列表。 以下代码将列出根目录中的文件和目录。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>上传文件

了解如何从本地存储上传文件。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

以下代码通过调用 [ShareFileClient.uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) 方法将本地文件上传到 Azure 文件存储。 以下示例方法返回一个 `Boolean` 值，该值指示它是否成功上传了指定的文件。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

通过对共享对象调用 getRootDirectoryReference 方法，获取对文件上传目录的引用。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

现在，你已经有了共享所在的根目录的引用，因此可以使用以下代码来上传文件。

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>下载文件

较常见的操作之一是从 Azure 文件共享下载文件。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

以下示例将指定文件下载到 destDir 参数中指定的本地目录。 示例方法通过在下载的文件名前面追加日期和时间，来实现文件名的唯一性。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

以下示例将下载 SampleFile.txt 并显示其内容。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>删除文件

另一项常见的 Azure 文件操作是删除文件。

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

以下代码将删除指定文件。 首先，该示例基于 dirName 参数创建一个 [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient)。 然后，代码根据 fileName 参数从目录客户端获取 [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient)。 最后，示例方法调用 [ShareFileClient.delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) 来删除文件。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

下面的代码会删除名为 SampleFile.txt 的文件，该文件存储在名为 **sampledir** 的目录中。

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>后续步骤

如果还想更多地了解其他 Azure 存储 API，请点击以下链接。

- [面向 Java 开发人员的 Azure](/azure/developer/java)
- [Azure SDK for Java](https://github.com/azure/azure-sdk-for-java)
- [适用于 Android 的 Azure SDK](https://github.com/azure/azure-sdk-for-android)
- [适用于 Java SDK 的 Azure 文件共享客户端库参考](/java/api/overview/azure/storage-file-share-readme)
- [Azure 存储空间服务 REST API](/rest/api/storageservices/)
- [Azure 存储团队博客](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [使用 AzCopy 命令行实用工具传输数据](../common/storage-use-azcopy-v10.md)
- [排查 Azure 文件问题 - Windows](storage-troubleshoot-windows-file-connection-problems.md)
