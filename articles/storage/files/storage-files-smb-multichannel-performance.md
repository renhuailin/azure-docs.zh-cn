---
title: SMB 多通道性能-Azure 文件
description: 了解 SMB 多通道性能。
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: f85df2e16554b4830d3e5f1bc82c5c8cdf22e28b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586624"
---
# <a name="smb-multichannel-performance"></a>SMB 多通道性能

Azure 文件 SMB 多通道 (预览版) 使 SMB 2.x 客户端能够与 FileStorage 帐户中的高级文件共享建立多个网络连接。 SMB 3.0 协议引入了 Windows Server 2012 和 Windows 8 客户端中的 SMB 多通道功能。 因此，支持 SMB 多通道的任何 Azure 文件 SMB 1.x 客户端都可以利用其 Azure 高级文件共享的功能。 在存储帐户上启用 SMB 多通道不会产生额外的费用。

## <a name="benefits"></a>优点

Azure 文件 SMB 多通道允许客户端使用多个网络连接，这些连接可提供更高的性能，同时降低拥有成本。 提高性能是通过多个 Nic 上的带宽聚合来实现的，并利用接收方缩放 (RSS) 支持 Nic 将 IO 负载分散到多个 Cpu。

- **提高了吞吐量**：多个连接允许以并行方式跨多个路径传输数据，从而极大地提高了使用较大的 IO 大小的文件大小的工作负荷，并要求从单个 VM 或一组较小的 vm 获得高吞吐量。 其中一些工作负荷包括用于内容创建或转码、基因组学和金融服务风险分析的媒体和娱乐。
- **IOPS 较高**： NIC RSS 功能允许跨多个连接的多个 cpu 进行有效负载分布。 这有助于实现更高的 IOPS 规模并有效利用 VM Cpu。 这对于具有较小 IO 大小（如数据库应用程序）的工作负荷很有用。
- **网络容错**：多个连接可降低中断风险，因为客户端不再依赖于单个连接。
- **自动配置**：在客户端和存储帐户上启用 SMB 多通道时，它允许动态发现现有连接，并且可以根据需要创建附加连接路径。
- **成本优化**：在连接到高级共享时，工作负荷可从单个 VM 或一小组 vm 获得更高的规模。 这样可以减少运行和管理工作负载所需的 Vm 数量，从而降低总体拥有成本。

若要了解有关 SMB 多通道的详细信息，请参阅 [Windows 文档](/azure-stack/hci/manage/manage-smb-multichannel)。

此功能为多线程应用程序提供了更好的性能优势，但通常不会帮助单线程应用程序。 有关更多详细信息，请参阅 [性能比较](#performance-comparison) 部分。

## <a name="limitations"></a>限制

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>区域可用性

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>配置

SMB 多通道仅适用于在客户端 (客户端) 和服务端 (Azure 存储帐户) 中启用此功能。

在 Windows 客户端上，SMB 多通道默认处于启用状态。 可以通过运行以下 PowerShell 命令来验证配置： 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
在 Azure 存储帐户中，你将需要启用 SMB 多通道。 请参阅 [启用 SMB 多通道 (预览) ](storage-files-enable-smb-multichannel.md)。

### <a name="disable-smb-multichannel"></a>禁用 SMB 多通道
在大多数情况下，特别是多线程工作负荷，客户端应看到 SMB 多通道性能提高。 但是，某些特定方案（例如单线程工作负荷或用于测试目的）可能需要禁用 SMB 多通道。 有关更多详细信息，请参阅 [性能比较](#performance-comparison) 。

## <a name="verify-smb-multichannel-is-configured-correctly"></a>验证是否正确配置了 SMB 多通道

1. 创建高级文件共享或使用现有文件共享。
1. 确保客户端支持 SMB 多通道 (一个或多个网络适配器) 启用了接收方缩放。 有关更多详细信息，请参阅 [Windows 文档](/azure-stack/hci/manage/manage-smb-multichannel) 。
1. 向客户端装载文件共享。
1. 在应用程序中生成负载。
    复制工具（例如 robocopy/MT）或任何性能工具（如 Diskspd）可生成负载。
1. 以管理员身份打开 PowerShell 并使用以下命令： `Get-SmbMultichannelConnection |fl`
1. 查找 **MaxChannels** 和 **CurrentChannels** 属性

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Smbmultichannelconnection 结果的屏幕截图。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>性能比较

读/写工作负荷模式有两种：单线程和多线程。 大多数工作负载使用多个文件，但在某些特定用例中，工作负荷与共享中的单个文件一起工作。 本部分介绍了不同的用例，以及每个用例的性能影响。 通常情况下，大多数工作负荷都是多线程的，并在多个文件之间分配工作负荷，因此它们应遵循 SMB 多通道的显著性能改进。

- **多线程/多个文件**：根据工作负荷模式，在多个通道上读取和写入 io 可显著提高性能。 对于 IOPS、吞吐量和延迟，性能提升从2x 到4x 之间的变化。 对于此类别，应启用 SMB 多通道以获得最佳性能。
- **多线程/单个文件**：对于此类中的大多数用例，工作负荷将受益于启用 SMB 多通道，尤其是在工作负荷的平均 IO 大小 > ~ 16k 时。 从 SMB 多通道中获益的几个示例方案是备份或恢复单个大型文件。 如果你的工作负荷的 Io 负载较小，则可能需要禁用 SMB 多通道。 在这种情况下，可能会看到大约10% 的性能下降。 根据用例，考虑在多个文件间分散负载，或禁用该功能。 有关详细信息，请参阅 [配置](#configuration) 部分。
- **单线程/多文件或单个文件**：对于大多数单线程工作负载，由于缺乏并行性，最小的性能优势是，如果启用 SMB 多通道，则性能下降约为 ~ 10%。 在这种情况下，最好禁用 SMB 多通道，但有一个例外。 如果单线程工作负载可以跨多个文件分配负载，并使用 ( # A0 ~ 16k) 的平均较大 IO 大小，则 SMB 多通道应该会有轻微的性能优势。

### <a name="performance-test-configuration"></a>性能测试配置

对于本文中的图表，使用了以下配置：单个标准 D32s v3 VM，其中包含具有四个通道的单个启用 RSS 的 NIC。 负载是使用 diskspd.exe、IO 深度为10的多线程和具有不同 IO 大小的随机 Io 生成的。

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="显示性能测试配置的屏幕截图。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>云中-带有 SMB 多通道的多个文件

针对具有不同 IO 大小的10个文件生成了负载。 向上缩放测试结果显示了已启用 SMB 多通道的 IOPS 和吞吐量测试结果中的显著改进。 下图说明了这些结果：

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="性能示意图" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="吞吐量性能示意图。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- 在单个 NIC 上，对于读取，观察到2到3倍的性能提高，并在 IOPS 和吞吐量方面获得3倍。
- SMB 多通道允许使用 IOPS 和吞吐量来达到 VM 限制，即使使用单个 NIC 和四通道限制也是如此。
- 由于传出 (或读取到存储) 不按流量计费，因此读取吞吐量能够超过 VM 发布的限制： 16000 Mbps (2 GiB/秒) 。 实现的测试 >2.7 GiB/s。 入口 (或写入存储) 仍受 VM 限制的限制。
- 将负载分散到多个文件以进行重大改进。

此测试中使用的示例命令为： 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>带有 SMB 多通道的多线程/单个文件工作负荷

针对单个 128 GiB 文件生成了负载。 启用 SMB 多线程后，多线程/单一文件的向上缩放测试显示了大多数情况下的改进。 下图说明了这些结果：

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="IOPS 性能示意图。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="单个文件吞吐量的关系图。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- 对于具有较大平均 IO 大小的单个 NIC ( # A0 ~ 16k) ，读取和写入操作都有重大改进。
- 对于较小的 IO 大小，启用 SMB 多通道后，性能会略微影响 ~ 10%。 可以通过将负载分散到多个文件或禁用该功能来缓解这种情况。
- 性能仍受  [单一文件限制的限制](storage-files-scale-targets.md#file-level-limits)。

## <a name="optimizing-performance"></a>优化性能

以下提示可帮助你优化性能：

- 请确保存储帐户和客户端在同一 Azure 区域中被定位，以减少网络延迟。
- 使用多线程应用程序并将负载分散到多个文件中。
- SMB 多通道性能优势增加了分布式负载的文件数。
- 高级共享性能由预配的共享大小（ (IOPS/出口/入口) 和单个文件限制）绑定。 有关详细信息，请参阅 [了解高级文件共享的设置](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。
- 单个 VM 客户端的最大性能仍绑定到 VM 限制。 例如， [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) 可以支持 16000 MBps (或 2GBps) 的最大带宽，则从 VM 传出的传出 (写入存储) 会按流量计费， (从存储) 进行读取。 文件共享性能受到计算机网络限制、Cpu、内部存储可用网络带宽、IO 大小、并行性以及其他因素的限制。
- 初始测试通常是一项预热，放弃其结果并重复测试。
- 如果性能受单个客户端限制并且工作负荷仍低于预配的共享限制，则可以通过将负载分散到多个客户端来实现更高的性能。

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>IOPS、吞吐量和 IO 大小之间的关系

**吞吐量 = IO 大小 * IOPS**

较高的 IO 大小会提高吞吐量，并具有更高的延迟，导致网络 IOPS 越少。 较小的 IO 大小会提高 IOPS，但会导致较低的网络吞吐量和延迟。

## <a name="next-steps"></a>后续步骤

- [在 FileStorage 帐户上启用 SMB 多通道 (预览) ](storage-files-enable-smb-multichannel.md)
- 有关 SMB 多通道的详细信息，请参阅 [Windows 文档](/azure-stack/hci/manage/manage-smb-multichannel) 。
