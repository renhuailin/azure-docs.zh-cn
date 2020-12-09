---
title: 使用 AzCopy v10 将数据复制或移到 Azure 存储 | Microsoft Docs
description: AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制数据，或者在存储帐户之间复制数据。 本文将帮助你下载 AzCopy，连接到存储帐户，然后传输文件。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperfq2
ms.openlocfilehash: 9379417fcb96dce8a59f7d81335cec44e1bb5e6a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96901766"
---
# <a name="get-started-with-azcopy"></a>AzCopy 入门

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文将帮助你下载 AzCopy，连接到存储帐户，然后传输文件。

> [!NOTE]
> AzCopy **V10** 是当前支持的 AzCopy 版本。
>
> 如果需要使用旧版 AzCopy，请参阅本文的[使用旧版 AzCopy](#previous-version) 部分。

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>下载 AzCopy

首先，将 AzCopy V10 可执行文件下载到计算机上的任意目录。 AzCopy V10 是一个免安装的可执行文件。

- [Windows 64 位](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 位](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

这些文件压缩成 zip 文件（Windows 和 Mac）或 tar 文件（Linux）。 要在 Linux 上下载并解压缩 tar 文件，请参阅 Linux 分发文档。

> [!NOTE]
> 若要向/从 [Azure 表存储](../tables/table-storage-overview.md)服务复制数据，请安装 [AzCopy 版本 7.3](https://aka.ms/downloadazcopynet)。


## <a name="run-azcopy"></a>运行 AzCopy

为方便使用，请考虑将 AzCopy 可执行文件的目录位置添加到系统路径。 这样就可以在系统上的任何目录中键入 `azcopy`。

如果不将 AzCopy 目录添加到系统路径，则必须将目录切换到 AzCopy 可执行文件所在的位置，然后在 Windows PowerShell 命令提示符中键入 `azcopy` 或 `.\azcopy`。

若要查看命令列表，请键入 `azcopy -h` 并按 ENTER 键。

若要了解特定的命令，只需包含该命令的名称（例如：`azcopy list -h`）。

> [!div class="mx-imgBorder"]
> ![联机帮助](media/storage-use-azcopy-v10/azcopy-inline-help.png)


若要查找每个命令和命令参数的详细参考文档，请参阅 [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> 系统不会自动向 Azure 存储帐户的所有者分配数据访问权限。 在使用 AzCopy 执行任何有意义的操作之前，需确定如何向存储服务提供身份验证凭据。 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>授权 AzCopy

可以使用 Azure Active Directory (AD) 或共享访问签名 (SAS) 令牌来提供授权凭据。

请参考下表：

| 存储类型 | 当前支持的授权方法 |
|--|--|
|**Blob 存储** | Azure AD 和 SAS |
|**Blob 存储（分层命名空间）** | Azure AD 和 SAS |
|**文件存储** | 仅限 SAS |

#### <a name="option-1-use-azure-active-directory"></a>选项 1：使用 Azure Active Directory

此选项仅适用于 blob 存储。 使用 Azure Active Directory 可以一次性提供凭据，而无需向每个命令追加 SAS 令牌。  

> [!NOTE]
> 在当前版本中，如果你打算在存储帐户之间复制 Blob，必须向每个源 URL 追加一个 SAS 令牌。 只能在目标 URL 中省略 SAS 令牌。 有关示例，请参阅[在存储帐户之间复制 Blob](storage-use-azcopy-blobs.md)。

若要使用 Azure AD 进行访问授权，请参阅[使用 AzCopy 和 Azure Active Directory (Azure AD) 授权访问 Blob](storage-use-azcopy-authorize-azure-active-directory.md)。

#### <a name="option-2-use-a-sas-token"></a>选项 2：使用 SAS 令牌

可将 SAS 令牌追加到在 AzCopy 命令中使用的每个源或目标 URL。

此示例命令以递归方式将本地目录中的数据复制到 Blob 容器。 一个虚构的 SAS 令牌将追加到容器 URL 的末尾。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要详细了解 SAS 令牌及其获取方式，请参阅[使用共享访问签名 (SAS)](./storage-sas-overview.md)。

<a id="transfer-data"></a>

## <a name="transfer-data"></a>传输数据

对标识授权或获取 SAS 令牌后，即可开始传输数据。

若要查找示例命令，请参阅以下文章中的任何一篇。

| 服务 | 项目 |
|--------|-----------|
|Azure Blob 存储 |[将文件上传到 Azure Blob 存储](storage-use-azcopy-blobs-upload.md)<br><br>[下载 Azure Blob 存储中的 blob](storage-use-azcopy-blobs-download.md)<br><br>[在 Azure 存储帐户之间复制 blob](storage-use-azcopy-blobs-download.md)<br><br>[与 Azure Blob 存储同步](storage-use-azcopy-blobs-download.md)|
|Azure 文件 |[使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)|
|Amazon S3|[使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)|
|Azure Stack 存储|[使用 AzCopy 和 Azure Stack 存储传输数据](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>在脚本中使用

#### <a name="obtain-a-static-download-link"></a>获取静态下载链接

在一段时间后，AzCopy [下载链接](#download-and-install-azcopy)将指向新版 AzCopy。 如果脚本会下载 AzCopy，而更高版本的 AzCopy 会修改该脚本所依赖的功能，则该脚本可能会停止工作。

若要避免这些问题，请获取当前 AzCopy 版本的静态（不会更改）链接。 这样，每次运行脚本时，它都会下载相同的 AzCopy 版本。

若要获取该链接，请运行以下命令：

| 操作系统  | 命令 |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> 对于 Linux，`tar` 命令中的 `--strip-components=1` 会删除包含版本名称的顶级文件夹，并改为将二进制文件直接提取到当前文件夹中。 这样，只需要更新 `wget` URL，即可使用 `azcopy` 的新版本更新脚本。

该 URL 显示在此命令的输出中。 然后，脚本可以使用该 URL 下载 AzCopy。

| 操作系统  | 命令 |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>转义 SAS 令牌中的特殊字符

在扩展名为 `.cmd` 的批处理文件中，必须转义 SAS 令牌中显示的 `%` 字符。 为此，可将一个附加的 `%` 字符添加到 SAS 令牌字符串中的现有 `%` 字符旁边。

#### <a name="run-scripts-by-using-jenkins"></a>使用 Jenkins 运行脚本

如果你打算使用 [Jenkins](https://jenkins.io/) 运行脚本，请确保将以下命令放在脚本的开头。

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>在 Azure 存储资源管理器中使用

[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)使用 AzCopy 执行其所有数据传输操作。 如果你想要利用 AzCopy 的性能优势，但同时又偏好使用图形用户界面而不是命令行来与文件进行交互，则可以使用[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

存储资源管理器使用帐户密钥执行操作，因此在登录到存储资源管理器后，无需额外提供授权凭据。

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>配置、优化和修复

请参阅[对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>使用以前的版本

如果需要使用旧版 AzCopy，请参阅以下链接之一：

- [Windows 上的 AzCopy (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [Linux 上的 AzCopy (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>后续步骤

如有任何疑问、问题或一般反馈，请在 [GitHub](https://github.com/Azure/azure-storage-azcopy) 页上提交。