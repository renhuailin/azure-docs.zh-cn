---
title: 使用 AzCopy v10 从 Azure Blob 存储下载 blob | Microsoft Docs
description: 本文包含一组 AzCopy 示例命令，可帮助从 Azure Blob 存储下载 blob。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 9ee927ff858c1242ddab687e28707049a27a23bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624835"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy"></a>使用 AzCopy 从 Azure Blob 存储下载 blob

你可以使用 AzCopy v10 命令行实用程序从 Blob 存储下载 Blob 和目录。

若要查看其他类型任务（如上传文件、与 Blob 存储同步或在帐户之间复制 Blob）的示例，请参阅本文的[下一步](#next-steps)部分中提供的链接。

## <a name="get-started"></a>入门

请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy，并了解如何提供存储服务的授权凭据。

> [!NOTE]
> 本文中的示例假定你已使用 Azure Active Directory (Azure AD) 提供了授权凭据。
>
> 如果你希望使用 SAS 令牌来授权访问 Blob 数据，可将该令牌追加到每个 AzCopy 命令中的资源 URL。 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

## <a name="download-a-blob"></a>下载 blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令下载 Blob。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

**示例（分层命名空间）**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> 如果 Blob 的 `Content-md5` 属性值包含哈希，AzCopy 将计算已下载的数据的 MD5 哈希，并验证存储在该 Blob 的 `Content-md5` 属性中的 MD5 哈希是否与计算出的哈希相匹配。 如果这些值不匹配，除非通过将 `--check-md5=NoCheck` 或 `--check-md5=LogOnly` 追加到 copy 命令来重写此行为，否则下载将会失败。

## <a name="download-a-directory"></a>下载目录

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令下载目录。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

**示例（分层命名空间）**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

此示例将生成名为 `C:\myDirectory\myBlobDirectory` 的目录，其中包含所有已下载的 Blob。

## <a name="download-directory-contents"></a>下载目录内容

可以使用通配符 (*) 下载目录的内容，而无需复制包含的目录本身。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

> [!NOTE]
> 目前，只有不使用分层命名空间的帐户才支持此方案。

**语法**

`azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'
```

追加 `--recursive` 标志可以下载所有子目录中的文件。

## <a name="download-specific-blobs"></a>下载特定的 Blob

可以使用完整的文件名、包含通配符 (*) 的部分名称或者日期和时间来下载特定 Blob。

> [!TIP]
> 这些示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

#### <a name="specify-multiple-complete-blob-names"></a>指定多个完整 Blob 名

结合 `--include-path` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 使用分号 (`;`) 分隔各个 Blob 名。

**语法**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

**示例（分层命名空间）**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive
```

在此示例中，AzCopy 将传输 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目录和 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` 文件。 包含 `--recursive` 选项可传输 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目录中的所有 Blob。

还可使用 `--exclude-path` 选项来排除 Blob。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

结合 `--include-pattern` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 指定包含通配符的部分名称。 使用分号 (`;`) 分隔名称。

**语法**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

**示例（分层命名空间）**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

还可使用 `--exclude-pattern` 选项来排除 Blob。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于 Blob 名，而不适用于路径。  若要复制目录树中存在的所有文本文件 (Blob)，请使用 `–recursive` 选项获取整个目录树，然后使用 `–include-pattern` 并指定 `*.txt` 来获取所有文本文件。

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>下载在某个日期和时间之前或之后修改的 Blob

将 [azcopy copy](storage-ref-azcopy-copy.md) 命令与 `--include-before` 或 `--include-after` 选项结合使用。 以 ISO-8601 格式指定日期和时间（例如 `2020-08-19T15:04:00Z`）。

以下示例下载在指定日期或之后修改的文件。

**语法**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**示例（分层命名空间）**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

如需详细的参考，请查看 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="download-previous-versions-of-a-blob"></a>下载 blob 的早期版本

如果已启用 [blob 版本控制](../blobs/versioning-enable.md)，则可以下载 blob 的一个或多个早期版本。

首先，创建一个包含[版本 ID](../blobs/versioning-overview.md) 列表的文本文件。 每个版本 ID 必须出现在单独的行中。 例如：

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

然后，结合 `--list-of-versions` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 指定包含版本列表的文本文件的位置（例如：`D:\\list-of-versions.txt`）。

#### <a name="download-a-blob-snapshot"></a>下载 Blob 快照

可通过引用 Blob 快照的 **DateTime** 值来下载 [Blob 快照](../blobs/snapshots-overview.md)。

**语法**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**示例（分层命名空间）**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> 如果使用 SAS 令牌授权访问 Blob 数据，则在 SAS 令牌之后追加快照 DateTime。 例如：`'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`。

## <a name="download-with-optional-flags"></a>下载可选标志

可以通过使用可选标志来调整下载操作。 下面是几个示例。

|方案|标志|
|---|---|
|自动解压缩文件。|**--decompress**|
|指定你希望与复制相关的日志条目达到何种详细程度。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
|指定是否以及如何覆盖目标位置的冲突文件和 Blob。|**--overwrite**=\[true\|false\|ifSourceNewer\|prompt\]|

有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

## <a name="next-steps"></a>后续步骤

如需了解更多示例，请参阅以下文章：

- [示例：上载](storage-use-azcopy-blobs-upload.md)
- [示例：在帐户之间复制](storage-use-azcopy-blobs-copy.md)
- [示例：同步](storage-use-azcopy-blobs-synchronize.md)
- [示例：Amazon S3 存储桶](storage-use-azcopy-s3.md)
- [示例：Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [示例：Azure 文件存储](storage-use-azcopy-files.md)
- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)

请参阅以下文章了解如何配置设置、优化性能和排查问题：

- [AzCopy 配置设置](storage-ref-azcopy-configuration-settings.md)
- [优化 AzCopy 的性能](storage-use-azcopy-optimize.md)
- [使用日志文件对 Azure 存储中的 AzCopy V10 问题进行故障排除](storage-use-azcopy-configure.md)
