---
title: 通过 Azure 存储优化 AzCopy v10 的性能 | Microsoft Docs
description: 本文可帮助你通过 Azure 存储优化 AzCopy v10 的性能。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 9870ff7f2033372e63de80138b2f37b3896eb4e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427599"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>通过 Azure 存储优化 AzCopy 的性能

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文可帮助你优化性能。

> [!NOTE]
> 如果你正在寻找 AzCopy 入门内容，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)

可以指定性能基准，然后使用命令和环境变量在性能与资源消耗量之间找到最佳的平衡。

## <a name="run-benchmark-tests"></a>运行基准测试

可对特定的 Blob 容器或文件共享运行性能基准测试，以查看常规的性能统计信息和识别性能瓶颈。 可以通过上传或下载生成的测试数据来运行测试。 

使用以下命令运行性能基准测试。

**语法**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**示例**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

此命令通过将测试数据上传到指定的目标来运行性能基准测试。 测试数据将在内存中生成、上传到目标，并在完成测试后从目标中删除。 可以使用可选的命令参数来指定要生成的文件数以及文件的大小。

如果希望通过下载数据来运行此测试，请将 `mode` 参数设置为 `download`。 如需详细的参考文档，请参阅 [azcopy benchmark](storage-ref-azcopy-bench.md)。 

## <a name="optimize-for-large-numbers-of-small-files"></a>针对大量小文件进行优化

传输小文件时，尤其是在传输大量小文件时，吞吐量可能会下降。 若要最大程度地提高性能，请减小每个作业的大小。 对于下载和上传操作，请提高并发，减少日志活动，并关闭导致较高性能成本的功能。

#### <a name="reduce-the-size-of-each-job"></a>减小每个作业的大小

若要实现最佳性能，请确保每个作业传输的文件少于 1000 万个。 传输 5000 万个以上文件的作业可能会性能不佳，因为 AzCopy 作业跟踪机制会产生大量开销。 若要降低开销，请考虑将大型作业划分为较小作业。 

减小作业大小的一种方法是限制作业影响的文件数。 可以使用命令参数执行该操作。 例如，作业可以在 [azcopy copy](storage-ref-azcopy-copy.md) 命令中使用 `include path` 参数，以便仅复制目录的子集。 

使用 `include-pattern` 参数可复制具有特定扩展名（例如：`*.pdf`）的文件。 在单独作业中，使用 `exclude-pattern` 参数可复制所有没有 `*.pdf` 扩展名的文件。 有关示例，请参阅[上传特定文件](storage-use-azcopy-blobs-upload.md#upload-specific-files)和[下载特定 blob](storage-use-azcopy-blobs-download.md#download-specific-blobs)。

确定如何将大型作业划分为较小作业之后，请考虑在多个虚拟机 (VM) 上运行作业。

#### <a name="increase-concurrency"></a>提高并发

如果要上传或下载文件，请使用 `AZCOPY_CONCURRENCY_VALUE` 环境变量增加计算机上可以进行的并发请求数。 可将此变量设置得尽可能高，而不会影响计算机的性能。 若要了解有关此变量的详细信息，请参阅本文的[增加并发请求数](#increase-the-number-of-concurrent-requests)部分。

如果要在存储帐户之间复制 blob，请考虑将 `AZCOPY_CONCURRENCY_VALUE` 环境变量的值设置为大于 `1000` 的值。 可以将此变量设置得较高，因为 AzCopy 使用服务器到服务器 API，因此数据会直接在存储服务器之间复制，不会使用计算机的处理能力。  

#### <a name="decrease-the-number-of-logs-generated"></a>减少生成的日志数

可以通过减少 AzCopy 在完成操作时创建的日志条目数来提高性能。 默认情况下，AzCopy 会记录与操作相关的所有活动。 若要实现最佳性能，请考虑将复制、同步或删除命令的 `log-level` 参数设置为 `ERROR`。 这样，AzCopy 便只会记录错误。 默认情况下，日志级别值设置为 `INFO`。 

#### <a name="turn-off-length-checking"></a>关闭长度检查 

如果要上传或下载文件，请考虑将复制和同步命令的 `--check-length` 设置为 `false`。 这可防止 AzCopy 在传输后验证文件的长度。 默认情况下，AzCopy 会在传输完成后检查长度，以确保源和目标文件匹配。 AzCopy 在每次文件传输后都会执行此检查。 当作业传输大量小文件时，此检查可能会降低性能。 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>启用并发本地扫描 (Linux)

某些 Linux 系统上的文件扫描的执行速度不够快，无法使所有并行网络连接饱和。 在这些情况下，可以将 `AZCOPY_CONCURRENT_SCAN` 设置为 `true`。 

## <a name="increase-the-number-of-concurrent-requests"></a>增加并发请求数

可以通过设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量来提高吞吐量。 此变量指定可发生的并发请求数。  

如果计算机中的 CPU 少于 5 个，则此变量的值将设置为 `32`。 否则，默认值等于 16 乘以 CPU 数。 此变量的最大默认值为 `300`，但可以手动增大或减小此值。 

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空白，你可以通过查看任何 AzCopy 日志文件的开头部分来读取所用的值。 日志中会报告所选的值以及选择该值的原因。

在设置此变量之前，我们建议运行基准测试。 基准测试过程将报告建议的并发值。 或者，如果网络条件和有效负载不同，请将此变量设置为单词 `AUTO` 而不是特定的数字。 这样，AzCopy 始终会运行它在基准测试中使用的相同自动优化过程。

## <a name="limit-the-throughput-data-rate"></a>限制吞吐量数据速率

可以在命令中使用 `cap-mbps` 标志来设置吞吐量数据速率的上限。 例如，以下命令恢复作业并将吞吐量上限设置为每秒 `10` 兆位 (Mb)。 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>优化内存用量

设置 `AZCOPY_BUFFER_GB` 环境变量，以指定 AzCopy 在下载和上传文件时要用于缓冲的最大系统内存量。 请以 GB 表示此值。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> 作业跟踪始终会在内存使用量方面产生额外开销。 开销量因作业中的传输数量而异。 缓冲区是内存使用量的最大组成部分。 可以使用 `AZCOPY_BUFFER_GB` 帮助控制开销，以大致满足需求，但没有可用于严格限制总体内存使用量的标志。

## <a name="optimize-file-synchronization"></a>优化文件同步

[sync](storage-ref-azcopy-sync.md) 命令标识目标中的所有文件，然后在开始同步操作前比较文件名和上次修改的时间戳。 如果有大量文件，则可通过消除此前期处理来提高性能。 

若要实现此目的，请改用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，并将 `--overwrite` 标志设置为 `ifSourceNewer`。 AzCopy 会在文件复制时比较文件，而不执行任何预先扫描和比较。 如果有大量文件要比较，这会提供性能优势。

[azcopy copy](storage-ref-azcopy-copy.md) 命令不会从目标中删除文件，因此，若要在源中不存在文件时删除目标中的文件，请使用 [azcopy sync](storage-ref-azcopy-sync.md) 命令，并将 `--delete-destination` 标志设置为 `true` 或 `prompt` 值。

## <a name="see-also"></a>另请参阅

- [AzCopy 入门](storage-use-azcopy-v10.md)
