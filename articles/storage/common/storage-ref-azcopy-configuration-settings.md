---
title: AzCopy v10 配置设置（Azure 存储）| Microsoft Docs
description: 本文提供 AzCopy V10 配置设置的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: edd90071125c65b7d4af3d0065e92b30f35e5f65
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436946"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>AzCopy v10 配置设置（Azure 存储）

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文包含可用于配置 AzCopy v10 的环境变量列表。

> [!NOTE]
> 如果你正在寻找 AzCopy 入门内容，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)。

## <a name="azcopy-v10-environment-variables"></a>AzCopy v10 环境变量

下表描述了每个环境变量并提供了相关链接，可利用链接中的内容使用这些环境变量。

| 环境变量 | 说明 |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services 访问密钥。 提供密钥以对 Amazon Web Services 进行授权。[使用 AzCopy 将数据从 Amazon S3 复制到 Azure 存储](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services 机密访问密钥 提供密钥以对 Amazon Web Services 进行授权。 [使用 AzCopy 将数据从 Amazon S3 复制到 Azure 存储](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | 要使用的 Azure Active Directory 终结点。 此变量仅用于自动登录，请在调用登录命令时改为使用命令行标志。 |
| AZCOPY_AUTO_LOGIN_TYPE | 将此变量设置为 `DEVICE`、`MSI` 或 `SPN`。 此变量提供在不使用 `azcopy login` 命令的情况下授权的功能。 当操作系统没有机密存储（如 Linux keyring）时，此机制非常有用。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)。 |
| AZCOPY_BUFFER_GB | 指定 AzCopy 在下载和上传文件时要使用的最大系统内存量。 请以 GB 表示此值。 请参阅[优化内存用量](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | 默认情况下，AzCopy on Windows 将在主机名级别缓存代理服务器查找（不考虑 URL 路径）。 设置为“true”以外的其他值可禁用缓存。 |
| AZCOPY_CONCURRENCY_VALUE | 指定可能发生的并发请求数。 可以使用此变量来提高吞吐量。 如果计算机中的 CPU 少于 5 个，则此变量的值将设置为 `32`。 否则，默认值等于 16 乘以 CPU 数。 此变量的最大默认值为 `3000`，但可以手动增大或减小此值。 请参阅[提高并发](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | 通过控制并发启动传输的文件的数量，覆盖任何时候正在进行的文件的（近似）数量。 |
| AZCOPY_CONCURRENT_SCAN | 控制扫描期间使用的（最大）并行度。 仅影响并行化枚举器，其中包括 Azure 文件存储/Blob 和本地文件系统。 |
| AZCOPY_CONTENT_TYPE_MAP  | 重写操作系统定义的一个或多个默认 MIME 类型映射。 将此变量设置为定义任何映射的 JSON 文件的路径。  下面是示例 JSON 文件的内容： <br><br> {<br>&nbsp;&nbsp;"MIMETypeMapping": { <br>&nbsp;&nbsp;&nbsp;&nbsp;".323": "text/h323",<br>&nbsp;&nbsp;&nbsp;&nbsp;".aaf": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp; ".aca": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp;&nbsp;".accdb": "application/msaccess",<br>&nbsp;&nbsp;&nbsp;&nbsp;  }<br>}
|
| AZCOPY_DEFAULT_SERVICE_API_VERSION | 覆盖服务 API 版本，使 AzCopy 可以容纳 Azure Stack 等自定义环境。 |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | 仅当 Azure Blob 为源时才适用。 并发扫描速度更快，但使用分层列表 API，这可能会导致增加 IO/成本。 指定为“true”会降低性能，但能节省成本。 |
| AZCOPY_JOB_PLAN_LOCATION | 覆盖作业计划文件（用于进度跟踪和恢复）存储的位置，以避免填满磁盘。 |
| AZCOPY_LOG_LOCATION | 覆盖存储日志文件的位置，以避免填满磁盘。 |
| AZCOPY_MSI_CLIENT_ID | 用户分配的托管标识的客户端 ID。 `AZCOPY_AUTO_LOGIN_TYPE` 设置为 `MSI` 时使用。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | 用户分配的托管标识的对象 ID。 `AZCOPY_AUTO_LOGIN_TYPE` 设置为 `MSI` 时使用。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | 用户分配的托管标识的资源 ID。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | 页 blob 的吞吐量是否应自动调整以匹配服务限制？ 默认值为 true。 设置为“false”以禁用 |
| AZCOPY_PARALLEL_STAT_FILES | 导致 AzCopy 在扫描本地文件系统时查找并行线程上的文件属性。  线程是从 AZCOPY_CONCURRENT_SCAN 定义的池绘制的。  将此值设置为 true 可提高 Linux 上的扫描性能。  不需要或不推荐在 Windows 上执行此操作。 |
| AZCOPY_SHOW_PERF_STATES | 如果设置为“任何内容”，屏幕上的输出将包括按状态的区块计数 |
| AZCOPY_SPA_APPLICATION_ID | 服务主体应用注册的应用程序 ID。 `AZCOPY_AUTO_LOGIN_TYPE` 设置为 `SPN` 时使用。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | 证书的密码。 `AZCOPY_AUTO_LOGIN_TYPE` 设置为 `SPN` 时使用。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | 证书文件的相对或完全限定的路径。 `AZCOPY_AUTO_LOGIN_TYPE` 设置为 `SPN` 时使用。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | 客户端机密。 `AZCOPY_AUTO_LOGIN_TYPE` 设置为 `SPN` 时使用。 请参阅[授权时不使用机密存储](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | Azure Active Directory 目录 ID，用于 OAuth 设备交互式登录。 此变量仅用于自动登录，请在调用登录命令时改为使用命令行标志。 |
| AZCOPY_TUNE_TO_CPU | 如果设置为 false，则会在优化 AzCopy 并发级别（例如，在基准命令中）时，阻止其将 CPU 使用率考虑在内。 |
| AZCOPY_USER_AGENT_PREFIX | 向默认 AzCopy 用户代理添加一个前缀，用于遥测。 将自动插入空格。 |
| GOOGLE_APPLICATION_CREDENTIALS | 服务帐户密钥文件的绝对路径 提供密钥以对 Amazon Web Services 进行授权。 [使用 AzCopy 将数据从 Google Cloud Storage 复制到 Azure 存储（预览版）](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | 为 AzCopy 配置代理设置。 将此变量设置为代理 IP 地址和代理端口号。 例如，`xx.xxx.xx.xxx:xx`。 如果在 Windows 中运行 AzCopy，AzCopy 会自动检测代理设置，因此你无需在 Windows 中使用此设置。 如果在 Windows 中选择使用此设置，此设置会替代自动检测。 请参阅[配置代理设置](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>配置代理设置

若要为 AzCopy 配置代理设置，请设置 `HTTPS_PROXY` 环境变量。 如果在 Windows 中运行 AzCopy，AzCopy 会自动检测代理设置，因此你无需在 Windows 中使用此设置。 如果在 Windows 中选择使用此设置，此设置会替代自动检测。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | 在命令提示符处使用 `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> 在 PowerShell 中使用 `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

AzCopy 目前不支持要求通过 NTLM 或 Kerberos 进行身份验证的代理。

### <a name="bypassing-a-proxy"></a>绕过代理

如果是在 Windows 上运行 AzCopy，并且想让它完全不使用代理（而不是自动检测设置），请使用以下命令。 使用这些设置时，AzCopy 不会寻求使用或尝试使用任何代理。

| 操作系统 | 环境 | 命令  |
|--------|-----------|----------|
| **Windows** | 命令提示符 (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

在其他操作系统上，如果想要不使用代理，只需不设置 HTTPS_PROXY 变量即可。

## <a name="see-also"></a>另请参阅

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [通过 Azure 存储优化 AzCopy v10 的性能](storage-use-azcopy-optimize.md)
- [使用日志文件对 Azure 存储中的 AzCopy V10 问题进行故障排除](storage-use-azcopy-configure.md)
