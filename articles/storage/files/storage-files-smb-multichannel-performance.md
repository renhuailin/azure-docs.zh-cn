---
title: SMB 多通道性能 - Azure 文件存储
description: 了解 SMB 多通道性能。
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374977"
---
# <a name="smb-multichannel-performance"></a>SMB 多通道性能

Azure 文件存储 SMB 多通道（预览版）使 SMB 3.x 客户端可以与 FileStorage 帐户中的高级文件共享建立多个网络连接。 SMB 3.0 协议在 Windows Server 2012 和 Windows 8 客户端中引入 SMB 多通道功能。 因此，支持 SMB 多通道的任何 Azure 文件存储 SMB 3.x 客户端都可以利用其 Azure 高级文件共享的功能。 在存储帐户上启用 SMB 多通道不会产生额外费用。

## <a name="benefits"></a>优点

Azure 文件存储 SMB 多通道使客户端可以使用多个网络连接，这些连接可提供更高性能，同时降低拥有成本。 提高性能通过多个 NIC 上的带宽聚合来实现，并利用 NIC 的接收方缩放 (RSS) 支持在多个 CPU 间分布 IO 负载。

- 更高的吞吐量：多个连接使数据可以并行地在多个路径上进行传输，从而大大有利于将较大文件大小和较大 IO 大小结合使用，并要求单个 VM 或较小 VM 集提供高吞吐量的工作负载。 其中一些工作负载包括用于内容创建或转码的媒体和娱乐、基因组学以及金融服务风险分析。
- 更高的 IOPS：NIC RSS 功能允许通过多个连接跨多个 CPU 进行有效负载分布。 这有助于实现更高的 IOPS 规模并有效利用 VM CPU。 这对于具有较小 IO 大小的工作负载（如数据库应用程序）十分有用。
- 网络容错：多个连接可降低中断风险，因为客户端不再依赖于单个连接。
- 自动配置：在客户端和存储帐户上启用 SMB 多通道时，它允许动态发现现有连接，并且可以根据需要创建附加连接路径。
- 成本优化：在连接到高级共享时，工作负载可以从单个 VM 或较小VM 集实现更高规模。 这样可以减少运行和管理工作负载所需的 VM 数量，从而降低总拥有成本。

若要了解有关 SMB 多通道的详细信息，请参阅 [Windows 文档](/azure-stack/hci/manage/manage-smb-multichannel)。

此功能可向多线程应用程序提供更好的性能优势，但通常对单线程应用程序没什么帮助。 有关更多详细信息，请参阅[性能比较](#performance-comparison)部分。

## <a name="limitations"></a>限制

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>区域可用性

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>配置

仅当在客户端（你的客户端）和服务端（你的 Azure 存储帐户）中启用此功能时，SMB 多通道才可发挥作用。

在 Windows 客户端上，SMB 多通道在默认情况下处于启用状态。 可以通过运行以下 PowerShell 命令来验证配置： 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
在 Azure 存储帐户中，需要启用 SMB 多通道。 请参阅[启用 SMB 多通道（预览版）](storage-files-enable-smb-multichannel.md)。

### <a name="disable-smb-multichannel"></a>禁用 SMB 多通道
在大多数方案中（特别是多线程工作负载），客户端应该可通过 SMB 多通道提高性能。 但是，某些特定方案（例如单线程工作负载或用于测试目的）可能要禁用 SMB 多通道。 有关更多详细信息，请参阅[性能比较](#performance-comparison)。

## <a name="verify-smb-multichannel-is-configured-correctly"></a>验证是否正确配置了 SMB 多通道

1. 创建高级文件共享或使用现有文件共享。
1. 确保客户端支持 SMB 多通道（一个或多个网络适配器启用了接收方缩放）。 有关更多详细信息，请参阅 [Windows 文档](/azure-stack/hci/manage/manage-smb-multichannel)。
1. 将文件共享装载到客户端。
1. 使用应用程序生成负载。
    复制工具（例如 robocopy /MT）或任何性能工具（如用于读/写文件的 Diskspd）可以生成负载。
1. 以管理员身份打开 PowerShell 并使用以下命令：`Get-SmbMultichannelConnection |fl`
1. 查找 MaxChannels 和 CurrentChannels  属性

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Get-SMBMultichannelConnection 结果的屏幕截图。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>性能比较

有两种类别的读/写工作负载模式 - 单线程和多线程。 大多数工作负载使用多个文件，但在某些特定用例中，工作负载使用共享中的单个文件。 本部分介绍了不同的用例，以及每个用例的性能影响。 一般而言，大多数工作负载都是多线程的，在多个文件之间分布工作负载，因此它们在使用 SMB 多通道时应出现显著的性能改进。

- 多线程/多个文件：根据工作负载模式，多个通道上的读取和写入 IO 应出现显著性能改进。 在 IOPS、吞吐量和延迟方面，性能增益从 2 倍到 4 倍不等。 对于此类别，应启用 SMB 多通道以获得最佳性能。
- 多线程/单个文件：对于此类别中的大多数用例，工作负载会受益于启用 SMB 多通道，尤其是在工作负载的平均 IO 大小 > ~16k 时。 从 SMB 多通道中获益的几种示例方案包括单个大型文件的备份或恢复。 一种可能要禁用 SMB 多通道的例外情况是在工作负载为大量小型 IO 时。 在这种情况下，可能会遇到大约 10% 的轻微性能损失。 根据用例，可考虑在多个文件间分散负载，或禁用该功能。 有关详细信息，请参阅[配置](#configuration)部分。
- 单线程/多个文件或单个文件：对于大多数单线程工作负载，由于缺乏并行性，因此性能优势最小，如果启用 SMB 多通道，则性能通常会降低 ~10%。 在这种情况下，最好禁用 SMB 多通道，但有一个例外。 如果单线程工作负载可以跨多个文件分布负载，并使用较大的平均 IO 大小 (> ~16k)，则 SMB 多通道应提供有轻微的性能优势。

### <a name="performance-test-configuration"></a>性能测试配置

对于本文中的图表，使用了以下配置：单个标准 D32s v3 VM，具有单个启用了 RSS 并且带四个通道的 NIC。 负载使用 diskspd.exe 生成，是 IO 深度为 10 的多线程，并且是具有各种 IO 大小的随机 IO。

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数|预期的网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="显示性能测试配置的屏幕截图。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>具有 SMB 多通道的多线程/多个文件

针对 10 个文件生成负载，具有各种 IO 大小。 纵向扩展测试结果显示，在启用了 SMB 多通道时，IOPS 和吞吐量测试结果得到显著改进。 下图描绘了这些结果：

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="性能图" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="吞吐量性能图。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- 在单个 NIC 上，对于读取，性能提高了 2 到 3 倍，而对于写入，在 IOPS 和吞吐量方面提高了 3-4 倍。
- SMB 多通道使 IOPS 和吞吐量可以达到 VM 限制，即使是使用单个 NIC 并且具有四通道限制也是如此。
- 由于流出量（或是对存储进行的读取）不按流量计费，因此读取吞吐量能够超过发布的 VM 限制，即 16,000 Mbps (2 GiB/s)。 测试实现的吞吐量 >2.7 GiB/s。 流入量（或是对存储进行的写入）仍受 VM 限制约束。
- 将负载分散到多个文件可实现重大改进。

此测试中使用的示例命令为： 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>具有 SMB 多通道的多线程/单个文件工作负荷

针对单个 128 GiB 文件生成负载。 启用了 SMB 多线程时，使用多线程/单个文件的纵向扩展测试在大多数情况下显示可得到改进。 下图描绘了这些结果：

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="IOPS 性能图。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="单个文件吞吐量性能图。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- 在具有较大平均 IO 大小 (> ~16k) 的 NIC 上，读取和写入方面都得到显著改进。
- 对于较小 IO 大小，启用 SMB 多通道后，会对性能产生 ~10% 的轻微影响。 可以通过将负载分散到多个文件或禁用该功能来缓解这种情况。
- 性能仍受[单个文件限制](storage-files-scale-targets.md#file-scale-targets)约束。

## <a name="optimizing-performance"></a>优化性能

以下提示可帮助优化性能：

- 请确保存储帐户和客户端共置在同一个 Azure 区域中，以减少网络延迟。
- 使用多线程应用程序并将负载分散到多个文件中。
- SMB 多通道的性能优势随着分布负载的文件数增加而增大。
- 高级共享性能受预配共享大小（IOPS/流出量/流入量）和单个文件限制约束。 有关详细信息，请参阅[了解高级文件共享的预配](understanding-billing.md#provisioned-model)。
- 单个 VM 客户端的最大性能仍受 VM 限制约束。 例如，[Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) 可以支持 16,000 MBps（或 2GBps）的最大带宽，来自 VM 的流出量（对存储进行的写入）按流量计费，而流入量（从存储进行的读取）不按流量计费。 文件共享性能受计算机网络限制、CPU、内部存储可用网络带宽、IO 大小、并行性以及其他因素的约束。
- 初始测试通常是一种预热，应放弃其结果并重复测试。
- 如果性能受单个客户端限制并且工作负载仍低于预配共享限制，则可以通过将负载分散到多个客户端来实现更高性能。

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>IOPS、吞吐量与 IO 大小之间的关系

**吞吐量 = IO 大小 * IOPS**

较高的 IO 大小会提高吞吐量，并产生更高的延迟，从而导致净 IOPS 减少。 较小的 IO 大小会提高 IOPS，但会导致较低的净吞吐量和延迟。

## <a name="next-steps"></a>后续步骤

- [在 FileStorage 帐户上启用 SMB 多通道（预览版）](storage-files-enable-smb-multichannel.md)
- 请参阅 [Windows 文档](/azure-stack/hci/manage/manage-smb-multichannel)以了解有关 SMB 多通道的详细信息。
