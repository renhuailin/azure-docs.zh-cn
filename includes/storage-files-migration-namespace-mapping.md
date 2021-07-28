---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: ffa5c899686596256622a6ea334e486c52ec710d
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2021
ms.locfileid: "109645302"
---
在此步骤中，你将决定需要多少个 Azure 文件共享。 单个 Windows Server 实例（或群集）可以同步最多 30 个 Azure 文件共享。

卷上可能有更多文件夹当前作为 SMB 共享在本地共享给用户和应用。 描述此场景的最简单方法是设想 1:1 映射到 Azure 文件共享的本地共享。 如果共享数目够小，即单个 Windows Server 实例低于 30 个时，建议使用 1:1 映射。

如果共享数目超过 30 个，通常不需要将本地共享 1:1 映射到 Azure 文件共享。 请考虑以下选项。

#### <a name="share-grouping"></a>共享分组

例如，如果人力资源 (HR) 部门有 15 个共享，则可以考虑将所有 HR 数据存储在一个 Azure 文件共享中。 将多个本地共享存储在一个 Azure 文件共享中不会阻止你在本地 Windows Server 实例上创建常规的 15 个 SMB 共享。 这只是意味着将这 15 个共享的根文件夹组织为公用文件夹下的子文件夹。 然后将此公用文件夹同步到 Azure 文件共享。 这样，这组本地共享便只需要云中的单个 Azure 文件共享。

#### <a name="volume-sync"></a>卷同步

Azure 文件同步支持将卷的根同步到 Azure 文件共享。 如果同步卷根，则所有子文件夹和文件都会转到相同的 Azure 文件共享。

同步卷的根并非总是最佳选项。 同步多个位置有很多好处。 例如，这样做可帮助降低每个同步范围的项目数。 我们测试了 Azure 文件共享和 Azure 文件同步，每个共享有 1 亿个项目（文件和文件夹）。 但是，在单个共享中，最好试着将数目控制在 2000 万或 3000 万以下。 使用较少数量的项目设置 Azure 文件同步不只是对文件同步有利。在下面这些场景中，项目数量较少的优势也是显而易见的：

* 对云内容的初始扫描可以更快地完成，这会减少命名空间出现在启用 Azure 文件同步的服务器上的等待时间。
* 从 Azure 文件共享快照进行云端还原会更快。
* 本地服务器的灾难恢复可以显著提高速度。
* 可以更快地检测和同步在 Azure 文件共享中直接进行的更改（在同步之外）。

> [!TIP]
> 如果你不知道你有多少个文件和文件夹，请使用 JAM Software GmbH 提供的 TreeSize 工具来确认。

#### <a name="a-structured-approach-to-a-deployment-map"></a>部署映射的结构化方法

在稍后的步骤中部署云存储之前，请务必在本地文件夹与 Azure 文件共享之间创建映射。 此映射会告知，你要预配哪些 Azure 文件同步“同步组”资源以及具体数量。 同步组会将 Azure 文件共享和服务器上的文件夹关联在一起，并建立同步连接。

若要确定需要多少个 Azure 文件共享，请参考以下限制和最佳做法。 这样做有助于优化映射。

* 安装了 Azure 文件同步代理的服务器可与最多 30 个 Azure 文件共享同步。
* Azure 文件共享部署在存储帐户中。 这样安排会使存储帐户成为 IOPS 和吞吐量等性能指标的缩放目标。

  理论上，一个标准 Azure 文件共享可以让存储帐户可提供的最大性能达到饱和。 如果将多个共享放置在单个存储帐户中，会为这些共享创建一个 IOPS 和吞吐量共享池。 如果计划仅将 Azure 文件同步附加到这些文件共享，则将多个 Azure 文件共享分组到相同存储帐户中不会产生问题。 请查看 Azure 文件共享性能目标，深入了解相关指标。 这些限制不适用于高级存储，高级存储为每个共享显式预配并保证性能。

  如果计划将应用提升到将在本机使用 Azure 文件共享的 Azure，则可能需要提高 Azure 文件共享的性能。 如果现在甚至以后都可以这样使用，最好在其自己的存储帐户中创建一个标准 Azure 文件共享。
* 每个 Azure 区域的每个订阅的存储帐户限制为 250 个。

> [!TIP]
> 考虑到这一点，通常需要将卷上的多个顶级文件夹分组到一个新的公用根目录中。 随后将这个新的根目录以及分组到其中的所有文件夹都同步到单个 Azure 文件共享。 此方法使你可以维持在每台服务器 30 个 Azure 文件共享同步的限制范围内。
>
> 在公用根下进行这种分组不会对数据访问产生影响。 你的 ACL 将保持原样。 只需要调整你在本地服务器文件夹上的任何共享路径（如 SMB 或 NFS 共享），现在你已将这些文件夹更改为公用根。 无其他更改。

> [!IMPORTANT]
> Azure 文件同步最重要的缩放矢量是需要同步的项目数（文件和文件夹）。 如需更多详细信息，请参阅 [Azure 文件同步缩放目标](../articles/storage/files/storage-files-scale-targets.md#azure-file-sync-scale-targets)。

最佳实践是使每个同步范围的项目数保持较低。 这是在将文件夹映射到 Azure 文件共享时要考虑的重要因素。 Azure 文件同步对每个共享使用 1 亿个项目（文件和文件夹）进行测试。 但是，在单个共享中，最好将项目数控制在 2000 万或 3000 万以下。 如果开始超过这些数量，请将命名空间拆分为多个共享。 如果一直大致低于这些数量，则可以继续将多个本地共享分组到相同 Azure 文件共享中。 这种做法可为你提供增长空间。

在这种情况下，可能能够以逻辑方式将一组文件夹同步到相同的 Azure 文件共享（使用前面提到的新的公用根文件夹方法）。 但是，重新分组文件夹使它们同步到两个（而不是一个）Azure 文件共享中可能会更好。 可以使用此方法使每个文件共享的文件和文件夹数在服务器间保持平衡。 还可以拆分本地共享并在更多的本地服务器上同步，从而增加每个额外服务器再同步 30 个 Azure 文件共享的能力。

#### <a name="create-a-mapping-table"></a>创建映射表

:::row:::
    :::column:::
        [![显示映射表的示例的关系图。下载以下文件以体验并使用此图像的内容。](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        使用前面的信息来确定所需的 Azure 文件共享数目，以及现有数据的哪些部分最终会出现在哪个 Azure 文件共享中。
        
        创建一个表来记录你的想法，这样你就可以在需要时进行参考。 保持井然有序非常重要，因为当你同时预配许多 Azure 资源时，可能会很容易丢失映射计划的详细信息。 下载以下 Excel 文件用作模板，以帮助创建映射。

[//]: # (HTML 似乎是在同一行中使用工作映像分析和文本/超链接完成嵌套双列表添加的唯一方法。)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Excel icon that sets the context for the download.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">下载命名空间映射模板。</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
