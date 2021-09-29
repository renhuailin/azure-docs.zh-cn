---
title: 使用 Java 管理 Azure Data Lake Storage Gen2 中的 ACL
description: 使用适用于 Java 的 Azure 存储库在启用了分层命名空间 (HNS) 的存储帐户中管理访问控制列表 (ACL)。
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 6414db11bac11cb9b6faab59e67980b8ee8a2a78
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555501"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>使用 Java 管理 Azure Data Lake Storage Gen2 中的 ACL

本文介绍如何使用 Java 来获取、设置和更新目录和文件的访问控制列表。

ACL 继承已可用于在父目录下创建的新子项。 但是，还可以为父目录的现有子项以递归方式添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。

[包 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 参考](/java/api/overview/azure/storage-file-datalake-readme) | [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 已启用分层命名空间 (HNS) 的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- Azure CLI 版本 `2.6.0` 或更高版本。

- 以下安全权限之一：

  - 一个预配的 Azure Active Directory (AD) [安全主体](../../role-based-access-control/overview.md#security-principal)，它在目标容器父资源组或订阅范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。

  - 计划将 ACL 设置应用到的目标容器或目录的拥有用户。 为了以递归方式设置 ACL，这包括目标容器或目录中的所有子项。

  - 存储帐户密钥。

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD) 进行连接

可以使用[适用于 Java 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)，通过 Azure AD 对应用程序进行身份验证。

获取客户端 ID、客户端机密和租户 ID。 若要执行此操作，请参阅[从 Azure AD 获取用于对客户端应用程序的请求进行授权的令牌](../common/storage-auth-aad-app.md)。 在该过程中，你必须为安全主体分配以下 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 角色之一。

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> 有关更多示例，请参阅[适用于 Java 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)文档。

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>设置 ACL

设置 ACL 时，你将替换整个 ACL，包括其所有条目。 如果要更改安全主体的权限级别，或将新的安全主体添加到 ACL 而不影响其他现有项，则应改为更新 ACL。 若要更新 ACL 而不是替换它，请参阅本文的[更新 ACL](#update-acls) 部分。

如果选择设置 ACL，则必须为责任用户添加一个条目，为责任组添加一个条目，为所有其他用户添加一个条目。 若要详细了解责任用户、责任组和所有其他用户，请参阅[用户和标识](data-lake-storage-access-control.md#users-and-identities)。

本节介绍如何完成下列操作：

- 设置目录的 ACL
- 设置文件的 ACL
- 以递归方式设置 ACL

### <a name="set-the-acl-of-a-directory"></a>设置目录的 ACL

此示例获取并设置名为 `my-directory` 的目录的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

还可以获取和设置容器根目录的 ACL。 若要获取根目录，请将空字符串 (`""`) 传递到“DataLakeFileSystemClient.getDirectoryClient”方法。

### <a name="set-the-acl-of-a-file"></a>设置文件的 ACL

此示例获取并设置名为 `upload-file.txt` 的文件的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>以递归方式设置 ACL

通过调用 **DataLakeDirectoryClient.setAccessControlRecursive** 方法以递归方式设置 ACL。 向此方法传递 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的[列表](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)。 每个 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 定义一个 ACL 条目。

如果要设置 **默认** ACL 条目，则可以调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法，并传入值 **true**。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否设置默认 ACL。 该参数在每次调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法时使用。 ACL 的条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>更新 ACL

更新 ACL 时，你将修改 ACL 而非替换 ACL。 例如，你可以将一个新的安全主体添加到 ACL，而不影响 ACL 中列出的其他安全主体。  若要替换 ACL 而不是更新它，请参阅本文的 [设置 ACL](#set-acls) 部分。

本节介绍如何完成下列操作：

- 更新 ACL
- 以递归方式更新 ACL

### <a name="update-an-acl"></a>更新 ACL

首先，通过调用 [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) 方法获取目录的 ACL。 将 ACL 条目列表复制到类型为 [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry) 的新的 List 对象。 然后找到要更新的条目并在列表中替换它。 通过调用 [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 方法设置 ACL。

此示例通过替换所有其他用户的条目来更新名为 `my-parent-directory` 的容器的 ACL。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

还可以获取和设置容器根目录的 ACL。 若要获取根目录，请将空字符串 (`""`) 传递到“DataLakeFileSystemClient.getDirectoryClient”方法。

### <a name="update-acls-recursively"></a>以递归方式更新 ACL

若要以递归方式更新 ACL，请创建包含要更新的 ACL 条目的一个新 ACL 对象，然后在“更新 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要更新的 ACL 条目。

通过调用 **DataLakeDirectoryClient.updateAccessControlRecursive** 方法以递归方式更新 ACL。  向此方法传递 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的[列表](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)。 每个 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 定义一个 ACL 条目。

如果要更新 **默认** ACL 条目，则可以调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法，并传入值 **true**。

此示例以写入权限更新某个 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否更新默认 ACL。 该参数在调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法时使用。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>删除 ACL 条目

可以删除一个或多个 ACL 条目。 本节介绍如何完成下列操作：

- 删除 ACL 条目
- 以递归方式删除 ACL 条目

### <a name="remove-an-acl-entry"></a>删除 ACL 条目

首先，通过调用 [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) 方法获取目录的 ACL。 将 ACL 条目列表复制到类型为 [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry) 的新的 List 对象。 然后找到要删除的条目，并调用 List 对象的 Remove 方法 。 通过调用 [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 方法设置已更新的 ACL。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>以递归方式删除 ACL 条目

若要以递归方式删除 ACL 条目，请为要删除的 ACL 条目创建一个新的 ACL 对象，然后在“删除 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要删除的 ACL 条目。

通过调用 **DataLakeDirectoryClient.removeAccessControlRecursive** 方法删除 ACL 条目。 向此方法传递 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的[列表](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)。 每个 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 定义一个 ACL 条目。

如果要删除 **默认** ACL 条目，则可以调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法，并传入值 **true**。

此示例从名为 `my-parent-directory` 的目录的 ACL 中删除 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否删除默认 ACL 中的条目。 该参数在调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法时使用。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>从故障中恢复

你可能会遇到运行时错误或权限错误。 对于运行时错误，请从头开始重启此过程。 如果安全主体没有足够的权限修改要修改的目录层次结构中的目录或文件的 ACL，则会出现权限错误。 请解决权限问题，然后选择通过使用继续标记从故障点继续执行此过程，或者从头重启此过程。 如果希望从头开始重启，则无需使用继续标记。 你可以重新应用 ACL 条目，而不会产生任何负面影响。

此示例在失败时返回一个继续标记。 应用程序可以在错误得到解决后再次调用此示例方法，并传入继续标记。 如果是第一次调用此示例方法，则应用程序可以为继续标记参数传入 `null` 值。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

如果你希望过程继续完成而不被权限错误中断，则可以指定它。

若要确保过程无中断地完成，请调用 [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的 **setContinueOnFailure** 方法，并传入值 **true**。

此示例以递归方式设置 ACL 条目。 如果此代码遇到权限错误，它会记录该故障并继续执行。 此示例将故障数输出到控制台。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>另请参阅

- [API 参考文档](/java/api/overview/azure/storage-file-datalake-readme)
- [包 (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)
- [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)
