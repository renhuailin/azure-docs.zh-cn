---
title: 使用 AzCopy v10 向/从 Azure 文件存储传输数据 | Microsoft Docs
description: 使用 AzCopy 和文件存储传输数据。 AzCopy 是一个命令行工具，用于向/从存储帐户复制 Blob 或文件。 将 AzCopy 与 Azure 文件存储配合使用。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 95a09a19a4bf569680ba3c16214bc10c6be37cd9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616410"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>使用 AzCopy 和文件存储传输数据

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制文件。 本文包含适用于 Azure 文件存储的示例命令。

在开始之前，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy 并熟悉该工具。

> [!TIP]
> 本文中的示例将路径参数括在单引号 ('') 中。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

## <a name="create-file-shares"></a>创建文件共享

可以使用 [azcopy make](storage-ref-azcopy-make.md) 命令创建文件共享。 本部分中的示例将创建名为 `myfileshare` 的文件共享。

**语法**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**示例**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

有关详细参考文档，请参阅 [azcopy make](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上传文件

可以使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令从本地计算机上传文件和目录。

本部分包含以下示例：

> [!div class="checklist"]
> - 上传文件
> - 上传目录
> - 上传目录的内容
> - 上传特定的文件

> [!TIP]
> 可以通过使用可选标志来调整上传操作。 下面是几个示例。  
>
> |方案|标志|
> |---|---|
> |将访问控制列表 (ACL) 与文件一起复制。|**--preserve-smb-permissions**=\[true\|false\]|
> |将 SMB 属性信息与文件一起复制。|**--preserve-smb-info**=\[true\|false\]|
>
> 有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

> [!NOTE]
> AzCopy 不会自动计算和存储文件的 MD5 哈希代码。 如果你希望 AzCopy 执行此操作，请将 `--put-md5` 标志追加到每个 copy 命令。 这样，在下载文件后，AzCopy 将计算已下载的数据的 MD5 哈希，并验证存储在该文件的 `Content-md5` 属性中的 MD5 哈希是否与计算出的哈希相匹配。

### <a name="upload-a-file"></a>上传文件

**语法**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**示例**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

还可以在文件路径或文件名中的任意位置使用通配符 (*) 来上传文件。 例如：`'C:\myDirectory\*.txt'` 或 `C:\my*\*.txt`。

### <a name="upload-a-directory"></a>上传目录

此示例将某个目录（以及该目录中的所有文件）复制到文件共享。 结果是该文件共享中出现一个同名的目录。

**语法**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**示例**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

若要复制到文件共享中的某个目录，只需在命令字符串中指定该目录的名称。

**示例**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

如果指定的目录名称在文件共享中不存在，AzCopy 将以该名称创建一个新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

可以使用通配符 (*) 上传目录的内容，而无需复制包含的目录本身。

**语法**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>'`

**示例**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --preserve-smb-permissions=true --preserve-smb-info=true
```

> [!NOTE]
> 追加 `--recursive` 标志可以上传所有子目录中的文件。

### <a name="upload-specific-files"></a>上传特定的文件

可以使用完整的文件名、包含通配符 (*) 的部分名称或者日期和时间来上传特定文件。

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整文件名

结合 `--include-path` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 使用分号 (`;`) 分隔各个文件名。

**语法**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**示例**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

在此示例中，AzCopy 将传输 `C:\myDirectory\photos` 目录和 `C:\myDirectory\documents\myFile.txt` 文件。 需要包含 `--recursive` 选项才能传输 `C:\myDirectory\photos` 目录中的所有文件。

还可以使用 `--exclude-path` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

结合 `--include-pattern` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 指定包含通配符的部分名称。 使用分号 (`;`) 分隔名称。

**语法**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**示例**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*' --preserve-smb-permissions=true --preserve-smb-info=true
```

还可以使用 `--exclude-pattern` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  若要复制目录树中存在的所有文本文件，请使用 `--recursive` 选项获取整个目录树，然后使用 `--include-pattern` 并指定 `*.txt` 来获取所有文本文件。

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>上传在某个日期和时间之后修改的文件

结合 `--include-after` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 以 ISO 8601 格式指定日期和时间（例如 `2020-08-19T15:04:00Z`）。

**语法**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**示例**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z' --preserve-smb-permissions=true --preserve-smb-info=true
```

如需详细的参考，请查看 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

## <a name="download-files"></a>下载文件

可以使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将文件、目录和文件共享下载到本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> - 下载文件
> - 下载目录
> - 下载目录的内容
> - 下载特定的文件

> [!TIP]
> 可以通过使用可选标志来调整下载操作。 下面是几个示例。
>
> |方案|标志|
> |---|---|
> |将访问控制列表 (ACL) 与文件一起复制。|**--preserve-smb-permissions**=\[true\|false\]|
> |将 SMB 属性信息与文件一起复制。|**--preserve-smb-info**=\[true\|false\]|
> |自动解压缩文件。|**--decompress**|
>
> 有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

> [!NOTE]
> 如果文件的 `Content-md5` 属性值包含哈希，AzCopy 将计算已下载的数据的 MD5 哈希，并验证存储在该文件的 `Content-md5` 属性中的 MD5 哈希是否与计算出的哈希相匹配。 如果这些值不匹配，除非通过将 `--check-md5=NoCheck` 或 `--check-md5=LogOnly` 追加到 copy 命令来重写此行为，否则下载将会失败。

### <a name="download-a-file"></a>下载文件

**语法**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="download-a-directory"></a>下载目录

**语法**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

此示例将生成名为 `C:\myDirectory\myFileShareDirectory` 的目录，其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

可以使用通配符 (*) 下载目录的内容，而无需复制包含的目录本身。

**语法**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory' --preserve-smb-permissions=true --preserve-smb-info=true
```

> [!NOTE]
> 追加 `--recursive` 标志可以下载所有子目录中的文件。

### <a name="download-specific-files"></a>下载特定的文件

可以使用完整的文件名、包含通配符 (*) 的部分名称或者日期和时间来下载特定文件。

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整文件名

结合 `--include-path` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 使用分号 (`;`) 分隔各个文件名。

**语法**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

在此示例中，AzCopy 将传输 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 目录和 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` 文件。 包含 `--recursive` 选项才能传输 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 目录中的所有文件。

还可以使用 `--exclude-path` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

结合 `--include-pattern` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 指定包含通配符的部分名称。 使用分号 (`;`) 分隔名称。

**语法**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*' --preserve-smb-permissions=true --preserve-smb-info=true
```

还可以使用 `--exclude-pattern` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  若要复制目录树中存在的所有文本文件，请使用 `--recursive` 选项获取整个目录树，然后使用 `--include-pattern` 并指定 `*.txt` 来获取所有文本文件。

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>下载在某个日期和时间之后修改的文件

结合 `--include-after` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 以 ISO-8601 格式指定日期和时间（例如 `2020-08-19T15:04:00Z`）。

**语法**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**示例**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z' --preserve-smb-permissions=true --preserve-smb-info=true
```

如需详细的参考，请查看 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="download-from-a-share-snapshot"></a>从共享快照下载

可以通过引用共享快照的 DateTime 值来下载特定版本的文件或目录。 若要详细了解共享快照，请参阅 [Azure 文件存储的共享快照概述](../files/storage-snapshots-files.md)。

**语法**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**示例（下载文件）**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt' --preserve-smb-permissions=true --preserve-smb-info=true
```

**示例（下载目录）**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

## <a name="copy-files-between-storage-accounts"></a>在存储帐户之间复制文件

可以使用 AzCopy 将文件复制到其他存储帐户。 复制操作是同步的，因此，当命令返回时，表示已复制所有文件。

AzCopy 使用[服务器到服务器](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url)，因此，数据会直接在存储服务器之间复制。 这些复制操作不会占用计算机的网络带宽。 可以通过设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量的值来提高这些操作的吞吐量。 若要了解详细信息，请参阅[提高并发性](storage-use-azcopy-optimize.md#increase-concurrency)。

还可以通过引用共享快照的 DateTime 值来复制文件的特定版本。 若要详细了解共享快照，请参阅 [Azure 文件存储的共享快照概述](../files/storage-snapshots-files.md)。

本部分包含以下示例：

> [!div class="checklist"]
> - 将文件复制到另一个存储帐户
> - 将目录复制到另一个存储帐户
> - 将文件共享复制到另一个存储帐户
> - 将所有文件共享、目录和文件复制到另一个存储帐户

> [!TIP]
> 可以通过使用可选标志来调整复制操作。 下面是几个示例。
>
> |方案|标志|
> |---|---|
> |将访问控制列表 (ACL) 与文件一起复制。|**--preserve-smb-permissions**=\[true\|false\]|
> |将 SMB 属性信息与文件一起复制。|**--preserve-smb-info**=\[true\|false\]|
>
> 有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

### <a name="copy-a-file-to-another-storage-account"></a>将文件复制到另一个存储帐户

**语法**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**示例**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

**示例（共享快照）**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-a-directory-to-another-storage-account"></a>将目录复制到另一个存储帐户

**语法**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**示例**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

**示例（共享快照）**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-a-file-share-to-another-storage-account"></a>将文件共享复制到另一个存储帐户

**语法**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**示例**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D --preserve-smb-permissions=true --preserve-smb-info=true
```

**示例（共享快照）**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>将所有文件共享、目录和文件复制到另一个存储帐户

**语法**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**示例**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

**示例（共享快照）**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

## <a name="synchronize-files"></a>同步文件

可以将本地文件系统的内容与文件共享同步，也可以将文件共享的内容与其他文件共享同步。 还可将一个文件共享中的目录内容，与位于另一个文件共享中的目录内容同步。 同步是单向的。 换言之，需要选择这两个终结点中哪一个是源，哪一个是目标。 同步也使用服务器到服务器 API。

> [!NOTE]
> 目前，通过 blob 终结点启用了分层命名空间的帐户支持此方案。

### <a name="guidelines"></a>指南

- [sync](storage-ref-azcopy-sync.md) 命令比较文件名和上次修改时间戳。 如果将 `--delete-destination` 可选标志设置为 `true` 或 `prompt` 值，当目标目录中的文件不在源目录中存在时，会删除这些文件。

- 如果将 `--delete-destination` 标志设置为 `true`，AzCopy 将删除文件且不提供提示。 若要在 AzCopy 删除文件之前显示提示，请将 `--delete-destination` 标志设置为 `prompt`。

- 如果计划将 `--delete-destination` 标志设置为 `prompt` 或 `false`，请考虑使用 [copy](storage-ref-azcopy-copy.md) 命令而不是 [sync](storage-ref-azcopy-sync.md) 命令，并将 `--overwrite` 参数设置为 `ifSourceNewer`。 [copy](storage-ref-azcopy-copy.md) 命令占用的内存较少，且费用较低，因为在移动文件之前，复制操作无需为源或目标编制索引。

- 运行 sync 命令的计算机应具有准确的系统时钟，因为上次修改时间对于确定是否应传输文件至关重要。 如果你的系统有严重的时钟偏差，请避免修改目标中的文件的时间与计划运行 sync 命令的时间太过接近。

> [!TIP]
> 可以通过使用可选标志来调整同步操作。 下面是几个示例。
>
> |方案|标志|
> |---|---|
> |将访问控制列表 (ACL) 与文件一起复制。|**--preserve-smb-permissions**=\[true\|false\]|
> |将 SMB 属性信息与文件一起复制。|**--preserve-smb-info**=\[true\|false\]|
> |基于模式排除文件。|**--exclude-path**|
> |指定你希望与同步相关的日志条目达到何种详细程度。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
>
> 有关完整列表，请参阅[选项](storage-ref-azcopy-sync.md#options)。

### <a name="update-a-file-share-with-changes-to-a-local-file-system"></a>使用对本地文件系统所做的更改来更新文件共享

在这种情况下，文件共享是目标，本地文件系统是源。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**示例**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-local-file-system-with-changes-to-a-file-share"></a>使用对文件共享所做的更改来更新本地文件系统

在这种情况下，本地文件系统是目标，文件共享是源。

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

**语法**

`azcopy sync 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'C:\myDirectory' --recursive`

**示例**

```azcopy
azcopy sync 'https://mystorageaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --recursive
```

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>使用对一个文件共享所做的更改来更新另一个文件共享

此命令中显示的第一个文件共享是源。 第二个文件共享是目标。

**语法**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**示例**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用对另一个文件共享中的目录所做的更改来更新某个目录

此命令中显示的第一个目录是源。 第二个是目标。

**语法**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**示例**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>更新文件共享，使之与共享快照的内容匹配

此命令中显示的第一个文件共享是源。 在 URI 的末尾追加字符串 `&sharesnapshot=`，后跟快照的 DateTime 值。

**语法**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**示例**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive --preserve-smb-permissions=true --preserve-smb-info=true
```

若要详细了解共享快照，请参阅 [Azure 文件存储的共享快照概述](../files/storage-snapshots-files.md)。

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [传输数据](storage-use-azcopy-v10.md#transfer-data)

请参阅以下文章了解如何配置设置、优化性能和排查问题：

- [AzCopy 配置设置](storage-ref-azcopy-configuration-settings.md)
- [优化 AzCopy 的性能](storage-use-azcopy-optimize.md)
- [使用日志文件对 Azure 存储中的 AzCopy V10 问题进行故障排除](storage-use-azcopy-configure.md)
