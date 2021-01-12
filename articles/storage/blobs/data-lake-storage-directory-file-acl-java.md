---
title: 用于文件和 ACL 的 Azure Data Lake Storage Gen2 Java SDK
description: 使用用于 Java 的 Azure 存储库在启用了分层命名空间 (HNS) 的存储帐户中管理目录和文件以及目录访问控制列表 (ACL)。
author: normesta
ms.service: storage
ms.date: 01/11/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1cc6954569c509c977634a8e1cdd52c5c55b2100
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108121"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Java 管理 Azure Data Lake Storage Gen2 中的目录、文件和 ACL

本文介绍了如何使用 Java 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录、文件与权限。 

[Package (Maven) ](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  | [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  | [API 参考](/java/api/overview/azure/storage-file-datalake-readme)  | [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  | [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](../common/storage-account-create.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

若要开始，请打开[此页](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)，找到最新版本的 Java 库。 然后，在文本编辑器中打开 pom.xml 文件。 添加引用该版本的依赖项元素。

如果计划使用 Azure Active Directory (AD) 验证客户端应用程序，可将依赖项添加到 Azure 机密客户端库。 请参阅[将机密客户端库包添加到项目](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)。

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

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。  若要获取这些值，请参阅[从 Azure AD 获取用于请求客户端应用程序授权的令牌](../common/storage-auth-aad-app.md)。

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

## <a name="manage-access-control-lists-acls"></a>管理访问控制列表 (ACL)

可以获取、设置和更新目录与文件的访问权限。

> [!NOTE]
> 若要使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已为安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

### <a name="manage-a-directory-acl"></a>管理目录 ACL

此示例获取并设置名为 `my-directory` 的目录的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

还可以获取和设置容器根目录的 ACL。 若要获取根目录，请将空字符串 (`""`) 传递到“DataLakeFileSystemClient.getDirectoryClient”方法。

### <a name="manage-a-file-acl"></a>管理文件 ACL

此示例获取并设置名为 `upload-file.txt` 的文件的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-an-acl-recursively"></a>以递归方式设置 ACL

你可以为父目录的现有子项以递归方式添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。 有关详细信息，请参阅[以递归方式为 Azure Data Lake Storage Gen2 设置访问控制列表 (ACL)](recursive-access-control-lists.md)。

## <a name="see-also"></a>另请参阅

* [API 参考文档](/java/api/overview/azure/storage-file-datalake-readme)
* [包 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)