---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094577"
---
## <a name="warm-up-the-cache"></a>预热缓存

具有 ReadOnly 主机缓存的磁盘能够提供比磁盘限制更高的 IOPS。 若要通过主机缓存来实现此最大读取性能，首先必须对此磁盘的缓存进行预热。 这样可确保需要通过基准测试工具在 CacheReads 卷上实现的读取 IO 实际上可以直接命中缓存而不是磁盘。 缓存命中会使启用了单一缓存的磁盘中的 IOPS 更多。

> [!IMPORTANT]
> 每次重启 VM 时，都必须预热缓存，然后再运行基准测试。

## <a name="diskspd"></a>DISKSPD

[下载 VM 上的 DISKSP 工具](https://github.com/Microsoft/diskspd) 。 DISKSPD 是一种可自定义的工具，可用于创建自己的综合工作负荷。 我们将使用上面介绍的相同设置来运行基准测试。 你可以更改规范以测试不同的工作负荷。

在此示例中，我们使用以下基准参数集：

- -c200G：创建) 测试中使用的示例文件 (或重新创建。 可以按字节、KiB、MiB、GiB 或块进行设置。 在这种情况下，将使用大文件200的 GiB 目标文件来最大程度地减少内存缓存。
- -w100：指定写入请求的操作的百分比 (-w0 等效于 100% read) 。
- -b4K：指示块大小（以字节为单位）、KiB、MiB 或 GiB。 在这种情况下，4K 块大小用于模拟随机 i/o 测试。
- -F4：设置四个线程的总数。
- -r：指示随机 i/o 测试 (重写-s 参数) 。
- -o128：指示每个线程的每个目标的未完成 i/o 请求数。 这也称为队列深度。 在这种情况下，128用于对 CPU 进行压力。
- -W7200：指定度量开始之前的预热时间。
- -d30：指定测试的持续时间，不包括预热。
- -Sh：禁用 (等效于-Suw) 的软件和硬件写入缓存。

有关参数的完整列表，请参阅 [GitHub 存储库](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters)。

### <a name="maximum-write-iops"></a>最大写入 IOPS
我们使用的队列深度为128，小型块大小为 8 KB，4个工作线程用于驱动写入操作。 写入辅助角色正在推动 "为 nocachewrites" 卷上的流量，该卷有三个磁盘缓存设置为 "无"。

运行以下命令30秒的预热和30秒的度量值：

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

结果显示 Standard_D8ds_v4 VM 提供的最大写入 IOPS 限制为12800。

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="对于3208642560的总字节数，最大总 i/o 数为391680，总计为 101.97 MiB/秒，总计为每秒 13052.65 i/o。":::

### <a name="maximum-read-iops"></a>最大读取 IOPS

我们使用的队列深度为128，小型块大小为 4 KB，4个工作线程用于驱动读取操作。 读取工作线程会推动 "CacheReads" 卷上的流量，该卷有一个磁盘的缓存设置为 "ReadOnly"。

运行以下命令两小时的预热和30秒的度量：

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

结果显示 Standard_D8ds_v4 VM 提供的最大读取 IOPS 限制为77000。

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="对于9652785152个字节，总共有2356637个总 i/o，在每秒306.72 个 MiB/秒，总计为每秒78521.23 个 i/o。":::

### <a name="maximum-throughput"></a>最大吞吐量

若要获取最大读取和写入吞吐量，可以更改为更大的块大小 64 KB。
## <a name="fio"></a>FIO

FIO 是一种常用工具，可以在 Linux VM 上对存储进行基准测试。 它可以灵活地选择不同的 IO 大小、顺序或随机读取和写入。 它生成的工作线程或进程可以执行指定的 I/O 操作。 可以指定每个工作线程使用作业文件时必须执行的 I/O 操作类型。 我们根据以下示例所描述的方案创建了一个作业文件。 可以更改这些作业文件中的规范，以便对在高级存储上运行的不同工作负荷进行基准测试。 在示例中，我们使用的是运行 **Ubuntu** 的 Standard_D8ds_v4。 使用基准部分开头所述的设置，并在运行基准测试之前预热缓存。

开始之前，请[下载 FIO](https://github.com/axboe/fio) 并将其安装在虚拟机上。

针对 Ubuntu 运行以下命令：

```
apt-get install fio
```

我们在磁盘上使用 4 个工作线程来执行写入操作，4 个工作线程来执行读取操作。 写入辅助角色正在推动 "nocache" 卷上的流量，该卷有三个磁盘缓存设置为 "无"。 读取工作线程会推动 "readcache" 卷上的流量，该卷有一个磁盘的缓存设置为 "ReadOnly"。

### <a name="maximum-write-iops"></a>最大写入 IOPS

使用以下规范创建作业文件，以便获得最大写入 IOPS。 将其命名为“fiowrite.ini”。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

请注意以下重要事项，这些事项必须符合前面部分讨论的设计准则。 这些规范是实现最大 IOPS 所必需的。  

* 较高的队列深度：256。  
* 较小的块大小：4 KB。  
* 多个执行随机写入的线程。

运行以下命令，开始进行 30 秒的 FIO 测试：  

```
sudo fio --runtime 30 fiowrite.ini
```

进行测试时，就能够看到 VM 和高级磁盘传送的写入 IOPS 数。 如以下示例中所示，Standard_D8ds_v4 VM 提供的最大写入 IOPS 限制为 12800 IOPS。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="VM 和 premium Ssd 提供的写入 IOPS 数，表示写入为 13.1 k IOPS。":::

### <a name="maximum-read-iops"></a>最大读取 IOPS

使用以下规范创建作业文件，以便获得最大读取 IOPS。 将其命名为“fioread.ini”。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

请注意以下重要事项，这些事项必须符合前面部分讨论的设计准则。 这些规范是实现最大 IOPS 所必需的。

* 较高的队列深度：256。  
* 较小的块大小：4 KB。  
* 多个执行随机写入的线程。

运行以下命令，开始进行 30 秒的 FIO 测试：

```
sudo fio --runtime 30 fioread.ini
```

进行测试时，就能够看到 VM 和高级磁盘传送的读取 IOPS 数。 如以下示例中所示，Standard_D8ds_v4 VM 提供了77000以上的读取 IOPS。 这是磁盘和缓存性能的组合。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="&quot;写入 IOPS&quot; VM 和 &quot;高级 Ssd&quot; 的屏幕截图显示的是 78.6 k。":::

### <a name="maximum-read-and-write-iops"></a>最大读取和写入 IOPS

使用以下规范创建作业文件，以便获得最大读写组合 IOPS。 将其命名为“fioreadwrite.ini”。

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

请注意以下重要事项，这些事项必须符合前面部分讨论的设计准则。 这些规范是实现最大 IOPS 所必需的。

* 较高的队列深度：128。  
* 较小的块大小：4 KB。  
* 多个执行随机读取和写入的线程。

运行以下命令，开始进行 30 秒的 FIO 测试：

```
sudo fio --runtime 30 fioreadwrite.ini
```

进行测试时，就能够看到 VM 和高级磁盘传送的组合型读取和写入 IOPS 数。 如以下示例中所示，Standard_D8ds_v4 VM 提供的读取和写入 IOPS 超过90000个。 这是磁盘和缓存性能的组合。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="合并的读取和写入 IOPS 显示，读取是 78.3 k，写入为 12.6 k IOPS。":::

### <a name="maximum-combined-throughput"></a>最大组合吞吐量

若要获得最大读写组合吞吐量，请使用较大的块大小和大的队列深度，并通过多个线程执行读取和写入操作。 可以使用 64 KB 的块大小，128 的队列深度。