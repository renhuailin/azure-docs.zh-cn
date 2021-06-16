---
title: 在 Azure SAP HANA（大型实例）上进行 HANA 备份和还原 | Microsoft Docs
description: 如何在 Azure SAP HANA（大型实例）上执行 HANA 备份和还原
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d72825dd00bdb90425a85f9fcfb7cff56f7241ef
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577816"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>在 HANA 大型实例上备份和还原 SAP HANA

>[!IMPORTANT]
>本文不是 SAP HANA 管理文档或 SAP 说明的替代文档。 我们假定你对 SAP HANA 管理和操作有扎实的了解和专业知识，尤其是在备份、还原、高可用性和灾难恢复方面。 本文中提供的是 SAP HANA Studio 的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

重要的是使用自己的 HANA 版本或发布，在相应的环境中执行步骤和过程。 本文描述的某些过程经过简化，更易理解。 它们不会用作最终操作手册的详细步骤。 如果想针对配置创建操作手册，请测试并实践相关过程，并记录与特定配置相关的过程。 

在操作数据库方面，一个要点是保护数据库免受灾难性事件的影响。 这些事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。

备份数据库并具备还原到任意时间点（例如，在某人删除关键数据之前的时间点）的能力，可以尽可能地还原到发生中断之前所处的状态。

若要具备还原能力，必须执行两种类型的备份：

- 数据库备份：完整备份、增量备份或差异备份
- 事务日志备份

除了在应用程序级别执行完整数据库备份以外，还可以使用存储快照执行备份。 存储快照不能代替事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 存储快照可以通过快速提供数据库的前滚映像来提升恢复速度。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- **自制 (DIY)。** 确保有足够的磁盘空间后，使用以下磁盘备份方法之一执行完整的数据库和日志备份。 可以直接备份到 HANA 大型实例单元上附加的卷，或者备份到 Azure 虚拟机 (VM) 中设置的 NFS 共享。 在后一种情况中，客户在 Azure 中设置 Linux VM、将 Azure 存储附加到 VM 并通过该 VM 中配置的 NFS 服务器共享此存储。 如果对直接附加到 HANA 大型实例单元的卷执行备份，则将备份复制到 Azure 存储帐户。 请在设置 Azure VM 以导出基于 Azure 存储的 NFS 共享之后，再执行此操作。 你还可以使用 Azure 备份保管库或 Azure 冷存储。 

   另一个选项是将备份复制到 Azure 存储帐户后，使用第三方数据保护工具来存储备份。 对于出于合规性和审核目的而需要长期存储的数据，还有必要使用 DIY 备份选项。 在所有情况下，备份均复制到通过 VM 和 Azure 存储表示的 NFS 共享中。

- **基础结构备份和还原功能。** 还可以使用 Azure SAP HANA（大型实例）底层基础结构提供的备份和还原功能。 此选项满足备份和快速还原的需要。 本部分的余下内容介绍 HANA 大型实例提供的备份和还原功能。 本部分还会介绍备份和还原功能与 HANA 大型实例提供的灾难恢复功能之间的关系。

> [!NOTE]
>   HANA 大型实例底层基础结构使用的快照技术依赖于 SAP HANA 快照。 目前，SAP HANA 快照无法与 SAP HANA 多租户数据库容器的多个租户配合使用。 如果只部署了一个租户，SAP HANA 快照确实有效，你可以使用这种方法。

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 Azure SAP HANA（大型实例）的存储快照

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 对 /hana/data 和 /hana/shared（包括 /usr/sap）卷触发快照时，快照技术会在运行存储快照前启动 SAP HANA 快照。 在恢复存储快照后，此 SAP HANA 快照是最终日志还原的设置点。 为使 HANA 快照成功，你需要一个活动的 HANA 实例。 在 HSR 方案中，不能执行 HANA 快照的当前辅助节点不支持存储快照。
- 成功运行存储快照后，将删除 SAP HANA 快照。
- 事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来创建快照。 在这种情况下，不需要运行 HANA 快照。
- 如果在发生生产中断时，必须将数据库还原到某个时间点，可请求 Microsoft Azure 支持部门或 Azure SAP HANA 服务部门还原到特定的存储快照。 一个例子就是按计划将沙盒系统还原到其原始状态。
- 存储快照中包含的 SAP HANA 快照是一个偏移点，用于应用在创建存储快照后运行和存储的事务日志备份。
- 创建这些事务日志备份是为了将数据库还原到某个时间点。

可针对三种类型的卷执行存储快照：

- 基于 /hana/data 和 /hana/shared（包括 /usr/sap）创建组合快照。 此快照需要创建 SAP HANA 快照，为存储快照进行准备工作。 从存储的角度来看，SAP HANA 快照可确保数据库处于一致状态。 对还原过程而言，这是一个可以设置的点。
- 基于 /hana/logbackups 创建单独的快照。
- 操作系统分区。

若要获取最新的快照脚本和文档，请参阅 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 下载快照脚本包时，会获得三个文件。 其中一个文件以 PDF 格式记录了所提供的功能。 下载工具集后，请按照“获取快照工具”中的说明进行操作。

## <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照使用分配给 HANA 大型实例单元的存储空间。 计划存储快照以及要保留的存储快照数量时，请考虑以下方面。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 特定的存储快照（在最初创建时）几乎不消耗存储空间。
- 随着数据内容以及存储卷上 SAP HANA 数据文件内容的不断更改，快照需要存储原始块内容以及数据更改。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 执行这些卷的快照会消耗卷空间。 你需要：

- 确定何时计划存储快照。
- 监视存储卷的空间消耗。 
- 管理所存储的快照数量。 

将大量数据导入 HANA 数据库或对其执行其他重大更改时，可以禁用存储快照。 


以下部分提供了有关如何执行这些快照的信息，以及一些常规建议：

- 尽管硬件可以为每个卷保留 255 个快照，但实际保留的快照数应低于这个数字。 建议设为 250 个或更少。
- 执行存储快照之前，请监视并跟踪可用空间。
- 根据可用空间减少存储快照数量。 可以减少保留的快照数量，或者扩展卷。 可以 1 TB 为单位订购附加存储。
- 在使用 SAP 平台迁移工具 (R3load) 将数据移入 SAP HANA，或者从备份还原 SAP HANA 数据库或执行其他活动期间，可以禁用 /hana/data 卷上的存储快照。 
- 在对 SAP HANA 表进行重大重组期间，应尽可能避免执行存储快照。
- 存储快照是利用 Azure 上的 SAP HANA（大型实例）灾难恢复功能的先决条件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服务存储快照的先决条件

为确保快照脚本成功运行，请务必在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。 Perl 已预安装在 HANA 大型实例单元中。 若要检查 Perl 版本，请使用以下命令：

`perl -v`

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>设置存储快照

若要设置 HANA 大型实例的存储快照，请遵循以下步骤。
1. 确保在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。
1. 修改 /etc/ssh/ssh\_config，添加代码行 _MACs hmac-sha1_。
1. 在运行的每个 SAP HANA 实例的主节点上创建一个 SAP HANA 备份用户帐户（如果适用）。
1. 在所有 SAP HANA 大型实例服务器上安装 SAP HANA HDB 客户端。
1. 在每个区域的第一台 SAP HANA 大型实例服务器上，创建一个公钥用于访问对快照创建操作进行控制的底层存储基础结构。
1. 将脚本和配置文件从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 复制到 SAP HANA 安装中的 **hdbsql** 位置。
1. 必要时，根据相应的客户规范修改 *HANABackupDetails.txt* 文件。

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 获取最新的快照脚本和文档。 对于前面列出的步骤，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 方案的注意事项
如果对一个 HANA 大型实例单元上的多个 SAP HANA 实例运行 [MCOD 方案](https://launchpad.support.sap.com/#/notes/1681092)，你需要为每个 SAP HANA 实例预配单独的存储卷。 有关 MDC 和其他注意事项的详细信息，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“要记住的重要事项”。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步骤 1：安装 SAP HANA HDB 客户端

Azure SAP HANA（大型实例）上安装的 Linux 操作系统包含运行 SAP HANA 存储快照（以进行备份和灾难恢复）所需的文件夹和脚本。 在 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 中查找最新版本。 

安装 SAP HANA 时，你需要负责在在 HANA 大型实例单元上安装 SAP HANA HDB 客户端。

### <a name="step-2-change-the-etcsshssh_config"></a>步骤 2：更改 /etc/ssh/ssh\_config

[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“启用与存储的通信”介绍了此步骤。


### <a name="step-3-create-a-public-key"></a>步骤 3：创建公钥

为能够访问 HANA 大型实例租户的存储快照界面，请通过公钥建立登录过程。 

在租户中的第一台 Azure SAP HANA（大型实例）服务器上，创建一个公钥来访问存储基础结构。 使用公钥时，无需密码即可登录到存储快照界面。 你也不需要维护包含公钥的密码凭据。 

若要生成公钥，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“启用与存储的通信”。


### <a name="step-4-create-an-sap-hana-user-account"></a>步骤 4：创建 SAP HANA 用户帐户

若要开始创建 SAP HANA 快照，请在 SAP HANA 中创建一个可供存储快照脚本使用的用户帐户。 在 SAP HANA Studio 中创建用于此目的的 SAP HANA 用户帐户。 必须在 SYSTEMDB 下创建用户，而不是在适用于 MDC 的 SID 数据库下创建。 在单容器环境中，在租户数据库中创建用户。 此帐户必须具有“备份管理”和“目录读取” 特权。 

若要设置和使用用户帐户，请参阅 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md) 中的“启用与 SAP HANA 的通信”。


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步骤 5：为 SAP HANA 用户帐户授权

在此步骤中，向已创建的 SAP HANA 用户帐户授权，以便脚本无需在运行时提交密码。 SAP HANA 命令 `hdbuserstore` 可用于创建 SAP HANA 用户密钥。 密钥存储在一个或多个 SAP HANA 节点上。 用户可以使用用户密钥从脚本流程内部访问 SAP HANA，而无需管理密码。 本文稍后会介绍脚本过程。

>[!IMPORTANT]
>在运行快照命令的用户上下文中运行这些配置命令。 否则，快照命令将无法正常运行。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步骤 6：获取快照脚本、配置快照及测试配置和连接

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 下载最新版本的脚本。 脚本的安装方式已随着 4.1 版脚本的发布而改变。 有关详细信息，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“启用与 SAP HANA 的通信”。

有关命令的确切顺序，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“轻松安装快照工具（默认）”。 建议使用默认安装。 

若要从版本 3.x 升级到 4.1，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“升级现有安装”。 若要卸载 4.1 工具集，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“卸载快照工具”。

不要忘记运行[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)的“完成快照工具的设置”中所述的步骤。

在 [用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中，“这些快照工具是什么？”介绍了安装不同脚本和文件的目的。

在配置快照工具之前，请确保还正确配置了 HANA 备份位置和设置。 有关详细信息，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“SAP HANA 配置”。

[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“配置文件 - HANABackupCustomerDetails.txt”介绍了快照工具集的配置。

#### <a name="test-connectivity-with-sap-hana"></a>测试与 SAP HANA 的连接

将所有配置数据填入 *HANABackupCustomerDetails.txt* 文件后，需要检查 HANA 实例数据的相关配置是否正确。 使用脚本独立于 SAP HANA 纵向扩展或横向扩展配置的脚本 `testHANAConnection`。

有关详细信息，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“检查与 SAP HANA 的连接 - testHANAConnection”。

#### <a name="test-storage-connectivity"></a>测试存储连接

下一测试步骤是基于放入 HANABackupCustomerDetails.txt 配置文件的数据检查与存储的连接。 然后运行测试快照。 在运行 `azure_hana_backup` 命令之前，必须运行此测试。 有关此测试的命令序列，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“检查与存储的连接 - testStorageSnapshotConnection”。

成功登录到存储虚拟机接口后，脚本会继续执行阶段 2 并创建测试快照。 此处显示的输出适用于 SAP HANA 的三节点横向扩展配置。

如果已使用脚本成功运行测试快照，则可以计划实际的存储快照。 如果不成功，请先调查问题，然后继续。 测试快照应保留至第一批实际快照完成为止。


### <a name="step-7-perform-snapshots"></a>步骤 7：执行快照

准备步骤完成后，可以开始配置和计划实际的存储快照。 要计划的脚本会使用 SAP HANA 纵向扩展和横向扩展配置。 备份脚本的定期和常规执行应使用 cron 实用工具来计划。 

有关确切的命令语法和功能，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“执行快照备份 - azure_hana_backup”。 

`azure_hana_backup` 脚本运行时，会在以下三个阶段创建存储快照：

1. 运行 SAP HANA 快照。
1. 运行存储快照。
1. 删除在存储快照运行之前创建的 SAP HANA 快照。

若要运行该脚本，请从脚本复制到的 HDB 可执行文件夹调用该脚本。 

系统根据运行脚本时作为参数提交的快照数管理保留期。 存储快照消耗的时间量由以下因素决定：执行时间长短，以及运行脚本时作为参数提交的快照数。 

如果保留的快照数超过了脚本调用中指定为参数的数字，则运行新快照之前，将删除同一标签中时间最早的存储快照。 作为最后一个调用参数指定的数字是可用于控制保留的快照数的数字。 通过该数字还可间接控制用于快照的磁盘空间。 


## <a name="snapshot-strategies"></a>快照策略
不同类型快照的频率取决于是否使用 HANA 大型实例灾难恢复功能。 此功能依赖于存储快照，这可能需要实施某些与存储快照频率和执行时间长短相关的特殊建议。 

以下注意事项和建议假设不使用 HANA 大型实例提供的灾难恢复功能。 假设用户使用存储快照来获取备份并提供过去 30 天的时点恢复。 鉴于快照数和空间的限制，请考虑以下要求：

- 时间点恢复的恢复时间。
- 使用的空间。
- 潜在灾难恢复的恢复点和恢复时间目标。
- 针对磁盘最终执行的 HANA 完整数据库备份。 每当针对磁盘执行完整数据库备份或执行 **backint** 接口时，存储快照的执行会失败。 如果打算基于存储快照运行完整数据库备份，请确保在此期间禁用存储快照执行。
- 每个卷的快照数限制为 250。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

如果不使用 HANA 大型实例的灾难恢复功能，快照频率会低一些。 在这类情况下，以 12 小时或 24 小时的周期对 /hana/data 和 /hana/shared（包括 /usr/sap）执行组合快照。 将这些快照保留一个月。 对日志备份卷的快照也执行相同操作。 日志备份卷的 SAP HANA 事务日志备份按 5 - 15 分钟的周期执行。

最好是使用 cron 执行计划的存储快照。 使用同一个脚本来满足所有备份和灾难恢复需求。 修改脚本输入，使之与请求的不同备份时间匹配。 根据这些快照的执行时间，在 cron 中做好上述一切计划。 它可以是每小时、12 小时、每天或每周。 

以下示例显示了 /etc/crontab 中的 cron 计划：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
上述示例中有一个每小时执行一次的组合快照，该快照的实施对象包括含 /hana/data 和 /hana/shared/SID（包括 /usr/sap）位置的卷。 使用此类型的快照快速实现过去两天内的时点恢复。 此外，将对这些卷执行每日快照。 因此，你将执行两天的每时快照和四周的每日快照。 事务日志备份卷也每天备份一次。 这些备份将保留四周。 

从 crontab 第三行可知，HANA 事务日志备份计划为每 5 分钟运行一次。 运行存储快照的不同 cron 作业的启动时间是错开的。 这样，便不会在某个时间点同时运行所有快照。 

以下示例中有一个每小时执行一次的组合快照，该快照的实施对象包括含 /hana/data 和 /hana/shared/SID（包括 /usr/sap）位置的卷。 这些快照会保留两天。 事务日志备份卷的快照每 5 分钟运行一次，并保留 4 个小时。 与之前一样，HANA 事务日志文件的备份计划为每 5 分钟运行一次。 

启动事务日志备份后，执行事务日志备份卷的快照时会存在 2 分钟的延迟。 一般情况下，SAP HANA 事务日志备份应在这 2 分钟内完成。 与之前一样，包含启动 LUN 的卷通过存储快照每天备份一次，且会保留四周。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下图演示了上述示例的序列。 不包括启动 LUN。

![备份和快照之间的关系](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 对 /hana/log 卷执行常规写入，将提交的更改记录到数据库。 SAP HANA 将保存点定期写入 /hana/data 卷。 如 crontab 中所指定，SAP HANA 事务日志备份每 5 分钟运行一次。 

由于对 /hana/data 和 /hana/shared/SID 卷触发组合存储快照，因此还会每小时运行一次 SAP HANA 快照。 成功运行 HANA 快照后，系统会运行组合存储快照。 如 crontab 中所指示，在 HANA 事务日志备份后 2 分钟左右，/hana/logbackup 卷上的存储快照每 5 分钟运行一次。

> 

>[!IMPORTANT]
> 仅当与 SAP HANA 事务日志备份配合执行存储快照时，才能针对 SAP HANA 备份使用存储快照。 这些事务日志备份需要能够涵盖执行存储快照的间隔时间段。 

如果向用户承诺时间点恢复目标为 30 天，则需要：

- 在极端情况下，访问针对 /hana/data 和 /hana/shared/SID 且保留期限已达 30 天的组合存储快照。 
- 创建保留时长长于任何两个组合存储快照间隔时间的连续事务日志备份。 因此事务日志备份卷快照的最长保留时长需为 30 天。 如果将事务日志备份复制到位于 Azure 存储的另一个 NFS 共享，则不存在这种情况。 在这种情况下，可以从该 NFS 共享拉取旧事务日志备份。

若要从存储快照和事务日志备份的最终存储复制中获益，请更改 SAP HANA 写入事务日志备份的位置。 可在 HANA Studio 中进行此更改。 

虽然 SAP HANA 会自动备份完整日志段，但仍应指定确定的日志备份间隔时间。 使用灾难恢复选项时尤其如此，因为通常需要以确定性的周期运行日志备份。 在以下案例中，日志备份间隔设为 15 分钟。

![在 SAP HANA Studio 中计划 SAP HANA 备份日志](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

还可以选择频率高于 15 分钟的备份。 较高的频率设置通常与 HANA 大型实例的灾难恢复功能结合使用。 某些客户每 5 分钟执行一次事务日志备份。

如果从未备份过数据库，则最后一步是执行基于文件的数据库备份，以创建必须在备份目录中存在的单个备份项。 否则，SAP HANA 无法启动指定的日志备份。

![创建基于文件的备份以创建单个备份项](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


成功运行第一个存储快照后，删除在步骤 6 中运行的测试快照。 有关详细信息，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“删除测试快照 - removeTestStorageSnapshot”。 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>监视磁盘卷上的快照数量和大小

在特定的存储卷上，可以监视快照的数量以及这些快照的存储消耗量。 `ls` 命令不会显示快照目录或文件。 Linux OS 命令 `du` 显示有关这些存储快照的详细信息（因为这些快照存储在相同的卷上）。 将此命令与以下选项结合使用：

- `du –sh .snapshot`：此选项提供快照目录中所有快照的总数。
- `du –sh --max-depth=1`：此选项列出 **.snapshot** 文件夹中保存的所有快照，以及每个快照的大小。
- `du –hc`：此选项提供所有快照占用的总大小。

使用这些命令可确保创建和存储的快照不会耗尽卷上的所有存储。

>[!NOTE]
>使用上述命令时，不显示启动 LUN 的快照。

### <a name="get-details-of-snapshots"></a>获取快照的详细信息
若要获取有关快照的更多详细信息，请使用脚本 `azure_hana_snapshot_details`。 如果灾难恢复位置处存在活动服务器，则可以在任一位置运行此脚本。 此脚本根据每个包含快照的卷提供以下细分输出： 
   * 一个卷中全部快照的大小
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）

有关命令语法和输出，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的的“列出快照 - azure_hana_snapshot_details”。 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>减少服务器上的快照数

如前所述，你可以减少存储的带有特定标签的快照数。 用于启动快照的命令的最后两个参数是要保留的快照的标签与数量。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在上面的示例中，快照标签为 dailyhana。 要保留的带有此标签的快照数为 28。 对磁盘空间消耗量做出响应时，可能想要减少存储的快照数。 例如，如果希望将快照数减少至 15，实现此操作的简单方法是将脚本的最后一个参数设置为 15 并运行该脚本：

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果使用此设置运行脚本，快照数（包括新存储快照）为 15。 会保存最新的 15 个快照，同时删除 15 个较旧的快照。

 >[!NOTE]
 > 仅当存在超过 1 小时的快照时，此脚本才能减少快照数。 该脚本不会删除不足 1 小时的快照。 这些限制与提供的可选灾难恢复功能相关。

如果不想再保留一组包含备份前缀（如语法示例中的 dailyhana）的快照，请使用 0 作为保留数量来运行该脚本。 然后删除所有与该标签匹配的快照。 删除所有快照可能会影响 HANA 大型实例灾难恢复的功能。

第二种删除特定快照的方法是使用脚本 `azure_hana_snapshot_delete`。 此脚本旨在使用在 HANA Studio 中找到的 HANA 备份 ID 或通过快照名称本身删除快照或一组快照。 目前，备份 ID 仅与为 **hana** 快照类型创建的快照绑定。 logs 和 boot 类型的快照备份不执行 SAP HANA 快照，因此，这些快照没有可供查找的备份 ID。 如果输入快照名称，将在不同卷上查找所有与输入的快照名称匹配的快照。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

有关该脚本的详细信息，请参阅[用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“删除快照 - azure_hana_snapshot_delete”。

以 root 用户身份运行脚本。

>[!IMPORTANT]
>如果有数据仅存在于计划删除的快照上，则删除快照后，会永久丢失数据。


## <a name="file-level-restore-from-a-storage-snapshot"></a>从存储快照实现文件级还原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
对于 **hana** 和 **logs** 快照类型，可以直接在 **.snapshot** 目录中的卷上访问快照。 每个快照有一个子目录。 将执行了快照的每个文件以文件在快照瞬间的状态从该子目录复制到实际目录结构。 

在当前的脚本版本中，未以自助服务的形式向快照还原提供还原脚本。 在故障转移期间，快照还原可以在灾难恢复站点上作为自助式灾难恢复脚本的一部分执行。 若要从现有可用快照还原所需快照，必须通过提出服务请求联系 Microsoft 操作团队。

>[!NOTE]
>单个文件还原不适用于独立于 HANA 大型实例单元类型的启动 LUN 的快照。 .snapshot 目录不在启动 LUN 中公开。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>恢复到最近的 HANA 快照

如果遇到生产中断的情况，可向 Microsoft Azure 支持部门发起客户事件，启动从存储快照恢复的过程。 在生产系统中删除了数据是一个高度紧急的问题，而恢复数据的唯一办法就是还原生产数据库。

在不同的情况下，时间点恢复可能不太紧急，因此可以提前几天规划。 可与 Azure SAP HANA 服务部门合作规划这种恢复，而不要提出高优先级标志。 例如，你可能计划通过应用新的增强包来升级 SAP 软件。 然后又需要还原到升级增强包之前的快照状态。

发送请求之前，需要做好准备。 然后，Azure SAP HANA 团队便可以处理请求，提供还原的卷。 接下来，需要基于快照还原 HANA 数据库。

有关使用新工具集还原快照的可能性，请参阅 [Azure SAP HANA 从存储快照手动恢复的指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“如何还原快照”。

若要为请求做好准备，请执行以下步骤。

1. 确定要还原的快照。 除非另有说明，否则只能还原 hana/data 卷。 

1. 关闭 HANA 实例。

   ![关闭 HANA 实例](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 卸载每个 HANA 数据库节点上的数据卷。 如果仍将数据卷载入操作系统，快照还原会失败。

   ![卸载每个 HANA 数据库节点上的数据卷](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 提出 Azure 支持请求，并包含有关如何还原特定快照的说明：

   - 还原期间：Azure SAP HANA 服务部门可能要求你参加电话会议，以进行协调、验证并确认还原的是正确的存储快照。 

   - 还原后：Azure SAP HANA 服务部门会告知存储快照已还原。

1. 完成还原过程后，请重新装载所有数据卷。

   ![重新装载所有数据卷](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



[Azure SAP HANA 从存储快照手动恢复的指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的步骤 7 记载了从存储快照恢复 SAP HANA 数据文件的另一种可能性。

若要从快照备份进行还原，请参阅 [Azure SAP HANA 从存储快照手动恢复的指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 

>[!Note]
>如果快照是由 Microsoft 操作还原的，则无需执行步骤 7。


### <a name="recover-to-another-point-in-time"></a>恢复到另一个时间点
若要还原到某个时间点，请参阅 [Azure SAP HANA 从存储快照手动恢复的指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的“将数据库恢复到以下时间点”。 





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>SAP HANA 大型实例中的 SnapCenter 集成

本部分介绍客户如何使用 NetApp SnapCenter 软件对 Microsoft Azure HANA 大型实例 (HLI) 上托管的 SAP HANA 数据库创建快照以及进行备份和还原。 

SnapCenter 为包括备份/恢复以及使用异步存储复制、系统复制和系统克隆的灾难恢复 (DR) 在内的场景提供了解决方案。 通过与 Azure SAP HANA 大型实例集成，客户现在可以使用 SnapCenter 进行备份和恢复操作。

有关其他参考资料，请参阅 SnapCenter 上的 NetApp TR-4614 和 TR-4646。

- [SAP HANA Backup/Recovery with SnapCenter (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)（使用 SnapCenter 进行 SAP HANA 备份/恢复 (TR-4614)）
- [SAP HANA Disaster Recovery with Storage Replication (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)（使用存储复制进行 SAP HANA 灾难恢复 (TR-4646)）
- [SAP HANA HSR with SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)（使用 SnapCenter 进行 SAP HANA HSR (TR-4719)）
- [SAP Cloning from SnapCenter (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)（从 SnapCenter 进行 SAP 克隆 (TR-4667)）

### <a name="system-requirements-and-prerequisites"></a>系统要求和先决条件

若要在 Azure HLI 上运行 SnapCenter，系统要求包括：
* Azure Windows 2016 或更高版本上的 SnapCenter 服务器，具有 4-vCPU、16 GB RAM 和至少 650 GB 托管高级 SSD 存储。
* 具有 1.5 TB - ​​24 TB RAM 的 SAP HANA 大型实例系统。 建议使用两个 SAP HANA 大型实例系统进行克隆操作和测试。

将 SnapCenter 集成到 SAP HANA 的步骤如下： 

1. 提出支持票证请求，以将用户生成的公钥告知 Microsoft 操作团队。 若要将 SnapCenter 用户设置为访问存储系统，这是必须要执行的操作。
1. 在 VNET 中创建可以访问 HLI 的 VM；此 VM 用于 SnapCenter。 
1. 下载并安装 SnapCenter。 
1. 备份和恢复操作。 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>为用户角色存储设置创建支持票证

1. 打开 Azure 门户，导航到“订阅”页面。 在“订阅”页面上，选择你的 SAP HANA 订阅（下图以红色勾勒的部分）。

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="为用户存储设置创建支持案例":::

1. 在 SAP HANA 订阅页面上，选择“资源组”子页面。

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="解决方案实验室订阅资源组" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. 在某个区域中选择适当的资源组。

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="在区域中选择适当的资源组" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. 选择与 Azure SAP HANA 存储对应的 SKU 条目。

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="选择与 SAP HANA 对应的 SKU 条目" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. 开具新的支持票证请求（以红色勾勒）。

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="开具新的支持票证请求":::

1. 在“基本信息”选项卡中，为票证提供以下信息：

   * **问题类型：** 技术方面
   * **订阅：** 你的订阅
   * **服务：** SAP HANA 大型实例
   * **资源：** 你的资源组
   * **摘要：** 提供用户生成的公钥
   * **问题类型：** 配置和设置
   * **问题子类型：** 为 HLI 设置 SnapCenter


1. 在“详细信息”选项卡上，在支持票证的“描述” 中提供： 
   
   * 为 HLI 设置 SnapCenter
   * SnapCenter 用户的公钥 (snapcenter.pem) - 请参阅下面的公钥创建示例

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="新建支持请求 -“详细信息”选项卡" lightbox="./media/snapcenter/new-support-request-details.png":::

1. 选择“查看 + 创建”以查看支持票证。 

1. 在 HANA 大型实例或任何 Linux 服务器上为 SnapCenter 用户名生成证书。

   SnapCenter 需要用户名和密码才能访问存储虚拟机 (SVM) 并创建 HANA 数据库的快照。 Microsoft 使用公钥，以便你（客户）能够设置用于访问存储系统的密码。

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. 将 snapcenter.pem 文件附加到支持票证，然后选择“创建”

   提交公钥证书后，Microsoft 会为你的租户设置 SnapCenter 用户名以及 SVM IP 地址。   

1. 收到 SVM IP 后，设置密码以访问由你控制的 SVM。

   下面是来自 HANA 大型实例或虚拟网络中的 VM 的 REST CALL（文档）示例，该示例可以访问 HANA 大型实例环境，并将用于设置密码。

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   确保 HANA 数据库系统上没有处于活动状态的代理变量。

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>下载并安装 SnapCenter
你已经为 SnapCenter 设置了用于访问存储系统的用户名，一旦 SnapCenter 安装完毕，你将使用 SnapCenter 用户名来配置它。 

在安装 SnapCenter 之前，请查看 [SAP HANA Backup/Recovery with SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)（使用 SnapCenter 进行 SAP HANA 备份/恢复），以定义备份策略。 

1. 登录到 [NetApp](https://mysupport.netapp.com)，以[下载](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0)最新版本的 SnapCenter。

1. 在 Windows Azure VM 上安装 SnapCenter。

   安装程序将检查 VM 先决条件。 

   >[!IMPORTANT]
   >请注意 VM 的大小，尤其是在较大的环境中。

1. 配置 SnapCenter 的用户凭据。 默认情况下，它会填充用于安装应用程序的 Windows 用户凭据。 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="安装用户输入对话框"::: 

1. 启动会话时，保存安全豁免证书并启动 GUI。

1. 使用 Windows 凭据登录到 VM 上的 SnapCenter (https://snapcenter-vm:8146) ，以配置环境。


### <a name="set-up-the-storage-system"></a>设置存储系统

1. 在 SnapCenter 中，选择“存储系统”，然后选择“+新建”。 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="SnapCenter 存储连接" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   默认为每个租户一个 SVM。 如果客户在多个区域有多个租户或 HLI，建议配置 SnapCenter 中的所有 SVM

1. 在“添加存储系统”中，提供要添加的存储系统的信息、SnapCenter 用户名和密码，然后选择“提交”。

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="新建存储连接":::

   >[!NOTE]
   >默认为每个租户一个 SVM。  如果有多个租户，建议配置 SnapCenter 中的所有 SVM。 

1. 在 SnapCenter 中，选择“主机”，然后选择“+添加”以设置 HANA 插件和 HANA 数据库主机。  最新版本的 SnapCenter 会自动在主机上检测 HANA 数据库。

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="在 SnapCenter 中，选择“主机”，然后选择“添加”。" lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. 提供新主机的信息：
   1. 针对主机类型选择操作系统。
   1. 输入 SnapCenter VM 主机名。
   1. 提供要使用的凭据。
   1. 选择“Microsoft Windows”和“SAP HANA”选项，然后选择“提交”。

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="有关新主机的信息":::

   >[!IMPORTANT]
   >在安装第一个节点之前，SnapCenter 允许非根用户在数据库上安装插件。  有关如何启用非根用户的信息，请参阅 [Adding a non-root user and configuring sudo privileges](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html)（添加非根用户并配置 sudo 特权）。

1. 查看主机详细信息，然后选择“提交”以在 SnapCenter 服务器上安装插件。

1. 安装插件后，在 SnapCenter 中，选择“主机”，然后选择“+添加”以添加 HANA 节点。

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="添加 HANA 节点" lightbox="media/snapcenter/add-hana-node.png":::

1. 提供 HANA 节点的信息：
   1. 针对主机类型选择操作系统。
   1. 输入 HANA 数据库主机名或 IP 地址。
   1. 选择 **+** 以添加在 HANA DB 主机操作系统上配置的凭据，然后选择“确定”。
   1. 选择“SAP HANA”，然后选择“提交”。

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="SAP HANA 节点详细信息":::

1. 确认指纹，然后选择“确认并提交”。

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="确认并提交指纹":::

1. 在 HANA 节点上的系统数据库下，选择“安全性” > “用户” > “SNAPCENTER”以创建 SnapCenter 用户。

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="在 HANA（系统数据库）中创建 SnapCenter 用户":::



### <a name="auto-discovery"></a>自动发现
默认情况下，SnapCenter 4.3 会启用自动发现功能。  配置了 HANA 系统复制 (HSR) 的 HANA 实例不支持自动发现。 你必须手动将该实例添加到 SnapCenter 服务器中。


### <a name="hana-setup-manual"></a>HANA 设置（手动）
如果配置了 HSR，则必须手动配置系统。  

1. 在 SnapCenter 中，选择“资源”和“SAN HANA”（位于顶部），然后选择“+添加 SAP HANA 数据库”（位于右侧）。

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="手动设置 HANA" lightbox="media/snapcenter/manual-hana-setup.png":::

1. 指定在 Linux 主机或安装插件的主机上配置的 HANA 管理员用户的资源详细信息。 系统将通过 Linux 系统上的插件管理备份。

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="指定在 Linux 主机上配置的 HANA 管理员用户的资源详细信息。":::

1. 选择需要为其创建快照的数据卷，选择“保存”，然后选择“完成”。

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="选择需要为其创建快照的数据卷，选择“保存”，然后选择“完成”。":::

### <a name="create-a-snapshot-policy"></a>创建快照策略

使用 SnapCenter 备份 SAP HANA 数据库资源之前，必须为要备份的资源或资源组创建备份策略。 在创建快照策略的过程中，系统将为你提供用于配置 pre/post 命令和特殊 SSL 密钥的选项。 有关如何创建快照策略的信息，请参阅[为 SAP HANA 数据库创建备份策略](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html)。

1. 在 SnapCenter 中，选择“资源”，然后选择数据库。

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="在 SnapCenter 中，选择“资源”，然后选择数据库。":::

1. 按照配置向导的工作流配置快照计划程序。

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="按照配置向导的工作流配置快照计划程序。" lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. 提供用于配置 pre/post 命令和特殊 SSL 密钥的选项。  在此示例中，我们没有使用任何特殊设置。

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="提供用于配置 pre-post 命令和特殊 SSL 密钥的选项。" lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. 选择“添加”以创建快照策略，该策略也可用于其他 HANA 数据库。 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="选择“添加”以创建快照策略，该策略也可用于其他 HANA 数据库。":::

1. 输入策略名称和描述。

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="输入策略名称和描述。":::


1. 选择备份类型和频率。

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="选择备份类型和频率。":::

1. 配置“按需备份保留设置”。  在我们的示例中，我们将保留设置为保留三个快照副本。

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="配置“按需备份保留设置”。":::

1. 配置“每小时保留设置”。 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="配置“每小时保留设置”。":::

1. 如果配置了 SnapMirror 设置，请选择“创建本地 SnapShot 副本后更新 SnapMirror”。

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="如果需要 SnapMirror，请选择“创建本地 SnapShot 副本后更新 SnapMirror”。":::

1. 选择“完成”以查看新备份策略的摘要。 
1. 在“配置计划”下，选择“添加”。

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="在“配置计划”下，选择“添加”。":::

1. 选择“开始日期”、“到期日期”和频率。

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="选择“开始日期”、“到期日期”和频率。":::

1. 提供通知的电子邮件详细信息。

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="提供通知的电子邮件详细信息。":::

1.  选择“完成”以创建备份策略。

### <a name="disable-ems-message-to-netapp-autosupport"></a>禁用 NetApp Autosupport 的 EMS 消息
默认情况下，EMS 数据收集处于启用状态，在安装日期后每七天运行一次。  你可以使用 PowerShell cmdlet `Disable-SmDataCollectionEms` 禁用数据收集。

1. 在 PowerShell 中，与 SnapCenter 建立会话。

   ```powershell
   Open-SmConnection
   ```

1. 使用凭据登录。
1. 禁用 EMS 消息收集。

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>崩溃后还原数据库
你可以使用 SnapCenter 还原数据库。  本部分将介绍一些概要步骤；若要了解详细信息，请参阅 [SAP HANA Backup/Recovery with SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)（使用 SnapCenter 进行 SAP HANA 备份/恢复）。


1. 停止数据库并删除所有数据库文件。

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. 卸载数据库卷。

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. 通过 SnapCenter 还原数据库文件。  选择数据库，然后选择“还原”。  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="选择数据库，然后选择“还原”。" lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. 选择还原类型。  在我们的示例中，我们将还原整个资源。 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="选择还原类型。":::

   >[!NOTE]
   >如果使用默认设置，无需指定命令即可从磁盘上的快照进行本地还原。 

   >[!TIP]
   >如果要还原卷内的特定 LUN，请选择“文件级别”。

1. 按照配置向导的工作流执行操作。
   
   SnapCenter 会将数据还原到原始位置，以便在 HANA 中启动还原过程。 此外，由于 SnapCenter 无法修改备份目录（数据库已关闭），因此系统会显示一条警告。

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="由于 SnapCenter 无法修改备份目录，因此系统会显示一条警告。":::

1. 由于所有数据库文件都已还原，因此请在 HANA 中启动还原过程。 在 HANA Studio 的“系统”下，右键单击系统数据库，然后选择“备份和恢复” > “恢复系统数据库”。

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="在 HANA 中启动还原过程。":::

1. 选择恢复类型。

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="选择恢复类型。":::

1. 选择备份目录的位置。

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="选择备份目录的位置。":::

1. 选择用于恢复 SAP HANA 数据库的备份。

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="选择用于恢复 SAP HANA 数据库的备份。":::

   恢复数据库后，系统会显示一条消息，其中包含“恢复到时间”和“恢复到日志位置”标记。

1. 在“系统”下，右键单击系统数据库，然后选择“备份和恢复” > “恢复租户数据库”。
1. 按照向导的工作流完成租户数据库的恢复。 

有关还原数据库的详细信息，请参阅 [SAP HANA Backup/Recovery with SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)（使用 SnapCenter 进行 SAP HANA 备份/恢复）。


### <a name="non-database-backups"></a>非数据库备份
你可以还原非数据卷，例如，网络文件共享 (/hana/shared) 或操作系统备份。  有关还原非数据卷的详细信息，请参阅 [SAP HANA Backup/Recovery with SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)（使用 SnapCenter 进行 SAP HANA 备份/恢复）。

### <a name="sap-hana-system-cloning"></a>SAP HANA 系统克隆

进行克隆之前，必须安装与源数据库相同的 HANA 版本。 SID 和 ID 可以不同。 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="SAP HANA 系统克隆" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. 从 /usr/sap/H34/HDB40 为 H34 数据库创建 HANA 数据库用户存储。

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. 禁用防火墙。

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. 安装 Java SDK。

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. 在 SnapCenter 中，添加将要装载克隆的目标主机。 有关详细信息，请参阅[添加主机并在远程主机上安装插件包](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html)。
   1. 提供要添加的运行方式凭据的信息。 
   1. 选择主机操作系统，然后输入主机信息。
   1. 在“要安装的插件”下，选择版本，输入安装路径，然后选择“SAP HANA”。
   1. 选择“验证”以运行安装前检查。

1. 停止 HANA 并卸载旧数据卷。  你将从 SnapCenter 装载克隆。  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. 为目标创建配置和 shell 脚本文件。
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >你可以从 [SAP Cloning from SnapCenter](https://www.netapp.com/us/media/tr-4667.pdf)（从 SnapCenter 进行 SAP 克隆）中复制脚本。

1. 修改配置文件。 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE="MDC_single_tenant"
   * KEY="H34KEY"
   * TIME_OUT_START=18
   * TIME_OUT_STOP=18
   * INSTANCENO="40"
   * STORAGE="10.250.101.33"

1. 修改 shell 脚本文件。

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOSE=NO
   * MY_NAME="`basename $0`"
   * BASE_SCRIPT_DIR="`dirname $0`"
   * MOUNT_OPTIONS="rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,nolock"

1. 从备份进程启动克隆。 选择要创建克隆的主机。 

   >[!NOTE]
   >有关详细信息，请参阅 [Cloning from a backup](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)（从备份进行克隆）。

1. 在“脚本”下，提供以下信息：

   * **装载命令：** /NetApp/sc-system-refresh.sh mount H34 %hana_data_h31_mnt00001_t250_vol_Clone
   * **克隆后命令：** /NetApp/sc-system-refresh.sh recover H34

1. 禁用（锁定）/etc/fstab 中的自动装载，因为不需要预装数据库的数据卷。 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>删除克隆

如果不再需要某个克隆，可以将其删除。 有关详细信息，请参阅[删除克隆](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)。

用于在删除克隆前执行的命令包括：
* **克隆前删除：** /NetApp/sc-system-refresh.sh shut down H34
* **卸载：** /NetApp/sc-system-refresh.sh umount H34

这些命令允许 SnapCenter 关闭数据库、卸载卷以及删除 fstab 条目。  之后，FlexClone 将被删除。 

### <a name="cloning-database-logfile"></a>克隆数据库日志文件

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>卸载适用于 Linux 的 SnapCenter 插件包

你可以从命令行卸载 Linux 插件包。 由于自动部署需要一个全新的系统，因此可以轻松卸载该插件。  

>[!NOTE]
>你可能需要手动卸载旧版插件。 

卸载插件。

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

现在，可以通过在 SnapCenter 中选择“提交”，在新节点上安装最新的 HANA 插件。 




## <a name="next-steps"></a>后续步骤
- 请参阅[灾难恢复原则和准备工作](hana-concept-preparation.md)。
