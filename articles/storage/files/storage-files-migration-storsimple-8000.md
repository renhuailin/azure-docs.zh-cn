---
title: StorSimple 8000 系列迁移到 Azure 文件同步
description: 了解如何将 StorSimple 8100 或8600设备迁移到 Azure 文件同步。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 1e45c39a8f562ca6264ab631dfadc84315b58030
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723972"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 和8600迁移到 Azure 文件同步

StorSimple 8000 系列由8100或8600物理、本地设备及其云服务组件表示。 可以将这些设备中的数据迁移到 Azure 文件同步的环境。 Azure 文件同步是 StorSimple 设备可以迁移到的默认和策略长期 Azure 服务。

StorSimple 8000 系列将在2022年12月 [结束](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) 。 请务必尽快开始规划迁移。 本文提供成功迁移到 Azure 文件同步所需的背景知识和迁移步骤。

## <a name="phase-1-prepare-for-migration"></a>阶段1：准备迁移

本部分包含从 StorSimple 卷迁移到 Azure 文件共享时应执行的步骤。

### <a name="inventory"></a>库存

开始规划迁移时，请首先确定需要迁移的所有 StorSimple 设备和卷。 完成此操作后，可以决定最佳迁移途径。

* StorSimple 物理设备 (8000 系列) 使用此迁移指南。
* 虚拟设备， [StorSimple 1200 系列使用其他迁移指南](storage-files-migration-storsimple-1200.md)。

### <a name="migration-cost-summary"></a>迁移成本摘要

通过 StorSimple 数据管理器资源中的数据转换服务作业从 StorSimple 卷迁移到 Azure 文件共享是免费的。 迁移期间和之后可能会产生其他成本：

* **网络出口：** StorSimple 文件位于特定 Azure 区域内的存储帐户中。 如果预配的 Azure 文件共享将迁移到位于同一 Azure 区域的存储帐户中，则不会发生出口费用。 在此迁移过程中，你可以将文件移动到其他区域中的存储帐户。 在这种情况下，出口费用将适用于你。
* **Azure 文件共享事务：** 如果将文件复制到 Azure 文件共享中 (作为迁移的一部分，或者在一个) 之外，将应用事务成本作为文件和元数据。 作为最佳方案，请在迁移过程中在事务优化层上启动 Azure 文件共享。 迁移完成后，切换到所需的层。 以下阶段将在适当的时间点调用此。
* **更改 Azure 文件共享层：** 更改 Azure 文件共享的层成本事务。 在大多数情况下，遵循上一要点的建议会更经济高效。
* **存储成本：** 此迁移开始将文件复制到 Azure 文件共享时，会消耗和计费 Azure 文件存储。
* **StorSimple：** 在有机会取消预配 StorSimple 设备和存储帐户之前，将继续进行存储、备份和设备的 StorSimple 成本。

### <a name="direct-share-access-vs-azure-file-sync"></a>直接共享访问与 Azure 文件同步

Azure 文件共享公开了一系列全新的机会来构建文件服务部署。 Azure 文件共享只是云中的一个 SMB 共享，你可以设置以使用户使用熟悉的 Kerberos 身份验证和现有 NTFS 权限直接通过 SMB 协议进行访问， (文件和文件夹 Acl) 本机工作。 详细了解 [如何对 Azure 文件共享进行基于标识的访问](storage-files-active-directory-overview.md)。

[Azure 文件同步](./storage-sync-files-planning.md)直接访问的替代方法。Azure 文件同步是 StorSimple 的直接模拟功能，用于在本地缓存经常使用的文件。

Azure 文件同步是一种 Microsoft 云服务，基于两个主要组件：

* 文件同步和云分层。
* 作为 Azure 中的本机存储的文件共享，可通过多个协议（如 SMB 和文件 REST）进行访问。

Azure 文件共享在存储的文件（如属性、权限和时间戳）上保留重要的文件保真度。 使用 Azure 文件共享时，应用程序或服务不再需要解释存储在云中的文件和文件夹。 你可以通过熟悉的协议和客户端（如 Windows 文件资源管理器）以本机方式访问它们。 Azure 文件共享允许你将常规用途的文件服务器数据和应用程序数据存储在云中。 Azure 文件共享的备份是一种内置功能，可以通过 Azure 备份进行进一步增强。

本文重点介绍迁移步骤。 如果要在迁移之前了解 Azure 文件同步的详细信息，请参阅以下文章：

* [Azure 文件同步概述](./storage-sync-files-planning.md "概述")
* [Azure 文件同步部署指南](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple 服务数据加密密钥

首次设置 StorSimple 设备时，它会生成服务数据加密密钥，并指示你安全地存储密钥。 此密钥用于对关联的 Azure 存储帐户中的所有数据进行加密，StorSimple 设备在该帐户中存储文件。

若要成功迁移，服务数据加密密钥是必需的。 现在是从记录中为库存中的每个设备检索此密钥的好时机。

如果在记录中找不到密钥，则可以从设备检索密钥。 每个设备都有一个唯一的加密密钥。 检索密钥：

* 通过 Azure 门户 Microsoft Azure 提供支持请求。 请求的内容应具有 StorSimple 设备序列号和检索 "服务数据加密密钥" 的请求。
* StorSimple 支持工程师将与你联系，请求提供屏幕共享会议。
* 确保在会议开始之前， [通过串行控制台](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) 或通过 [远程 PowerShell 会话](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)连接到 StorSimple 设备。

> [!CAUTION]
> 在决定如何连接到 StorSimple 设备时，请考虑以下事项：
>
> * 通过 HTTPS 会话进行连接是最安全的建议选项。
> * 直接连接到设备串行控制台是安全的，但通过网络交换机连接到串行控制台并不安全。
> * HTTP 会话连接是一个选项，但 *未加密*。 不建议这样做，除非它们在已关闭、受信任的网络中使用。

### <a name="storsimple-volume-backups"></a>StorSimple 卷备份

StorSimple 在卷级别上提供差异备份。 Azure 文件共享还具有这种功能，称为共享快照。

决定是否作为迁移的一部分，还会有移动任何备份的义务。

> [!CAUTION]
> 如果必须从 StorSimple 卷迁移备份，请停止。
>
> 当前只能迁移最近的卷备份。 备份迁移支持将于2020年底送达。 如果你现在开始，你稍后不能 "启用螺栓"。 在即将推出的版本中，必须将备份从最旧到最新的 Azure 文件共享 "播放"，并在两者之间进行 Azure 文件共享快照。

如果要仅迁移实时数据且不需要备份，则可以继续执行本指南。 如果有一个月或两个短期的备份保留要求，则可以决定立即继续迁移，并在该时间段后取消预配 StorSimple 资源。 此方法允许你根据需要在 Azure 文件共享端创建尽可能多的备份历史记录。 如果你将两个系统都保持运行状态，则会产生额外的费用，这使得如果你需要的备份保留期超过短期，则不应考虑这种方法。

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>将现有 StorSimple 卷映射到 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>存储帐户数

迁移可能会从多个存储帐户的部署中受益，其中每个帐户都包含较少数量的 Azure 文件共享。

如果文件共享高度活跃 (多个用户或应用程序) 使用，则两个 Azure 文件共享可能会达到存储帐户的性能限制。 因此，最佳做法是迁移到多个存储帐户，每个帐户都有其自己的单独文件共享，并且每个存储帐户通常不超过两个或三个共享。

最佳做法是部署具有一个文件共享的存储帐户。 如果有存档共享，则可以将多个 Azure 文件共享加入同一存储帐户。

这些注意事项更适用于通过 Azure VM 或服务) 将 [云访问权限定向](#direct-share-access-vs-azure-file-sync) 到 Azure 文件同步 (。如果你计划仅在这些共享上使用 Azure 文件同步，则可以将多个组分组到单个 Azure 存储帐户中。 另外，请考虑将应用程序直接转移到云中，然后再直接访问文件共享。 或者，你可以开始使用 Azure 中的服务，这些服务还会受益于提供更高的 IOPS 和吞吐量。

如果已创建共享列表，请将每个共享映射到它将驻留的存储帐户。

> [!IMPORTANT]
> 确定 Azure 区域，并确保每个存储帐户和 Azure 文件同步资源与所选的区域匹配。

### <a name="phase-1-summary"></a>阶段1摘要

阶段 1 末尾：

* 你可以很好地了解 StorSimple 设备和卷。
* 由于已为每个 StorSimple 设备检索服务数据加密密钥，数据转换服务已准备好访问云中的 StorSimple 卷。
* 你计划了需要迁移的卷，以及如何将你的卷映射到相应数量的 Azure 文件共享和存储帐户。

> [!CAUTION]
> 如果必须从 StorSimple 卷迁移备份，请 **在此处停止**。
>
> 此迁移方法依赖于当前无法迁移备份的新数据转换服务功能。 备份迁移支持将于2020年底送达。 当前只能迁移实时数据。 如果你现在开始，你稍后不能 "启用螺栓"。 必须将备份 "播放" 从最旧到最新的 Azure 文件共享备份到实时数据，并在两者之间进行 Azure 文件共享快照。

如果要仅迁移实时数据且不需要备份，则可以继续执行本指南。

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>阶段2：部署 Azure 存储和迁移资源

本部分介绍有关部署 Azure 中所需的不同资源类型的注意事项。 某些将保留你的数据迁移后，某些数据只是为了进行迁移。 在完成部署计划之前，请不要开始部署资源。 在部署 Azure 资源后，有时很难更改 Azure 资源的某些方面。

### <a name="deploy-storage-accounts"></a>部署存储帐户

你可能需要部署多个 Azure 存储帐户。 根据你的部署计划，每个 Azure 文件共享都将保留较少数量的 Azure 文件共享，这将在本文前面的部分中完成。 请参阅 Azure 门户 [部署计划的存储帐户](../common/storage-account-create.md#create-a-storage-account)。 对于任何新的存储帐户，请考虑遵循以下基本设置。

#### <a name="subscription"></a>订阅

你可以使用与你的 StorSimple 部署相同的订阅，也可以使用其他订阅。 唯一的限制是，你的订阅必须与 StorSimple 订阅位于同一个 Azure Active Directory 租户中。 在开始迁移之前，请考虑将 StorSimple 订阅移动到正确的租户。 只能移动整个订阅。 单个 StorSimple 资源不能移到不同的租户或订阅。

#### <a name="resource-group"></a>资源组

资源组帮助组织资源和管理管理权限。 详细了解 [Azure 中的资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)。

#### <a name="storage-account-name"></a>存储帐户名称

存储帐户的名称将成为 URL 的一部分，并且具有特定的字符限制。 在命名约定中，假设存储帐户名称在世界中必须是唯一的，只允许使用小写字母和数字，要求介于3到24个字符之间，并且不允许使用连字符或下划线等特殊字符。 有关详细信息，请参阅 [Azure 存储资源命名规则](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)。

#### <a name="location"></a>位置

存储帐户的位置或 Azure 区域非常重要。 如果使用 Azure 文件同步，则所有存储帐户都必须与存储同步服务资源位于同一区域。 你选择的 Azure 区域应接近或集中于你的本地服务器和用户。 部署资源后，无法更改其区域。

您可以选择一个不同的区域，在该区域中 (存储帐户) 当前所在的位置。

> [!IMPORTANT]
> 如果从当前 StorSimple 存储帐户位置选择不同的区域，则在迁移过程中 [会收取出口费用](https://azure.microsoft.com/pricing/details/bandwidth) 。 数据将离开 StorSimple 区域，并输入新的存储帐户区域。 如果你离开同一 Azure 区域，则不收取任何带宽费用。

#### <a name="performance"></a>性能

你可以选择为 Azure 文件共享或标准存储 (SSD) 选择高级存储。 标准存储包含 [用于文件共享的多个层](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share)。 对于从 StorSimple 迁移的大多数客户，标准存储是正确的选择。

仍不确定？

* 如果需要 [高级 Azure 文件共享的性能](understanding-billing.md#provisioned-billing)，请选择 "高级存储"。
* 为常规用途文件服务器工作负荷（包括热数据和存档数据）选择 "标准存储"。 如果在云中共享中的唯一工作负荷将 Azure 文件同步，请选择 "标准存储"。

#### <a name="account-kind"></a>帐户类型

* 对于标准存储，请选择 *StorageV2 (常规用途 v2)*。
* 对于高级文件共享，请选择 " *FileStorage*"。

#### <a name="replication"></a>复制

有多种可用的复制设置。 详细了解不同的复制类型。

只能从以下两个选项中选择一个：

* *本地冗余存储 (LRS)*。
* *区域冗余存储 (ZRS)*，不适用于所有 Azure 区域。

> [!NOTE]
> 只有 LRS 和 ZRS 冗余类型与大 100-TiB 容量的 Azure 文件共享兼容。

当前不支持所有变体中的异地冗余存储 (GRS) 。 稍后可以切换冗余类型，并在 Azure 中的支持到达时切换到 GRS。

#### <a name="enable-100-tib-capacity-file-shares"></a>启用 100-TiB 文件共享

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="显示用于创建存储帐户的 Azure 门户中的 &quot;高级&quot; 选项卡的图像。":::
    :::column-end:::
    :::column:::
        在 Azure 门户中新建存储帐户向导的 " **高级** " 部分下，可以在此存储帐户中启用 **大型文件共享** 支持。 如果此选项不可用，则很可能选择了错误的冗余类型。 确保仅选择 "LRS" 或 "ZRS"，以使此选项变为可用。
    :::column-end:::
:::row-end:::

选择 TiB 的100大容量文件共享具有以下优点：

* 与较小的 5 TiB 文件共享相比，性能会大大增加 (例如，IOPS) 的10倍。
* 迁移的速度要快得多。
* 确保文件共享的容量足以容纳要迁移到其中的所有数据。
* 覆盖未来的增长。

### <a name="azure-file-shares"></a>Azure 文件共享

创建存储帐户后，请根据第1阶段的迁移计划，中转到存储帐户的 " **文件共享** " 部分，并部署适当数量的 Azure 文件共享。 对于 Azure 中的新文件共享，请考虑遵循以下基本设置。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="显示新文件共享 UI 的 Azure 门户屏幕截图。":::
    :::column-end:::
    :::column:::
        </br>**名称**</br>支持小写字母、数字和连字符。</br></br>**配额**</br>此处的配额相当于 Windows Server 实例上的 SMB 硬配额。 最佳做法是不要在此处设置配额，因为在达到配额时，迁移和其他服务将会失败。</br></br>**层级**</br>为新的文件共享选择 " **事务优化** "。 在迁移过程中，将发生许多事务。 将层更改为最适合你的工作负荷的层会更经济高效。
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple 数据管理器

将保存迁移作业的 Azure 资源称为 **StorSimple 数据管理器**。 选择 " **新资源**"，然后搜索。 然后选择“创建”。

此临时资源用于业务流程。 迁移完成后，可以取消对其的设置。 它应部署在与 StorSimple 存储帐户相同的订阅、资源组和区域中。

### <a name="azure-file-sync"></a>Azure 文件同步

通过 Azure 文件同步，你可以添加最常访问的文件的本地缓存。 与 StorSimple 的缓存能力类似，Azure 文件同步云分层功能提供了本地访问延迟，并改进了对 Windows Server 实例和多站点同步上可用缓存容量的控制。如果你的目标是本地缓存，则在本地网络中准备 Windows Server VM (物理服务器和故障转移群集，并且具有足够的直接附加存储容量) 支持。

> [!IMPORTANT]
> 尚无 Azure 文件同步设置。 最好在完成共享的迁移后设置 Azure 文件同步。 部署 Azure 文件同步不应在迁移的第4阶段之前开始。

### <a name="phase-2-summary"></a>阶段2摘要

在第2阶段结束时，你将在其中部署存储帐户和所有 Azure 文件共享。 还有一个 StorSimple 数据管理器资源。 在配置迁移作业时，将使用第3阶段中的后一项。

## <a name="phase-3-create-and-run-a-migration-job"></a>阶段3：创建和运行迁移作业

本部分介绍如何设置迁移作业，并仔细地将 StorSimple 卷上的目录映射到所选的目标 Azure 文件共享。 若要开始，请转到 StorSimple 数据管理器，查找菜单上的 " **作业定义** "，然后选择 " **+ 作业定义**"。 目标存储类型为默认的 **Azure 文件共享**。

![StorSimple 8000 系列迁移作业类型。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "作业定义 Azure 门户的屏幕截图，其中打开了新的 "作业定义" 对话框，该对话框要求提供作业类型： "复制到文件共享" 或 "blob 容器"。")

> [!IMPORTANT]
> 在运行任何迁移作业之前，请停止 StorSimple 卷的任何自动计划的备份。

:::row:::
    :::column:::
        ![StorSimple 8000 系列迁移作业。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "数据转换服务作业的新作业创建窗体的屏幕截图。")
    :::column-end:::
    :::column:::
        **作业定义名称**</br>此名称应指示你要移动的文件集。 为其提供类似于 Azure 文件共享的名称是一种很好的做法。 </br></br>**作业运行的位置**</br>选择区域时，必须选择与 StorSimple 存储帐户相同的区域，如果该帐户不可用，则必须选择该区域。 </br></br><h3>源</h3>**源订阅**</br>选择要在其中存储 StorSimple 设备管理器资源的订阅。 </br></br>**StorSimple 资源**</br>选择你的 StorSimple 设备管理器你的设备已注册到。 </br></br>**服务数据加密密钥**</br>请查看 [本文前面的部分](#storsimple-service-data-encryption-key) ，以防找不到记录中的密钥。 </br></br>**设备**</br>选择包含要迁移的卷的 StorSimple 设备。 </br></br>**Volume**</br>选择源卷。 稍后你将决定是否要将整个卷或子目录迁移到目标 Azure 文件共享中。 </br></br><h3>目标</h3>选择 "订阅"、"存储帐户" 和 "Azure 文件共享" 作为此迁移作业的目标。
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 将使用最新的卷备份来执行迁移。 请确保至少存在一个卷备份，否则作业将失败。 还要确保最新的备份是最新的，以便尽可能小地使实时共享保持增量。 在运行刚刚创建的作业 *之前* ，可以手动触发并完成其他卷备份。

### <a name="directory-mapping"></a>目录映射

对于迁移作业，目录映射是可选的。 如果将此部分留空，StorSimple 卷的根目录上的 *所有* 文件和文件夹都将移动到目标 Azure 文件共享的根目录中。 大多数情况下，在 Azure 文件共享中存储整个卷的内容并不是最佳方法。 通常更好的做法是在 Azure 中的多个文件共享上拆分卷的内容。 如果尚未制定计划，请先参阅 [将 StorSimple 卷映射到 Azure 文件共享](#map-your-existing-storsimple-volumes-to-azure-file-shares) 。

作为迁移计划的一部分，你可能已决定 StorSimple 卷上的文件夹需要跨多个 Azure 文件共享进行拆分。 如果是这种情况，可以完成以下操作：

1. 定义多个作业以将文件夹迁移到一个卷上。 每个都具有相同的 StorSimple 卷源，但与目标不同的 Azure 文件共享。
1. 通过使用作业创建窗体的 " **目录映射** " 部分并遵循特定的 [映射语义](#semantic-elements)，精确指定 StorSimple 卷中的哪些文件夹需要迁移到指定的文件共享中。

> [!IMPORTANT]
> 提交表单时，无法验证此窗体中的路径和映射表达式。 如果未正确指定映射，作业可能会完全失败或产生不需要的结果。 在这种情况下，通常最好删除 Azure 文件共享，然后重新创建它，然后在新的迁移作业中修复此共享的映射语句。 使用固定映射语句运行新作业可以修复省略的文件夹并将其导入现有共享。 但是，只有由于路径拼写错误而被忽略的文件夹才能以这种方式寻址。

#### <a name="semantic-elements"></a>语义元素

映射以从左到右的形式表示： [\source path] \> [\target path]。

|语义字符          | 含义  |
|:---------------------------|:---------|
| **\\**                     | 根级别指示器。       |
| **\>**                     | [Source] 和 [target 映射] 运算符。     |
|**\|** 或返回 (换行)  | 两个文件夹映射指令的分隔符。 </br>或者，您可以省略此字符，然后选择 **Enter** 以在其自己的行上获取下一个映射表达式。        |

### <a name="examples"></a>示例
将文件夹 *用户数据* 的内容移动到目标文件共享的根目录：
``` console
\User data > \
```
将整个卷内容移动到目标文件共享上的新路径中：
``` console
\ > \Apps\HR tracker
```
将源文件夹内容移动到目标文件共享上的新路径中：
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
将多个源位置排序为新的目录结构：
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>语义规则

* 始终指定相对于根级别的文件夹路径。
* 开始每个具有根级别指示器 "" 的文件夹路径 \\ 。
* 不要包含驱动器号。
* 指定多个路径时，源路径或目标路径不能重叠：</br>
   无效的源路径重叠示例：</br>
    *\\folder\1 > \\ 文件夹*</br>
    *\\文件夹 \\ 1 \\ 2 > \\ folder2*</br>
   无效的目标路径重叠示例：</br>
   *\\文件夹 > \\*</br>
   *\\folder2 > \\*</br>
* 将忽略不存在的源文件夹。
* 将创建目标中不存在的文件夹结构。
* 与 Windows 一样，文件夹名称不区分大小写，但保留大小写。

> [!NOTE]
> 转换作业不会复制 *\System Volume Information* 文件夹和 StorSimple 卷上的 *$Recycle* 的内容。

### <a name="phase-3-summary"></a>阶段3摘要

在第3阶段结束时，你将从 StorSimple 卷运行数据转换服务作业到 Azure 文件共享。 现在，你可以专注于在共享的迁移作业完成) 或将信息工作者和应用的共享访问权限定向到 Azure 文件共享后，为共享 (设置 Azure 文件同步。

## <a name="phase-4-access-your-azure-file-shares"></a>阶段4：访问 Azure 文件共享

访问 Azure 文件共享的主要策略有两种：

* **Azure 文件同步**：将 [Azure 文件同步部署](#deploy-azure-file-sync) 到本地 Windows Server 实例。 Azure 文件同步具有本地缓存的所有优点，就像 StorSimple 一样。
* **直接共享访问**： [部署直接共享访问](#deploy-direct-share-access)。 如果你的给定 Azure 文件共享的访问方案无法从本地缓存中获益，或者你不再能够承载本地 Windows Server 实例，请使用此策略。 在这里，你的用户和应用将继续通过 SMB 协议访问 SMB 共享。 这些共享不再位于本地服务器上，而是直接在云中。

你应该已经决定了在本指南的 [第1阶段](#phase-1-prepare-for-migration) 中最适合的选项。

本部分的其余部分重点介绍部署说明。

### <a name="deploy-azure-file-sync"></a>部署 Azure 文件同步

现在可以部署 Azure 文件同步的一部分了。

1. 创建 Azure 文件同步云资源。
1. 在本地服务器上部署 Azure 文件同步代理。
1. 向云资源注册服务器。

不要创建任何同步组。 使用 Azure 文件共享设置同步仅应在完成将迁移作业迁移到 Azure 文件共享后发生。 如果你在迁移完成之前开始使用 Azure 文件同步，则可能会导致你的迁移不太困难，因为你无法轻松地判断何时启动剪切。

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果要在迁移完成后更改数据驻留的 Azure 区域，请在此迁移的目标存储帐户所在的同一区域中部署存储同步服务。

#### <a name="deploy-an-on-premises-windows-server-instance"></a>部署本地 Windows Server 实例

* 以虚拟机或物理服务器的 2012R2) 至少创建 Windows Server 2019 (。 还支持 Windows Server 故障转移群集。 不要重复使用前方 StorSimple 8100 或8600的服务器。
* 预配或添加直接附加存储。 不支持网络附加存储。

最佳做法是，为新的 Windows Server 实例提供相当或更大的存储量，而不是 StorSimple 8100 或8600设备在本地提供缓存。 你将使用与 StorSimple 设备相同的方式使用 Windows Server 实例。 如果它具有与设备相同的存储量，则缓存体验应该类似，如果不相同。 你可以在中添加或删除 Windows Server 实例中的存储。 此功能使你能够扩展本地卷大小和可用于缓存的本地存储量。

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>为文件同步准备 Windows Server 实例

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>在 Windows Server 实例上配置 Azure 文件同步

注册的本地 Windows Server 实例必须已准备就绪，并且已连接到 internet 以执行此过程。

> [!IMPORTANT]
> 在继续操作之前，必须先完成 StorSimple 将文件和文件夹迁移到 Azure 文件共享。 请确保不会对文件共享进行更多更改。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 确保启用云分层。 云分层是一项 Azure 文件同步功能，它允许本地服务器的存储容量比存储在云中的存储容量少，同时还提供完整的命名空间。 本地关注的数据也会在本地缓存，以实现快速的本地访问性能。 在此步骤中启用云分层的另一个原因是我们不想在此阶段同步文件内容。 此时只应移动命名空间。

### <a name="deploy-direct-share-access"></a>部署直接共享访问

:::row:::
    :::column:::
        [![有关如何安全地将 Azure 文件共享直接公开给信息工作者和应用程序的分步指南和演示-单击播放！](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        此视频介绍了如何通过五个简单的步骤将 Azure 文件共享直接安全地公开给信息工作者和应用。</br>
        该视频引用一些主题的专用文档：

* [标识概述](storage-files-active-directory-overview.md)
* [如何域加入存储帐户](storage-files-identity-auth-active-directory-enable.md)
* [Azure 文件共享的网络概述](storage-files-networking-overview.md)
* [如何配置公共和专用终结点](storage-files-networking-endpoints.md)
* [如何配置 S2S VPN](storage-files-configure-s2s-vpn.md)
* [如何配置 Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [如何配置 Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [如何配置 DNS 转发](storage-files-networking-dns.md)
* [配置 DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>阶段4摘要

在此阶段中，你已在 StorSimple 数据管理器中创建并运行多个数据转换服务作业。 这些作业已将文件和文件夹迁移到 Azure 文件共享。 你还 Azure 文件同步或准备好了网络和存储帐户，以便进行直接共享访问。

## <a name="phase-5-user-cut-over"></a>阶段5：用户剪切

此阶段就是对迁移进行汇总：

* 规划停机时间。
* 当阶段3中的数据转换作业正在运行时，请及时了解你的用户和应用在 StorSimple 端产生的任何更改。
* 通过直接共享访问 Azure 文件同步或 Azure 文件共享将用户转移到新的 Windows Server 实例。

### <a name="plan-your-downtime"></a>计划停机时间

此迁移方法对于用户和应用需要一定的停机时间。 目标是将停机时间保持在最低限度。 以下注意事项可能会有所帮助：

* 在运行数据转换作业时，保留 StorSimple 卷可用。
* 为共享运行完数据迁移作业后，就可以从 StorSimple 卷或共享中删除用户访问权限， (至少) 写入访问权限。 最终 RoboCopy 将捕获 Azure 文件共享。 然后，您可以对用户进行剪切。 运行 RoboCopy 的位置取决于您选择使用 Azure 文件同步还是直接共享访问。 关于 RoboCopy 的即将发布的部分介绍了该主题。
* 完成 RoboCopy 追赶后，便可以通过 Azure 文件共享直接向用户公开新位置，也可以使用 Azure 文件同步将 Windows Server 实例上的 SMB 共享公开给用户。通常，使用 DFS N 部署可快速高效地完成剪切。 它会使现有共享地址保持一致，并 repoint 到包含已迁移文件和文件夹的新位置。

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>确定你的命名空间完全同步到你的服务器的时间

当你对 Azure 文件共享使用 Azure 文件同步时，必须在启动任何本地 RoboCopy *之前* 确定整个命名空间已完成下载到服务器，这一点很重要。 下载命名空间所花的时间取决于 Azure 文件共享中的项目数。 可以通过两种方法来确定命名空间是否已完全到达服务器。

#### <a name="azure-portal"></a>Azure 门户

你可以使用 Azure 门户来查看你的命名空间已完全到达的时间。

* 登录到 Azure 门户，并中转到你的同步组。 检查同步组和服务器终结点的同步状态。
* 有趣的方向就是下载。 如果新预配了服务器终结点，则它将显示 **初始同步**，这表示命名空间仍处于关闭状态。
更改了除 **初始同步** 之外的任何内容之后，你的命名空间将在服务器上完全填充。 你现在可以使用本地 RoboCopy 继续操作。

#### <a name="windows-server-event-viewer"></a>Windows Server 事件查看器

你还可以使用 Windows Server 实例上的事件查看器来告知命名空间已完全到达的时间。

1. 打开 **事件查看器**，并中转到 " **应用程序和服务**"。
1. 请参阅并打开 **Microsoft\FileSync\Agent\Telemetry**。
1. 查找与已完成的同步会话对应的最近 **事件 9102**。
1. 选择 " **详细信息**"，并确认正在查看 **SyncDirection** 值为 "正在 **下载**" 的事件。
1. 对于命名空间已完成下载到服务器的时间，将会出现单个事件，其中包含 **方案**、值 **FullGhostedSync** 和 **HResult**  =  **0**。
1. 如果错过了该事件，还可以通过 **SyncDirection**   =  **下载** 和 **方案**  =  **"RegularSync"** 查找其他9102事件。 查找这些事件中的一个还表明命名空间已完成下载并同步到定期同步会话，无论是否有任何同步操作。

### <a name="a-final-robocopy"></a>最终 RoboCopy

此时，本地 Windows Server 实例与 StorSimple 8100 或8600设备之间存在差异。

1. 在迁移过程中，需要与在 StorSimple 端产生的用户或应用所做的更改保持同步。
1. 在使用 Azure 文件同步的情况下： StorSimple 设备具有填充的缓存和 Windows Server 实例，其中只包含一个命名空间，此时不会在本地存储任何文件内容。 最终 RoboCopy 可以通过在本地缓存的文件内容中提取尽可能多的内容来快速开始本地 Azure 文件同步缓存，并且可以在 Azure 文件同步服务器上使用。
1. 由于无效字符，数据转换作业可能会遗留某些文件。 如果是这样，请将它们复制到已启用 Azure 文件同步的 Windows Server 实例。 稍后，你可以对其进行调整以使其同步。如果不将 Azure 文件同步用于特定共享，则可以更好地使用 StorSimple 卷上的无效字符来重命名文件。 然后直接对 Azure 文件共享运行 RoboCopy。

> [!WARNING]
> 在服务器具有完全下载的 Azure 文件共享的命名空间之前， *不能* 启动 RoboCopy。 有关详细信息，请参阅 [确定何时将命名空间完全下载到你的服务器](#determine-when-your-namespace-has-fully-synced-to-your-server)。

 你只想要复制在迁移作业上次运行后更改的文件，以及之前尚未完成这些作业的文件。 完成迁移后，你可以解决此问题，原因是它们没有在以后的服务器上移动。 有关详细信息，请参阅 [Azure 文件同步故障排除](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

RoboCopy 具有多个参数。 下面的示例展示了一个完成的命令以及选择这些参数的原因列表。

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景色：

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      允许 RoboCopy 运行多线程。 默认值为8，最大值为128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      将状态作为 UNICODE 输出到日志文件 (覆盖现有日志) 。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      输出到控制台窗口。 与输出一起用于日志文件。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      省略日志记录以保持日志的可读性。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      在备份应用程序使用的相同模式下运行 RoboCopy。 它允许 RoboCopy 移动当前用户没有权限的文件。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允许 RoboCopy 仅考虑源 (StorSimple 设备) 和目标 (Windows Server 目录) 之间的增量。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      文件副本的保真度 (默认值为/COPY： DAT) 、复制标志： D = 数据、A = 属性、T = 时间戳、S = Security = NTFS Acl、O = Owner information、U = 审核信息。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      将所有文件信息复制 (等效于/COPY： DATSOU) 。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      目录副本的保真度 (默认值为/DCOPY： DA) ，复制标志： D = 数据，A = 属性，T = 时间戳。
   :::column-end:::
:::row-end:::

在配置 RoboCopy 命令的源和目标位置时，请确保查看源和目标的结构，以确保它们匹配。 如果使用了迁移作业的目录映射功能，则根目录结构可能不同于 StorSimple 卷的结构。 如果是这种情况，则可能需要多个 RoboCopy 作业，每个子目录一个。 如果不确定命令是否将按预期执行，则可以使用 */l* 参数，该参数将模拟命令而不进行任何更改。

此 RoboCopy 命令使用/MIR，因此不会移动与 (分层文件相同的文件，例如) 。 但如果源路径和目标路径错误，/MIR 还会清除 StorSimple 源路径上不存在的 Windows Server 实例或 Azure 文件共享上的目录结构。 对于 RoboCopy 作业，它们必须完全匹配，以达到其预期目的：通过在迁移过程中进行的最新更改来更新已迁移的内容。

请查阅 RoboCopy 日志文件，查看文件是否已遗留。 如果存在问题，请修复问题，然后重新运行 RoboCopy 命令。 在修复你关注的文件或文件夹的未解决问题之前，请不要取消预配任何 StorSimple 资源。

如果不使用 Azure 文件同步来缓存相关的特定 Azure 文件共享，而是选择进行直接共享访问：

1. 将[Azure 文件共享](storage-how-to-use-files-windows.md#mount-the-azure-file-share)作为网络驱动器装载到本地 Windows 计算机。
1. 在 StorSimple 与装载的 Azure 文件共享之间执行 RoboCopy。 如果文件未复制，请在 StorSimple 端修复其名称以删除无效字符。 然后重试 RoboCopy。 前面列出的 RoboCopy 命令可以多次运行，而不会导致不必要的重新调用 StorSimple。

### <a name="user-cut-over"></a>用户剪切

如果使用 Azure 文件同步，则可能需要在与 StorSimple 卷上的共享匹配的已启用 Azure 文件同步 Windows Server 实例上创建 SMB 共享。 你可以事先加载此步骤，并在之前执行此步骤，而不会在此丢失时间。 但在此之前，你必须确保无人有权访问 Windows Server 实例。

如果你有一个 DFS-N 部署，则可以将 DFN-Namespaces 指向新的服务器文件夹位置。 如果你没有 DFS-N 部署，并且你在本地使用 Windows Server 实例前端了8100或8600设备，则可以将该服务器从域中取出。 然后，该域加入启用了新 Azure 文件同步的 Windows Server 实例。 在此过程中，请为服务器提供与旧服务器相同的服务器名称和共享名称，以便对用户、组策略和脚本的剪切不变。

详细了解 [DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)。

## <a name="deprovision"></a>预配

取消设置资源时，将失去对该资源及其数据的配置的访问权限。 取消预配无法撤消。 在确认之前，请不要继续操作：

* 迁移已完成。
* 你要取消预配的 StorSimple 文件、文件夹或卷备份没有依赖项。

在开始之前，最佳做法是在生产环境中观察新的 Azure 文件同步部署。 这种时间使您有机会解决您可能遇到的任何问题。 在至少经过几天的时间内观察到 Azure 文件同步部署后，可以按以下顺序开始预配资源：

1. 通过 Azure 门户取消预配 StorSimple 数据管理器资源。 所有 DTS 作业都将随之一起删除。 你将无法轻松地检索复制日志。 如果它们对你的记录很重要，请在取消预配前检索它们。
1. 确保已迁移 StorSimple 物理设备，然后将其注销。 如果你不完全确定它们已迁移，请勿继续。 如果在这些资源仍是必需的情况下将其取消预配，则无法恢复数据或其配置。<br>或者，你可以首先取消预配 StorSimple 卷资源，这会清除设备上的数据。 这可能需要几天时间，并 **不会** 取证式设备上的数据。 如果这对你来说很重要，请分别从资源取消预配和根据策略处理磁盘清。
1. 如果 StorSimple 设备管理器中没有剩余的已注册的设备，则可以继续删除设备管理器资源本身。
1. 现在可以在 Azure 中删除 StorSimple 存储帐户。 同样，在继续操作之前，停止并确认迁移已完成，并且不会有任何内容依赖于此数据。
1. 从数据中心拔出 StorSimple 物理设备。
1. 如果你拥有 StorSimple 设备，则可以自由地将其回收。 如果设备已租用，请通知 lessor 并根据需要返回设备。

迁移已完成。

> [!NOTE]
> 仍有疑问或遇到了什么问题？</br>
> 这里我们将为你提供帮助 AzureFilesMigration@microsoft.com 。

## <a name="next-steps"></a>后续步骤

* 更熟悉 [Azure 文件同步： aka.ms/AFS](./storage-sync-files-planning.md)。
* 了解 [云分层](storage-sync-cloud-tiering.md) 策略的灵活性。
* 在 Azure 文件共享上[启用 Azure 备份](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane)，以安排快照并定义备份保留计划。
* 如果在 Azure 门户中看到某些文件未同步，请参阅 [故障排除指南](storage-sync-files-troubleshoot.md) 以获取解决这些问题的步骤。