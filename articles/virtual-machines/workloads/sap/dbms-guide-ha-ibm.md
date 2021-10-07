---
title: 在 Azure 虚拟机 (VM) 上设置 IBM Db2 HADR | Microsoft Docs
description: 在 Azure 虚拟机 (VM) 上建立 IBM Db2 LUW 的高可用性。
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: e14ace59aa97457fc43f197476f8a1e34cf5e319
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355174"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>采用 Pacemaker 的 SUSE Linux Enterprise Server 上 Azure VM 中 IBM Db2 LUW 的高可用性

采用[高可用性和灾难恢复 (HADR) 配置](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)的 IBM Db2 for Linux、UNIX 和 Windows (LUW) 包含一个运行主数据库实例的节点，以及至少一个运行辅助数据库实例的节点。 对主数据库实例进行的更改将以同步或异步方式复制到辅助数据库实例，具体取决于你的配置。 

> [!NOTE]
> 本文包含对术语“主”和“从”的引用，Microsoft 不再使用这些术语 。 当这些术语在软件中被删除后，我们会将它们从本文中删除。
   
本文介绍如何部署和配置 Azure 虚拟机 (VM) 、安装群集框架，以及安装具有 HADR 配置的 IBM Db2 LUW。 

本文不介绍如何安装和配置具有 HADR 的 IBM Db2 LUW 或 SAP 软件安装。 为了帮助你完成这些任务，我们提供了对 SAP 和 IBM 安装手册的参考。 本文重点介绍特定于 Azure 环境的部件。 

受支持的 IBM Db2 版本为 10.5 及更高版本，如 SAP 说明 [1928533] 中所述。

在开始安装之前，请参阅以下 SAP 说明和文档：

| SAP 说明 | 说明 |
| --- | --- |
| [1928533] | Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] | Azure 上的 SAP：支持先决条件 |
| [2178632] | Azure 上的 SAP 的关键监视指标 |
| [2191498] | Azure 的 Linux 上的 SAP：增强型监视 |
| [2243692] | Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] | SUSE LINUX Enterprise Server 12：安装说明 |
| [1999351] | 适用于 SAP 的增强型 Azure 监视故障排除 |
| [2233094] | DB6：Azure 上使用 IBM DB2 for Linux、UNIX 和 Windows 的 SAP 应用程序 - 附加信息 |
| [1612105] | DB6：具有 HADR 的 Db2 的常见问题解答 |


| 文档 | 
| --- |
| [SAP 社区 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：包含适用于 Linux 的所有必需 SAP 说明 |
| 《[针对 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]》指南 |
| [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]（本文） |
| 《[适用于 Linux 上的 SAP 的 Azure 虚拟机数据库管理系统 (DBMS) 部署][dbms-guide]》指南 |
| [Azure 上的 SAP 工作负荷规划和部署清单][azr-sap-plancheck] |
| 《[SUSE Linux Enterprise Server for SAP Applications 12 SP4 最佳做法指南][sles-for-sap-bp]》 |
| [SUSE Linux Enterprise High Availability Extension 12 SP4][sles-ha-guide] |
| [适用于 SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>概述
为实现高可用性，至少在两台 Azure 虚拟机上安装具有 HADR 的 IBM Db2 LUW，这些虚拟机部署在 [Azure 可用性集](../../windows/tutorial-availability-sets.md)中或 [Azure 可用性区域](./sap-ha-availability-zones.md)上。 

下图显示了两个数据库服务器 Azure VM 的设置。 两个数据库服务器 Azure VM 都附加了其自己的存储，并且已启动并运行。 在 HADR 中，其中一个 Azure VM 中的一个数据库实例具有主实例的角色。 所有客户端均连接到这个主实例。 数据库事务中的所有更改都将在 Db2 事务日志中本地保存。 由于事务日志记录在本地保存，将通过 TCP/IP 将记录传输到第二个数据库服务器、备用服务器或备用实例上的数据库实例。 备用实例通过前滚传输的事务日志记录来更新本地数据库。 通过这种方式，备用服务器与主服务器保持同步。

HADR 只是一种复制功能。 它没有故障检测，也没有自动接管或故障转移功能。 接管或传输到备用服务器时，必须由数据库管理员手动启动。 要实现自动接管和故障检测，可以使用 Linux Pacemaker 群集功能。 Pacemaker 监视两个数据库服务器实例。 当主数据库服务器实例发生故障时，Pacemaker 将通过备用服务器启动“自动”HADR 接管。 Pacemaker 还可确保将虚拟 IP 地址分配给新的主服务器。

![IBM Db2 高可用性概述](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

要让 SAP 应用程序服务器连接到主数据库，需要使用虚拟主机名和虚拟 IP 地址。 发生故障转移时，SAP 应用程序服务器将连接到新的主数据库实例。 在 Azure 环境中，需要 [Azure 负载均衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)来使用虚拟 IP 地址，这与 IBM DB2 的 HADR 所需的方式相同。 

为了帮助你充分了解具有 HADR 和 Pacemaker 的 IBM Db2 LUW 如何适应高度可用的 SAP 系统设置，下图概述了基于 IBM Db2 数据库的 SAP 系统的高可用性设置。 本文仅介绍 IBM Db2，但它提供了有关如何设置 SAP 系统其他组件的其他文章的参考。

![IBM DB2 高可用性完整环境概述](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>所需步骤的高级概述
要部署 IBM Db2 配置，需执行以下步骤：

  + 计划环境。
  + 部署 VM。
  + 更新 SUSE Linux 并配置文件系统。
  + 安装并配置 Pacemaker。
  + 安装[高可用性 NFS][nfs-ha]。
  + [在单独的群集上安装 ASCS/ERS][ascs-ha]。
  + 安装具有分布式/高可用性选项 (SWPM) 的 IBM Db2 数据库。
  + 安装和创建辅助数据库节点和实例，并配置 HADR。
  + 确认 HADR 是否正常工作。
  + 应用 Pacemaker 配置以控制 IBM Db2。
  + 配置 Azure 负载均衡器。
  + 安装主应用程序服务器和对话应用程序服务器。
  + 检查并改编 SAP 应用程序服务器的配置。
  + 执行故障转移和接管测试。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>计划用于托管具有 HADR 的 IBM Db2 LUW 的 Azure 基础结构

在执行部署之前完成规划过程。 规划为在 Azure 中部署具有 HADR 的 Db2 的配置奠定了基础。 下表列出了需要成为 IMB Db2 LUW 规划一部分（SAP 环境的数据库部分）的关键元素：

| 主题 | 简短说明 |
| --- | --- |
| 定义 Azure 资源组 | 部署 VM、VNet、Azure 负载均衡器和其他资源的资源组。 可以是现有的，也可以是新的。 |
| 虚拟网络/子网定义 | 用于 IBM Db2 和 Azure 负载均衡器的 VM 的部署位置。 可以是现有的，也可以是新创建的。 |
| 托管 IBM Db2 LUW 的虚拟机 | VM 大小、存储、网络、IP 地址。 |
| IBM Db2 数据库的虚拟主机名和虚拟 IP| 用于连接 SAP 应用程序服务器的虚拟 IP 或主机名。 db-virt-hostname、db-virt-ip。  |
| Azure 隔离 | Azure 隔离或 SBD 隔离（强烈建议）。 避免拆分的方法。 |
| SBD VM | SBD 虚拟机大小、存储、网络 |
| Azure 负载均衡器 | 使用基本或标准（建议）、用于 Db2 数据库的探测端口（建议 62500）“探测端口”。 |
| 名称解析| 名称解析在环境中的工作方式。 强烈建议使用 DNS 服务。 可以使用本地主机文件。 |
    
有关 Azure 中 Linux Pacemaker 的详细信息，请参阅[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md)。

## <a name="deployment-on-suse-linux"></a>SUSE Linux 上的部署

SUSE Linux Enterprise Server for SAP Applications 随附 IBM Db2 LUW 的资源代理。 对于本文档中所述的设置，必须使用 SUSE Linux Server for SAP Applications。 Azure 市场中包含适用于 SUSE Linux Enterprise Server for SAP Applications 12 的映像，可以用于部署新的 Azure 虚拟机。 选择 Azure VM 市场中的 VM 映像时，请注意 SUSE 通过 Azure 市场提供的各种支持或服务模型。 

### <a name="hosts-dns-updates"></a>主机：DNS 更新
创建所有主机名（包括虚拟主机名）的列表，并更新 DNS 服务器，以便为主机名解析启用适当的 IP 地址。 如果 DNS 服务器不存在，或者无法更新和创建 DNS 条目，则需要使用参与此方案的各个 VM 的本地主机文件。 如果使用的是主机文件条目，请确保将条目应用到 SAP 系统环境中的所有 VM。 但是，建议使用 DNS，在理想情况下扩展到 Azure


### <a name="manual-deployment"></a>手动部署

请确保用于 IBM Db2 LUW 的 IBM/SAP 支持所选操作系统。 SAP 说明 [1928533] 中提供了 Azure VM 和 Db2 版本支持的操作系统版本列表。 SAP 产品可用性对照表中提供了各个 Db2 版本的操作系统版本列表。 强烈建议至少使用 SLES 12 SP4，因为此版本或更高版本的 SUSE Linux 中的 Azure 相关性能已改进。

1. 创建或选择资源组。
1. 创建或选择虚拟网络和子网。
1. 创建 Azure 可用性集或部署可用性区域。
    + 对于可用性集，将最大更新域数设置为 2。
1. 创建虚拟机 1。
    + 在 Azure 市场中使用 SLES for SAP 映像。
    + 选择在步骤 3 中创建的 Azure 可用性集，或选择“可用性区域”。
1.  创建虚拟机 2。
    + 在 Azure 市场中使用 SLES for SAP 映像。
    + 选择在步骤 3 中创建的 Azure 可用性集，或选择“可用性区域”（与步骤 3 中的区域不同）。
1. 向 VM 中添加数据磁盘，然后在[适用于 SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署][dbms-db2]一文中查看文件系统设置建议。

## <a name="create-the-pacemaker-cluster"></a>创建 Pacemaker 群集
    
要为此 BM Db2 服务器创建基本 Pacemaker 群集，请参阅[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker][sles-pacemaker]。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>安装 IBM Db2 LUW 和 SAP 环境

在开始安装基于 IBM Db2 LUW 的 SAP 环境之前，请查看以下文档：

+ Azure 文档
+ SAP 文档
+ IBM 文档

本文的介绍部分提供了本文档的链接。

请查看有关在 IBM Db2 LUW 上安装基于 NetWeaver 的应用程序的 SAP 安装手册。

可以使用 [SAP 安装指南查找器][sap-instfind]查找有关 SAP 帮助门户的指南。

通过设置以下筛选器可以减少门户中显示的指南数：

- 我想：“安装新系统”
- 我的数据库：“IBM DB2 for Linux、UNIX 和 Windows”
- SAP NetWeaver 版本、堆栈配置或操作系统的其他筛选器

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>设置具有 HADR 的 IBM Db2 LUW 的安装提示

要设置主 IBM Db2 LUW 数据库实例，请执行以下操作：

- 使用高可用性或分布式选项。
- 安装 SAP ASCS/ERS 和数据库实例。
- 对新安装的数据库进行备份。


> [!IMPORTANT] 
> 记下在安装过程中设置的“数据库通信端口”。 两个数据库实例的端口号必须相同

要使用 SAP 同类系统复制过程来设置备用数据库服务器，请执行以下步骤：

1. 选择“系统复制”选项 >“目标系统” > “分布式” > “数据库实例”。
1. 作为一种复制方法，选择“同类系统”，以便可以使用备份在备用服务器实例上还原备份。
1. 到达为同类系统副本还原数据库的退出步骤时，请退出安装程序。 从主主机的备份中还原数据库。 所有后续安装阶段都已在主数据库服务器上执行。
1. 设置适用于 IBM Db2 的 HADR。

   > [!NOTE]
   > 对于特定于 Azure 和 Pacemaker 的安装和配置：在通过 SAP 软件预配管理器进行安装的过程中，有一个关于 IBM Db2 LUW 高可用性的明确问题：
   >+ 请勿选择 IBM Db2 pureScale。
   >+ 请勿选择“安装适用于多平台的 IBM Tivoli 系统自动化”。
   >+ 请勿选择“生成群集配置文件”。

   使用适用于 Linux Pacemaker 的 SBD 设备时，请设置以下 Db2 HADR 参数：
   + HADR 对等窗口持续时间（秒）(HADR_PEER_WINDOW) = 300  
   + HADR 超时值 (HADR_TIMEOUT) = 60

   使用 Azure Pacemaker 隔离代理时，请设置以下参数：
   + HADR 对等窗口持续时间（秒）(HADR_PEER_WINDOW) = 900  
   + HADR 超时值 (HADR_TIMEOUT) = 60

建议前面的参数基于初始故障转移/接管测试。 必须使用这些参数设置测试故障转移和接管功能是否正常。 由于各个配置可能会有所不同，因此参数可能需要调整。 

> [!IMPORTANT]
> 特定于通过正常启动的具有 HADR 的 IBM Db2 配置：辅助或备用数据库实例必须启动并运行，然后才能启动主数据库实例。

为了便于演示以及用于本文中所述的过程，数据库 SID 为 PTR。

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 检查
配置 HADR 并且主节点和备用节点上的状态为 PEER 和 CONNECTED 后，请执行以下检查：

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 配置

如果在发生节点故障时使用 Pacemaker 进行自动故障转移，则需要相应地配置 Db2 实例和 Pacemaker。 本部分介绍此类型的配置。

以下各项带有任一前缀：

- **[A]** ：适用于所有节点
- **[1]** ：仅适用于节点 1 
- **[2]** ：仅适用于节点 2

**[A]** Pacemaker 配置的先决条件：
1. 以用户 db2\<sid> 身份通过 db2stop 关闭两个数据库服务器。
1. 将 db2\<sid> 用户的 shell 环境更改为 /bin/ksh。 建议使用 Yast 工具。 


### <a name="pacemaker-configuration"></a>Pacemaker 配置

> [!IMPORTANT]
> 最近的测试表明，由于积压工作 (backlog) 及其仅处理一个连接的限制，netcat 停止响应请求。 netcat 资源停止侦听 Azure 负载均衡器请求，并且浮动 IP 变为不可用。  
> 对于现有 Pacemaker 群集，我们过去建议将 netcat 替换为 socat。 当前，我们建议使用 azure-lb 资源代理，它是包 resource-agents 的一部分，具有以下包版本要求：
> - 对于 SLES 12 SP4/SP5，版本必须至少为 resource-agents-4.3.018.a7fb5035-3.30.1。  
> - 对于 SLES 15/15 SP1，版本必须至少为 resource-agents-4.3.0184.6ee15eb2-4.13.1。  
>
> 请注意，更改将需要短暂的停机时间。  
> 对于现有的 Pacemaker 群集，如果已经按照 [Azure 负载平衡器检测强化](https://www.suse.com/support/kb/doc/?id=7024128)中所述将配置更改为使用 socat，则无需立即切换到 azure-lb 资源代理。

**[1]** IBM Db2 HADR 特定的 Pacemaker 配置：
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** 创建 IBM Db2 资源：
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** 启动 IBM Db2 资源：
* 将 Pacemaker 退出维护模式。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** 确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nodes configured
# <a name="5-resources-configured"></a>5 resources configured

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Full list of resources:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Started azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Masters: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Slaves: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> 必须使用 Pacemaker 工具管理 Pacemaker 群集 Db2 实例。 如果使用 db2 命令（如 db2stop），则 Pacemaker 会将操作检测为资源故障。 如果要执行维护，可以将节点或资源置于维护模式。 Pacemaker 暂停监视资源，然后你可以使用普通的 db2 管理命令。


### <a name="configure-azure-load-balancer"></a>配置 Azure 负载均衡器
要配置 Azure 负载均衡器，建议使用 [Azure 标准负载均衡器 SKU](../../../load-balancer/load-balancer-overview.md)，然后执行以下操作：

> [!NOTE]
> 标准负载均衡器 SKU 被限制从负载均衡器下方的节点访问公共 IP 地址。 [在 SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)一文中介绍了如何启用这些节点来访问公共 IP 地址

> [!IMPORTANT]
> 负载均衡方案中的 NIC 辅助 IP 配置不支持浮动 IP。 有关详细信息，请参阅 [Azure 负载均衡器限制](../../../load-balancer/load-balancer-multivip-overview.md#limitations)。 如果你需要为 VM 提供其他 IP 地址，请部署第二个 NIC。  

1. 创建前端 IP 池：

   a. 在 Azure 门户中，打开“Azure 负载均衡器”，选择“前端 IP 池”，然后选择“添加”。

   b. 输入新前端 IP 池的名称（例如，Db2-connection）。

   c. 将“分配”设置为“静态”，并输入开头定义的 IP 地址“Virtual-IP”。

   d. 选择“确定”。

   e. 创建新前端 IP 池后，请记下池 IP 地址。

1. 创建后端池：

   a. 在 Azure 门户中，打开“Azure 负载均衡器”，选择“后端池”，然后选择“添加”。

   b. 输入新后端池的名称（例如，Db2-backend）。

   c. 选择“添加虚拟机”。

   d. 选择在上一步中创建的托管 IBM Db2 数据库的可用性集或虚拟机。

   e. 选择 IBM Db2 群集的虚拟机。

   f. 选择“确定”。

1. 创建运行状况探测：

   a. 在 Azure 门户中，打开“Azure 负载均衡器”，选择“运行状况探测”，然后选择“添加”。

   b. 输入新运行状况探测的名称（例如，Db2-hp）。

   c. 选择“TCP”作为协议，并选择端口 62500。  将“间隔”值保留设置为 5，将“运行不正常阈值”的值设置为 2。   

   d. 选择“确定”。

1. 创建负载均衡规则：

   a. 在 Azure 门户中，打开“Azure 负载均衡器”，选择“负载均衡规则”，然后选择“添加”。

   b. 输入新的负载均衡器规则的名称（例如，Db2-SID）。

   c. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如，Db2-frontend）。

   d. 将“协议”保留设置为“TCP”，并输入端口“数据库通信端口”。 

   e. 将“空闲超时”增大到 30 分钟。

   f. 确保 **启用浮动 IP**。

   g. 选择“确定”。


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>对 SAP 配置文件进行更改，以使用虚拟 IP 进行连接
要连接到 HADR 配置的主实例，SAP 应用程序层需要使用为 Azure 负载均衡器定义和配置的虚拟 IP 地址。 需要进行以下更改：

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>安装主应用程序服务器和对话应用程序服务器

在针对 Db2 HADR 配置安装主应用程序服务器和对话应用程序服务器时，请使用为配置选取的虚拟主机名。 

如果在创建 Db2 HADR 配置之前执行了安装，请按照上一节中所述进行更改，并遵循 SAP Java 堆栈。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java 或 Java 堆栈系统 JDBC URL 检查

使用 J2EE Config 工具检查或更新 JDBC URL。 由于 J2EE Config 工具是图形工具，因此需要安装 X 服务器：
 
1. 登录到 J2EE 实例的主应用程序服务器并执行以下命令：`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. 在左框架中，选择“安全性存储”。
1. 在右框架中，选择密钥 jdbc/pool/\<SAPSID>/url。
1. 将 JDBC URL 中的主机名更改为虚拟主机名。
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. 选择“添加”。
1. 要保存所做的更改，请在左上角选择磁盘图标。
1. 关闭配置工具。
1. 重启 Java 实例。

## <a name="configure-log-archiving-for-hadr-setup"></a>配置 HADR 设置的日志存档
要为 HADR 设置配置 Db2 日志存档，建议将主数据库和备用数据库都配置为从所有日志存档位置自动检索日志。 主数据库和备用数据库都必须能够从其中一个数据库实例可能将日志文件存档到的所有日志存档位置检索日志存档文件。 

日志存档仅由主数据库中执行。 如果更改数据库服务器的 HADR 角色或发生故障，则新的主数据库将负责日志存档。 如果已设置多个日志存档位置，则日志可能会存档两次。 对于本地或远程捕获，可能还必须手动将存档的日志从旧的主服务器复制到新的主服务器的活动日志位置。

建议配置一个公共 NFS 共享，其中日志从两个节点写入。 NFS 共享必须高度可用。 

可以将现有的高可用 NFS 共享用于传输或配置文件目录。 有关详情，请参阅：

- [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性][nfs-ha] 
- [带有适用于 SAP 应用程序的 Azure NetApp 文件的 SUSE Linux Enterprise Server 上 Azure VM 上的 SAP NetWeaver 的高可用性](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)（用于创建 NFS 共享）


## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试 Db2 HADR 设置。 每个测试都假设你以用户根用户身份登录，并且 IBM Db2 主节点在 azibmdb01 虚拟机上运行。 

所有测试用例的初始状态如下所述：（crm_mon -r 或 crm status）

- “crm status”是在执行时 Pacemaker 状态的快照 
- “crm_mon-r”是 Pacemaker 状态的连续输出

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

SAP 系统中的原始状态记录在“事务 DBACOCKPIT”>“配置”>“概述”中，如下图所示：

![DBACockpit - 迁移前](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>测试 IBM Db2 的接管


> [!IMPORTANT] 
> 在开始测试前，请确保：
> * Pacemaker 没有任何失败的操作 (crm status)。
> * 没有位置约束（迁移测试的 leftovers）
> * IBM Db2 HADR 同步正在运行。 以用户 db2\<sid> 身份进行检查 <pre><code>db2pd -hadr -db \<DBSID></code></pre>


通过执行以下命令迁移运行主 Db2 数据库的节点：
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

完成迁移后，crm status 输出如下所示：
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

SAP 系统中的原始状态记录在“事务 DBACOCKPIT”>“配置”>“概述”中，如下图所示：

![DBACockpit - 迁移后](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

使用“crm resource migrate”的资源迁移创建位置约束。 应删除位置约束。 如果不删除位置约束，则资源无法进行故障回复，或者可能会遇到不需要的接管。 

将资源迁移回 azibmdb01 ，并清除位置约束
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm resource migrate\<res_name>\<host>：** 创建位置约束并可能导致出现接管问题
- **crm resource clear \<res_name>** ：清除位置约束
- **crm resource cleanup \<res_name>** ：清除所有资源错误

### <a name="test-the-fencing-agent"></a>测试隔离代理

在这种情况下，我们将测试 SBD 隔离，建议在使用 SUSE Linux 时执行此操作。

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

应重新启动群集节点 azibmdb01。 IBM Db2 主 HADR 角色将被移动到 azibmdb02。 当 azibmdb01 恢复联机时，Db2 实例将以辅助数据库实例的角色移动。 

如果 Pacemaker 服务没有在重新启动以前的主节点时自动启动，请务必使用以下命令手动启动：

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>测试手动接管

可以通过停止节点 azibmdb01 上的 Pacemaker 服务来测试手动接管：
<pre><code>service pacemaker stop</code></pre>

azibmdb02 上的状态
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

故障转移后，可以在 azibmdb01 上再次启动该服务。
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>终止运行 HADR 主数据库的节点上的 Db2 进程

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2 实例将失败，Pacemaker 将报告以下状态：

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker 将在同一节点上重启 Db2 主数据库实例，或将故障转移到运行辅助数据库实例的节点，并报告错误。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>终止运行辅助数据库实例的节点上的 Db2 进程

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

节点进入故障状态，并报告错误
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2 实例将在之前分配的辅助角色中重启。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>在运行 HADR 主数据库实例的节点上通过 db2stop force 停止 DB

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

以用户 db2\<sid> 身份执行命令 db2stop force：
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

检测到故障
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR 辅助数据库实例已提升为主角色
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>通过在运行 HADR 主数据库实例的节点上重启的故障 VM

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker 会将辅助实例提升为主实例角色。 VM 和所有服务在 VM 重新启动后完全恢复之后，旧的主实例将移入辅助角色：

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>使用“halt”运行 HADR 主数据库实例的 VM 出现故障

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

在这种情况下，Pacemaker 将检测到运行主数据库实例的节点没有响应。

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

下一步是检查“拆分”情况。 在幸存节点确定最后运行主数据库实例的节点出现故障后，将执行资源的故障转移。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


如果节点“正在停止”，则必须通过 Azure 管理工具（Azure 门户、PowerShell 或 Azure CLI 中）重启故障节点。 故障节点重新联机后，会将 Db2 实例启动到辅助角色。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>后续步骤
- [SAP NetWeaver 的高可用性体系结构和方案](./sap-high-availability-architecture-scenarios.md)
- [在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:dbms_guide_ibm.md
[sles-pacemaker]:high-availability-guide-suse-pacemaker.md
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:high-availability-guide-suse-nfs.md
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:high-availability-guide-suse.md

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:sap-deployment-checklist.md
