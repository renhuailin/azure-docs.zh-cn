---
title: 使用 .NET 管理 Azure Data Lake Storage Gen2 中的 ACL
description: 使用 .NET 在启用了分层命名空间 (HNS) 的存储帐户中管理访问控制列表 (ACL)。
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: c3f0d0dd82201a3a339f05929ffefa988e1c8692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564935"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>使用 .NET 管理 Azure Data Lake Storage Gen2 中的 ACL

本文介绍如何使用 .NET 来获取、设置和更新目录和文件的访问控制列表。

ACL 继承已可用于在父目录下创建的新子项。 但是你也可以为父目录的现有子项以递归方式添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。

[包 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [递归 ACL 示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0) | [API 参考](/dotnet/api/azure.storage.files.datalake) | [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [提供反馈](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 已启用分层命名空间 (HNS) 的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- Azure CLI 版本 `2.6.0` 或更高版本。

- 以下安全权限之一：

  - 一个预配的 Azure Active Directory (AD) [安全主体](../../role-based-access-control/overview.md#security-principal)，它在目标容器父资源组或订阅范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。

  - 计划将 ACL 设置应用到的目标容器或目录的拥有用户。 为了以递归方式设置 ACL，这包括目标容器或目录中的所有子项。

  - 存储帐户密钥。

## <a name="set-up-your-project"></a>设置项目

若要开始，请安装 [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 包。

1. 打开一个命令窗口（例如：Windows PowerShell）。

2. 从你的项目目录中，使用 `dotnet add package` 命令安装 Azure.Storage.Files.DataLake 预览版包。

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   然后，将这些 using 语句添加到代码文件的顶部。

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码片段，需创建一个表示存储帐户的 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 实例。

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

> [!NOTE]
> 若要使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已为安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改这些权限所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](./data-lake-storage-access-control-model.md)。

可以使用[适用于 .NET 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)，通过 Azure AD 对应用程序进行身份验证。

安装包后，将此 using 语句添加到代码文件的顶部。

```csharp
using Azure.Identity;
```

获取客户端 ID、客户端机密和租户 ID。 若要执行此操作，请参阅[从 Azure AD 获取用于对客户端应用程序的请求进行授权的令牌](../common/storage-auth-aad-app.md)。 在该过程中，你必须为安全主体分配以下 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 角色之一。

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 有关更多示例，请参阅[适用于 .NET 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)文档。

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>设置 ACL

设置 ACL 时，你将替换整个 ACL，包括其所有条目。 如果要更改安全主体的权限级别，或将新的安全主体添加到 ACL 而不影响其他现有项，则应改为更新 ACL。 若要更新 ACL 而不是替换它，请参阅本文的[更新 ACL](#update-acls) 部分。

如果选择设置 ACL，则必须为责任用户添加一个条目，为责任组添加一个条目，为所有其他用户添加一个条目。 若要详细了解责任用户、责任组和所有其他用户，请参阅[用户和标识](data-lake-storage-access-control.md#users-and-identities)。

本节介绍如何完成下列操作：

- 设置目录的 ACL
- 设置文件的 ACL
- 以递归方式设置 ACL

### <a name="set-the-acl-of-a-directory"></a>设置目录的 ACL

可以通过调用 [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 方法获取目录的访问控制列表 (ACL)，并通过调用 [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 方法来设置 ACL。

此示例获取并设置名为 `my-directory` 的目录的 ACL。 字符串 `user::rwx,group::r-x,other::rw-` 为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取和写入权限。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

还可以获取和设置容器根目录的 ACL。 若要获取根目录，请将空字符串 (`""`) 传递到 [DataLakeFileSystemClient.GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) 方法。

### <a name="set-the-acl-of-a-file"></a>设置文件的 ACL

可以通过调用 [DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) 方法获取文件的访问控制列表 (ACL)，并通过调用 [DataLakeFileClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) 方法来设置 ACL。

此示例获取并设置名为 `my-file.txt` 的文件的 ACL。 字符串 `user::rwx,group::r-x,other::rw-` 为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取和写入权限。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>以递归方式设置 ACL

通过调用 DataLakeDirectoryClient.SetAccessControlRecursiveAsync 方法以递归方式设置 ACL。 向此方法传递 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的[列表](/dotnet/api/system.collections.generic.list-1)。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。

如果要设置 **默认** ACL 条目，请将 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 属性设置为 **true**。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否设置默认 ACL。 该参数用在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的构造函数中。 ACL 的条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

## <a name="update-acls"></a>更新 ACL

更新 ACL 时，你将修改 ACL 而非替换 ACL。 例如，你可以将一个新的安全主体添加到 ACL，而不影响 ACL 中列出的其他安全主体。  若要替换 ACL 而不是更新它，请参阅本文的 [设置 ACL](#set-acls) 部分。

本节介绍如何完成下列操作：

- 更新 ACL
- 以递归方式更新 ACL

### <a name="update-an-acl"></a>更新 ACL

首先，通过调用 [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 方法获取目录的 ACL。 将 ACL 条目列表复制到 [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) 对象的新[列表](/dotnet/api/system.collections.generic.list-1)。 然后找到要更新的条目并在列表中替换它。 通过调用 [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 方法设置 ACL。

此示例通过替换所有其他用户的 ACL 条目来更新容器的根 ACL。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>以递归方式更新 ACL

若要以递归方式更新 ACL，请创建包含要更新的 ACL 条目的一个新 ACL 对象，然后在“更新 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要更新的 ACL 条目。

通过调用 **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync** 方法以递归方式更新 ACL。  向此方法传递 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的[列表](/dotnet/api/system.collections.generic.list-1)。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。

如果要更新 **默认** ACL 条目，请将 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 属性设置为 **true**。

此示例以写入权限更新某个 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否更新默认 ACL。 该参数用在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的构造函数中。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

若要查看通过指定批大小以递归方式成批更新 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

## <a name="remove-acl-entries"></a>删除 ACL 条目

可以删除一个或多个 ACL 条目。 本节介绍如何完成下列操作：

- 删除 ACL 条目
- 以递归方式删除 ACL 条目

### <a name="remove-an-acl-entry"></a>删除 ACL 条目

首先，通过调用 [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 方法获取目录的 ACL。 将 ACL 条目列表复制到 [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) 对象的新[列表](/dotnet/api/system.collections.generic.list-1)。 然后找到要删除的条目并调用集合的 [Remove](/dotnet/api/system.collections.ilist.remove) 方法。 通过调用 [DataLakeDirectoryClient.SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 方法设置已更新的 ACL。

此示例通过替换所有其他用户的 ACL 条目来更新容器的根 ACL。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>以递归方式删除 ACL 条目

若要以递归方式删除 ACL 条目，请为要删除的 ACL 条目创建一个新的 ACL 对象，然后在“删除 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要删除的 ACL 条目。

通过调用 **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync** 方法删除 ACL 条目。 向此方法传递 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的[列表](/dotnet/api/system.collections.generic.list-1)。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。

如果要删除 **默认** ACL 条目，请将 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 属性设置为 **true**。

此示例从名为 `my-parent-directory` 的目录的 ACL 中删除 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否删除默认 ACL 中的条目。 该参数用在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的构造函数中。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

若要查看通过指定批大小以递归方式成批删除 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

## <a name="recover-from-failures"></a>从故障中恢复

以递归方式修改 ACL 时，可能会遇到运行时或权限错误。 对于运行时错误，请从头开始重启此过程。 如果安全主体没有足够的权限修改要修改的目录层次结构中的目录或文件的 ACL，则会出现权限错误。 请解决权限问题，然后选择通过使用继续标记从故障点继续执行此过程，或者从头重启此过程。 如果希望从头开始重启，则无需使用继续标记。 你可以重新应用 ACL 条目，而不会产生任何负面影响。

此示例在失败时返回一个继续标记。 应用程序可以在错误得到解决后再次调用此示例方法，并传入继续标记。 如果是第一次调用此示例方法，则应用程序可以为继续标记参数传入 `null` 值。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

如果你希望过程继续完成而不被权限错误中断，则可以指定它。

若要确保过程无中断地完成，请传入 **AccessControlChangedOptions** 对象，并将该对象的 **ContinueOnFailure** 属性设置为 ``true``。

此示例以递归方式设置 ACL 条目。 如果此代码遇到权限错误，它会记录该故障并继续执行。 此示例将故障数输出到控制台。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>另请参阅

- [API 参考文档](/dotnet/api/azure.storage.files.datalake)
- [包 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [提供反馈](https://github.com/Azure/azure-sdk-for-net/issues)
- [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)
