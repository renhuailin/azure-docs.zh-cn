---
title: Azure HPC 缓存数据引入 - msrsync
description: 如何使用 msrsync 将数据移动到 Azure HPC 缓存中的 Blob 存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: db277b475641fec7ea5fddce89d8c9745fac7aa3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618203"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC 缓存数据引入 - msrsync 方法

本文提供了有关使用 ``msrsync`` 实用工具将数据复制到用于 Azure HPC 缓存的 Azure Blob 存储容器的详细说明。

若要详细了解如何将数据移到 Azure HPC 缓存的 Blob 存储，请阅读[将数据移到 Azure Blob 存储](hpc-cache-ingest.md)。

``msrsync`` 工具可用于将数据移到 Azure HPC 缓存的后端存储目标。 此工具旨在通过运行多个并行 ``rsync`` 进程来优化带宽的使用。 可从 GitHub 获取此工具：https://github.com/jbd/msrsync。

``msrsync`` 将源目录分解成独立的“桶”，然后针对每个桶运行单个 ``rsync`` 进程。

使用四核 VM 进行的初步测试表明，使用 64 个进程时效率最高。 使用 ``msrsync`` 选项 ``-p`` 将进程数设置为 64。

请注意，``msrsync`` 只能与本地卷相互写入。 必须可将源和目标作为用于发出命令的工作站上的本地装载进行访问。

按照这些说明操作，使用 ``msrsync`` 填充用于 Azure HPC 缓存的 Azure Blob 存储：

1. 安装 ``msrsync`` 及其必备组件（``rsync`` 和 Python 2.6 或更高版本）
1. 确定要复制的文件和目录总数。

   例如，结合参数 ```prime.py --directory /path/to/some/directory``` 使用实用工具 ``prime.py``（可通过下载 <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> 获取）。

   如果不使用 ``prime.py``，则可以使用 Gnu ``find`` 工具计算项数，如下所示：

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 将项数除以 64 即可得出每个进程的项数。 运行该命令时，在 ``-f`` 选项中使用此数字可以设置桶的大小。

1. 发出 ``msrsync`` 命令以复制文件：

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   例如，此命令可将 64 个进程中的 11,000 个文件从 /test/source-repository 移到 /mnt/hpccache/repository：

   `mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository`
