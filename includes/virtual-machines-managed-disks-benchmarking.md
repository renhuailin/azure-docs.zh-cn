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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094577"
---
## <a name="warm-up-the-cache"></a>预热缓存

启用 ReadOnly 主机缓存的磁盘能够提供比磁盘限制更高的 IOPS。 若要通过主机缓存来实现此最大读取性能，首先必须对此磁盘的缓存进行预热。 这样可确保需要通过基准测试工具在 CacheReads 卷上实现的读取 IO 实际上可以直接命中缓存而不是磁盘。 命中缓存导致单个启用缓存的磁盘可以实现更高的 IOPS。

> [!IMPORTANT]
> 每次重启 VM 后，必须在运行基准测试之前预热缓存。

## <a name="diskspd"></a>DISKSPD

在 VM 上[下载 DISKSP 工具](https://github.com/Microsoft/diskspd)。 DISKSPD 是一种可自定义的工具，用于创建你自己的合成工作负载。 我们将使用上面介绍的相同设置来运行基准测试。 你可以更改规范以测试不同的工作负载。

在此示例中，我们使用以下基准参数集：

- -c200G：创建（或重新创建）测试中使用的示例文件。 可以按字节、KiB、MiB、GiB 或块进行设置。 本例中使用大型的 200 GiB 目标文件来最大程度地减少内存缓存。
- -w100：指定属于写入请求的操作的百分比（-w0 等效于 100% 读取）。
- -b4K：表示块大小（以字节、KiB、MiB 或 GiB 为单位）。 本例中使用 4K 块大小来模拟随机 I/O 测试。
- -F4：共设置四个线程。
- -r：标识随机 I/O 测试（替代 -s 参数）。
- -o128：表示每个线程的每个目标的未完成 I/O 请求数。 这也称为队列深度。 本例中使用 128 来对 CPU 施加压力。
- -W7200：指定测量开始之前的预热时长。
- -d30：指定测试的持续时间，不包括预热。
- -Sh：禁用软件和硬件写入缓存（等效于 -Suw）。

如需参数的完整列表，请参阅 [GitHub 存储库](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters)。

### <a name="maximum-write-iops"></a>最大写入 IOPS
我们使用 128 的高队列深度，8 KB 的小型块大小，以及 4 个工作线程用于推动写入操作。 写入辅助角色正在推动“NoCacheWrites”卷上的流量，该卷有 3 个磁盘的缓存设置为“无”。

运行以下命令，进行 30 秒的预热和 30 秒的测量：

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

结果显示 Standard_D8ds_v4 VM 传送的最大写入 IOPS 限制为 12,800。

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="对于 3208642560 的总字节数，最大总 I/O 次数为 391680 次，总计 101.97 MiB/s，每秒总计 13052.65 次 I/O。":::

### <a name="maximum-read-iops"></a>最大读取 IOPS

我们使用 128 的高队列深度，4 KB 的小型块大小，以及 4 个工作线程用于推动读取操作。 读取辅助角色正在推动“CacheReads”卷上的流量，该卷有 1 个磁盘的缓存设置为“ReadOnly”。

运行以下命令，进行两小时的预热和 30 秒的测量：

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

结果显示 Standard_D8ds_v4 VM 传送的最大读取 IOPS 限制为 77,000。

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="对于 9652785152 的总字节数，共有 2356637 次 I/O，总计 306.72 MiB/s，每秒总计 78521.23 次 I/O。":::

### <a name="maximum-throughput"></a>最大吞吐量

若要获取最大读取和写入吞吐量，可以更改为更大的块大小 64 KB。
## <a name="fio"></a>FIO

FIO 是一种常用工具，可以在 Linux VM 上对存储进行基准测试。 它可以灵活地选择不同的 IO 大小、顺序或随机读取和写入。 它生成的工作线程或进程可以执行指定的 I/O 操作。 可以指定每个工作线程使用作业文件时必须执行的 I/O 操作类型。 我们根据以下示例所描述的方案创建了一个作业文件。 可以更改这些作业文件中的规范，以便对在高级存储上运行的不同工作负荷进行基准测试。 在这些示例中，我们使用运行 Ubuntu 的 Standard_D8ds_v4。 运行基准测试之前，请使用基准测试部分开头所述的相同设置并预热缓存。

开始之前，请[下载 FIO](https://github.com/axboe/fio) 并将其安装在虚拟机上。

针对 Ubuntu 运行以下命令：

```
apt-get install fio
```

我们在磁盘上使用 4 个工作线程来执行写入操作，4 个工作线程来执行读取操作。 写入工作线程推动“nocache”卷上的流量，该卷有 3 个磁盘的缓存设置为“无”。 读取工作线程推动“readcache”卷上的流量，该卷有 1 个磁盘的缓存设置为“ReadOnly”。

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

进行测试时，就能够看到 VM 和高级磁盘传送的写入 IOPS 数。 如以下示例所示，Standard_D8ds_v4 VM 传送的最大写入 IOPS 限制为 12,800 IOPS。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="VM 和高级 SSD 传送的写入 IOPS 数，显示写入数为 13.1k IOPS。":::

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

进行测试时，就能够看到 VM 和高级磁盘传送的读取 IOPS 数。 如以下示例所示，Standard_D8ds_v4 VM 传送了超过 77,000 个读取 IOPS。 这是磁盘和缓存性能的组合。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="VM 和高级 SSD 传送的写入 IOPS 的屏幕截图，其中显示的读数为 78.6k。":::

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

进行测试时，就能够看到 VM 和高级磁盘传送的组合型读取和写入 IOPS 数。 如以下示例所示，Standard_D8ds_v4 VM 传送了超过 90,000 个读写组合 IOPS。 这是磁盘和缓存性能的组合。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="合并的读取和写入 IOPS，显示读取数为 78.3k IOPS，写入数为 12.6k IOPS。":::

### <a name="maximum-combined-throughput"></a>最大组合吞吐量

若要获得最大读写组合吞吐量，请使用较大的块大小和大的队列深度，并通过多个线程执行读取和写入操作。 可以使用 64 KB 的块大小，128 的队列深度。