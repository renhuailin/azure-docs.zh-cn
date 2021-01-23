---
title: 用于文件和 ACL 的 Azure Data Lake Storage Gen2 Python SDK
description: 在启用了分层命名空间 (HNS) 的存储帐户中使用 Python 来管理目录和文件以及目录访问控制列表 (ACL)。
author: normesta
ms.service: storage
ms.date: 01/22/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 5036930c7bb49578582fbc1b347b11518579b53e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740612"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Python 管理 Azure Data Lake Storage Gen2 中的目录、文件和 ACL

本文介绍如何使用 Python 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录、文件与权限。 

[包 (Python 包索引) ](https://pypi.org/project/azure-storage-file-datalake/)  | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  | [API 参考](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  | [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  | [提供反馈](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](../common/storage-account-create.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

使用 [pip](https://pypi.org/project/pip/) 安装适用于 Python 的 Azure Data Lake Storage 客户端库。

```
pip install azure-storage-file-datalake
```

将这些 import 语句添加到代码文件的顶部。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。 

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。 

此示例使用帐户密钥创建 DataLakeServiceClient 实例。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- 将 `storage_account_name` 占位符值替换为存储帐户的名称。

- 将 `storage_account_key` 占位符值替换为存储帐户访问密钥。

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory (AD) 进行连接

可以使用[适用于 Python 的 Azure 标识客户端库](https://pypi.org/project/azure-identity/)，通过 Azure AD 对应用程序进行身份验证。

此示例使用客户端 ID、客户端密码和租户 ID 创建 DataLakeServiceClient 实例。  若要获取这些值，请参阅[从 Azure AD 获取用于请求客户端应用程序授权的令牌](../common/storage-auth-aad-app.md)。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 有关更多示例，请参阅[适用于 Python 的 Azure 标识客户端库](https://pypi.org/project/azure-identity/)文档。

## <a name="create-a-container"></a>创建容器

容器充当文件的文件系统。 可以通过调用 **FileSystemDataLakeServiceClient.create_file_system** 方法来创建一个。

此示例创建一个名为 `my-file-system` 的容器。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>创建目录

通过调用 **FileSystemClient.create_directory** 方法来创建目录引用。

此示例将名为 `my-directory` 的目录添加到容器中。 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用 **DataLakeDirectoryClient.rename_directory** 方法来重命名或移动目录。 以参数形式传递所需目录的路径。 

此示例将子目录重命名为 `my-subdirectory-renamed` 的名称。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>删除目录

通过调用 **DataLakeDirectoryClient.delete_directory** 方法来删除目录。

此示例删除名为 `my-directory` 的目录。  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录 

首先，通过创建 **DataLakeFileClient** 类的实例，在目标目录中创建文件引用。 通过调用 **DataLakeFileClient.append_data** 方法上传文件。 确保通过调用 **DataLakeFileClient.flush_data** 方法完成上传。

此示例将文本文件上传到名为 `my-directory` 的目录。   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> 如果文件很大，则代码必须多次调用 DataLakeFileClient.append_data 方法。 请考虑改用 DataLakeFileClient.upload_data 方法。 这样就可以在单个调用中上传整个文件。 

## <a name="upload-a-large-file-to-a-directory"></a>将大型文件上传到目录

使用 DataLakeFileClient.upload_data 方法上传大型文件，无需多次调用 DataLakeFileClient.append_data 方法 。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>从目录下载 

打开用于写入的本地文件。 然后，创建一个 **DataLakeFileClient** 实例，该实例表示要下载的文件。 调用 **DataLakeFileClient.read_file**，以便从文件读取字节，然后将这些字节写入本地文件。 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>列出目录内容

通过调用 **FileSystemClient.get_paths** 方法列出目录内容，然后枚举结果。

此示例输出名为 `my-directory` 的目录中的每个子目录和文件的路径。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>管理访问控制列表 (ACL)

可以获取、设置和更新目录与文件的访问权限。

> [!NOTE]
> 若要使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已为安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

### <a name="manage-directory-acls"></a>管理目录 ACL

通过调用 **DataLakeDirectoryClient.get_access_control** 方法获取目录的访问控制列表 (ACL)，并通过调用 **DataLakeDirectoryClient.set_access_control** 方法来设置 ACL。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

此示例获取并设置名为 `my-directory` 的目录的 ACL。 字符串 `rwxr-xrw-` 为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取和写入权限。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

还可以获取和设置容器根目录的 ACL。 若要获取根目录，请调用 **FileSystemClient._get_root_directory_client** 方法。

### <a name="manage-file-permissions"></a>管理文件权限

通过调用 **DataLakeFileClient.get_access_control** 方法获取文件的访问控制列表 (ACL)，并通过调用 **DataLakeFileClient.set_access_control** 方法来设置 ACL。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](./data-lake-storage-access-control.md)。

此示例获取并设置名为 `my-file.txt` 的文件的 ACL。 字符串 `rwxr-xrw-` 为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取和写入权限。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>以递归方式设置 ACL

你可以为父目录的现有子项以递归方式添加、更新和删除 ACL，而不必为每个子项单独进行这些更改。 有关详细信息，请参阅[以递归方式为 Azure Data Lake Storage Gen2 设置访问控制列表 (ACL)](recursive-access-control-lists.md)。

## <a name="see-also"></a>另请参阅

* [API 参考文档](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [包（Python 包索引）](https://pypi.org/project/azure-storage-file-datalake/)
* [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供反馈](https://github.com/Azure/azure-sdk-for-python/issues)