---
title: 使用 AzCopy v10 与 Azure Blob 存储同步 |Microsoft Docs
description: 本文包含可帮助您与 Azure Blob 存储进行同步的 AzCopy 示例命令的集合。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f8210428e772241134b57ac4fccb5b1549e04483
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617266"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>使用 AzCopy v10 与 Azure Blob 存储同步

可以使用 AzCopy v10 命令行实用程序将本地存储与 Azure Blob 存储同步。 

可将本地文件系统的内容与 Blob 容器同步。 还可以将容器和虚拟目录相互同步。 同步是一种方法。 换言之，需要选择这两个终结点中哪一个是源，哪一个是目标。 同步也使用服务器到服务器 API。 本部分中提供的示例也适用于具有分层命名空间的帐户。 

> [!NOTE]
> 当前版本的 AzCopy 不会在其他源和目标之间同步（例如：文件存储或 Amazon Web Services (AWS) S3 桶）。

若要查看其他类型的任务（例如上载文件、下载 blob 或在帐户之间复制 blob）的示例，请参阅本文 [后续步骤](#next-steps) 部分中提供的链接。

## <a name="get-started"></a>入门

请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy，并了解如何提供存储服务的授权凭据。

> [!NOTE] 
> 本文中的示例假定你已通过使用 Azure Active Directory (Azure AD) 提供了授权凭据。
>
> 如果你希望使用 SAS 令牌来授权访问 Blob 数据，可将该令牌追加到每个 AzCopy 命令中的资源 URL。 例如： `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'` ken> ""。

## <a name="guidelines"></a>指南

- [sync](storage-ref-azcopy-sync.md) 命令比较文件名和上次修改时间戳。 如果将 `--delete-destination` 可选标志设置为 `true` 或 `prompt` 值，当目标目录中的文件不在源目录中存在时，会删除这些文件。

- 如果将标志设置 `--delete-destination` 为 `true` ，则 AzCopy 将删除文件而不提供提示。 若要在 AzCopy 删除文件之前显示提示，请将 `--delete-destination` 标志设置为 `prompt`。

- 为了防止意外删除，请务必在使用 `--delete-destination=prompt|true` 标志之前启用[软删除](../blobs/soft-delete-blob-overview.md)功能。

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用对本地文件系统所做的更改来更新容器

在这种情况下，容器是目标，本地文件系统是源。 

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用对容器所做的更改来更新本地文件系统

在这种情况下，本地文件系统是目标，容器是源。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **示例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>使用一个容器的更改来更新另一个容器

此命令中显示的第一个容器是源。 第二个是目标。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>使用对其他容器中的目录的更改更新目录

此命令中显示的第一个目录是源。 第二个是目标。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>与可选标志同步

可以通过使用可选标志来调整同步操作。 下面是几个示例。

|方案|标志|
|---|---|
|指定下载时应验证 MD5 哈希的严格程度。|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
|基于模式排除文件。|**--exclude-path**|
|指定你希望与同步相关的日志条目达到何种详细程度。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|

有关完整列表，请参阅[选项](storage-ref-azcopy-sync.md#options)。

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [示例：上载](storage-use-azcopy-blobs-upload.md)
- [示例：下载](storage-use-azcopy-blobs-download.md)
- [示例：在帐户之间复制](storage-use-azcopy-blobs-copy.md)
- [示例：Amazon S3 存储桶](storage-use-azcopy-s3.md)
- [示例： Azure 文件](storage-use-azcopy-files.md)
- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)