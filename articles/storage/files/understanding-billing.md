---
title: 了解 Azure 文件计费 |Microsoft Docs
description: 了解如何解释 Azure 文件共享的预配和即用即付计费模式。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/20/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 19ecbea70d9cb6b8cc31c72ed3c1294cd137ce93
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632472"
---
# <a name="understanding-azure-files-billing"></a>了解 Azure 文件计费
Azure 文件提供两种不同的计费模型：预配和即用即付。 预配的模型仅适用于高级文件共享，这是部署在 **FileStorage** 存储帐户类型中的文件共享。 即用即付模型仅适用于标准文件共享，标准文件共享是部署在 **常规用途版本 2** 中的文件共享 (GPv2) 存储帐户类型。 本文介绍如何使用这两个模型来帮助你了解每月的 Azure 文件帐单。

Azure 文件的当前定价可在 [Azure 文件定价页](https://azure.microsoft.com/pricing/details/storage/files/)中找到。

## <a name="provisioned-model"></a>预配模型
Azure 文件使用预配的高级文件共享模型。 在已设置的业务模型中，你主动指定了你的存储要求所需的 Azure 文件服务，而不是根据你使用的内容进行计费。 这类似于在本地购买硬件，在预配具有一定量的存储的 Azure 文件共享时，无论是否使用该存储，都需要支付此存储的费用，就像在开始使用空间时，不会开始支付本地物理媒体成本。 与购买本地物理媒体不同，预配的文件共享可根据存储和 IO 性能特征动态增加或缩减。

预配高级文件共享时，需要指定工作负荷所需的 Gib 等数量。 你预配的每个 GiB 都可为你提供更高的 IOPS 和吞吐量。 除了可保证的基线 IOPS 之外，每个高级文件共享都支持最大程度的突发。 IOPS 和吞吐量的公式如下所示：

- 基线 IOPS = 400 + 1 * 预配的 GiB。 （最大可为 100,000 IOPS）。
- 突发限制 = 最大 (4000，3 * 基准 IOPS) 。
- 出口速率 = 60 MiB/s + 0.06 * 预配的 GiB。
- 入口速率 = 40 MiB/s + 0.04 * 预配的 GiB。

可以在任何时间增加文件共享的预配大小，但只能在自上次增加后的24小时后减少。 等待 24 小时且不要提高配额，然后，可将共享配额降低任意次数，直到再次提高配额为止。 在预配大小更改后的几分钟内，IOPS/吞吐量规模更改将生效。

可将预配共享的大小减至所用 GiB 以下。 这样做不会丢失数据，但仍会根据所用大小计费，并且性能（基线 IOPS、吞吐量和突发 IOPS）与预配的共享（而不是所用大小）相符。

下表演示了这些预配共享大小公式的几个示例：

|容量 (GiB) | 基线 IOPS | 突发 IOPS | 出口速率（MiB/秒） | 入口速率（MiB/秒） |
|---------|---------|---------|---------|---------|
|100         | 500     | 最大 4,000     | 66   | 44   |
|500         | 900     | 最大 4,000  | 90   | 60   |
|1,024       | 1,424   | 最大 4,000   | 122   | 81   |
|5,120       | 5,520   | 最大 15,360  | 368   | 245   |
|10,240      | 10,640  | 最大 30,720  | 675   | 450   |
|33,792      | 34,192  | 最大 100,000 | 2,088 | 1,392   |
|51,200      | 51,600  | 最大 100,000 | 3,132 | 2,088   |
|102,400     | 100,000 | 最大 100,000 | 6,204 | 4,136   |

有效的文件共享性能受到计算机网络限制、可用网络带宽、IO 大小、并行性，以及许多其他因素的限制。 例如，基于具有8个 KiB 读取/写入 IO 大小的内部测试，不启用 SMB 多通道的单个 Windows 虚拟机， *标准 F16s_v2*，通过 smb 连接到高级文件共享可以实现20K 读取 Iops 和15K 写入 iops。 读/写 IO 大小为 512 MiB 时，同一 VM 可以实现 1.1 GiB/秒的出口吞吐量和 370 MiB/秒的入口吞吐量。 \~如果对高级共享启用了 SMB 多通道，则同一客户端的性能最高可达3倍。 若要实现最大性能规模，请 [启用 SMB 多通道](storage-files-enable-smb-multichannel.md) 并将负载分散到多个 vm。 有关一些常见性能问题和解决方法，请参阅 [SMB 多通道性能](storage-files-smb-multichannel-performance.md) 和 [故障排除指南](storage-troubleshooting-files-performance.md) 。

### <a name="bursting"></a>突发
如果工作负荷需要额外的性能来满足峰值需求，则你的共享可以使用突发积分，从而超越共享基线 IOPS 限制来提供满足需求所需的共享性能。 高级文件共享可将其 IOPS 突发到 4000 或其基线 IOPS 的 3 倍（以二者中较大的值为准）。 突发是自动进行的，根据额度系统运行。 突发会尽力工作，并且突发限制并不保证，文件 *共享的最* 大持续时间限制为60分钟。

每当文件共享的流量低于基线 IOPS 时，额度将累积在突发桶中。 例如，一个 100 GiB 的共享有 500 个基线 IOPS。 如果共享中的实际流量在特定的 1 秒间隔内为 400 个 IOPS，则会将 400 个未使用的 IOPS 积分计入到突发桶。 类似地，一个空闲的 1 TiB 共享会在 1424 个 IOPS 上累积突发积分。 以后在操作要超过基线 IOPS 时，将使用这些积分。

每当共享超过基线 IOPS 并且在突发桶中有积分时，就会以允许的最大峰值突发率进行突发。 只要还有积分，共享就可以继续突发，最多可达 60 分钟的持续时间，但这取决于所累积的突发积分的数量。 超出基线 IOPS 的每个 IO 会消耗一个积分，一旦耗尽所有积分，共享就会返回到基线 IOPS。

共享额度具有三种状态：

- 应计：文件共享使用的 IOPS 小于基线 IOPS。
- 下降（在文件共享使用的 IOPS 超过基线 IOPS 并且文件共享处于突发模式时）。
- 如果文件共享完全使用基线 IOPS，则不会有任何信用额度或使用信用额度。

新文件共享最初在其突发桶中包含所有额度。 如果由于服务器的限制，导致共享 IOPS 低于基线 IOPS，则不会对突发额度进行应计。

## <a name="pay-as-you-go-model"></a>即用即付模型
Azure 文件使用标准文件共享的即用即付业务模式。 在即用即付业务模式中，你支付的金额取决于实际使用的数量，而不是基于预配量。 在较高级别上，你需要为存储在磁盘上的数据量支付费用，然后根据你对数据的使用情况支付额外的一组事务。 即用即付模型可能是经济高效的，因为无需考虑到未来的增长或性能要求，或者，如果工作负荷的数据占用量随时间而变化，则不需要 overprovision。 另一方面，即用即付模型也很难作为预算过程的一部分进行规划，因为即用即付计费模型由最终用户消耗驱动。）。

### <a name="differences-in-standard-tiers"></a>标准层之间的差异
创建标准文件共享时，可以在 "优化"、"热" 和 "冷" 层之间进行选择。 这三个层存储在完全相同的标准存储硬件上。 这三个层级的主要区别是静态数据存储价格（层级越冷，价格越低）和事务价格（层级越冷，价格越高）。 这意味着：

- 顾名思义，事务优化会优化高事务工作负载的价格。 事务优化的静态数据存储价格最高，但事务价格最低。
- 热层适合不涉及大量事务的活跃工作负载；与事务优化相比，它的静态数据存储价格稍微低一些，但事务价格要高一些。 将其视为事务优化层和冷层之间的中间部分。
- 冷优化不包含大量活动的工作负荷的价格，提供最低程度的静态数据存储价格，但最高的事务价格。

如果你将不常访问的工作负载放在事务优化层中，你将在一个月内为你的共享进行交易，但你需要支付较高的数据存储费用。 如果要将此同一共享迁移到冷层，只需为事务成本付费，只需对此工作负荷进行较少的事务，但酷层的数据存储价格要低得多。 为用例选择适当的层可大大降低成本。 为用例选择适当的层可大大降低成本。

同样，如果你将高度访问的工作负载放在冷层中，你将需要支付更多的事务成本，但不会降低数据存储成本。 这可能会导致这样一种情况：交易价格增加的成本超过了减少的数据存储价格的节省量，因此，你可以在更多的情况下，在优化交易时支付更多资金。 虽然热层将是最经济高效的层，但很多情况下，冷却层比优化事务更昂贵。

对于标准文件共享，将由工作负载和活动级别来确定最具成本效益的层级。 在实践中，选择最经济高效的层的最佳方法就是查看 (存储、写入事务等 ) 的共享的实际资源消耗情况。

### <a name="what-are-transactions"></a>什么是事务？
事务是针对 Azure 文件的操作或请求，用于上传、下载或以其他方式处理文件共享的内容。 对文件共享执行的每个操作都将转换为一个或多个事务，并且在使用即用即付计费模型的标准共享上转换为事务成本。

有五种基本事务类别： "写入"、"列出"、"读取"、"其他" 和 "删除"。 通过 REST API 或 SMB 完成的所有操作都将分段为以下4个类别之一：

| 操作类型 | 写入事务 | 列出事务 | 读取事务 | 其他事务 | 删除事务 |
|-|-|-|-|-|-|
| 管理操作 | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| 数据操作 | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1 仅适用于使用预配计费模型的高级文件共享，而不会影响高级文件共享的计费。

## <a name="see-also"></a>另请参阅
- [Azure 文件定价页](https://azure.microsoft.com/pricing/details/storage/files/)。
- [规划 Azure 文件部署](./storage-files-planning.md) 和 [规划 Azure 文件同步部署](./storage-sync-files-planning.md)。
- [创建文件共享](./storage-how-to-create-file-share.md) 并 [部署 Azure 文件同步](./storage-sync-files-deployment-guide.md)。