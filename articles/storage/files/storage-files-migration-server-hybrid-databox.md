---
title: 使用 Azure DataBox 将数据迁移到 Azure 文件同步
description: 以脱机方式迁移与 Azure 文件同步兼容的大量数据。避免文件冲突，并使文件共享与服务器上的最新更改保持同步，以实现零停机时间的云迁移。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 1fc3f423c1ca02c6acb995ab4469d7d33607000f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255535"
---
# <a name="migrate-data-offline-to-azure-file-sync-with-azure-data-box"></a>使用 Azure Data Box 将数据脱机迁移到 Azure 文件同步

本迁移文章是适用于关键字“Azure 文件同步”和“Azure Data Box”的几篇文章之一。 请检查本文是否适用于你的场景：

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept.png" alt-text="本迁移指南中所述的三个依序步骤的示意图。插图旁边的栏详细描述了每个步骤。" lightbox="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept-expanded.png":::
    :::column-end:::
    :::column:::
        > [!div class="checklist"]
        > * 数据源：将在其中安装 Azure 文件同步并指向原始文件集的 Windows Server 2012 R2 或更高版本。
        > * 迁移路线：Windows Server 2012 R2 或更高版本 &rArr; Data Box &rArr; Azure 文件共享 &rArr; 与 Windows Server 原始文件位置同步
        > * 在本地缓存文件：是，最终目标是实现 Azure 文件同步部署，以将文件从其现有位置同步。 
        :::column-end:::
:::row-end:::

使用 Azure Data Box 是将大量数据从本地 Windows Server 移到不同的 Azure 文件共享，然后选择性地在原始源服务器上添加 Azure 文件同步的一种可行途径。

可以使用不同的迁移路径，但必须遵循正确的迁移路径：

* 数据驻留在 Windows Server 2012 R2 或更高版本上，你打算在该服务器上安装 AFS 并同步原始位置。 在此方案中，你不希望上传所有文件，而是想要改用 Data Box，然后对持续进行的更改使用文件同步。 如果你的方案是这样，可以参考本文中所述的迁移路径。
* 数据所在的源是你不希望或无法在其上安装 AFS 的源。 例如，数据位于 NAS（网络附加存储）或其他服务器上。 你希望新建一个空服务器并在该服务器上使用 Azure 文件同步。 如果你的方案是这样，则本迁移指南并不适合你。 对于这种方案，请查看：[通过 Data Box 从 NAS 迁移到 Azure 文件同步](../files/storage-files-migration-nas-hybrid-databox.md)，或者在[迁移概述](../files/storage-files-migration-overview.md)页上找到最适合你方案的指南。
* 对于所有其他方案，请查看 [Azure 文件共享迁移指南表](../files/storage-files-migration-overview.md)。 此概述页为所有迁移方案提供了一个很好的起点。
 
## <a name="migration-overview"></a>迁移概述

迁移过程包括多个阶段。 你将需要：
- 部署存储帐户和文件共享。
- 部署一个或多个 Azure Data Box 设备，以移动 Windows Server 2012 R2 或更高版本中的数据。 
- 为 Azure 文件同步配置权威上传。

以下各部分详细介绍了迁移过程的各个阶段。

> [!TIP]
> 如果你返回到本文，请使用屏幕右侧的导航栏转到你离开的迁移阶段。

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>第 1 阶段：确定需要多少个 Azure 文件共享

在本迁移指南中，必须继续使用包含你的文件的本地直接附加存储 (DAS)。 Data Box 将从该位置馈送，Azure 文件同步也会设置到该位置上。 NAS（网络附加存储）不适用于此迁移路径。

可以通过设置 Azure 文件同步同步组来确定要同步哪些内容，其中的每个同步组确定了要在哪一组文件之间同步。 每个同步组至少有一个服务器位置（称为服务器终结点）和一个 Azure 文件共享（称为云终结点） 。 

可以将一组文件的子路径同步到每个文件自己的 Azure 文件共享。 这意味着需要设置多个同步组以完全涵盖一组文件。 本部分的余下内容将介绍具体的方法。 如果需要重新构建数据，则必须首先执行此操作，然后再继续遵循本指南订购 Data Box 或设置同步。 

> [!CAUTION]
> 在开始迁移之前，必须确保文件和文件夹结构符合长期需求。 在迁移过程中避免任何不必要的文件夹重新构建。 这会降低初次使用 Azure Data Box 将文件批量传输到 Azure 所带来的积极影响。

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>第 2 阶段：部署 Azure 存储资源

在此阶段中，请查阅第 1 阶段中的映射表，并使用它来预配正确数量的 Azure 存储帐户和其中的文件共享。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>第 3 阶段：确定需要多少台 Azure Data Box 设备

请仅在完成上一阶段后，才开始执行此步骤。 此时应创建 Azure 存储资源（存储帐户和文件共享）。 订购 Data Box 时，需指定 Data Box 要将数据移到哪些存储帐户。

在此阶段中，需要将上一阶段中迁移计划的结果映射到可用 Data Box 选项的各种限制。 这些考虑事项将帮助你针对将 NAS 共享移到 Azure 文件共享时要选择哪些 Data Box 选项以及需要多少台这种设备而进行规划。

若要确定所需设备的数量及其类型，请考虑以下重要限制：

* 任一 Azure Data Box 设备可将数据移到最多 10 个存储帐户中。 
* 每个 Data Box 选项都有其各自的可用容量。 请参阅 [Data Box 选项](#data-box-options)。

请查阅迁移计划以找到你决定创建的存储帐户数以及每个帐户中的共享数。 然后，查看 NAS 上每个共享的大小。 通过组合使用这些信息，你可以进行优化并决定哪台设备应当将数据发送到哪些存储帐户。 可以使用两台 Data Box 设备将文件移到同一存储帐户中，但不要将单个文件共享的内容拆分到两台 Data Box 设备中。

### <a name="data-box-options"></a>Data Box 选项

对于标准迁移，请选择以下 Data Box 选项中的一个或多个： 

* Data Box Disk。
  Microsoft 将向你寄送一到五块 SSD 磁盘，其中每个磁盘的容量为 8 TiB，所有磁盘的最大总容量为 40 TiB。 由于加密和文件系统开销，可用容量大约少 20%。 有关详细信息，请参阅 [Data Box Disk 文档](../../databox/data-box-disk-overview.md)。
* Data Box.
  此选项最常用。 Microsoft 将向你寄送一台加固型 Data Box 设备，该设备的工作方式类似于 NAS。 它的可用容量为 80 TiB。 有关详细信息，请参阅 [Data Box 文档](../../databox/data-box-overview.md)。
* Data Box Heavy。
  此选项提供一台带轮子的加固型 Data Box 设备，其工作方式类似于 NAS。 其容量为 1 PiB。 由于加密和文件系统开销，可用容量大约少 20%。 有关详细信息，请参阅 [Data Box Heavy 文档](../../databox/data-box-heavy-overview.md)。

## <a name="phase-4-copy-files-onto-your-data-box"></a>第 4 阶段：将文件复制到 Data Box

当 Data Box 到货时，需要将 Data Box 设置为与 NAS 设备直通。 请按照订购的 Data Box 类型的设置文档进行操作：

* [设置 Data Box](../../databox/data-box-quickstart-portal.md)。
* [设置 Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)。
* [设置 Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)。

根据 Data Box 的类型，可以使用 Data Box 复制工具。 目前，我们不建议使用这些工具迁移到 Azure 文件共享，因为它们不会以全保真度将文件复制到 Data Box。 请改用 Robocopy。

当 Data Box 到货时，其上已预先预配了可供在订购时指定的每个存储帐户使用的 SMB 共享。

* 如果你的文件进入高级 Azure 文件共享，则每个高级“文件存储”存储帐户都将有一个 SMB 共享。
* 如果文件进入标准存储帐户，则每个标准（GPv1 和 GPv2）存储帐户都将有三个 SMB 共享。 只有以 `_AzFiles` 结尾的文件共享才与迁移相关。 请忽略任何块和页 blob 共享。

按照 Azure Data Box 文档中的步骤进行操作：

1. [连接到 Data Box](../../databox/data-box-deploy-copy-data.md)。
1. 将数据复制到 Data Box。
1. [准备 Data Box 以上传到 Azure](../../databox/data-box-deploy-picked-up.md)。

链接的 Data Box 文档指定了一个 Robocopy 命令。 该命令不适合用于保持文件和文件夹的完全保真度。 请改用以下命令：

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-5-deploy-the-azure-file-sync-cloud-resource"></a>第 5 阶段：部署 Azure 文件同步云资源

在继续学习本指南之前，请等待所有文件均已进入正确的 Azure 文件共享。 传输和引入 Data Box 数据的过程需要一段时间。

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-6-deploy-the-azure-file-sync-agent"></a>第 6 阶段：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-7-configure-azure-file-sync-on-the-existing-windows-server"></a>第 7 阶段：在现有的 Windows Server 上配置 Azure 文件同步

要完成此过程，已注册的本地 Windows Server 实例必须准备就绪，并已连接到 Internet。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

:::row:::
    :::column:::
        [![显示了 Azure 门户中的“创建服务器终结点”向导部分。突出显示了一个复选框，它与使用数据为 Azure 文件共享播种的方案对应。如果将 AFS 连接到先前从其向 Data Box 进行复制的同一本地位置，请选中此框。](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox.png)](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        进入“创建服务器终结点”向导后，利用文件夹路径下提供的复选框。 请仅在输入了一个指向与可在 Azure 文件共享（Data Box 已将此命名空间的文件和文件夹移到其中）中找到的文件和文件夹结构相同的路径时，才进行此选择。 </br> </br> 如果文件夹层次结构不匹配，则此问题将作为无法自动解决的差异出现。 请避免出现不匹配的情况，否则为 Data Box 流程所做的任何投入都不会产生回报。 Azure 文件共享中的所有数据将被删除。 需要从本地服务器上传所有数据。 目录结构必须匹配，才能获得使用 Azure Data Box 进行批量迁移以及使用服务器中的最新更改无缝更新云共享所带来的好处。
:::column-end:::
:::row-end:::

> [!NOTE]
> 启用此复选框会将“初始同步”模式设置为“使用此服务器的路径中的内容权威覆盖 Azure 文件共享中的文件和文件夹”。 此选项仅适用于同步组中的第一个服务器终结点。

为此新服务器终结点配置权威上传后，可以选择性地启用云分层。

云分层是一项 Azure 文件同步功能，它允许本地服务器的存储容量小于云中的存储容量但本地服务器仍然有完整的命名空间可用。 在本地有用的数据也缓存在本地，以实现快速访问性能。 云分层是可选的。 可为每个 Azure 文件同步服务器终结点单独设置此功能。 使用此功能可使本地存储占用空间保持固定，同时仍为用户提供本地性能缓存，并在云中存储冷数据。

请查看[云分层概述](../file-sync/file-sync-cloud-tiering-overview.md)了解详细信息，或者更详细地了解可用于在本地服务器上微调所要缓存/分层的内容的各种[云分层策略](../file-sync/file-sync-cloud-tiering-policy.md)。

## <a name="complete-your-migration"></a>完成迁移

创建服务器终结点后，将开始进行同步。 但是，同步需要枚举（发现）你通过 Azure Data Box 移到 Azure 文件共享中的文件和文件夹。 可能需要很长时间才能将最新的服务器更改同步到云中，具体取决于命名空间的大小。 你的用户不会受到影响，并且可以继续使用服务器上的数据。 此策略可实现零停机时间的云迁移。

针对需要进行配置以便同步的所有 Azure 文件共享/服务器位置，请重复相关步骤来创建同步组并添加匹配的服务器文件夹作为服务器终结点。 现在，你已使用 Azure Data Box 将文件移到多个 Azure 文件共享中。 创建了用于将本地数据连接到这些 Azure 文件共享的所有服务器终结点之后，迁移即告完成。

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享和 Azure 文件同步，有更多的知识有待探索。以下文章可帮助你了解高级选项和最佳做法。 其中还提供了故障排除方面的帮助。 在适当的情况下，这些文章会包含 [Azure 文件共享文档](storage-files-introduction.md)的链接。

* [迁移概述](storage-files-migration-overview.md)
* [规划 Azure 文件同步部署](../file-sync/file-sync-planning.md)
* [创建文件共享](storage-how-to-create-file-share.md)
* [Azure 文件同步排除故障](../file-sync/file-sync-troubleshoot.md)
