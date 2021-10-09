---
title: 使用 Azure CLI 管理数据 (Azure Data Lake Storage Gen2)
description: 使用 Azure CLI 在具有分层命名空间的存储帐户中管理目录和文件。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8dec2e939c6f39169d4c8fbbdfbcdf08d4d42599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651572"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的目录和文件

本文介绍了如何使用 [Azure 命令行接口 (CLI)](/cli/azure/) 在具有分层命名空间的存储帐户中创建和管理目录与文件。

若要了解如何获取、设置和更新目录与文件的访问控制列表 (ACL)，请参阅[使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-cli.md)。

[示例](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [提高反馈](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 有关详细信息，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

- 一个已启用分层命名空间的存储帐户。 按[这些](create-data-lake-storage-account.md)说明创建一个。

- Azure CLI 版本 `2.6.0` 或更高版本。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>确保安装正确版本的 Azure CLI

1. 打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 使用以下命令验证安装的 Azure CLI 版本是否是 `2.6.0` 或更高版本。

   ```azurecli
    az --version
   ```

   如果 Azure CLI 版本低于 `2.6.0`，则安装更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

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

## <a name="create-a-container"></a>创建容器

容器充当文件的文件系统。 可以使用 `az storage fs create` 命令创建文件系统。

此示例创建一个名为 `my-file-system` 的容器。

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>显示容器属性

可使用 `az storage fs show` 命令将容器的属性输出到控制台。

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>列出容器内容

使用 `az storage fs file list` 命令列出目录内容。

此示例列出名为 `my-file-system` 的容器的内容。

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>删除容器

使用 `az storage fs delete` 命令删除容器。

此示例删除一个名为 `my-file-system` 的容器。

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>创建目录

使用 `az storage fs directory create` 命令创建目录引用。

此示例将名为 `my-directory` 的目录添加到名为 `my-file-system` 的容器中，该容器位于名为 `mystorageaccount` 的帐户下。

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>显示目录属性

可以使用 `az storage fs directory show` 命令将目录属性打印到控制台。

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

使用 `az storage fs directory move` 命令重命名或移动目录。

此示例在同一容器中将目录的名称 `my-directory` 重命名为 `my-new-directory`。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

此示例将目录移到名为 `my-second-file-system` 的容器。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>删除目录

使用 `az storage fs directory delete` 命令删除目录。

此示例删除名为 `my-directory` 的目录。

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login
```

## <a name="check-if-a-directory-exists"></a>检查目录是否存在

使用 `az storage fs directory exists` 命令确定容器中是否存在特定的目录。

此示例显示 `my-file-system` 容器中是否存在名为 `my-directory` 的目录。

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="download-from-a-directory"></a>从目录下载

使用 `az storage fs file download` 命令从目录下载文件。

此示例从名为 `my-directory` 的目录中下载名为 `upload.txt` 的文件。

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>列出目录内容

使用 `az storage fs file list` 命令列出目录内容。

此示例列出名为 `my-directory` 的目录的内容，该目录位于名为 `mystorageaccount` 的存储帐户的 `my-file-system` 容器中。

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

使用 `az storage fs file upload` 命令将文件上传到目录。

此示例将名为 `upload.txt` 的文件上传到名为 `my-directory` 的目录。

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>显示文件属性

可以使用 `az storage fs file show` 命令将文件属性打印到控制台。

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>重命名或移动文件

使用 `az storage fs file move` 命令重命名或移动文件。

下面的示例将文件从名称 `my-file.txt` 重命名为名称 `my-file-renamed.txt`。

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>删除文件

使用 `az storage fs file delete` 命令删除文件。

下面的示例删除名为 `my-file.txt` 的文件

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>另请参阅

- [示例](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)
- [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的 ACL](data-lake-storage-acl-cli.md)
