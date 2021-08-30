---
title: 将 StorSimple 8000 系列迁移到 Azure 文件同步
description: 了解如何将 StorSimple 8100 或 8600 设备迁移到 Azure 文件同步。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f51f4b9a838a7a08259b280c4d9cd185b42e588b
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112116983"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>将 StorSimple 8100 和 8600 迁移到 Azure 文件同步

StorSimple 8000 系列的代表是 8100 或 8600 本地物理设备及其云服务组件。 可以将这些设备中的任一设备的数据迁移到 Azure 文件同步环境。 Azure 文件同步是 StorSimple 设备可迁移到的、默认的、策略性的长期 Azure 服务。

StorSimple 8000 系列将于 2022 年 12 月[停用](/lifecycle/products/azure-storsimple-8000-series)。 请务必尽快开始进行迁移规划。 本文提供成功迁移到 Azure 文件同步所需的背景知识和迁移步骤。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="phase-1-prepare-for-migration"></a>阶段 1 - 准备迁移

此部分包含开始从 StorSimple 卷迁移到 Azure 文件共享时应执行的步骤。

### <a name="inventory"></a>库存

开始规划迁移时，请首先确定需要迁移的所有 StorSimple 设备和卷。 完成该任务后，就可以决定最佳迁移路径。

* StorSimple 物理设备（8000 系列）使用此迁移指南。
* 虚拟设备 [StorSimple 1200 系列使用其他迁移指南](storage-files-migration-storsimple-1200.md)。

### <a name="migration-cost-summary"></a>迁移成本摘要

可以免费通过 StorSimple 数据管理器资源中的迁移作业从 StorSimple 卷迁移到 Azure 文件共享。 迁移期间和之后可能会产生其他成本：

* **网络出口：** 你的 StorSimple 文件位于特定 Azure 区域内的存储帐户中。 如果预配的 Azure 文件共享将迁移到同一 Azure 区域的存储帐户中，则不会产生出口成本。 在此迁移过程中，你可以将文件移动到其他区域中的存储帐户。 在这种情况下，会产生出口成本。
* **Azure 文件共享事务：** 如果将文件复制到 Azure 文件共享中（在迁移过程中或过程外进行），则在写入文件和元数据时，会产生事务成本。 最佳做法是在迁移过程中在事务优化层上启动 Azure 文件共享。 迁移完成后，切换到所需的层。 以下阶段会在适当的时机阐述这一点。
* **更改 Azure 文件共享层：** 更改 Azure 文件共享的层会产生事务成本。 大多数情况下，按照上一要点中包含的建议进行操作会更经济高效。
* **存储成本：** 当此迁移开始将文件复制到 Azure 文件共享时，会消耗 Azure 文件存储并对其计费。 迁移的备份将成为 [Azure 文件共享快照](storage-snapshots-files.md)。 文件共享快照仅耗用与其包含的差异相对应的存储容量。
* **StorSimple：** 在你取消预配 StorSimple 设备和存储帐户之前，将会持续产生存储、备份和设备的 StorSimple 成本。

### <a name="direct-share-access-vs-azure-file-sync"></a>Direct-share-access 与 Azure 文件同步

可以通过 Azure 文件共享来构建文件服务部署，这是一种全新的方式。 Azure 文件共享只是云中的一个 SMB 共享，你可以对其进行设置，让用户使用可以在本机使用的、熟悉的 Kerberos 身份验证和现有的 NTFS 权限（文件和文件夹 ACL）直接通过 SMB 协议进行访问。 详细了解[如何对 Azure 文件共享进行基于标识的访问](storage-files-active-directory-overview.md)。

直接访问的替代方法是 [Azure 文件同步](../file-sync/file-sync-planning.md)。Azure 文件同步直接模拟 StorSimple 在本地缓存常用文件的功能。

Azure 文件同步是一种 Microsoft 云服务，基于两个主要组件：

* 文件同步和云分层，用于在任何 Windows Server 上创建高性能的访问缓存。
* 作为 Azure 中的原生存储的文件共享，可通过多个协议（例如 SMB 和文件 REST）访问。

Azure 文件共享在存储的文件上保留重要的文件保真特性（如属性、权限和时间戳）。 使用 Azure 文件共享时，应用程序或服务不再需要解释存储在云中的文件和文件夹。 你可以通过熟悉的协议和客户端（例如 Windows 文件资源管理器）以原生方式访问它们。 Azure 文件共享允许你将常规用途的文件服务器数据和应用程序数据存储在云中。 Azure 文件共享备份是一种内置功能，可以通过 Azure 备份进一步增强。

本文重点介绍迁移步骤。 如果要在迁移之前详细了解 Azure 文件同步，请参阅以下文章：

* [Azure 文件同步概述](../file-sync/file-sync-planning.md "概述")
* [Azure 文件同步部署指南](../file-sync/file-sync-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple 服务数据加密密钥

当你首次设置 StorSimple 设备时，它生成了一个“服务数据加密密钥”，并指示你安全地存储密钥。 此密钥用于对 StorSimple 设备将你的文件存储到的关联 Azure 存储帐户中的所有数据进行加密。

需要提供“服务数据加密密钥”才能成功进行迁移。 现在可以从记录中检索此密钥了，清单中的每个设备都有自己的一个密钥。

如果在记录中找不到密钥，则可以从设备中生成一个新密钥。 每个设备都有唯一的加密密钥。

#### <a name="change-the-service-data-encryption-key"></a>更改服务数据加密密钥

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> 在决定连接到 StorSimple 设备的方式时，请考虑以下方面：
>
> * 通过 HTTPS 会话进行连接是最安全的选项（建议使用）。
> * 直接连接到设备串行控制台是安全的，但通过网络交换机连接到串行控制台并不安全。
> * HTTP 会话连接是一个选项，但未进行加密。 不建议你使用 HTTP 会话连接，除非是在封闭的受信任网络中使用。

### <a name="storsimple-volume-backups"></a>StorSimple 卷备份

StorSimple 在卷级别提供差异备份。 Azure 文件共享也具有这种功能（称为共享快照）。
迁移作业只能移动备份，而不能移动实时卷中的数据。 因此，最新备份应始终位于在迁移中移动的备份的列表中。

决定是否需要在迁移期间移动任何较旧的备份。
最佳做法是使此列表尽可能小，从而使迁移作业的完成速度更快。

若要标识必须迁移的关键备份，请创建备份策略的核对清单。 例如：
* 最新备份。 （注意：最新备份应始终是此列表的一部分。）
* 每月备份一次，连续备份 12 个月。
* 每年备份一次，连续备份三年。 

稍后在创建迁移作业时，可以使用此列表来确定必须迁移的具体的 StorSimple 卷备份，以满足列表中的要求。

> [!CAUTION]
> 不支持选择超过 **50** 个 StorSimple 卷备份。
> 迁移作业只能移动备份，而不能移动实时卷中的数据。 因此，最新备份与实时数据最接近，因此应始终是要在迁移中移动的备份列表的一部分。

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>将现有 StorSimple 卷映射到 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>存储帐户数量

部署多个存储帐户时，每个帐户存储较少数量的 Azure 文件共享，这可能有益于迁移。

如果文件共享的活跃度很高（文件共享由多个用户或应用程序使用），则两个 Azure 文件共享就可能达到存储帐户的性能限制。 因此，最佳做法是迁移到多个存储帐户，每个帐户都有其自己的单独的文件共享，并且每个存储帐户通常都不超过两个或三个共享。

最佳做法是在部署存储帐户时，让每个存储帐户都有一个文件共享。 可以将多个 Azure 文件共享加入同一存储帐户，前提是你在其中有存档共享。

相对于 Azure 文件同步，这些注意事项更适用于[直接云访问](#direct-share-access-vs-azure-file-sync)（通过 Azure VM 或服务进行）。如果计划在这些共享上专门使用 Azure 文件同步，则可以将多个文件共享分组到一个 Azure 存储帐户中。 将来，你可以将应用直接迁移到云中，然后直接访问文件共享，这样可以提高 IOPS 和吞吐量，因此是有益的。 也可以一开始就使用 Azure 中的服务，同样可以受益于 IOPS 和吞吐量的提高。

如果已创建共享列表，请将每个共享映射到它将驻留在其中的存储帐户。

> [!IMPORTANT]
> 确定一个 Azure 区域，确保每个存储帐户和 Azure 文件同步资源与所选的区域匹配。
> 请勿立即配置存储帐户的网络和防火墙设置。 此时进行这些配置会导致迁移无法进行。 请在完成迁移后配置这些 Azure 存储设置。

### <a name="storage-account-settings"></a>存储帐户设置

可以对存储帐户进行许多配置。 以下清单应该用于确认存储帐户配置。 例如，可以在迁移完成后更改网络配置。 

> [!div class="checklist"]
> * 大型文件共享：已启用 - 大型文件共享可提高性能，并使共享中的存储容量最高可达 100TiB。 此设置适用于具有 Azure 文件共享的目标存储帐户。
> * 防火墙和虚拟网络：已禁用 - 不要配置任何 IP 限制或限制存储帐户对特定 VNET 的访问。 在迁移过程中使用存储帐户的公共终结点。 必须允许来自 Azure VM 的所有 IP 地址。 迁移后，最好在存储帐户上配置任何防火墙规则。 按此方式配置源和目标存储帐户。
> * 专用终结点：支持 - 可以启用专用终结点，但使用公共终结点进行迁移，并且必须保持公共终结点可用。 此注意事项适用于源和目标存储帐户。

### <a name="phase-1-summary"></a>阶段 1 摘要

阶段 1 末尾：

* 你已经大致了解 StorSimple 设备和卷。
* 数据管理器服务已准备好访问云中的 StorSimple 卷，因为你已为每个 StorSimple 设备检索了“服务数据加密密钥”。
* 你已经为需要迁移的具体卷和备份（可能不是最新的）制定了计划。
* 你知道如何将卷映射到相应数量的 Azure 文件共享和存储帐户。

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>阶段 2：部署 Azure 存储和迁移资源

此部分讨论的注意事项涉及如何部署 Azure 中所需的不同资源类型。 某些资源类型用于保留迁移后数据，某些资源类型仅仅是用于迁移。 请不要在完成部署计划之前开始部署资源。 在部署 Azure 资源后，就很难（有时甚至不可能）更改 Azure 资源的某些方面。

### <a name="deploy-storage-accounts"></a>部署存储帐户

你可能需要部署多个 Azure 存储帐户。 根据部署计划，每个 Azure 存储帐户都会保留较少数量的 Azure 文件共享，这在本文前面部分已经完成。 请转到 Azure 门户来[部署计划的存储帐户](../common/storage-account-create.md#create-a-storage-account)。 对于任何新的存储帐户，请考虑遵循基本设置。

> [!IMPORTANT]
> 请勿立即配置存储帐户的网络和防火墙设置。 此时进行那些配置会导致迁移无法进行。 请在完成迁移后配置这些 Azure 存储设置。

#### <a name="subscription"></a>订阅

你可以使用那个用于 StorSimple 部署的订阅，也可以使用其他订阅。 唯一限制是，你的订阅必须与 StorSimple 订阅位于同一个 Azure Active Directory 租户中。 在开始迁移之前，请考虑将 StorSimple 订阅移到相应的租户。 只能移动整个订阅，不能将单个 StorSimple 资源移到不同的租户或订阅。

#### <a name="resource-group"></a>资源组

资源组可用于对资源和管理员管理权限进行组织。 详细了解 [Azure 中的资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)。

#### <a name="storage-account-name"></a>存储帐户名称

存储帐户的名称将成为 URL 的一部分，具有特定的字符限制。 在命名约定中，要注意以下几点：存储帐户名称必须独一无二；只允许使用小写字母和数字；长度要求 3 到 24 个字符之间；不允许使用连字符或下划线之类的特殊字符。 有关详细信息，请参阅 [Azure 存储资源命名规则](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)。

#### <a name="location"></a>位置

存储帐户的位置或 Azure 区域非常重要。 如果使用 Azure 文件同步，则所有存储帐户都必须与存储同步服务资源位于同一区域。 你选取的 Azure 区域应靠近本地服务器和用户，或者是后者的几何中心。 部署资源后，无法更改其区域。

可以选取一个不同于 StorSimple 数据（存储帐户）当前驻留位置的区域。

> [!IMPORTANT]
> 如果选取一个不同于 StorSimple 存储帐户当前位置的区域，则在迁移过程中[会产生出口费用](https://azure.microsoft.com/pricing/details/bandwidth)。 数据会离开 StorSimple 区域，进入新的存储帐户区域。 如果你始终在同一 Azure 区域，则没有任何带宽费用。

#### <a name="performance"></a>性能

你可以选择为 Azure 文件共享或标准存储选取高级存储 (SSD)。 标准存储包括[用于文件共享的多个层](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share)。 对于从 StorSimple 进行迁移的大多数客户，标准存储是正确的选择。

仍不确定？

* 如果需要[高级 Azure 文件共享的性能](understanding-billing.md#provisioned-model)，请选择高级存储。
* 为常规用途文件服务器工作负荷（包括热数据和存档数据）选择标准存储。 如果云中共享上的唯一工作负荷将是 Azure 文件同步，也请选择标准存储。

#### <a name="account-kind"></a>帐户种类

* 对于标准存储，请选择“StorageV2 (常规用途 v2)”。
* 对于高级文件共享，请选择“FileStorage”。

#### <a name="replication"></a>复制

有多种可用的复制设置。 详细了解不同的复制类型。

只能从以下两个选项中选择一个：

* 本地冗余存储 (LRS)。
* 区域冗余存储 (ZRS)：并非在所有 Azure 区域都可用。

> [!NOTE]
> 只有 LRS 和 ZRS 冗余类型与大型（100 TiB 容量）Azure 文件共享兼容。

当前并不支持所有变体形式的异地冗余存储 (GRS)。 你可以稍后切换冗余类型，在 Azure 提供对 GRS 的支持时切换到 GRS。

#### <a name="enable-100-tib-capacity-file-shares"></a>启用 100 TiB 容量文件共享

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="一个图像，显示 Azure 门户中用于创建存储帐户的“高级”选项卡。":::
    :::column-end:::
    :::column:::
        在 Azure 门户中新建存储帐户向导的“高级”部分，你可以在此存储帐户中启用“大型文件共享”支持。 如果此选项不可用，你很可能选择了错误的冗余类型。 确保仅选择 LRS 或 ZRS，这样此选项才会变得可用。
    :::column-end:::
:::row-end:::

选择大型（100 TiB 容量）文件共享有以下优点：

* 与较小的 5 TiB 容量文件共享相比，性能会大大提高（例如，IOPS 是原来的 10 倍）。
* 迁移的完成速度要快得多。
* 确保文件共享的容量足以容纳要迁移到其中的所有数据，包括存储容量差异备份所需的所有数据。
* 涵盖未来的增长。

> [!IMPORTANT]
> 在迁移之前或迁移过程中，请勿将特殊网络应用于存储帐户。 必须可在源和目标存储帐户上访问公共终结点。 不支持对特定 IP 范围或 VNET 的任何限制。 可以在迁移后更改存储帐户网络配置。

### <a name="azure-file-shares"></a>Azure 文件共享

创建存储帐户后，请转到存储帐户的“文件共享”部分，根据阶段 1 的迁移计划部署适当数量的 Azure 文件共享。 对于 Azure 中的新文件共享，请考虑遵循以下基本设置。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Azure 门户屏幕截图，显示新文件共享 UI。":::
    :::column-end:::
    :::column:::
        </br>**名称**</br>支持小写字母、数字和连字符。</br></br>**配额**</br>此处的配额相当于 Windows Server 实例上的 SMB 硬配额。 最佳做法是不要在此处设置配额，因为在达到配额时迁移和其他服务会失败。</br></br>**层级**</br>为新的文件共享选择“事务优化”。 在迁移过程中，会发生许多事务。 稍后将层更改为最适合工作负荷的层会更经济高效。
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple 数据管理器

将会保存迁移作业的 Azure 资源称为 **StorSimple 数据管理器**。 选择“新建资源”，搜索它。 然后选择“创建”。

此临时资源用于业务流程。 迁移完成后，可以取消对其的预配。 它应部署在与 StorSimple 存储帐户相同的订阅、资源组和区域中。

### <a name="azure-file-sync"></a>Azure 文件同步

使用 Azure 文件同步，你可以添加本地缓存，其中包含最常访问的文件。 与 StorSimple 的缓存能力类似，Azure 文件同步云分层功能带来的延迟相当于进行本地访问，并改进了对 Windows Server 实例和多站点同步上的可用缓存容量的控制。如果你的目标是有一个本地缓存，则请在本地网络中准备一个有足够的直连存储容量的 Windows Server VM（也支持物理服务器和故障转移群集）。

> [!IMPORTANT]
> 目前不要设置 Azure 文件同步。 最好是在迁移完共享后设置 Azure 文件同步。 部署 Azure 文件同步的操作不应在迁移的阶段 4 之前开始。

### <a name="phase-2-summary"></a>阶段 2 摘要

在阶段 2 结束时，你应该部署了存储帐户以及跨这些帐户的所有 Azure 文件共享。 你还会有一个 StorSimple 数据管理器资源。 在配置迁移作业时，你将在阶段 3 使用后者。

## <a name="phase-3-create-and-run-a-migration-job"></a>阶段 3：创建和运行迁移作业

此部分介绍如何设置迁移作业，并谨慎地映射 StorSimple 卷上那些应该复制到所选目标 Azure 文件共享中的目录。 若要开始操作，请转到 StorSimple 数据管理器，找到菜单上的“作业定义”，然后选择“+ 作业定义”。 正确的目标存储类型为默认值：Azure 文件共享。

![StorSimple 8000 系列迁移作业类型。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Azure 门户中的作业定义的屏幕截图，其中打开了一个新的“作业定义”对话框，该对话框要求用户提供作业类型：复制到文件共享还是 blob 容器。")

:::row:::
    :::column:::
       ![StorSimple 8000 系列迁移作业。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "屏幕截图，显示了用于迁移作业的新作业创建窗体。")
    :::column-end:::
    :::column:::
        **作业定义名称**</br>此名称应指示你要移动的文件集。 为其提供一个类似于 Azure 文件共享的名称是一种好的做法。 </br></br>**作业运行的位置**</br>选择区域时，必须选择与 StorSimple 存储帐户相同的区域；如果该区域不可用，则必须选择一个靠近该区域的区域。 </br></br><h3>源</h3>**源订阅**</br>选择在其中存储 StorSimple 设备管理器资源的订阅。 </br></br>**StorSimple 资源**</br>选择你的设备注册到的 StorSimple 设备管理器。 </br></br>**服务数据加密密钥**</br>如果在记录中找不到该密钥，请查看[本文前面的这个部分](#storsimple-service-data-encryption-key)。 </br></br>**设备**</br>选择包含你要迁移到其中的卷的 StorSimple 设备。 </br></br>**音量**</br>选择源卷。 稍后你将决定是否要将整个卷或子目录迁移到目标 Azure 文件共享中。</br></br> **卷备份**</br>你可以选择“选择卷备份”，以便选择要在此作业的过程中移动的特定备份。 [本文中的专门部分](#selecting-volume-backups-to-migrate)（即将发布）详细介绍了该过程。</br></br><h3>目标</h3>选择订阅、存储帐户和 Azure 文件共享作为此迁移作业的目标。</br></br><h3>目录映射</h3>[本文中的专门部分](#directory-mapping)讨论了所有相关的详细信息。
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>选择要迁移的卷备份

选择需要迁移的备份时，有一些重要的注意事项：

- 迁移作业只能移动备份，而不能移动实时卷中的数据。 因此，最新备份与实时数据最接近，应始终位于在迁移中移动的备份的列表上。
- 确保最新备份是最新的，以便尽可能小地将差异保存到实时共享。 在创建迁移作业之前，可能需要手动触发并完成其他卷备份。 实时共享基础上的小增量会改善迁移体验。 如果此增量可以为零（在列表中进行最新备份后 StorSimple 卷没有变化），则“阶段 5：用户直接转换”会大大简化和加速。
- 必须将备份按照 **从最旧到最新** 的顺序回放到 Azure 文件共享中。 在迁移作业运行后，不能将较旧的备份“归类到”Azure 文件共享上的备份列表。 因此，在创建作业之前，必须确保备份列表已完成。 
- 创建作业后，不能修改作业中的备份列表，即使作业从未运行也是如此。 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="新作业创建窗体的屏幕截图，详细介绍了为迁移选择 StorSimple 备份的部分。" lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        若要为迁移作业选择 StorSimple 卷的备份，请在作业创建窗体上选择“选择卷备份”。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="一个图像，显示用于选择备份的边栏选项卡的上半部分列出了所有可用的备份。此列表中的选定备份将会灰显，并会被添加到边栏选项卡下半部分的第二个列表中。在那里，该备份也可以被再次删除。" lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        当备份选择边栏选项卡打开时，它将分为两个列表。 在第一个列表中，将显示所有可用备份。 你可以通过对特定时间范围进行筛选来扩展和缩小结果集。 （请参阅下一部分） </br></br>选定备份将会灰显，并会被添加到边栏选项卡下半部分的第二个列表中。 第二个列表显示为迁移选定的所有备份。 还可以再次删除错误选择的备份。
        > [!CAUTION]
        > 你必须选择要迁移的 **所有** 备份。 不能在以后添加旧备份。 创建作业后，将无法通过修改作业来更改你的选择。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="屏幕截图，显示备份选择边栏选项卡的时间范围选择。" lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        默认情况下，该列表经过筛选后会显示过去 7 天内的 StorSimple 卷备份，方便用户选择最新备份。 对于之前的备份，请使用边栏选项卡顶部的时间范围筛选器。 你可以从现有筛选器中进行选择，也可以设置自定义时间范围，以便仅筛选在此期间进行的备份。
    :::column-end:::
:::row-end:::

> [!CAUTION]
> 不支持选择超过 50 个 StorSimple 卷备份。 包含大量备份的作业可能会失败。

### <a name="directory-mapping"></a>目录映射

对于迁移作业，目录映射是可选的。 如果将此部分留空，则 StorSimple 卷的根目录上的所有文件和文件夹都会移到目标 Azure 文件共享的根目录中。 大多数情况下，在 Azure 文件共享中存储整个卷的内容并不是最佳方法。 通常情况下，更好的做法是在 Azure 的多个文件共享中拆分卷的内容。 如果尚未制定计划，请先参阅[将 StorSimple 卷映射到 Azure 文件共享](#map-your-existing-storsimple-volumes-to-azure-file-shares)。

你可能已在迁移计划中决定：必须将 StorSimple 卷上的文件夹跨多个 Azure 文件共享进行拆分。 如果是这种情况，你可以通过以下操作来完成拆分：

1. 定义多个作业，以便将文件夹迁移到一个卷上。 每个作业都会有相同的 StorSimple 卷源，但会有与目标不同的 Azure 文件共享。
1. 通过使用作业创建窗体的“目录映射”部分并遵循特定的[映射语义](#semantic-elements)，精确指定 StorSimple 卷中的哪些文件夹需要迁移到指定的文件共享中。

> [!IMPORTANT]
> 提交此窗体时，无法验证窗体中的路径和映射表达式。 如果未正确指定映射，作业可能会彻底失败或产生不需要的结果。 在这种情况下，通常最好的办法是删除 Azure 文件共享，重新创建它，然后在新的迁移作业中修复此共享的映射语句。 使用固定映射语句运行新作业可以修复省略的文件夹并将其导入现有共享。 但是，只有因路径拼写错误而被忽略的文件夹才能以这种方式寻址。

#### <a name="semantic-elements"></a>语义元素

映射以从左到右的形式表示：[\源路径] \> [\目标路径]。

|语义字符          | 含义  |
|:---------------------------|:---------|
| **\\**                     | 根级别指示符。       |
| **\>**                     | [源] 和 [目标映射] 运算符。     |
|**\|** 或回车（换行符） | 两个文件夹映射指令的分隔符。 </br>也可省略此字符，然后选择 **Enter** 以在其自己的行上获取下一个映射表达式。        |

### <a name="examples"></a>示例
将 User data 文件夹的内容移到目标文件共享的根目录：
``` console
\User data > \
```
将整个卷内容移到目标文件共享上的新路径中：
``` console
\ > \Apps\HR tracker
```
将源文件夹内容移到目标文件共享上的新路径中：
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
将多个源位置排序，形成一个新的目录结构：
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>语义规则

* 始终指定相对于根级别的文件夹路径。
* 每个文件夹路径都以根级别指示符“\\”开头。
* 不要包含驱动器号。
* 指定多个路径时，源路径或目标路径不能重叠：</br>
   无效的源路径重叠示例：</br>
    \\folder\1 > \\folder</br>
    \\folder\\1\\2 > \\folder2</br>
   无效的目标路径重叠示例：</br>
   \\folder > \\</br>
   \\folder2 > \\</br>
* 将会忽略不存在的源文件夹。
* 将会创建目标中不存在的文件夹结构。
* 与 Windows 一样，文件夹名称不区分大小写，但会保留大小写。

> [!NOTE]
> 迁移作业不会复制 StorSimple 卷上的 \System Volume Information 文件夹和 $Recycle.Bin 的内容。

### <a name="run-a-migration-job"></a>运行迁移作业

你的迁移作业列在已部署到资源组的数据管理器资源中的“作业定义”下。
从作业定义列表中，选择要运行的作业。

在打开的作业边栏选项卡中，你可以在下面的列表中看到作业在运行。 一开始此列表将为空。 在边栏选项卡顶部，有一个名为“运行作业”的命令。 此命令不会立即运行作业，它会打开“作业运行”边栏选项卡：

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="一个显示作业运行边栏选项卡的图像，边栏选项卡中的下拉控件处于打开状态，其中显示了要迁移的选定备份。最早的备份已突出显示，需要首先选择它。" lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        在此版本中，每个作业必须运行多次。 </br></br>**必须从要迁移的备份列表中最早的备份开始。** （已在图像中突出显示）</br></br>每次选择备份时都要再次运行该作业，每次都要针对以渐进方式进行更新的备份来运行该作业。
        </br></br>
        > [!CAUTION]
        > 必须先对所选的最早备份运行迁移作业，并且每次都要针对以渐进方式进行更新的备份来运行该作业。 始终必须以手动方式维护备份顺序 - 从最旧到最新。
    :::column-end:::
:::row-end:::

#### <a name="run-jobs-in-parallel"></a>并行运行作业

你可能有多个 StorSimple 位置，每个位置都需要复制到不同的 Azure 文件共享。 对于单个 StorSimple 设备，如果以每个不同的 Azure 文件共享为目标，则最多可以并行运行四个迁移作业。 

每个作业都要经过多个阶段。 仅当之前的作业进入文件复制阶段时，才能启动另一个作业。 通常在启动作业后 25 到 35 分钟内可以启动另一个作业，最多可并行启动四个作业。 以相同文件共享为目标的作业（对于后续备份）需要逐个备份地进行复制。

> [!CAUTION]
> 对于进入同一 Azure 文件共享的任何数据，一次仅启动一个迁移作业。

#### <a name="interpret-the-log-files"></a>解释日志文件

已完成的迁移作业显示指向复制日志的链接。 这些日志是 \*.csv 文件，其中了成功复制的命名空间项和未能复制的项。

访问日志文件的位置后，可以通过使用搜索词“失败”筛选列表来查找失败文件的日志。 结果将是一组未能复制的文件的日志。 然后按大小对它们进行排序。 可能会生成大小为 17 字节的额外日志。 它们是空的，可以忽略。 通过排序，可以轻松地将重点放在包含内容的日志上。

同一过程适用于记录成功复制的日志文件。

### <a name="phase-3-summary"></a>阶段 3 摘要

在阶段 3 结束时，你将至少运行了一个迁移作业（从 StorSimple 卷迁移到 Azure 文件共享）。 你将从必须迁移的最旧备份到最新备份，多次运行相同的迁移作业。 你现在可以重点关注以下事项：为共享设置 Azure 文件同步（在某个共享的迁移作业完成后进行），或将信息工作者和应用的共享访问权限定向到 Azure 文件共享。

## <a name="phase-4-access-your-azure-file-shares"></a>阶段 4：访问 Azure 文件共享

访问 Azure 文件共享的主要策略有两种：

* **Azure 文件同步**：将 [Azure 文件同步部署](#deploy-azure-file-sync)到本地 Windows Server 实例。 Azure 文件同步具有本地缓存的所有优点，就像 StorSimple 一样。
* **Direct-share-access**：[部署 Direct-share-access](#deploy-direct-share-access)。 如果给定 Azure 文件共享的访问方案无法受益于本地缓存，或者你不再能够承载本地 Windows Server 实例，请使用此策略。 在这里，你的用户和应用将继续通过 SMB 协议访问 SMB 共享。 这些共享不再位于本地服务器上，而是直接位于云中。

你应该已经确定了本指南的[阶段 1](#phase-1-prepare-for-migration) 中最合适的选项。

此部分的其余内容重点介绍部署说明。

### <a name="deploy-azure-file-sync"></a>部署 Azure 文件同步

现在可以部署 Azure 文件同步的一部分了。

1. 创建 Azure 文件同步云资源。
1. 在本地服务器上部署 Azure 文件同步代理。
1. 将服务器注册到云资源。

此时请不要创建任何同步组。 只有在完成迁移作业（迁移到 Azure 文件共享）后，才应使用 Azure 文件共享设置同步。 如果在迁移完成之前开始使用 Azure 文件同步，则可能会给迁移带来不必要的困难，因为你无法轻松判断何时启动直接转换。

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果要在迁移完成后更改数据驻留的 Azure 区域，请在此迁移的目标存储帐户所在的区域中部署存储同步服务。

#### <a name="deploy-an-on-premises-windows-server-instance"></a>部署本地 Windows Server 实例

* 以虚拟机或物理服务器的形式创建 Windows Server 2019（最低版本为 2012R2）。 也支持 Windows Server 故障转移群集。 不要重复使用面向 StorSimple 8100 或 8600 的服务器。
* 预配或添加直连存储。 不支持网络连接存储 (NAS)。

最佳做法是，为新的 Windows Server 实例提供相当的或更大的存储量（相对于 StorSimple 8100 或 8600 设备在本地提供的用于缓存的存储量而言）。 你使用 Windows Server 实例的方式与使用 StorSimple 设备的方式相同。 如果它具有与设备相同的存储量，则缓存体验应该类似（即使不是完全相同）。 你可以在 Windows Server 实例中随意添加或删除存储。 此功能使你能够扩展本地卷大小以及可用于缓存的本地存储量。

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>为文件同步准备 Windows Server 实例

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>在 Windows Server 实例上配置 Azure 文件同步

要完成此过程，已注册的本地 Windows Server 实例必须准备就绪，并已连接到 Internet。

> [!IMPORTANT]
> 在继续操作之前，必须先完成 StorSimple 迁移，将文件和文件夹迁移到 Azure 文件共享中。 请确保不会对文件共享进行更多更改。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 确保启用云分层。 云分层是一项 Azure 文件同步功能，它允许本地服务器的存储容量小于云中的存储容量，但仍然提供完整的命名空间。 在本地有用的数据也缓存在本地，以便进行快速的本地访问。 在此步骤启用云分层的另一个原因是我们不想在此阶段同步文件内容。 此时只应移动命名空间。

### <a name="deploy-direct-share-access"></a>部署 direct-share-access

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        此视频是有关如何通过五个简单步骤将 Azure 文件共享直接安全地公开给信息工作者和应用的指南和演示。</br>
        该视频提到了某些主题的专用文档：

* [标识概述](storage-files-active-directory-overview.md)
* [如何将存储帐户加入域](storage-files-identity-auth-active-directory-enable.md)
* [Azure 文件共享的网络概述](storage-files-networking-overview.md)
* [如何配置公共和专用终结点](storage-files-networking-endpoints.md)
* [如何配置 S2S VPN](storage-files-configure-s2s-vpn.md)
* [如何配置 Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [如何配置 Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [如何配置 DNS 转发](storage-files-networking-dns.md)
* [配置 DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>阶段 4 摘要

在此阶段，你已在 StorSimple 数据管理器中创建并运行了多个迁移作业。 这些作业已将文件和文件夹迁移到 Azure 文件共享。 你还为 direct-share-access 部署了 Azure 文件同步或准备了网络和存储帐户。

## <a name="phase-5-user-cut-over"></a>阶段 5：用户直接转换

此阶段就是完成迁移：

* 计划停机时间。
* 当阶段 3 中的迁移作业已经运行后，追加你的用户和应用已在 StorSimple 端生成的任何更改。
* 通过 direct-share-access 将用户故障转移到使用 Azure 文件同步或 Azure 文件共享的新 Windows Server 实例。

### <a name="plan-your-downtime"></a>计划停机时间

对于用户和应用来说，此迁移方法都需要一定的停机时间。 目标是将停机时间尽量缩短。 可以查看以下注意事项：

* 确保 StorSimple 卷在运行迁移作业时始终可用。
* 为共享运行完数据迁移作业后，就可以从 StorSimple 卷或共享中删除用户访问权限（至少可以删除写入访问权限）。 最终的 RoboCopy 会跟上 Azure 文件共享的进度。 然后，你可以对用户进行直接转换。 运行 RoboCopy 的位置取决于你选择使用 Azure 文件同步还是 direct-share-access。 随后的关于 RoboCopy 的部分介绍了该主题。
* 完成 RoboCopy 追加以后，便可以通过 Azure 文件共享直接向用户公开新位置，也可以使用 Azure 文件同步通过 Windows Server 实例上的 SMB 共享向用户公开新位置。通常，使用 DFS-N 部署可快速高效地完成直接转换。 它会使现有共享地址保持一致，并重新指向包含已迁移文件和文件夹的新位置。

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>确定你的命名空间完全同步到你的服务器的时间

对 Azure 文件共享使用 Azure 文件同步时，必须在启动任何本地 RoboCopy 之前确定整个命名空间已下载到服务器，这一点很重要。 下载命名空间所花的时间取决于 Azure 文件共享中的项目数。 可以通过两种方法来确定命名空间是否已完全到达服务器。

#### <a name="azure-portal"></a>Azure 门户

可以使用 Azure 门户来查看命名空间完全到达的时间。

* 登录到 Azure 门户，转到同步组。 检查同步组和服务器终结点的同步状态。
* 下载是一种有意思的指示。 如果服务器终结点是新预配的，则会显示“初始同步”，表示命名空间仍在下载。
在该状态更改为除“初始同步”之外的任何状态后，命名空间将在服务器上完全填充。 你现在可以使用本地 RoboCopy 继续操作了。

#### <a name="windows-server-event-viewer"></a>Windows Server 事件查看器

你还可以使用 Windows Server 实例上的事件查看器来确定命名空间完全到达的时间。

1. 打开“事件查看器”，转到“应用程序和服务”。
1. 转到 **Microsoft\FileSync\Agent\Telemetry** 并将其打开。
1. 查找与已完成的同步会话对应的最近的事件 (9102)。
1. 选择“详细信息”，确认你在查看 **SyncDirection** 值为 “Download”的事件。
1. 若要了解命名空间完全下载到服务器的时间，可以查看一个包含 Scenario 的事件，其值为 **FullGhostedSync**，**HResult** = **0**。
1. 如果错过了该事件，也可查找 **SyncDirection** = **Download** 且 **Scenario** =  **"RegularSync"** 的其他 9102 事件。 如果找到这些事件中的一个，则还表明命名空间已下载完毕且同步已进展到定期同步会话阶段，不管此时是否有需要同步的内容。

### <a name="a-final-robocopy"></a>最终 RoboCopy

此时，本地 Windows Server 实例与 StorSimple 8100 或 8600 设备之间存在差异。

1. 在迁移过程中，需要追加用户或应用已在 StorSimple 端生成的更改。
1. 在使用 Azure 文件同步的情况下：StorSimple 设备具有填充的缓存，而 Windows Server 实例此时只有一个命名空间，没有在本地存储的文件内容。 可以通过最终 RoboCopy 来快速启动本地 Azure 文件同步缓存，方法是：尽可能多地拉取本地缓存的文件内容，只要这些内容可用且 Azure 文件同步服务器能够容纳它们。
1. 迁移作业可能会遗留某些文件（由于存在无效的字符）。 如果是这样，请将它们复制到已启用 Azure 文件同步的 Windows Server 实例。 稍后可以调整它们，这样它们就会同步。如果不将 Azure 文件同步用于特定共享，则最好是在 StorSimple 卷上重命名那些使用无效字符的文件， 然后直接对 Azure 文件共享运行 RoboCopy。

> [!WARNING]
> Windows Server 2019 中的 Robocopy 目前遇到一个问题，该问题会导致在使用 robocopy 的 /MIR 函数时，在目标服务器上由 Azure 文件同步分层的文件会从源重新复制并重新上传到 Azure。 在 2019 以外的 Windows Server 上，必须使用 Robocopy。 首选是 Windows Server 2016。 如果能够通过 Windows 更新解决该问题，我们会更新此备注。

> [!WARNING]
> 在服务器具有下载完的用于 Azure 文件共享的命名空间之前，不得启动 RoboCopy。 有关详细信息，请参阅[确定何时将命名空间完全下载到服务器](#determine-when-your-namespace-has-fully-synced-to-your-server)。

 你只希望复制在上次运行迁移作业后更改过的文件，以及之前尚未通过这些作业进行移动的文件。 你可以稍后（在完成迁移后）在服务器上解决它们为何没有移动的问题。 有关详细信息，请参阅 [Azure 文件同步故障排除](../file-sync/file-sync-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

RoboCopy 有多个参数。 以下示例展示了一个完成的命令，以及选择这些参数的原因的列表。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

在配置 RoboCopy 命令的源和目标位置时，请务必查看源和目标的结构，以确保它们匹配。 如果使用了迁移作业的目录映射功能，则根目录结构可能不同于 StorSimple 卷的结构。 如果是这种情况，则可能需要多个 RoboCopy 作业，每个子目录一个。 如果不确定命令是否会按预期执行，则可以使用 /L 参数，该参数会模拟命令，实际上并不进行任何更改。

此 RoboCopy 命令使用 /MIR，因此不会移动相同的文件（例如分层文件）。 但是，如果你获得的源路径和目标路径错误，则 /MIR 还会清除 StorSimple 源路径上不存在的 Windows Server 实例或 Azure 文件共享上的目录结构。 它们必须完全匹配，否则 RoboCopy 作业无法达到其预期目的：使用在迁移过程中所做的最新更改来更新已迁移的内容。

请查阅 RoboCopy 日志文件，看文件是否遗留了文件。 如果存在问题，请修复问题，然后重新运行 RoboCopy 命令。 在解决你关注的文件或文件夹的未决问题之前，请不要取消预配任何 StorSimple 资源。

如果不使用 Azure 文件同步来缓存相关且特定的 Azure 文件共享，而是选择使用 direct-share-access，请执行以下操作：

1. 将 [Azure 文件共享作为网络驱动器装载](storage-how-to-use-files-windows.md#mount-the-azure-file-share)到本地 Windows 计算机。
1. 在 StorSimple 与装载的 Azure 文件共享之间执行 RoboCopy。 如果文件无法复制，请在 StorSimple 端修复其名称，删除无效字符， 然后重试 RoboCopy。 前面列出的 RoboCopy 命令可以多次运行，而不会导致对 StorSimple 进行不必要的重新调用。

### <a name="troubleshoot-and-optimize"></a>故障排除和优化

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

### <a name="user-cut-over"></a>用户完全转移

如果使用 Azure 文件同步，则可能需要在那个与 StorSimple 卷上的已有共享匹配且已启用 Azure 文件同步的 Windows Server 实例上创建 SMB 共享。 你可以事先安排好此步骤，提前进行操作，这样就不会在这里浪费时间。 但在此之前，必须确保无人有权更改 Windows Server 实例。

如果你有一个 DFS-N 部署，则可将 DFN-Namespaces 指向新的服务器文件夹位置。 如果你没有 DFS-N 部署，并且在本地为 8100 或 8600 设备前置了 Windows Server 实例，则可将该服务器从域中取出。 然后，将启用了 Azure 文件同步的新 Windows Server 实例加入域。 在该过程中，请为服务器提供与旧服务器相同的服务器名称和共享名称。这样，对用户、组策略和脚本来说，直接转换就仍然是透明的。

详细了解 [DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)。

## <a name="deprovision"></a>预配

取消预配资源时，你将失去对该资源及其数据的配置的访问权限。 取消预配无法撤消。 在确认以下事项之前，请不要继续操作：

* 迁移已完成。
* 没有对你要取消预配的 StorSimple 文件、文件夹或卷备份的任何依赖项。

在开始之前，最佳做法是在生产环境中观察新的 Azure 文件同步部署一段时间。 你可以在该段时间内解决可能遇到的任何问题。 观察 Azure 文件同步部署至少几天后，就可以开始按以下顺序预配资源：

1. 通过 Azure 门户取消预配 StorSimple 数据管理器资源。 所有 DTS 作业都将随之一起删除。 你将无法轻松地检索复制日志。 如果这些日志对你的记录很重要，请在取消预配前检索它们。
1. 确保已迁移 StorSimple 物理设备，然后将其注销。 如果不完全确定它们已迁移，请勿继续。 如果在这些资源仍是必需的情况下将其取消预配，则无法恢复数据或其配置。<br>也可以先取消预配 StorSimple 卷资源，这会清除设备上的数据。 此过程可能需要几天的时间，不会对设备上的数据进行取证式清除。 如果这对你很重要，请按照策略独立于资源取消预配操作进行磁盘清除处理。
1. 如果 StorSimple 设备管理器中没有剩余的已注册设备，则可以继续删除设备管理器资源本身。
1. 现在可以在 Azure 中删除 StorSimple 存储帐户。 同样，在继续操作之前，请停下来确认迁移已完成，并且不会有任何内容和任何人员依赖于此数据。
1. 从数据中心拔出 StorSimple 物理设备。
1. 如果你拥有 StorSimple 设备，则可以自由地对其执行电脑回收操作。 如果设备是租用的，请通知出租人，在合适的情况下返回设备。

迁移已完成。

> [!NOTE]
> 仍有疑问或问题？</br>
> 我们会通过 AzureFilesMigration@microsoft.com 随时为你提供帮助。

## <a name="next-steps"></a>后续步骤

* 进一步熟悉 [Azure 文件同步：aka.ms/AFS](../file-sync/file-sync-planning.md)。
* 了解[云分层](../file-sync/file-sync-cloud-tiering-overview.md)策略的灵活性。
* 在 Azure 文件共享上[启用 Azure 备份](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane)，进行快照安排并定义备份保留计划。
* 如果在 Azure 门户中看到某些文件始终未同步，请参阅[故障排除指南](../file-sync/file-sync-troubleshoot.md)以获取解决这些问题的步骤。
