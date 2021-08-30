---
title: 使用 Azure Data Box 保留文件 ACL、属性和时间戳
description: 在通过 SMB 将数据复制到 Azure Data Box 过程中保留的 ACL、时间戳和属性。 使用 Windows 和 Linux 数据复制工具复制元数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 96d3957a7626e393728d4a309bc56ecaa19d4e83
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400914"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>使用 Azure Data Box 保留文件 ACL、属性和时间戳

向 Azure 发送数据时，Azure Data Box 支持保留访问控制列表 (ACL)、时间戳和文件属性。 本文介绍通过服务器消息块 (SMS) 将数据复制到 Data Box 以便上传到 Azure 文件时可以传输的元数据。 

本文提供使用 Windows 和 Linux 数据复制工具复制元数据的具体步骤。 将数据传输到 Blob 存储时不保留元数据。

在本文中，传输的 ACL、时间戳和文件属性统称为元数据。

## <a name="transferred-metadata"></a>传输的元数据

将 Data Box 中的数据上传到 Azure 文件时，将传输以下元数据。

#### <a name="timestamps"></a>时间戳

传输以下时间戳：
- CreationTime
- LastWriteTime

不传输以下时间戳：
- LastAccessTime
  
#### <a name="file-attributes"></a>文件属性

除非另有说明，否则文件和目录上的文件属性都会传输。

传输以下文件属性：
- FILE_ATTRIBUTE_READONLY（仅限文件）
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY（仅限目录）
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY（仅限文件）
- FILE_ATTRIBUTE_NO_SCRUB_DATA

不传输以下文件属性：
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
不传输目录上的只读属性。

#### <a name="acls"></a>ACL

通过 SMB 复制到 Data Box 的目录和文件的所有 ACL 都会复制和传输。 传输包括任意 ACL (DACL) 和系统 ACL (SACL)。 对于 Linux，只传输 Windows NT ACL。

通过网络文件系统 (NTS) 复制数据和使用数据复制服务传输数据时，不会传输 ACL。 数据复制服务直接从共享读取数据，而不会读取 ACL。

即使数据复制工具不会复制 ACL，目录和文件上的默认 ACL 也会传输到 Azure 文件。 对于内置管理员帐户、SYSTEM 帐户和用于在 Data Box 中装载和复制数据的 SMB 共享用户帐户，默认 ACL 拥有权限。

ACL 包含具有以下属性的安全描述符： ACL、所有者、组、SACL。

默认情况下已启用 ACL 传输。 你可能需要在 Data Box 的本地 Web UI 中禁用此设置。 有关详细信息，请参阅[使用本地 Web UI 管理 Data Box 和 Data Box Heavy](./data-box-local-web-ui-admin.md)。

> [!NOTE]
> 不会复制 ACL 包含条件访问控制项 (ACE) 字符串的文件。 这是一个已知问题。 要解决此问题，请装载 Azure 文件共享，然后使用支持复制 ACL 的复制工具将这些文件手动复制到 Azure 文件共享。

## <a name="copying-data-and-metadata"></a>复制数据和元数据

要传输数据的 ACL、时间戳和属性，请按照以下过程将数据复制到 Data Box。 

### <a name="windows-data-copy-tool"></a>Windows 数据复制工具

要通过 SMB 将数据复制到 Data Box，请使用兼容 SMB 的文件复制工具，例如 `robocopy`。 以下示例命令可复制所有文件和目录，并将元数据与数据一起传输。

使用 `/copyall` 或 `/dcopy:DAT` 选项时，请确保未禁用所需的备份操作员权限。 有关详细信息，请参阅[使用本地 Web UI 管理 Data Box 和 Data Box Heavy](./data-box-local-web-ui-admin.md)。 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|选项 |说明 |
|------------------- | ----- |
|`/copyall` |复制所有属性。|
|`/e`      |复制子目录，包括空目录。         |
|`/dcopy:DAT`  |复制数据、属性和时间戳。 注意：必须使用 /dcopy:DAT 选项来传输目录上的 `CreationTime`。 |
|`/r:3`    |指定对复制失败进行 3 次重试。         |
|`/w:60`   |指定两次重试之间的等待时间为 60 秒。         |
|`/is`     |包括相同的文件。         |
|`/nfl`    |不记录文件名。         |
|`/ndl`    |不记录目录名称。        |
|`/np`     |不显示复制操作的进度。         |
|`/MT:32 or 64`  |使用支持 32 或 64 线程的多线程处理。           |
|`/fft`    |降低用于任何文件系统的时间戳粒度。        |
|`/log+:<LogFile>`  |将输出追加到现有的日志文件。|

有关这些 `robocopy` 参数的详细信息，请参阅[教程：通过 SMB 将数据复制到 Azure Data Box](./data-box-deploy-copy-data.md)

> [!NOTE]
> 如果使用 `/copyall` 复制数据，则会将目录和文件上的源 ACL 传输到 Azure 文件。 如果你对源数据只有读取访问权限，并且无法修改源数据，则只对 Data Box 中的数据具有读取访问权限。 只有当你打算将目录和文件上的所有 ACL 连同数据一起复制时，才使用 `/copyall`。

#### <a name="use-robocopy-to-list-copy-modify-files-on-data-box"></a>使用 robocopy 在 Data Box 中列出、复制、修改文件

下面是使用 `robocopy` 复制数据时将使用的一些常见方案。

- **只将数据复制到 Data Box，而不会复制目录和文件上的 ACL**

    使用 `/dcopy:DAT` 选项只复制数据、属性和时间戳。 不会复制目录和文件上的 ACL。

- **将目录和文件上的数据和 ACL 复制到 Data Box**

    使用 `/copyall` 复制所有源数据，包括目录和文件上的所有 ACL。

- **使用 robocopy 列出 Data Box 上的 filesystem**

    使用此命令列出目录内容：

    `robocopy <source-dir> NULL /l /s /xx /njh /njs /fp /B`

    请注意，文件资源管理器不允许列出这些文件。
    
- **复制或删除 Data Box 上的文件夹和文件**

    使用此命令复制单个文件：

    `robocopy <source-dir> <destination-dir> <file-name> /B`

    使用此命令删除单个文件：

    `robocopy <source-dir> <destination-dir> <file-name> /purge /B`

    在上述命令中，`<source-dir>` 不应包含文件：`<file-name>`。 然后，上述命令将目标与源同步，导致从目标中删除该文件。

    请注意，文件资源管理器可能不允许你执行上述操作。

有关详细信息，请参阅[使用 robocopy 命令](/windows-server/administration/windows-commands/robocopy)。

### <a name="linux-data-copy-tool"></a>Linux 数据复制工具

在 Linux 中传输元数据的过程分为两步。 首先，使用 `rsync`（不会复制元数据）等工具复制源数据。 复制数据后，可以使用 `smbcacls` 或 `cifsacl` 等工具复制元数据。 

以下示例命令执行第一步，使用 `rsync` 复制数据。 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>后续步骤

- [通过 SMB 将数据复制到 Azure Data Box](./data-box-deploy-copy-data.md)
