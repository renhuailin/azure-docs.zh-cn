---
title: 以递归方式为 Azure Data Lake Storage Gen2 设置 ACL | Microsoft Docs
description: 你可以以递归方式为父目录的现有子项添加、更新和删除访问控制列表。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 626e626cbd8fa86bd0366516cbaf5a54789f3988
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741037"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>以递归方式为 Azure Data Lake Storage Gen2 设置访问控制列表 (ACL)

ACL 继承已可用于在父目录下创建的新子项。 你现在还可以以递归方式为父目录的现有子项添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。

[库](#libraries) | [示例](#code-samples) | [最佳做法](#best-practice-guidelines)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- 执行递归 ACL 过程所需的正确权限。 正确的权限包括下列任一项： 

  - 一个预配的 Azure Active Directory (AD) [安全主体](../../role-based-access-control/overview.md#security-principal)，它在目标容器父资源组或订阅范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。   

  - 你计划向其应用递归 ACL 过程的目标容器或目录的所有者用户。 这包括目标容器或目录中的所有子项。 

- 了解 ACL 如何应用于目录和文件。 请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。 

请参阅本文的“设置项目”部分，查看 PowerShell、.NET SDK 和 Python SDK 的安装指南。

## <a name="set-up-your-project"></a>设置项目

安装所需的库。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 使用以下命令验证安装的 PowerShell 版本是否为 `5.1` 或以上。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升级 PowerShell 版本，请参阅[升级现有的 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell)
    
2. 安装 Az.Storage 模块。

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 使用以下命令验证安装的 Azure CLI 版本是否是 `2.14.0` 或更高版本。

   ```azurecli
    az --version
   ```
   如果 Azure CLI 版本低于 `2.14.0`，则安装更高版本。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="net"></a>[.NET](#tab/dotnet)

1. 打开一个命令窗口（例如：Windows PowerShell）。

2. 从你的项目目录中，使用 `dotnet add package` 命令安装 Azure.Storage.Files.DataLake 预览版包。

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 将这些 using 语句添加到代码文件的顶部。

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

若要开始，请打开[此页](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)，找到最新版本的 Java 库。 然后，在文本编辑器中打开 pom.xml 文件。 添加引用该版本的依赖项元素。

如果计划使用 Azure Active Directory (AD) 验证客户端应用程序，可将依赖项添加到 Azure 机密客户端库。 请参阅[将机密客户端库包添加到项目](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)。

接下来，将这些 import 语句添加到代码文件。

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. 下载[适用于 Python 的 Azure Data Lake Storage 客户端库](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)。

2. 安装使用 [pip](https://pypi.org/project/pip/) 下载的库。

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. 将这些 import 语句添加到代码文件的顶部。

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>连接到帐户

你可以使用 Azure Active Directory (AD) 或帐户密钥进行连接。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

如果使用此方法，系统可确保用户帐户具有适当的 Azure 基于角色的访问控制 (Azure RBAC) 分配和 ACL 权限。 

下表显示了每个受支持的角色及其 ACL 设置功能。

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

1. 打开 Windows PowerShell 命令窗口，使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的指示进行操作。

   ```powershell
   Connect-AzAccount
   ```

2. 如果你的标识已关联到多个订阅，请将活动订阅设置为要在其中创建和管理目录的存储帐户的订阅。 在此示例中，请将 `<subscription-id>` 占位符值替换为你的订阅 ID。

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```
3. 获取存储帐户上下文。

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

#### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

如果使用此方法，系统不会检查 Azure RBAC 或 ACL 权限。 使用帐户密钥获取存储帐户上下文。

```powershell
$ctx = New-AzStorageContext -StorageAccountName "<storage-account-name>" -StorageAccountKey "<storage-account-key>"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 如果在本地使用 Azure CLI，请运行 login 命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

   否则，请在浏览器中打开 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然后输入终端中显示的授权代码。 然后，在浏览器中使用帐户凭据登录。

   若要详细了解不同的身份验证方法，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](./authorize-data-operations-cli.md)。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

> [!NOTE]
> 本文中提供的示例演示 Azure Active Directory (AD) 授权。 若要详细了解身份验证方法，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](./authorize-data-operations-cli.md)。

### <a name="net"></a>[.NET](#tab/dotnet)

若要使用本文中的代码片段，需创建一个表示存储帐户的 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 实例。

#### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

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

#### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

此方法是连接到帐户的最简单方法。 

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

> [!NOTE]
> 有关更多示例，请参阅[适用于 .NET 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)文档。

### <a name="java"></a>[Java](#tab/java)

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。 

#### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。 

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD) 进行连接

可以使用[适用于 Java 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)，通过 Azure AD 对应用程序进行身份验证。

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。  若要获取这些值，请参阅[从 Azure AD 获取用于请求客户端应用程序授权的令牌](../common/storage-auth-aad-app.md)。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> 有关更多示例，请参阅[适用于 Java 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)文档。

### <a name="python"></a>[Python](#tab/python)

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。 

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

可以使用[适用于 Python 的 Azure 标识客户端库](https://pypi.org/project/azure-identity/)，通过 Azure AD 对应用程序进行身份验证。

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。  若要获取这些值，请参阅[从 Azure AD 获取用于请求客户端应用程序授权的令牌](../common/storage-auth-aad-app.md)。 在该过程中，你必须为安全主体分配以下 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 角色之一。 

|角色|ACL 设置功能|
|--|--|
|[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|帐户中的所有目录和文件。|
|[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|仅限安全主体拥有的目录和文件。|

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 有关更多示例，请参阅[适用于 Python 的 Azure 标识客户端库](https://pypi.org/project/azure-identity/)文档。

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。 

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- 将 `storage_account_name` 占位符值替换为存储帐户的名称。

- 将 `storage_account_key` 占位符值替换为存储帐户访问密钥。

---

## <a name="set-an-acl-recursively"></a>以递归方式设置 ACL

设置 ACL 时，你将替换整个 ACL，包括其所有条目。 如果要更改安全主体的权限级别，或将新的安全主体添加到 ACL 而不影响其他现有项，则应改为更新 ACL。 若要更新 ACL 而不是替换它，请参阅本文的[以递归方式更新 ACL](#update-an-acl-recursively) 部分。  

如果选择设置 ACL，则必须为责任用户添加一个条目，为责任组添加一个条目，为所有其他用户添加一个条目。 若要详细了解责任用户、责任组和所有其他用户，请参阅[用户和标识](data-lake-storage-access-control.md#users-and-identities)。 

本部分包含有关如何设置 ACL 的示例。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 **Set-AzDataLakeGen2AclRecursive** cmdlet 以递归方式设置 ACL。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 这些条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> 如果要设置 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`。

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请查看 [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 参考文章。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az storage fs access set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) 命令以递归方式设置 ACL。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 这些条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 如果要设置 **默认** ACL 条目，请将前缀 `default:` 添加到每个条目。 例如，`default:user::rwx` 或 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`。 

### <a name="net"></a>[.NET](#tab/dotnet)

通过调用 **DataLakeDirectoryClient.SetAccessControlRecursiveAsync** 方法以递归方式设置 ACL。 向此方法传递 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的[列表](/dotnet/api/system.collections.generic.list-1)。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。 

如果要设置 **默认** ACL 条目，请将 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 属性设置为 **true**。 

此示例设置名为 `my-parent-directory` 的目录的 ACL。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否设置默认 ACL。 该参数用在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的构造函数中。 ACL 的条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

### <a name="java"></a>[Java](#tab/java)

通过调用 **DataLakeDirectoryClient.setAccessControlRecursive** 方法以递归方式设置 ACL。 向此方法传递 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的[列表](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)。 每个 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 定义一个 ACL 条目。 

如果要设置 **默认** ACL 条目，则可以调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法，并传入值 **true**。 

此示例设置名为 `my-parent-directory` 的目录的 ACL。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否设置默认 ACL。 该参数在每次调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法时使用。 ACL 的条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

通过调用 **DataLakeDirectoryClient.set_access_control_recursive** 方法以递归方式设置 ACL。

如果要设置 **默认** ACL 条目，请将字符串 `default:` 添加到每个 ACL 条目字符串的开头。 

此示例设置名为 `my-parent-directory` 的目录的 ACL。 

此方法接受一个名为 `is_default_scope` 的布尔参数，该参数指定是否设置默认 ACL。 如果该参数为 `True`，则 ACL 条目列表前面将带有字符串 `default:`。 

ACL 的条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目向对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户授予读取和执行权限。这些条目向所有者用户授予读取、写入和执行权限，只向负责人组授予读取和执行权限，不向所有其他人授予任何权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

若要查看通过指定批大小以递归方式成批处理 ACL 的示例，请参阅 python [示例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)。

---

## <a name="update-an-acl-recursively"></a>以递归方式更新 ACL

更新 ACL 时，你将修改 ACL 而非替换 ACL。 例如，你可以将一个新的安全主体添加到 ACL，而不影响 ACL 中列出的其他安全主体。  若要替换 ACL 而不是更新它，请参阅本文的[以递归方式设置 ACL](#set-an-acl-recursively) 部分。 

若要更新 ACL，请创建包含要更新的 ACL 条目的一个新的 ACL 对象，然后在“更新 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要更新的 ACL 条目。

本部分包含有关如何更新 ACL 的示例。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 **Update-AzDataLakeGen2AclRecursive** cmdlet 以递归方式更新 ACL。 

此示例以写入权限更新某个 ACL 条目。 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> 如果要更新 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`。

若要查看通过指定批大小以递归方式成批更新 ACL 的示例，请查看 [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) 参考文章。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az storage fs access update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) 命令以递归方式更新 ACL。 

此示例以写入权限更新某个 ACL 条目。 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 如果要更新 **默认** ACL 条目，请将前缀 `default:` 添加到每个条目。 例如 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`。

### <a name="net"></a>[.NET](#tab/dotnet)

通过调用 **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync** 方法以递归方式更新 ACL。  向此方法传递 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的[列表](/dotnet/api/system.collections.generic.list-1)。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。 

如果要更新 **默认** ACL 条目，请将 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 属性设置为 **true**。 

此示例以写入权限更新某个 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否更新默认 ACL。 该参数用在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的构造函数中。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

若要查看通过指定批大小以递归方式成批更新 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

### <a name="java"></a>[Java](#tab/java)

通过调用 **DataLakeDirectoryClient.updateAccessControlRecursive** 方法以递归方式更新 ACL。  向此方法传递 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的[列表](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)。 每个 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 定义一个 ACL 条目。 

如果要更新 **默认** ACL 条目，则可以调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法，并传入值 **true**。 

此示例以写入权限更新某个 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否更新默认 ACL。 该参数在调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法时使用。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

通过调用 **DataLakeDirectoryClient.update_access_control_recursive** 方法以递归方式更新 ACL。 如果要更新 **默认** ACL 条目，请将字符串 `default:` 添加到每个 ACL 条目字符串的开头。 

此示例以写入权限更新某个 ACL 条目。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 此方法接受一个名为 `is_default_scope` 的布尔参数，该参数指定是否更新默认 ACL。 如果该参数为 `True`，则更新后的 ACL 条目前面将带有字符串 `default:`。  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

若要查看通过指定批大小以递归方式成批处理 ACL 的示例，请参阅 python [示例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)。

---

## <a name="remove-acl-entries-recursively"></a>以递归方式删除 ACL 条目

你可以采用递归方式删除一个或多个 ACL 条目。 若要删除 ACL 条目，请为要删除的 ACL 条目创建一个新的 ACL 对象，然后在“删除 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要删除的 ACL 条目。 

本部分包含有关如何删除 ACL 的示例。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 **Remove-AzDataLakeGen2AclRecursive** cmdlet 删除 ACL 条目。 

此示例从容器的根目录中删除 ACL 条目。  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> 如果要删除 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`。

若要查看通过指定批大小以递归方式成批删除 ACL 的示例，请查看 [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) 参考文章。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az storage fs access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) 命令删除 ACL 条目。 

此示例从容器的根目录中删除 ACL 条目。  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 如果要删除 **默认** ACL 条目，请将前缀 `default:` 添加到每个条目。 例如 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。

### <a name="net"></a>[.NET](#tab/dotnet)

通过调用 **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync** 方法删除 ACL 条目。 向此方法传递 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的[列表](/dotnet/api/system.collections.generic.list-1)。 每个 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 定义一个 ACL 条目。 

如果要删除 **默认** ACL 条目，请将 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的 [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 属性设置为 **true**。 

此示例从名为 `my-parent-directory` 的目录的 ACL 中删除 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否删除默认 ACL 中的条目。 该参数用在 [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 的构造函数中。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

若要查看通过指定批大小以递归方式成批删除 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

### <a name="java"></a>[Java](#tab/java)

通过调用 **DataLakeDirectoryClient.removeAccessControlRecursive** 方法删除 ACL 条目。 向此方法传递 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的[列表](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)。 每个 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 定义一个 ACL 条目。 

如果要删除 **默认** ACL 条目，则可以调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法，并传入值 **true**。  

此示例从名为 `my-parent-directory` 的目录的 ACL 中删除 ACL 条目。 此方法接受一个名为 `isDefaultScope` 的布尔参数，该参数指定是否删除默认 ACL 中的条目。 该参数在调用 [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 的 **setDefaultScope** 方法时使用。

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

通过调用 **DataLakeDirectoryClient.remove_access_control_recursive** 方法删除 ACL 条目。 如果要删除 **默认** ACL 条目，请将字符串 `default:` 添加到 ACL 条目字符串的开头。 

此示例从名为 `my-parent-directory` 的目录的 ACL 中删除 ACL 条目。 此方法接受一个名为 `is_default_scope` 的布尔参数，该参数指定是否删除默认 ACL 中的条目。 如果该参数为 `True`，则更新后的 ACL 条目前面将带有字符串 `default:`。 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

若要查看通过指定批大小以递归方式成批处理 ACL 的示例，请参阅 python [示例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)。

---

## <a name="recover-from-failures"></a>从故障中恢复

你可能会遇到运行时错误或权限错误。 对于运行时错误，请从头开始重启此过程。 如果安全主体没有足够的权限修改要修改的目录层次结构中的目录或文件的 ACL，则会出现权限错误。 请解决权限问题，然后选择通过使用继续标记从故障点继续执行此过程，或者从头重启此过程。 如果希望从头开始重启，则无需使用继续标记。 你可以重新应用 ACL 条目，而不会产生任何负面影响。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

此示例将结果返回到变量，然后将失败的条目通过管道传递给已设置格式的表。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

根据表的输出，你可以修复所有权限错误，然后使用继续标记继续执行。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请查看 [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 参考文章。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

发生故障时，你可以通过将 `--continue-on-failure` 参数设置为 `false` 来返回一个延续令牌。 解决错误后，你可以从故障点继续执行该过程，方法是：再次运行命令，然后将 `--continuation` 参数设置为该延续令牌， 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

此示例在失败时返回一个继续标记。 应用程序可以在错误得到解决后再次调用此示例方法，并传入继续标记。 如果是第一次调用此示例方法，则应用程序可以为继续标记参数传入 `null` 值。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

### <a name="java"></a>[Java](#tab/java)

此示例在失败时返回一个继续标记。 应用程序可以在错误得到解决后再次调用此示例方法，并传入继续标记。 如果是第一次调用此示例方法，则应用程序可以为继续标记参数传入 `null` 值。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

此示例在失败时返回一个继续标记。 应用程序可以在错误得到解决后再次调用此示例方法，并传入继续标记。 如果是第一次调用此示例方法，则应用程序可以为继续标记参数传入 ``None`` 值。 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

若要查看通过指定批大小以递归方式成批处理 ACL 的示例，请参阅 python [示例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)。

---

如果你希望过程继续完成而不被权限错误中断，则可以指定它。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

此示例使用 `ContinueOnFailure` 参数，这样，即使操作遇到权限错误也会继续执行。 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请查看 [Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 参考文章。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要确保过程无中断地完成，请将 `--continue-on-failure` 参数设置为 `true`。 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

若要确保过程无中断地完成，请传入 **AccessControlChangedOptions** 对象，并将该对象的 **ContinueOnFailure** 属性设置为 ``true``。

此示例以递归方式设置 ACL 条目。 如果此代码遇到权限错误，它会记录该故障并继续执行。 此示例将故障数输出到控制台。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

若要查看通过指定批大小以递归方式成批设置 ACL 的示例，请参阅 .NET [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)。

### <a name="java"></a>[Java](#tab/java)

若要确保过程无中断地完成，请调用 [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 对象的 **setContinueOnFailure** 方法，并传入值 **true**。

此示例以递归方式设置 ACL 条目。 如果此代码遇到权限错误，它会记录该故障并继续执行。 此示例将故障数输出到控制台。 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

### <a name="python"></a>[Python](#tab/python)

若要确保过程无中断地完成，请不要将延续令牌传递给 **DataLakeDirectoryClient.set_access_control_recursive** 方法。

此示例以递归方式设置 ACL 条目。 如果此代码遇到权限错误，它会记录该故障并继续执行。 此示例将故障数输出到控制台。 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

若要查看通过指定批大小以递归方式成批处理 ACL 的示例，请参阅 python [示例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)。

---

## <a name="resources"></a>资源

本部分包含指向库和代码示例的链接。

#### <a name="libraries"></a>库

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure CLI](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>代码示例

- PowerShell：[自述文件](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D) | [示例](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI：[示例](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET：[自述文件](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0) | [示例](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python:[自述文件](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0) | [示例](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>最佳做法准则

本部分提供了有关以递归方式设置 ACL 的一些最佳做法指南。 

#### <a name="handling-runtime-errors"></a>处理运行时错误

发生运行时错误可能有许多原因（例如：中断或客户端连接问题）。 如果遇到运行时错误，请重启递归 ACL 过程。 可以将 ACL 重新应用于项，而不会造成负面影响。 

#### <a name="handling-permission-errors-403"></a>处理权限错误 (403)

如果在运行递归 ACL 过程时遇到访问控制异常，则表明 AD [安全主体](../../role-based-access-control/overview.md#security-principal)可能没有足够的权限将 ACL 应用于目录层次结构中的一个或多个子项。 发生权限错误时，此过程会停止，系统会提供一个继续标记。 请修复权限问题，然后使用继续标记来处理剩余的数据集。 已成功处理的目录和文件不需要再次处理。 你还可以选择重启递归 ACL 过程。 可以将 ACL 重新应用于项，而不会造成负面影响。 

#### <a name="credentials"></a>凭据 

建议你预配一个在目标存储帐户或容器范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色的 Azure AD 安全主体。 

#### <a name="performance"></a>性能 

为了减少延迟，建议你在与存储帐户位于同一区域中的 Azure 虚拟机 (VM) 中运行递归 ACL 过程。 

#### <a name="acl-limits"></a>ACL 限制

可应用于目录或文件的 ACL 的最大数目为 32 个访问 ACL 和 32 个默认 ACL。 有关详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)
- [已知问题](data-lake-storage-known-issues.md)
