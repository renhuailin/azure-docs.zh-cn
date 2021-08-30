---
title: 将数据移动到 Azure HPC 缓存云容器
description: 如何填充用于 Azure HPC 缓存的 Azure Blob 存储
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/30/2021
ms.author: v-erkel
ms.openlocfilehash: ccb052a9eacacdc18d954fedd940bcc6792fb361
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586917"
---
# <a name="move-data-to-azure-blob-storage"></a>将数据移到 Azure Blob 存储

如果工作流中包括将数据移动到 Azure Blob 存储，确保采用有效的策略操作。 你可以先在新的 blob 容器中预加载数据，再将其定义为存储目标，或者添加容器，然后使用 Azure HPC 缓存复制数据。

本文介绍将数据移动到 blob 存储以用于 Azure HPC 缓存的最佳方式。

> [!TIP]
>
> 本文不适用于已安装 NFS 的 blob 存储（ADLS-NFS 存储目标）。 可以使用任何基于 NFS 的方法填充 ADLS-NFS blob 容器，然后将其添加到 HPC 缓存。 请参阅[预加载数据与 NFS 协议](nfs-blob-considerations.md#pre-load-data-with-nfs-protocol)以了解详细信息。

请注意以下内容：

* Azure HPC 缓存会使用专门的存储格式来组织 blob 存储中的数据。 因此，blob 存储目标必须是新的空容器，或者是以前用于 Azure HPC 缓存数据的 blob 容器。

* 当使用多个客户端和并行操作时，通过 Azure HPC 缓存将数据复制到后端存储目标，效率将更高。 从一个客户端发出的简单的复制命令将缓慢地移动数据。

基于 Python 的实用工具可用于将内容加载到 blob 存储容器中。 请参阅[在 blob 存储中预加载数据](#pre-load-data-in-blob-storage-with-clfsload)以了解详细信息。

如果不想使用加载实用工具，或者如果要将内容添加到现有存储目标，请按照[通过 AZURE HPC 缓存复制数据](#copy-data-through-the-azure-hpc-cache)中的并行数据引入提示操作。

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>通过 CLFSLoad 在 blob 存储中预加载数据

在将数据添加为存储目标之前，可以先使用 Avere CLFSLoad 实用工具将数据复制到新的 blob 存储容器中。 此实用工具在单个 Linux 系统上运行，并以 Azure HPC 缓存所要求的专用格式写入数据。 对于填充用于缓存的 blob 存储容器，CLFSLoad 是最有效的方式。

因而，Azure HPC 缓存团队请求使用 Avere CLFSLoad 实用工具。 向你的团队联系人发出相应请求，或者开具[支持票证](hpc-cache-support-ticket.md)获取帮助。

这种方式仅适用于新的空容器。 使用 Avere CLFSLoad 之前先创建容器。

Avere CLFSLoad 分发资料中包含了详细信息，可从 Azure HPC 缓存团队请求获得。

以下内容概述了该过程：

1. 使用 Python 3.6 版或更高版本准备 Linux 系统（VM 或硬软件准备）。 建议使用 Python 3.7，将能获得更好的性能。
1. 在 Linux 系统上安装 Avere-CLFSLoad 软件。
1. 通过 Linux 命令行执行传输。

Avere CLFSLoad 实用工具需要以下信息：

* 包含 blob 存储容器的存储帐户 ID
* 空的 blob 存储容器的名称
* 允许实用工具写入容器的共享访问签名 (SAS) 令牌
* 数据源的本地路径，即一个本地目录，其中包含要复制的数据，或包含数据的已安装远程系统本地路径

## <a name="copy-data-through-the-azure-hpc-cache"></a>通过 Azure HPC 缓存复制数据

如果不想使用 Avere CLFSLoad 实用工具，或者想要将大量数据添加到现有 blob 存储目标，则可以通过缓存来复制。 Azure HPC 缓存可同时服务多个客户端，因此，若要通过缓存复制数据，应使用多个客户端的并行写入。

![显示多客户端、多线程数据移动的示意图：在左上方，本地硬件存储对应的图标引出了多个箭头。 箭头指向四台客户端计算机。 每台客户端计算机引出了三个指向 Azure HPC 缓存的箭头。 在 Azure HPC 缓存中，多个箭头指向 blob 存储。](media/hpc-cache-parallel-ingest.png)

专门用于将数据从一个存储系统传输到另一个存储系统的 ``cp`` 和 ``copy`` 命令是单线程进程，它们每次只复制一个文件。 这意味着，文件服务器每次只会引入一个文件 - 这给群集资源造成了浪费。

本部分介绍了使用 Azure HPC 缓存创建多客户端、多线程文件复制系统以将数据移到 blob 存储的有效策略。 其中解释了文件传输的概念，以及可用于有效通过多个客户端和简单复制命令来复制数据的决策点。

此外，本文还介绍了一些有用的实用工具。 ``msrsync`` 实用工具可用于部分自动化将数据集分割成桶和使用 rsync 命令的过程。 ``parallelcp`` 脚本是可以读取源目录和自动发出复制命令的另一个实用工具。

### <a name="strategic-planning"></a>策略规划

在制定并行复制数据的策略时，应该知道如何在文件大小、文件计数和目录深度方面做出取舍。

* 如果文件较小，应该关注的指标是每秒文件数。
* 如果文件较大（10MiBi 或更大），则应该关注的指标是每秒字节数。

每个复制进程都有相关的吞吐率和文件传输速率，可以通过计量复制命令的时长和分解文件大小与文件计数来测量这些参数。 有关如何测量速率超出了本文档的范畴，但必须了解要处理的是小型还是大型文件。

通过 Azure HPC 缓存引入并行数据的策略包括：

* 手动复制 - 可以通过针对预定义的文件或路径集在后台一次性运行多个复制命令，在客户端上手动创建多线程复制。 有关详细信息，请参阅 [Azure HPC 缓存数据引入 - 手动复制方法](hpc-cache-ingest-manual.md)。

* 部分自动复制 ``msrsync`` - ``msrsync`` 是一个运行多个并行 ``rsync`` 进程的包装实用工具。 有关详细信息，请参阅 [AZURE HPC 缓存数据引入 - msrsync 方法](hpc-cache-ingest-msrsync.md)。

* 使用脚本复制 ``parallelcp`` - 在 [Azure HPC 缓存数据引入 - 并行复制脚本方法](hpc-cache-ingest-parallelcp.md)中了解如何创建和运行并行复制脚本。

## <a name="next-steps"></a>后续步骤

设置存储后，了解客户端如何装载缓存。

* [访问 Azure HPC 缓存系统](hpc-cache-mount.md)
