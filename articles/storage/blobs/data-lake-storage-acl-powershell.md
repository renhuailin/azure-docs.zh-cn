---
title: 使用 PowerShell 管理 Azure Data Lake Storage Gen2 中的 ACL
description: 使用 PowerShell cmdlet 在启用了分层命名空间 (HNS) 的存储帐户中管理访问控制列表 (ACL)。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d0d7a6c1cb91daa4eb75f5e980355d866346c8e3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555520"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>使用 PowerShell 管理 Azure Data Lake Storage Gen2 中的 ACL

本文介绍如何使用 PowerShell 来获取、设置和更新目录和文件的访问控制列表。

ACL 继承已可用于在父目录下创建的新子项。 但是你也可以为父目录的现有子项以递归方式添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。

[引用](/powershell/module/Az.Storage/) | [递归 ACL 示例](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) | [提供反馈](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 有关详细信息，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 已启用分层命名空间 (HNS) 的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- Azure CLI 版本 `2.6.0` 或更高版本。

- 以下安全权限之一：

  - 一个预配的 Azure Active Directory (AD) [安全主体](../../role-based-access-control/overview.md#security-principal)，它在目标容器父资源组或订阅范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。

  - 计划将 ACL 设置应用到的目标容器或目录的拥有用户。 为了以递归方式设置 ACL，这包括目标容器或目录中的所有子项。

  - 存储帐户密钥。

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

1. 使用以下命令验证安装的 PowerShell 版本是否为 `5.1` 或以上。

   ```powershell
   echo $PSVersionTable.PSVersion.ToString()
   ```

   若要升级 PowerShell 版本，请参阅[升级现有的 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. 安装 Az.Storage 模块。

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>连接到帐户

选择希望命令如何获取存储帐户的授权。

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>选项 1：使用 Azure Active Directory (AD) 获取授权

> [!NOTE]
> 若要使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已为安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改这些权限的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](./data-lake-storage-access-control-model.md)。

如果使用此方法，系统可确保用户帐户具有适当的 Azure 基于角色的访问控制 (Azure RBAC) 分配和 ACL 权限。

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

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>选项 2：使用存储帐户密钥获取授权

如果使用此方法，系统不会检查 Azure RBAC 或 ACL 权限。 使用帐户密钥获取存储帐户上下文。

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>获取 ACL

使用 `Get-AzDataLakeGen2Item` cmdlet 获取目录或文件的 ACL。

此示例获取某个容器根目录的 ACL，然后将该 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

此示例获取某个 **目录** 的 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

此示例获取某个 **文件** 的 ACL，然后将 ACL 输出到控制台。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下图显示了获取目录 ACL 后的输出。

![获取目录的 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

在本示例中，负责人用户具有读取、写入和执行权限。 负责人组仅具有读取和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

## <a name="set-acls"></a>设置 ACL

设置 ACL 时，你将替换整个 ACL，包括其所有条目。 如果要更改安全主体的权限级别，或将新的安全主体添加到 ACL 而不影响其他现有项，则应改为更新 ACL。 若要更新 ACL 而不是替换它，请参阅本文的[更新 ACL](#update-acls) 部分。

如果选择设置 ACL，则必须为责任用户添加一个条目，为责任组添加一个条目，为所有其他用户添加一个条目。 若要详细了解责任用户、责任组和所有其他用户，请参阅[用户和标识](data-lake-storage-access-control.md#users-and-identities)。

本节介绍如何完成下列操作：

- 设置 ACL
- 以递归方式设置 ACL

### <a name="set-an-acl"></a>设置 ACL

使用 `set-AzDataLakeGen2ItemAclObject` cmdlet 为所有者用户、所有者组或其他用户创建 ACL。 然后使用 `Update-AzDataLakeGen2Item` cmdlet 提交 ACL。

此示例针对所有者用户、所有者组或其他用户的容器根目录设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

此示例针对所有者用户、所有者组或其他用户的 **目录** 设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> 如果要设置 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`。

此示例针对所有者用户、所有者组或其他用户的 **文件** 设置 ACL，然后将 ACL 输出到控制台。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw-
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> 要设置特定组或用户的 ACL，请使用其各自的对象 ID。 例如 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 或 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。

下图显示了设置文件 ACL 后的输出。

![获取文件的 ACL 输出](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

在本示例中，负责人用户和负责人组只有读取和写入权限。 所有其他用户都具有写入和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-acls-recursively"></a>以递归方式设置 ACL

使用 Set-AzDataLakeGen2AclRecursive cmdlet 以递归方式设置 ACL。

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

## <a name="update-acls"></a>更新 ACL

更新 ACL 时，你将修改 ACL 而非替换 ACL。 例如，你可以将一个新的安全主体添加到 ACL，而不影响 ACL 中列出的其他安全主体。 若要替换 ACL 而不是更新它，请参阅本文的 [设置 ACL](#set-acls) 部分。

若要更新 ACL，请创建包含要更新的 ACL 条目的一个新的 ACL 对象，然后在“更新 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要更新的 ACL 条目。

本节介绍如何完成下列操作：

- 更新 ACL
- 以递归方式更新 ACL

### <a name="update-an-acl"></a>更新 ACL

首先，获取 ACL。 然后，使用 `set-AzDataLakeGen2ItemAclObject` cmdlet 添加或更新 ACL 条目。 使用 `Update-AzDataLakeGen2Item` cmdlet 提交 ACL。

此示例创建或更新用户目录的 ACL。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> 如果要更新 **默认** ACL 条目，请在运行 **Set-AzDataLakeGen2ItemAclObject** 命令时使用 **-DefaultScope** 参数。 例如：`$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`。

### <a name="update-acls-recursively"></a>以递归方式更新 ACL

使用 Update-AzDataLakeGen2AclRecursive cmdlet 以递归方式更新 ACL。

此示例以写入权限更新某个 ACL 条目。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> 要设置特定组或用户的 ACL，请使用其各自的对象 ID。 例如 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 或 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。

若要查看通过指定批大小以递归方式成批更新 ACL 的示例，请查看 [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) 参考文章。

## <a name="remove-acl-entries"></a>删除 ACL 条目

本节介绍如何完成下列操作：

- 删除 ACL 条目
- 以递归方式删除 ACL 条目

### <a name="remove-an-acl-entry"></a>删除 ACL 条目

此示例从现有 ACL 中删除条目。

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>以递归方式删除 ACL 条目

你可以采用递归方式删除一个或多个 ACL 条目。 若要删除 ACL 条目，请为要删除的 ACL 条目创建一个新的 ACL 对象，然后在“删除 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要删除的 ACL 条目。

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

## <a name="recover-from-failures"></a>从故障中恢复

以递归方式修改 ACL 时，可能会遇到运行时或权限错误。

对于运行时错误，请从头开始重启此过程。 如果安全主体没有足够的权限修改要修改的目录层次结构中的目录或文件的 ACL，则会出现权限错误。 请解决权限问题，然后选择通过使用继续标记从故障点继续执行此过程，或者从头重启此过程。 如果希望从头开始重启，则无需使用继续标记。 你可以重新应用 ACL 条目，而不会产生任何负面影响。

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

如果你希望过程继续完成而不被权限错误中断，则可以指定它。

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

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>另请参阅

- [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [存储 PowerShell cmdlet](/powershell/module/az.storage)
- [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)
