---
title: 使用 Java 管理 Azure Data Lake Storage Gen2 中的数据
description: 使用适用于 Java 的 Azure 存储库在启用了分层命名空间的存储帐户中管理目录和文件。
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 25002640a81b260ced61c25e2d157a100d6dbc2a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593394"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>使用 Java 管理 Azure Data Lake Storage Gen2 中的目录和文件

本文介绍如何使用 Java 在具有分层命名空间的存储帐户中创建和管理目录与文件。

若要了解如何获取、设置和更新目录与文件的访问控制列表 (ACL)，请参阅[使用 Java 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-java.md)。

[包 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 参考](/java/api/overview/azure/storage-file-datalake-readme) | [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 有关详细信息，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 一个已启用分层命名空间的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

若要开始，请打开[此页](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)，找到最新版本的 Java 库。 然后，在文本编辑器中打开 pom.xml 文件。 添加引用该版本的依赖项元素。

如果计划通过使用 Azure Active Directory (Azure AD) 来验证客户端应用程序，请将依赖项添加到 Azure 机密客户端库。 有关详细信息，请参阅[将机密客户端库包添加到项目](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)。

接下来，将这些 import 语句添加到代码文件。

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD) 进行连接

可以使用[适用于 Java 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)，通过 Azure AD 对应用程序进行身份验证。

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。 若要获取这些值，请参阅[从 Azure AD 获取用于请求客户端应用程序授权的令牌](../common/storage-auth-aad-app.md)。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> 有关更多示例，请参阅[适用于 Java 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)文档。

## <a name="create-a-container"></a>创建容器

容器充当文件的文件系统。 可以通过调用 **DataLakeServiceClient.createFileSystem** 方法来创建一个。

此示例创建一个名为 `my-file-system` 的容器。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>创建目录

可以通过调用 **DataLakeFileSystemClient.createDirectory** 方法来创建目录引用。

此示例向容器添加名为 `my-directory` 的目录，然后添加名为 `my-subdirectory` 的子目录。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用 **DataLakeDirectoryClient.rename** 方法来重命名或移动目录。 以参数形式传递所需目录的路径。

此示例将某个子目录重命名为名称 `my-subdirectory-renamed`。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

此示例将名为 `my-subdirectory-renamed` 的目录移到名为 `my-directory-2` 的目录的子目录中。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>删除目录

通过调用 **DataLakeDirectoryClient.deleteWithResponse** 方法来删除目录。

此示例删除名为 `my-directory` 的目录。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

首先，通过创建 **DataLakeFileClient** 类的实例，在目标目录中创建文件引用。 通过调用 **DataLakeFileClient.append** 方法上传文件。 确保通过调用 **DataLakeFileClient.FlushAsync** 方法完成上传。

此示例将文本文件上传到名为 `my-directory` 的目录。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> 如果文件较大，则代码必须多次调用 DataLakeFileClient.append 方法。 可考虑改用 DataLakeFileClient.uploadFromFile 方法。 这样就可以在单个调用中上传整个文件。
>
> 有关示例，请参阅下一节。

## <a name="upload-a-large-file-to-a-directory"></a>将大型文件上传到目录

使用 DataLakeFileClient.uploadFromFile 方法上传大型文件，无需多次调用 DataLakeFileClient.append 方法 。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>从目录下载

首先，创建表示要下载的文件的一个 **DataLakeFileClient** 实例。 使用 **DataLakeFileClient.read** 方法读取该文件。 使用任何 .NET 文件处理 API 将来自流的字节保存到文件。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>列出目录内容

此示例输出名为 `my-directory` 的目录中每个文件的路径。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>另请参阅

- [API 参考文档](/java/api/overview/azure/storage-file-datalake-readme)
- [包 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)
