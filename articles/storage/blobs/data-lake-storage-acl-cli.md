---
title: 使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的 ACL
description: 使用 Azure CLI 管理具有分层命名空间的存储帐户中的访问控制列表 (ACL)。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe26f8f61acbbf454cd2eb2928fdcab0f0bc7e47
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866965"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的 ACL

本文介绍如何使用 [Azure 命令行界面 (CLI)](/cli/azure/) 来获取、设置和更新目录和文件的访问控制列表。

ACL 继承已可用于在父目录下创建的新子项。 但是你也可以为父目录的现有子项以递归方式添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。

[参考](/cli/azure/storage/fs/access) | [示例](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 一个已启用分层命名空间的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- Azure CLI 版本 `2.14.0` 或更高版本。

- 以下安全权限之一：

  - 一个预配的 Azure Active Directory (Azure AD) [安全主体](../../role-based-access-control/overview.md#security-principal)，它在目标容器、父资源组或订阅范围中分配有[存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)角色。  

  - 计划将 ACL 设置应用到的目标容器或目录的拥有用户。 为了以递归方式设置 ACL，这包括目标容器或目录中的所有子项。
  
  - 存储帐户密钥。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>确保安装正确版本的 Azure CLI

1. 打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 使用以下命令验证安装的 Azure CLI 版本是否是 `2.14.0` 或更高版本。

   ```azurecli
    az --version
   ```

   如果 Azure CLI 版本低于 `2.14.0`，则安装更高版本。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="connect-to-the-account"></a>连接到帐户

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
> 本文中提供的示例演示 Azure Active Directory (Azure AD) 授权操作。 若要详细了解身份验证方法，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](./authorize-data-operations-cli.md)。

## <a name="get-acls"></a>获取 ACL

使用 [az storage fs access show](/cli/azure/storage/fs#az_storage_fs_show) 命令获取目录的 ACL。

下面的示例获取目录的 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 [az storage fs access show](/cli/azure/storage/fs#az_storage_fs_show) 命令获取文件的访问权限。 

下面的示例获取文件的 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下图显示了获取目录 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在本示例中，负责人用户具有读取、写入和执行权限。 负责人组仅具有读取和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

## <a name="set-acls"></a>设置 ACL

设置 ACL 时，你将替换整个 ACL，包括其所有条目。 如果要更改安全主体的权限级别，或将新的安全主体添加到 ACL 而不影响其他现有项，则应改为更新 ACL。 若要更新 ACL 而不是替换它，请参阅本文的[更新 ACL](#update-acls) 部分。  

如果选择设置 ACL，则必须为责任用户添加一个条目，为责任组添加一个条目，为所有其他用户添加一个条目。 若要详细了解责任用户、责任组和所有其他用户，请参阅[用户和标识](data-lake-storage-access-control.md#users-and-identities)。

本节介绍如何完成下列操作：

- 设置 ACL
- 以递归方式设置 ACL

### <a name="set-an-acl"></a>设置 ACL

使用 [az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set) 命令设置目录的 ACL。 

下面的示例在目录中为负责人用户、负责人组或其他用户设置 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

下面的示例在目录中为负责人用户、负责人组或其他用户设置默认 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 [az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set) 命令设置文件的 ACL。 

下面的示例在文件中为负责人用户、负责人组或其他用户设置 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 若要设置特定组或用户的 ACL，请使用其各自的对象 ID。 例如 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 或 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。

下图显示了设置文件 ACL 后的输出。

![获取 ACL 输出 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在本示例中，负责人用户和负责人组只有读取和写入权限。 所有其他用户都具有写入和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-acls-recursively"></a>以递归方式设置 ACL

使用 [az storage fs access set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) 命令以递归方式设置 ACL。

此示例设置名为 `my-parent-directory` 的目录的 ACL。 这些条目为所有者用户提供读取、写入和执行权限，仅为负责人组授予读取和执行权限，不为所有其他用户提供任何访问权限。 此示例中的最后一个 ACL 条目为对象 ID 为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”的特定用户提供读取和执行权限。

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 如果要设置 **默认** ACL 条目，请将前缀 `default:` 添加到每个条目。 例如，`default:user::rwx` 或 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`。

## <a name="update-acls"></a>更新 ACL

更新 ACL 时，你将修改 ACL 而非替换 ACL。 例如，你可以将一个新的安全主体添加到 ACL，而不影响 ACL 中列出的其他安全主体。  若要替换 ACL 而不是更新它，请参阅本文的 [设置 ACL](#set-acls) 部分。

若要更新 ACL，请创建包含要更新的 ACL 条目的一个新的 ACL 对象，然后在“更新 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要更新的 ACL 条目。

本节介绍如何完成下列操作：

- 更新 ACL
- 以递归方式更新 ACL

### <a name="update-an-acl"></a>更新 ACL

设置此权限的另一种方法是使用 [az storage fs access set](/cli/azure/storage/fs/access#az_storage_fs_access_set) 命令。 

通过将 `-permissions` 参数设置为 ACL 的简短形式，来更新目录或文件的 ACL。

下面的示例更新目录的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

下面的示例更新文件的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 若要更新特定组或用户的 ACL，请使用其各自的对象 ID。 例如 `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 或 `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。

另外，还可以通过将 `--owner` 或 `group` 参数设置为用户的实体 ID 或用户主体名称 (UPN)，更新目录或文件的负责人用户和组。

下面的示例更改目录所有者。

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

下面的示例更改文件所有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>以递归方式更新 ACL

使用 [az storage fs access update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) 命令以递归方式更新 ACL。

此示例以写入权限更新某个 ACL 条目。

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 如果要更新 **默认** ACL 条目，请将前缀 `default:` 添加到每个条目。 例如 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`。

## <a name="remove-acl-entries-recursively"></a>以递归方式删除 ACL 条目

你可以采用递归方式删除一个或多个 ACL 条目。 若要删除 ACL 条目，请为要删除的 ACL 条目创建一个新的 ACL 对象，然后在“删除 ACL”操作中使用该对象。 不要获取现有 ACL，只需要提供要删除的 ACL 条目。

使用 [az storage fs access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) 命令删除 ACL 条目。

此示例从容器的根目录中删除 ACL 条目。  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> 如果要删除 **默认** ACL 条目，请将前缀 `default:` 添加到每个条目。 例如 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。

## <a name="recover-from-failures"></a>从故障中恢复

以递归方式修改 ACL 时，可能会遇到运行时或权限错误。 对于运行时错误，请从头开始重启此过程。 如果安全主体没有足够的权限修改要修改的目录层次结构中的目录或文件的 ACL，则会出现权限错误。 请解决权限问题，然后选择通过使用继续标记从故障点继续执行此过程，或者从头重启此过程。 如果希望从头开始重启，则无需使用继续标记。 你可以重新应用 ACL 条目，而不会产生任何负面影响。

发生故障时，你可以通过将 `--continue-on-failure` 参数设置为 `false` 来返回一个延续令牌。 解决错误后，你可以从故障点继续执行该过程，方法是：再次运行命令，然后将 `--continuation` 参数设置为该延续令牌，

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

如果你希望过程继续完成而不被权限错误中断，则可以指定它。

若要确保过程无中断地完成，请将 `--continue-on-failure` 参数设置为 `true`。

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>另请参阅

- [示例](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)
- [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure Data Lake Storage Gen2 中的访问控制模型](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2 中的访问控制列表 (ACL)](data-lake-storage-access-control.md)