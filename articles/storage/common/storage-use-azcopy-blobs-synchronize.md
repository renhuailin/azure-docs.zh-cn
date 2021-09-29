---
title: 使用 AzCopy v10 与 Azure Blob 存储同步 | Microsoft Docs
description: 本文包含一组 AzCopy 示例命令，可帮助你与 Azure Blob 存储同步。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 84c8c1e0eeb402185b7451ce953581d23b39c860
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624816"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>使用 AzCopy 与 Azure Blob 存储同步

可以使用 AzCopy v10 命令行实用程序将本地存储与 Azure Blob 存储同步。

可将本地文件系统的内容与 Blob 容器同步。 还可以将容器和虚拟目录相互同步。 同步是单向的。 换言之，需要选择这两个终结点中哪一个是源，哪一个是目标。 同步也使用服务器到服务器 API。 本部分中提供的示例也适用于具有分层命名空间的帐户。

> [!NOTE]
> 当前版本的 AzCopy 不会在其他源和目标之间同步（例如：文件存储或 Amazon Web Services (AWS) S3 桶）。

若要查看其他类型任务（如上传文件、下载 blob 或在帐户之间复制 Blob）的示例，请参阅本文的[后续步骤](#next-steps)部分中提供的链接。

## <a name="get-started"></a>入门

请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy，并了解如何提供存储服务的授权凭据。

> [!NOTE]
> 本文中的示例假定你已使用 Azure Active Directory (Azure AD) 提供了授权凭据。
>
> 如果你希望使用 SAS 令牌来授权访问 Blob 数据，可将该令牌追加到每个 AzCopy 命令中的资源 URL。 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

## <a name="guidelines"></a>指南

- [sync](storage-ref-azcopy-sync.md) 命令比较文件名和上次修改时间戳。 如果将 `--delete-destination` 可选标志设置为 `true` 或 `prompt` 值，当目标目录中的文件不在源目录中存在时，会删除这些文件。

- 如果将 `--delete-destination` 标志设置为 `true`，AzCopy 将删除文件且不提供提示。 若要在 AzCopy 删除文件之前显示提示，请将 `--delete-destination` 标志设置为 `prompt`。

- 如果计划将 `--delete-destination` 标志设置为 `prompt` 或 `false`，请考虑使用 [copy](storage-ref-azcopy-copy.md) 命令而不是 [sync](storage-ref-azcopy-sync.md) 命令，并将 `--overwrite` 参数设置为 `ifSourceNewer`。 [copy](storage-ref-azcopy-copy.md) 命令占用的内存较少，且费用较低，因为在移动文件之前，复制操作无需为源或目标编制索引。

- 为了防止意外删除，请务必在使用 `--delete-destination=prompt|true` 标志之前启用[软删除](../blobs/soft-delete-blob-overview.md)功能。

- 运行 sync 命令的计算机应具有准确的系统时钟，因为上次修改时间对于确定是否应传输文件至关重要。 如果你的系统有严重的时钟偏差，请避免修改目标中的文件的时间与计划运行 sync 命令的时间太过接近。

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用对本地文件系统所做的更改来更新容器

在这种情况下，容器是目标，本地文件系统是源。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**示例**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用对容器所做的更改来更新本地文件系统

在这种情况下，本地文件系统是目标，容器是源。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**示例**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>使用一个容器的更改来更新另一个容器

此命令中显示的第一个容器是源。 第二个是目标。 确保向每个源 URL 追加一个 SAS 令牌。

如果使用 Azure Active Directory (Azure AD) 提供授权凭据，则只能从目标 URL 中省略 SAS 令牌。 请确保已在目标帐户中设置了适当的角色。 请参阅[选项 1：使用 Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**示例**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>使用对另一个容器中的目录所做的更改来更新一个目录

此命令中显示的第一个目录是源。 第二个是目标。 确保向每个源 URL 追加一个 SAS 令牌。

如果使用 Azure Active Directory (Azure AD) 提供授权凭据，则只能从目标 URL 中省略 SAS 令牌。 请确保已在目标帐户中设置了适当的角色。 请参阅[选项 1：使用 Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**示例**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>使用可选标志同步

可以通过使用可选标志来调整同步操作。 下面是几个示例。

|方案|标志|
|---|---|
|指定下载时应验证 MD5 哈希的严格程度。|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
|基于模式排除文件。|**--exclude-path**|
|指定你希望与同步相关的日志条目达到何种详细程度。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|

有关标志的完整列表，请参阅[选项](storage-ref-azcopy-sync.md#options)。

> [!NOTE]
> 默认情况下，`--recursive` 标志设置为 `true`。 `--exclude-pattern` 和 `--include-pattern` 标志仅适用于文件名，而不适用于文件路径的其他部分。

## <a name="next-steps"></a>后续步骤

如需了解更多示例，请参阅以下文章：

- [示例：上载](storage-use-azcopy-blobs-upload.md)
- [示例：下载](storage-use-azcopy-blobs-download.md)
- [示例：在帐户之间复制](storage-use-azcopy-blobs-copy.md)
- [示例：Amazon S3 存储桶](storage-use-azcopy-s3.md)
- [示例：Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [示例：Azure 文件存储](storage-use-azcopy-files.md)
- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)

请参阅以下文章了解如何配置设置、优化性能和排查问题：

- [AzCopy 配置设置](storage-ref-azcopy-configuration-settings.md)
- [优化 AzCopy 的性能](storage-use-azcopy-optimize.md)
- [使用日志文件对 Azure 存储中的 AzCopy V10 问题进行故障排除](storage-use-azcopy-configure.md)
