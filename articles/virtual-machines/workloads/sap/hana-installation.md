---
title: 安装 Azure SAP HANA（大型实例）| Microsoft Docs
description: 了解如何安装和配置 Azure SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 37eff4049973c208ebe70ef452f121109f0c92ed
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435827"
---
# <a name="install-and-configure-sap-hana-large-instances-on-azure"></a>安装和配置 Azure 上的 SAP HANA（大型实例）

本文逐步介绍如何在 Azure 上验证、配置和安装 SAP HANA 大型实例 (HLI)（也称为裸机基础结构）。

## <a name="prerequisites"></a>先决条件

在阅读本文之前，请熟悉：
- [HANA 大型实例常见术语](hana-know-terms.md)
- [HANA 大型实例 SKU](hana-available-skus.md)。

另请参阅：
- [将 Azure VM 连接到 HANA 大型实例](hana-connect-azure-vm-large-instances.md)
- [将虚拟网络连接到 HANA 大型实例](hana-connect-vnet-express-route.md)

## <a name="planning-your-installation"></a>规划安装

SAP HANA 的安装由你负责。 可以在 Azure 虚拟网络与 HANA 大型实例单元之间建立连接之后再开始安装新的 Azure 上的 SAP HANA（大型实例）。 

> [!Note]
> 根据 SAP 政策，SAP HANA 的安装必须由通过了“SAP 技术认证专员”考试、“SAP HANA 安装”认证考试的人员或者 SAP 认证的系统集成商 (SI) 来完成。

在规划安装 HANA 2.0 时，请参阅 [SAP 支持说明 #2235581 - SAP HANA：支持的操作系统](https://launchpad.support.sap.com/#/notes/2235581/E)。 确保要安装的 SAP HANA 版本支持所用的操作系统 (OS)。 HANA 2.0 支持的 OS 比 HANA 1.0 支持的 OS 限制性更强。 确认特定的 HANA 大型实例支持所需的 OS 版本。 请使用此[列表](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)；选择“HLI”查看该单元支持的 OS 列表的详细信息。 

在开始安装 HANA 之前，请验证以下项：
- [HLI 单元](#validate-the-hana-large-instance-units)
- [操作系统配置](#operating-system)
- [网络配置](#networking)
- [存储配置](#storage)


## <a name="validate-the-hana-large-instance-units"></a>验证 HANA 大型实例单元

收到来自 Microsoft 的 HANA 大型实例后，请与之建立访问和连接。 然后验证以下设置并根据需要进行调整。

1. 在 Azure 门户中检查实例是否已随正确的 SKU 和 OS 显示出来。 有关详细信息，请参阅[通过 Azure 门户控制 Azure HANA 大型实例](./hana-li-portal.md)。

2. 将实例的 OS 注册到 OS 提供程序。 此步骤包括将 SUSE Linux OS 注册到在 Azure VM 上部署的 SUSE 订阅管理工具 (SMT) 实例中。 

    HANA 大型实例可以连接到此 SMT 实例。 （有关详细信息，请参阅[如何为 SUSE Linux 设置 SMT 服务器](hana-setup-smt.md)）。 如果使用 Red Hat OS，需要将其注册到你要连接的 Red Hat 订阅管理器。 有关详细信息，请参阅[什么是 Azure 上的 SAP HANA（大型实例）？](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json)中的备注。 

    必须执行此步骤才能修补 OS（由你负责）。 对于 SUSE，请参阅有关[安装和配置 SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html) 的文档。

3. 检查特定 OS 发行版/版本的新补丁和修补程序。 验证 HANA 大型实例是否有最新的补丁。 有时会未包含最新的补丁，因此务必要进行检查。

4. 查看有关在特定 OS 发行版/版本上安装和配置 SAP HANA 的 SAP 说明。 Microsoft 未必总是完全配置 HLI。 由于建议会不断更改或者与个别方案相关的 SAP 说明或配置会发生更改，Microsoft 可能无法总是完全配置 HLI。  

    因此，请务必阅读与确切 Linux 发行版的 SAP HANA 相关的 SAP 说明。 还要检查 OS 发行版/版本的配置，并应用尚未应用的配置设置。

    具体而言，请检查以下参数，最终将其调整为：

    - net.core.rmem_max = 16777216
    - net.core.wmem_max = 16777216
    - net.core.rmem_default = 16777216
    - net.core.wmem_default = 16777216
    - net.core.optmem_max = 16777216
    - net.ipv4.tcp_rmem = 65536 16777216 16777216
    - net.ipv4.tcp_wmem = 65536 16777216 16777216

    从 SLES12 SP1 和 Red Hat Enterprise Linux (RHEL) 7.2 开始，必须在 /etc/sysctl.d 目录中的一个配置文件内设置这些参数。 例如，必须创建名为 91-NetApp-HANA.conf 的配置文件。 对于旧版 SLES 和 RHEL，必须在 in/etc/sysctl.conf 中设置这些参数。

    对于从 RHEL 6.3 开始的所有 RHEL 版本，请记住以下事项： 
    - 必须在 in/etc/modprobe.d/sunrpc-local.conf 中设置 sunrpc.tcp_slot_table_entries = 128 参数。 如果该文件不存在，请先通过添加以下条目来创建该文件： 
        - options sunrpc tcp_max_slot_table_entries=128

5. 检查 HANA 大型实例的系统时间。 部署实例时使用的是系统时区。 此时区表示 HANA 大型实例阵列所在 Azure 区域的位置。 可以更改自己拥有的实例的系统时间或时区。 

    如果在租户中订购了更多的实例，则需要调整新交付的实例的时区。 在交接后，Microsoft 并不了解你在实例上设置的系统时区。 因此，新部署的实例可能不是在与你已更改到的时区相同的时区中设置的。 在必要时，你需要负责调整交接后的实例的时区。 

6. 检查 etc/hosts。 交接刀片服务器后，会根据不同的用途为它们分配不同的 IP 地址。 将单元添加到现有租户后，必须检查 etc/hosts 文件。 新部署的系统的 etc/hosts 可能未使用以前交付的系统的 IP 地址正确进行维护。 请确保新部署的实例能够解析租户中以前部署的单元的名称。 


## <a name="operating-system"></a>操作系统

根据 [SAP 支持说明 #1999997 - 常见问题解答：SAP HANA 内存](https://launchpad.support.sap.com/#/notes/1999997/E)，交付的 OS 映像的交换空间设置为 2GB。 如果需要不同的设置，必须自行设置。

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/download/) 是为 Azure SAP HANA（大型实例）安装的 Linux 分发版。 此发行版提供特定于 SAP 的功能，包括有效运行 SAP on SLES 所需的预设参数。

如需与部署 SAP HANA on SLES 相关的多个有用资源，请参阅：
- SUSE 网站上的[资源库/白皮书](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)。
- SAP 社区网络 (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)。

这些资源包括有关设置高可用性、特定于 SAP 操作的安全强化等方面的信息。

下面是适用于 SAP on SUSE 的其他资源：

- [SAP HANA on SUSE Linux 站点](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.scribd.com/document/351887168/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12-color-en-pdf)（SAP 最佳做法：排队复制 - SAP NetWeaver on SUSE Linux Enterprise 12）
- [ClamSAP – SLES virus protection for SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)（ClamSAP - 适用于 SAP 的 SLES 病毒防护）（包括 SLES 12 for SAP Applications）

以下文档是适用于实施 SAP HANA on SLES 12 的 SAP 支持说明：

- [SAP support note #1944799 – SAP HANA guidelines for SLES operating system installation](http://service.sap.com/sap/support/notes/1944799)（SAP 支持说明 #1944799 - 适用于 SLES 操作系统安装的 SAP HANA 指导原则）
- [SAP support note #2205917 – SAP HANA DB recommended OS settings for SLES 12 for SAP applications](https://launchpad.support.sap.com/#/notes/2205917/E)（SAP 支持说明 #2205917 - SAP HANA DB 建议的适用于 SLES 12 for SAP Applications 的 OS 设置）
- [SAP support note #1984787 – SUSE Linux Enterprise Server 12:  installation notes](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 #1984787 - SUSE Linux Enterprise Server 12：安装说明）
- [SAP support note #171356 – SAP software on Linux:  General information](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 #171356 - Linux 上的 SAP 软件：常规信息）
- [SAP support note #1391070 – Linux UUID solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 #1391070 - Linux UUID 解决方案）

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) 是用于在 HANA 大型实例上运行 SAP HANA 的另一个产品。 RHEL 7.2 和 7.3 版本可供使用并且受支持。 有关 SAP on Red Hat 的详细信息，请参阅 [SAP HANA on Red Hat Linux 站点](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)。

以下文档是适用于实施 SAP HANA on Red Hat 的 SAP 支持说明：

- [SAP support note #2009879 - SAP HANA guidelines for Red Hat Enterprise Linux (RHEL) operating system](https://launchpad.support.sap.com/#/notes/2009879/E)（SAP 支持说明 #2009879 - 适用于 Red Hat Enterprise Linux (RHEL) 操作系统的 SAP HANA 指导原则）
- [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
- [SAP support note #1391070 – Linux UUID solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 #1391070 - Linux UUID 解决方案）
- [SAP support note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11](https://launchpad.support.sap.com/#/notes/2228351)（SAP 支持说明 #2228351 - Linux：RHEL 6 或 SLES 11 上的 SAP HANA Database SPS 11 修订版 110（或更高版本））
- [SAP support note #2397039 - FAQ: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039)（SAP 支持说明 #2397039 - 常见问题解答：SAP on RHEL）
- [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation and upgrade](https://launchpad.support.sap.com/#/notes/2002167)（SAP 支持说明 #2002167 - Red Hat Enterprise Linux 7.x：安装和升级）

### <a name="time-synchronization"></a>时间同步

基于 SAP NetWeaver 体系结构构建的 SAP 应用程序对 SAP 系统组件的时差非常敏感。 可能经常会出现标题为 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 简短转储。 这是因为，当不同服务器或虚拟机 (VM) 的系统时差过大时，就会出现这些简短转储。

对于 Azure SAP HANA（大型实例），Azure 中的时间同步不适用于大型实例缩放单元中的计算单元。 它也不适用于在原生 Azure VM 中运行的 SAP 应用程序，因为 Azure 会确保系统时间正确同步。 

因此，需要设置单独的时间服务器。 在 Azure VM 上运行的 SAP 应用程序服务器将使用此服务器。 在 HANA 大型实例上运行的 SAP HANA 数据库实例也会使用此服务器。 大型实例缩放单元中的存储基础结构与网络时间协议 (NTP) 服务器保持时间同步。


## <a name="networking"></a>网络
在设计 Azure 虚拟网络并将这些虚拟网络连接到 HANA 大型实例时，请务必遵循以下文档中所述的建议：

- [Azure 上的 SAP HANA（大型实例）概述和体系结构](./hana-overview-architecture.md)
- [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md)

在单个单元的网络方面，有些详细信息值得注意。 每个 HANA 大型实例单元附带两个或三个 IP 地址，这些地址已分配到两个或三个网络接口控制器 (NIC) 端口。 HANA 横向扩展配置和 HANA 系统复制方案中使用三个 IP 地址。 分配给单元 NIC 的一个 IP 地址位于 [Azure 上的 SAP HANA（大型实例）概述和体系结构](./hana-overview-architecture.md)中所述的服务器 IP 池外部。

有关体系结构以太网的详细信息，请参阅 [HLI 支持的方案](hana-supported-scenario.md)。

## <a name="storage"></a>存储

SAP HANA 使用 SAP 建议的指导原则在 Azure 服务管理中配置 SAP HANA（大型实例）存储布局。 [SAP HANA 存储要求](https://blogs.saphana.com/wp-content/uploads/2015/02/Storage-Whitepaper-2-54.pdf)中阐述了这些指导原则。 

有关包含不同 HANA 大型实例 SKU 的各卷的粗略大小，请参阅 [Azure 上的 SAP HANA（大型实例）概述和体系结构](hana-overview-architecture.md)。

下表列出了存储卷的命名约定：

| 存储用途 | 装入点名称 | 卷名 | 
| --- | --- | ---|
| HANA 数据 | /hana/data/SID/mnt0000\<m> | 存储 IP：/hana_data_SID_mnt00001_tenant_vol |
| HANA 日志 | /hana/log/SID/mnt0000\<m> | 存储 IP：/hana_log_SID_mnt00001_tenant_vol |
| HANA 日志备份 | /hana/log/backups | 存储 IP：/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共享 | /hana/shared/SID | 存储 IP：/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 存储 IP：/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* 表示 HANA 实例系统 ID。 

*Tenant* 表示部署租户时的操作的内部枚举。

HANA usr/sap 共享同一个卷。 装入点的命名法包括 HANA 实例系统 ID 和装入点编号。 在纵向扩展部署中只有一个装入点，例如 mnt00001。 在横向扩展部署中，装入点数量与工作器节点和主节点一样多。 

对于横向扩展环境，数据、日志和日志备份卷都会共享并附加到横向扩展配置中的每个节点。 对于运行多个 SAP 实例的配置，将创建一组不同的卷，并将其附加到 HANA 大型实例。 有关方案存储布局的详细信息，请参阅 [HLI 支持的方案](hana-supported-scenario.md)。

HANA 大型实例附带了很大的 HANA/data 磁盘卷，并且还有一个 HANA/log/backup 卷。 我们之所以提供这么大的 HANA/data 卷，是因为存储快照使用同一个磁盘卷。 执行的存储快照越多，分配的存储卷中的快照消耗的空间就越多。 

HANA/log/backup 卷并不旨在用作数据库备份的卷。 它的大小适合用作 HANA 事务日志备份的备份卷。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md) 

可以通过购买额外的容量（以 1-TB 为增量）来增大存储。 可将此额外存储作为新卷添加到 HANA 大型实例。

在通过 Azure SAP HANA 服务管理执行加入操作期间，你将指定 sidadm 用户和 sapsys 组的用户 ID (UID) 和组 ID (GID)（例如：1000,500）。 在安装 SAP HANA 系统期间，必须使用与此相同的值。 由于要在一个单元上部署多个 HANA 实例，因此将获得多个卷集（每个实例有一个集）。 因此在部署时需要定义：

- 不同 HANA 实例的 SID（sidadm 由此派生）。
- 不同 HANA 实例的内存大小。 每个实例的内存大小定义各个卷集的卷大小。

根据存储提供程序的建议，为所有已装载卷（不包括启动 LUN）配置以下装入选项：

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

这些装入点是在 /etc/ fstab 中配置的，如以下屏幕截图中所示：

![该屏幕截图显示 HANA 大型实例单元中已装载卷的 fstab。](./media/hana-installation/image1_fstab.PNG)

S72m HANA 大型实例上的命令 df -h 的输出如下所示：

![该屏幕截图显示 HANA 大型实例的命令输出。](./media/hana-installation/image2_df_output.PNG)


大型实例戳中的存储控制器和节点将同步到 NTP 服务器。 根据 NTP 服务器同步 Azure SAP HANA（大型实例）和 Azure VM 非常重要。 同步后，Azure 或大型实例缩放单元中的基础结构与计算单元之间就不会出现很大的时间偏差。

若要优化 SAP HANA，使其不超过使用的存储，请设置以下 SAP HANA 配置参数：

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
对于 SAP HANA 1.0 SPS12 或更低版本，可根据 [SAP 说明 #2267798 - SAP HANA 数据库的配置](https://launchpad.support.sap.com/#/notes/2267798)中所述，在安装 SAP HANA 数据库期间设置这些参数

也可以在安装 SAP HANA 数据库之后使用 hdbparam 框架配置这些参数。 

HANA 大型实例中使用的存储有文件大小限制。 每个文件的[大小限制为 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)。 与 EXT3 文件系统中的文件大小限制不同，HANA 不会隐式识别 HANA 大型实例存储强制实施的存储限制。 因此，在达到 16 TB 的文件大小限制时，HANA 不会自动创建新数据文件。 HANA 尝试将文件增大到 16 TB 以上时，它会报告错误，并且索引服务器最终将崩溃。

> [!IMPORTANT]
> 为了防止 HANA 尝试将数据文件增长到超出 HANA 大型实例存储的 16 TB 文件大小限制，请在 SAP HANA global.ini 配置文件中设置以下参数：
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - 另请参阅 SAP 说明 [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - 请注意 SAP 说明 [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


在 SAP HANA 2.0 中，hdbparam 框架已弃用。 因此，必须使用 SQL 命令设置这些参数。 有关详细信息，请参阅 [SAP 说明 #2399079：HANA 2 中已弃用 hdbparam](https://launchpad.support.sap.com/#/notes/2399079)。

请参阅 [HLI 支持的方案](hana-supported-scenario.md)，详细了解体系结构的存储布局。


## <a name="next-steps"></a>后续步骤

完成安装 Azure SAP HANA（大型实例）的步骤。

> [!div class="nextstepaction"]
> [在 Azure SAP HANA（大型实例）上安装 HANA](hana-example-installation.md)
