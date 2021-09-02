---
title: 了解 Azure 文件存储计费 | Microsoft Docs
description: 了解如何解释针对 Azure 文件共享的预配和即用即付的计费模型。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f133600f800881f462583ca5bee2972a5c914fa
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323726"
---
# <a name="understand-azure-files-billing"></a>了解 Azure 文件存储计费
Azure 文件存储提供了两种不同的计费模型：预配和即用即付。 预配模型只适用于高级文件共享，即部署在 FileStorage 存储帐户类型中的文件共享。 即用即付模型只适用于标准文件共享，即部署在常规用途版本 2 (GPv2) 存储帐户类型中的文件共享。 本文介绍了这两种模型的工作原理，以帮助你理解每月的 Azure 文件存储帐单。

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/m5_-GsKv4-o" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        该视频的内容是一段采访讨论，涵盖 Azure 文件存储计费模型的基础知识，包括如何优化 Azure 文件共享以实现尽可能最低的成本，以及如何将 Azure 文件存储与其他本地和云端文件存储产品进行比较。
   :::column-end:::
:::row-end:::

有关 Azure 文件存储的定价信息，请参阅 [Azure 文件存储定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

## <a name="storage-units"></a>存储单元    
Azure 文件存储使用 base-2 度量单位来表示存储容量：KiB、MiB、GiB 和 TiB。 你的操作系统不一定会使用相同的度量单位或计算系统。

### <a name="windows"></a>Windows
Windows 操作系统和 Azure 文件存储都使用 base-2 计算系统来衡量存储容量，但在标记单位时存在差异。 Azure 文件存储使用 base-2 度量单位来标记其存储容量，而 Windows 使用 base-10 度量单位标记其存储容量。 报告存储容量时，Windows 不会将其存储容量的度量单位从 base-2 转换为 base-10。

| 首字母缩写词 | 定义                         | 计价单位     | Windows 的显示方式 |
|---------|------------------------------------|----------|---------------------|
| KiB     | 1,024 字节                        | kibibyte | KB（千字节）       |
| MiB     | 1,024 KiB（1,048,576 字节）        | mebibyte | MB（兆字节）       |
| GiB     | 1024 MiB（1,073,741,824 字节）     | gibibyte | GB（十亿字节）       |
| TiB     | 1024 GiB（1,099,511,627,776 字节） | tebibyte | TB（万亿字节）       |

### <a name="macos"></a>macOS
请参阅 Apple 网站上的 [iOS 和 macOS 报告存储容量的方式](https://support.apple.com/HT201402)，以确定使用的是哪种计算系统。

### <a name="linux"></a>Linux
每个操作系统或软件部分都可使用不同的计算系统。 请参阅其对应的文档，以确定它们报告存储容量的方式。

## <a name="reserve-capacity"></a>保留容量
Azure 文件存储支持存储产能预留，让你可以通过预先承诺存储利用率来获得存储折扣。 应考虑为任何生产工作负载或具有一致内存占用情况的开发/测试工作负载购买预留实例。 购买预留产能时，预留必须指定以下维度：

- 产能大小：产能预留可以是 10 TiB 或 100 TiB，购买更高的产能预留可以获得更多折扣。 可以购买多个预留，包括不同产能大小的预留，以满足工作负载要求。 例如，如果生产部署具有 120 TiB 的文件共享，可以购买一个 100 TiB 预留和两个 10 TiB 预留，以满足总产能要求。
- 期限：预留的购买期限为一年或三年，购买更长的预留期可以获得更多折扣。 
- 层：产能预留的 Azure 文件存储层。 目前，Azure 文件存储的高级、热存储层和冷存储层支持预留。
- 位置：产能预留的 Azure 区域。 产能预留在 Azure 区域的一个子集提供。
- 冗余：产能预留的存储冗余。 可以对所有冗余 Azure 文件存储支持（包括 LRS、ZRS、GRS 和 GZRS）进行预留。

购买产能预留后，现有存储利用率会自动使用该预留。 如果使用的存储量超过了预留的量，则将为产能预留未涵盖的部分支付标价。 预留中不包括事务、带宽和数据传输费用。

有关如何购买存储预留的详细信息，请参阅[通过预留产能优化 Azure 文件存储的成本](files-reserve-capacity.md)。

## <a name="provisioned-model"></a>预配模型
Azure 文件存储将预配模型用于高级文件共享。 在预配业务模型中，可以主动向 Azure 文件存储服务指定存储需求，而不是根据使用的资源进行计费。 这类似于在本地购买硬件，因为当你预配具有一定存储量的 Azure 文件共享时，无论是否使用该存储，都需要为其付费，就像在开始使用空间时，你不会支付本地物理媒体的费用一样。 与在本地购买物理媒体不同，可以根据存储和 IO 性能特征来灵活地纵向扩展或纵向缩放预配的文件共享。

文件共享的预配大小可以随时增加，但只能在自上次增加后的 24 小时之后减小。 等待 24 小时且不要提高配额，然后，可将共享配额降低任意次数，直到再次提高配额为止。 IOPS/吞吐量规模更改将在预配大小更改后的数分钟内生效。

可将预配共享的大小减至所用 GiB 以下。 这样做不会丢失数据，但仍会根据所用大小计费，并且性能（基线 IOPS、吞吐量和突发 IOPS）与预配的共享（而不是所用大小）相符。

### <a name="provisioning-method"></a>设置方法
预配高级文件共享时，可以指定工作负载所需的 GiB。 预配的每个 GiB 均可提供固定比率的附加 IOPS 和吞吐量。 除得到保证的基线 IOPS 之外，每个高级文件共享还支持最大程度的突发限制。 IOPS 和吞吐量的公式如下所示：

| 项目 | “值” |
|-|-|
| 文件共享的最小大小 | 100 GiB |
| 预配单元 | 1 GiB |
| 基线 IOPS 公式 | `MIN(400 + 1 * ProvisionedGiB, 100000)` |
| 突增限制 | `MIN(MAX(4000, 3 * BaselineIOPS), 100000)` |
| 入口速率 | `40 MiB/sec + 0.04 * ProvisionedGiB` |
| 出口速率 | `60 MiB/sec + 0.06 * ProvisionedGiB` |

下表演示了这些预配共享大小公式的几个示例：

| 容量 (GiB) | 基线 IOPS | 突发 IOPS | 出口速率（MiB/秒） | 入口速率（MiB/秒） |
|-|-|-|-|-|
| 100 | 500 | 最大 4,000 | 66 | 44 |
| 500 | 900 | 最大 4,000 | 90 | 60 |
| 1,024 | 1,424 | 最大 4,000 | 122 | 81 |
| 5,120 | 5,520 | 最大 15,360 | 368 | 245 |
| 10,240 | 10,640 | 最大 30,720 | 675 | 450 |
| 33,792 | 34,192 | 最大 100,000 | 2,088 | 1,392 |
| 51,200 | 51,600 | 最大 100,000 | 3,132 | 2,088 |
| 102,400 | 100,000 | 最大 100,000 | 6,204 | 4,136 |

有效的文件共享性能与计算机网络限制、可用网络带宽、IO 大小、并行度和其他许多因素相关。 例如，根据使用 8 KiB 读/写 IO 大小的内部测试，一个通过 SMB 连接到高级文件共享的未启用 SMB 多通道的 Windows 虚拟机（即标准 F16s_v2）可以实现 20K 读取 IOPS 和 15K 写入 IOPS。 读/写 IO 大小为 512 MiB 时，同一 VM 可以实现 1.1 GiB/秒的出口吞吐量和 370 MiB/秒的入口吞吐量。 如果对高级共享启用了 SMB 多通道，则同一客户端可以实现最高 \~3 倍的性能。 为了实现最大性能规模，请[启用 SMB 多通道](storage-files-enable-smb-multichannel.md)，并将负载分散到多个 VM。 有关一些常见性能问题和解决方法，请参阅 [SMB 多通道性能](storage-files-smb-multichannel-performance.md)和[故障排除指南](storage-troubleshooting-files-performance.md)。

### <a name="bursting"></a>突发
如果工作负荷需要额外的性能来满足峰值需求，则你的共享可以使用突发积分，从而超越共享基线 IOPS 限制来提供满足需求所需的共享性能。 高级文件共享可将其 IOPS 突发到 4000 或其基线 IOPS 的 3 倍（以二者中较大的值为准）。 突发是自动进行的，根据额度系统运行。 突发采用“尽力而为”的原则，并且突发限制不是一个保证，文件共享可以在 60 分钟的最大持续时间内最多突发到该限制。

每当文件共享的流量低于基线 IOPS 时，额度将累积在突发桶中。 例如，一个 100 GiB 的共享有 500 个基线 IOPS。 如果共享中的实际流量在特定的 1 秒间隔内为 400 个 IOPS，则会将 400 个未使用的 IOPS 积分计入到突发桶。 类似地，一个空闲的 1 TiB 共享会在 1424 个 IOPS 上累积突发积分。 以后在操作要超过基线 IOPS 时，将使用这些积分。

每当共享超过基线 IOPS 并且在突发桶中有积分时，就会以允许的最大峰值突发率进行突发。 只要还有积分，共享就可以继续突发，最多可达 60 分钟的持续时间，但这取决于所累积的突发积分的数量。 超出基线 IOPS 的每个 IO 会消耗一个积分，一旦耗尽所有积分，共享就会返回到基线 IOPS。

共享额度具有三种状态：

- 应计：文件共享使用的 IOPS 小于基线 IOPS。
- 下降（在文件共享使用的 IOPS 超过基线 IOPS 并且文件共享处于突发模式时）。
- 恒定（在文件共享只使用基线 IOPS 时，不会累积或使用任何额度）。

新文件共享最初在其突发桶中包含所有额度。 如果由于服务器的限制，导致共享 IOPS 低于基线 IOPS，则不会对突发额度进行应计。

## <a name="pay-as-you-go-model"></a>即用即付模型
Azure 文件存储对标准文件共享使用即用即付业务模型。 在即用即付业务模型中，你支付的金额是由实际使用量（而不是预配量）决定的。 概括来讲，你需要为存储在磁盘上的数据量支付成本，然后根据你对此数据的使用情况支付一组额外事务的费用。 即用即付模型可能具有成本效益，因为无需过度预配来考虑未来的增长或性能需求，也无需在工作负载是随时间变化的数据占用时取消预配。 但另一方面，即用即付模型也很难作为预算流程的一部分进行计划，因为即用即付计费模型是由最终用户使用量驱动的。

### <a name="differences-in-standard-tiers"></a>标准层的区别
当你创建标准文件共享时，可以在事务优化层、热层和冷层之间进行选择。 所有这三层都存储在完全相同的标准存储硬件上。 这三个层级的主要区别是静态数据存储价格（层级越冷，价格越低）和事务价格（层级越冷，价格越高）。 这意味着：

- 顾名思义，事务优化会优化高事务工作负载的价格。 事务优化的静态数据存储价格最高，但事务价格最低。
- 热层适合不涉及大量事务的活跃工作负载；与事务优化相比，它的静态数据存储价格稍微低一些，但事务价格要高一些。 可以把它看作是事务优化层和冷层之间的中间地带。
- 冷层为没有大量活动的工作负载优化了价格，提供了最低的静态数据存储价格，但事务价格最高。

如果你将不经常访问的工作负载放在事务优化层中，那么对于在一个月内针对你的共享执行的几次事务，你几乎不用支付任何费用，但你需要支付很高金额的数据存储成本。 如果你将此同一共享迁移到冷层中，则仍然几乎不用支付事务成本，这仅仅是因为你很少为此工作负载执行事务，但冷层的数据存储价格要便宜得多。 为用例选择适当的层可以大大降低你的成本。

同样地，如果你将经常访问的工作负载放在冷层中，则支付的事务成本更多，支付的数据存储成本更少。 这可能会导致一种情况，即事务价格上涨带来的成本增加超过了数据存储价格下降带来的金额节省，导致你在冷层中支付的成本多于事务优化层。 对于某些使用级别，虽然热层是最具成本效益的层，但冷层比事务优化层更昂贵。

对于标准文件共享，将由工作负载和活动级别来确定最具成本效益的层级。 在实践中，选择最具成本效益的层的最佳方法是，查看共享的实际资源使用量（存储的数据量、写入的事务量等）。

### <a name="what-are-transactions"></a>什么是事务？
事务是针对 Azure 文件存储的操作或请求，用于上传、下载或以其他方式处理文件共享的内容。 对文件共享执行的每项操作都转换为一个或多个事务，而对使用即用即付计费模型的标准共享执行的每项操作则转换为事务成本。

共有五种基本的事务类别：写入、列出、读取、其他和删除。 通过 REST API 或 SMB 完成的所有操作都归入以下四个类别之一：

| 操作类型 | 写入事务 | 列出事务 | 读取事务 | 其他事务 | 删除事务 |
|-|-|-|-|-|-|
| 管理操作 | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li><li>`SetShareACL`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| 数据操作 | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1 只适用于使用预配计费模型的高级文件共享，事务不会影响高级文件共享的计费。

## <a name="file-storage-comparison-checklist"></a>文件存储比较清单
若要正确评估 Azure 文件存储的成本（与其他文件存储选项相比），请考虑以下问题：

- 如何为存储、IOPS 和带宽付费？  
    对于 Azure 文件存储，所使用的计费模型取决于是部署[高级](#provisioned-model)还是[标准](#pay-as-you-go-model)文件共享。 大多数云解决方案的模型都符合预配存储（价格固定且简单）或即用即付存储（只为实际使用量付费）的原则。 对于预配的模型，值得特别关注的是最小预配份额大小、预配单位以及增加和减少预配的功能。 

- 如何实现存储复原和冗余？  
    使用 Azure 文件存储时，存储复原和冗余已融入到产品中。 所有层和冗余级别都可确保数据高度可用，你的数据至少有三个可供访问的副本。 考虑其他文件存储选项时，请考虑存储复原和冗余是内置的功能还是必须自行组装的功能。 

- 需要管理什么？  
    使用 Azure 文件存储时，管理的基本单位是存储帐户。 其他解决方案可能需要额外的管理，例如操作系统更新或虚拟资源管理（VM、磁盘、网络 IP 地址等）。

- 备份成本有哪些？  
    借助 Azure 文件存储，可以轻松地启用 Azure 备份集成，并将备份存储作为成本共享的一部分进行计费（备份作为差异快照存储）。 其他解决方案可能需要备份软件许可和额外的备份存储费用。

## <a name="see-also"></a>另请参阅
- [Azure 文件存储定价页](https://azure.microsoft.com/pricing/details/storage/files/)。
- [计划 Azure 文件存储部署](storage-files-planning.md)和[计划 Azure 文件同步部署](../file-sync/file-sync-planning.md)。
- [创建文件共享](storage-how-to-create-file-share.md)和[部署 Azure 文件同步](../file-sync/file-sync-deployment-guide.md)。