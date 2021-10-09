---
title: 使用 AzCopy 从 Google Cloud Storage 复制到 Azure 存储 | Microsoft Docs
description: 使用 AzCopy 将数据从 Google Cloud Storage 复制到 Azure 存储。 AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8e592f8783bb0659a453c8d7d54cddf921f5b1e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606924"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>使用 AzCopy 将数据从 Google Cloud Storage 复制到 Azure 存储（预览版）

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文介绍如何使用 AzCopy 将对象、目录和桶从 Google Cloud Storage 复制到 Azure Blob 存储。

> [!IMPORTANT]
> 将数据从 Google Cloud Storage 复制到 Azure 存储当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="choose-how-youll-provide-authorization-credentials"></a>选择如何提供授权凭据

- 若要对 Azure 存储授权，请使用 Azure Active Directory (AD) 或共享访问签名 (SAS) 令牌。

- 若要对 Google Cloud Storage 进行授权，请使用服务帐户密钥。

### <a name="authorize-with-azure-storage"></a>对 Azure 存储授权

请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy，并了解如何提供存储服务的授权凭据。

> [!NOTE]
> 本文中的示例假定你已使用 Azure Active Directory (Azure AD) 提供了授权凭据。
>
> 如果你希望使用 SAS 令牌来授权访问 Blob 数据，可将该令牌追加到每个 AzCopy 命令中的资源 URL。 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

### <a name="authorize-with-google-cloud-storage"></a>对 Google Cloud Storage 授权

若要对 Google Cloud Storage 进行授权，你需要使用服务帐户密钥。 有关如何创建服务帐户密钥的信息，请参阅[创建和管理服务帐户密钥](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)。

获取服务密钥后，请将 `GOOGLE_APPLICATION_CREDENTIALS` 环境变量设置为服务帐户密钥文件的绝对路径：

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>复制对象、目录和桶

AzCopy 使用[放置来自 URL 的块](/rest/api/storageservices/put-block-from-url) API，因此数据将在 Google Cloud Storage 与存储服务器之间直接复制。 这些复制操作不会占用计算机的网络带宽。

> [!TIP]
> 本部分中的示例将路径参数括在单引号 ('') 中。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

 这些示例也适用于具有分层命名空间的帐户。 [Data Lake Storage 上的多协议访问](../blobs/data-lake-storage-multi-protocol-access.md)使你可以在这些帐户上使用相同的 URL 语法 (`blob.core.windows.net`)。

### <a name="copy-an-object"></a>复制对象

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

**语法**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**示例**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

### <a name="copy-a-directory"></a>复制目录

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

**语法**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**示例**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> 此示例追加 `--recursive` 标志以复制所有子目录中的文件。

### <a name="copy-the-contents-of-a-directory"></a>复制目录的内容

可以使用通配符 (*) 复制目录的内容，而无需复制包含内容的目录本身。

**语法**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**示例**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>复制云存储桶

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

**语法**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**示例**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>复制 Google Cloud 项目中的所有桶

首先，将 `GOOGLE_CLOUD_PROJECT` 设置为 Google Cloud 项目的项目 ID。

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

**语法**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**示例**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>在 Google Cloud 项目中复制一部分桶

首先，将 `GOOGLE_CLOUD_PROJECT` 设置为 Google Cloud 项目的项目 ID。

在桶名称中使用通配符 (*) 来复制一部分桶。 对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

**语法**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**示例**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>处理桶命名规则的差异

相比 Azure Blob 容器，Google Cloud Storage 对桶的名称实施一组不同的命名约定。 可在[此处](https://cloud.google.com/storage/docs/naming-buckets)了解相关信息。 如果选择将一组桶复制到 Azure 存储帐户，复制操作可能因命名差异而失败。

AzCopy 会处理可能出现的三个最常见问题：包含句点的桶、包含连续连字符的桶和包含下划线的桶。 Google Cloud Storage 桶名称可以包含句点和连续的连字符，但 Azure 中的容器不能。 AzCopy 会将句点替换为连字符，并将连续的连字符替换为表示连续连字符数目的数字（例如：名为 `my----bucket` 的桶将变成 `my-4-bucket`）。 如果桶名称包含下划线 (`_`)，则 AzCopy 会将下划线替换为连字符。 例如，名为 `my_bucket` 的桶将变为 `my-bucket`。

## <a name="handle-differences-in-object-naming-rules"></a>处理对象命名规则的差异

相比 Azure Blob，Google Cloud Storage 为对象名称实施一组不同的命名约定。 可在[此处](https://cloud.google.com/storage/docs/naming-objects)了解相关信息。

Azure 存储不允许对象名称（或虚拟目录路径中的任何段）以尾随句点结束（例如 `my-bucket...`）。 执行复制操作时，尾随句点会被剪裁。

## <a name="handle-differences-in-object-metadata"></a>处理对象元数据的差异

Google Cloud Storage 和 Azure 允许在对象键名称中使用不同的字符集。 可在[此处](https://cloud.google.com/storage/docs/metadata)阅读有关 Google Cloud Storage 中的元数据的信息。 在 Azure 端，Blob 对象键遵守 [C# 标识符](/dotnet/csharp/language-reference/)的命名规则。

在 AzCopy `copy` 命令中，可为 `s2s-handle-invalid-metadata` 可选标志提供一个值，用于指定如何处理其中的元数据包含不兼容键名称的文件。 下表描述了每个标志值。

| 标志值 | 说明  |
|--------|-----------|
| **ExcludeIfInvalid** | （默认选项）不在传输的对象中包含元数据。 AzCopy 将记录警告。 |
| **FailIfInvalid** | 不复制对象。 AzCopy 将记录错误，并将该错误包含到传输摘要显示的失败计数中。  |
| **RenameIfInvalid**  | AzCopy 将解析无效的元数据键，并使用已解析的元数据键值对将对象复制到 Azure。 若要确切地了解 AzCopy 采取哪些步骤来重命名对象键，请参阅下面的 [AzCopy 如何重命名对象键](#rename-logic)部分。 如果 AzCopy 无法重命名该键，则不会复制该对象。 |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重命名对象键

AzCopy 执行以下步骤：

1. 将无效字符替换为“_”。

2. 将字符串 `rename_` 添加到新的有效键的开头。

   此键将用于保存原始元数据的 **值**。

3. 将字符串 `rename_key_` 添加到新的有效键的开头。
   此键将用于保存原始元数据的无效 **键**。
   可以使用此键在 Azure 端尝试恢复元数据，因为元数据键作为值保留在 Blob 存储服务中。

## <a name="next-steps"></a>后续步骤

如需了解更多示例，请参阅以下文章：

- [示例：上载](storage-use-azcopy-blobs-upload.md)
- [示例：下载](storage-use-azcopy-blobs-download.md)
- [示例：在帐户之间复制](storage-use-azcopy-blobs-copy.md)
- [示例：同步](storage-use-azcopy-blobs-synchronize.md)
- [示例：Amazon S3 存储桶](storage-use-azcopy-s3.md)
- [示例：Azure 文件存储](storage-use-azcopy-files.md)
- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)

请参阅以下文章了解如何配置设置、优化性能和排查问题：

- [AzCopy 配置设置](storage-ref-azcopy-configuration-settings.md)
- [优化 AzCopy 的性能](storage-use-azcopy-optimize.md)
- [使用日志文件对 Azure 存储中的 AzCopy V10 问题进行故障排除](storage-use-azcopy-configure.md)
