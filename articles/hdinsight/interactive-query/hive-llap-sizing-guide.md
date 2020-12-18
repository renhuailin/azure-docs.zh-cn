---
title: HDInsight Interactive Query 群集 (LLAP) 大小调整指南
description: LLAP 大小调整指南
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 626b061cc237f7238d47863a3e1ed88961d2f742
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680529"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight Interactive Query 群集 (Hive LLAP) 大小调整指南

本文档介绍了 HDInsight Interactive Query 群集（Hive LLAP 群集）典型工作负载的大小调整，以实现合理的性能。 注意，本文档中提供的建议是通用准则，特定的工作负载可能需要进行特定的调整。

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**HDInsight Interactive Query 群集 (LLAP) 的 Azure 默认 VM 类型**

| 节点类型      | 实例 | 大小     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 个 VCPU，56 GB RAM，400 GB SSD   |
| 辅助角色   | **D14 v2**        | **16 个 VCPU，112 GB RAM，800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 个 VCPU，8-GB RAM，40 GB SSD       |

**_注意：所有建议的配置值都基于 D14 v2 类型的工作器节点_* _  

### <a name="_configuration"></a>_ *配置：* *    
| 配置密钥      | 建议的值  | 说明 |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | 给定节点上所有 YARN 容器的总内存 (MB) | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | RM 上每个容器请求的最大分配 (MB)。 大于此值的内存请求数将不会生效 |
| yarn.scheduler.maximum-allocation-vcores | 12 |资源管理器中每个容器请求的最大 CPU 核心数。 大于此值的请求数将不会生效。 |
| yarn.nodemanager.resource.cpu-vcores | 12 | 可分配给容器的每个 NodeManager 的 CPU 内核数。 |
| yarn.scheduler.capacity.root.llap.capacity | 85 (% )  | LLAP 队列的 YARN 容量分配  |
| tez.am.resource.memory.mb | 4096 (MB) | tez AppMaster 使用的内存量 (MB) |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |hive.server2.tez.default.queues 中命名的每个队列的会话数。 这个数字对应于查询协调器的数量 (Tez AM) |
| hive.tez.container.size | 4096 (MB) | 指定的 Tez 容器大小 (MB) |
| hive.llap.daemon.num.executors | 19 | LLAP 守护程序的执行程序数 | 
| hive.llap.io.threadpool.size | 19 | 执行程序的线程池大小 |
| hive.llap.daemon.yarn.container.mb | 81920 (MB)  | 单个 LLAP 守护程序使用的总内存（每个守护程序的内存），以 MB 为单位
| hive.llap.io.memory.size | 242688 (MB)  | 在启用了 SSD 缓存的情况下每个 LLAP 守护程序的缓存大小 (MB) |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | 要进行映射联接的内存大小 (MB) |

### <a name="llap-architecturecomponents"></a>**LLAP 体系结构/组件：**  

![' LLAP 体系结构/组件 '](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP 体系结构/组件")

### <a name="llap-daemon-size-estimations"></a>**LLAP 守护程序大小估计：** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1.确定节点上所有容器的总 YARN 内存分配**    
配置：**_yarn.nodemanager.resource.memory-mb_* _  

此值表示每个节点上的 YARN 容器可以使用的最大内存总和 (MB)。 指定的值应小于该节点上的物理内存总量。   
节点上所有 YARN 容器的总内存量 = (总物理内存– OS + 其他服务的内存)   
建议将此值设置为可用 RAM 大小的约 90%。  
对于 D14 v2，建议的值为“102400 MB”。 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2.确定每个 YARN 容器请求的最大内存量**  
配置：**_yarn.scheduler.maximum-allocation-mb_* _

此值指示资源管理器上每个容器请求的最大分配 (MB)。 高于此指定值的内存请求将不会生效。 资源管理器可以按“yarn.scheduler.minimum-allocation-mb”增量为容器提供内存，单不能超过“yarn.scheduler.maximum-allocation-mb”指定的大小。 指定的值不应超过由“yarn.nodemanager.resource.memory-mb”指定的节点上所有容器的给定总内存。    
对于 D14 v2 工作器节点，建议的值为 102400 MB

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3.确定每个 YARN 容器请求的最大 vCore 量**  
配置：**_yarn.scheduler.maximum-allocation-vcores_* _  

此值指示资源管理器中每个容器请求的最大虚拟 CPU 内核数。 请求的 vCore 数大于此值将不会生效。 这是 YARN 计划程序的全局属性。 对于 LLAP 守护程序容器，此值可以设置为总可用 vCore 的 75%。 剩余的 25% 应保留给在工作器节点上运行的 NodeManager、DataNode 和其他服务。  
D14 v2 VM 上总共有 16 个 vCore，在这 16 个 vCore 中，LLAP 守护程序容器可使用 75% 的 vCore。  
对于 D14 v2，建议的值为“12”。  

#### <a name="4-number-of-concurrent-queries"></a>**4.并发查询数**  
配置：**_hive.server2.tez.sessions.per.default.queue_* _

此配置值确定可以并行启动的 Tez 会话数。 将为“hive.server2.tez.default.queues”指定的每个队列启动这些 Tez 会话。 它对应于 Tez-AM（查询协调器）的数量。 建议使用与工作器节点数相同的值。 Tez AM 的数量可以大于 LLAP 守护程序节点的数量。 Tez AM 的主要职责是协调查询执行，并将查询计划片段分配给相应的 LLAP 守护程序执行。 将此值保持为多个 LLAP 守护程序节点的倍数，以实现更高的吞吐量。  

默认 HDInsight 群集中有四个 LLAP 守护程序在四个工作器节点上运行，因此建议的值为 4。  

**Hive 配置变量的 Ambari UI 滑块 `hive.server2.tez.sessions.per.default.queue` ：**

!["LLAP 最大并发查询数"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "LLAP 并发查询的最大数目")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5.Tez 容器和 Tez 应用程序主机大小**    
配置：**_tez.am.resource.memory.mb、hive.tez.container.size_* _  

_tez.am.resource.memory.mb* - 定义 Tez 应用程序主机大小。  
建议的值为 4096 MB。
   
hive.tez.container.size - 定义分配给 Tez 容器的内存量。 必须将此值设置为介于 YARN 最小容器大小 (yarn.scheduler.minimum-allocation-mb) 和 YARN 最大容器大小 (yarn.scheduler.maximum-allocation-mb) 之间的值 。 LLAP 守护程序执行程序使用此值来限制每个执行器的内存使用量。  
建议的值为 4096 MB。  

#### <a name="6-llap-queue-capacity-allocation"></a>**6.LLAP 队列容量分配**   
配置：**_yarn.scheduler.capacity.root.llap.capacity_* _  

此值表示分配给 LLAP 队列的容量百分比。 根据 YARN 队列的配置方式，对于不同的工作负载，容量分配的值可能会有所不同。 如果工作负载是只读操作，那么将其设置为高达 90% 的容量应该是可以的。 但是，如果你的工作负荷使用托管表混合了更新/删除/合并操作，建议为 llap queue 提供85% 的容量。 剩余的15% 容量可由其他任务（如压缩等）用于从默认队列分配容器。 这样，默认队列中的任务就不会剥夺 YARN 资源。    

对于 D14v2 工作节点，建议 llap queue 值为 _ * 85 * *。     
（对于只读工作负载，可以适当地将其增加到 90。）  

#### <a name="7-llap-daemon-container-size"></a>**7.LLAP 守护程序容器大小**    
配置：**_hive.llap.daemon.yarn.container.mb_* _  
   
LLAP 守护程序作为 YARN 容器在每个工作器节点上运行。 LLAP 守护程序容器的总内存大小取决于以下因素，    
_  YARN 容器大小的配置（yarn.scheduler.minimum-allocation-mb、yarn.scheduler.maximum-allocation-mb、yarn.nodemanager.resource.memory-mb）
*  节点上的 Tez AM 数量
*  为节点上的所有容器配置的总内存和 LLAP 队列容量  

Tez Application Master (Tez AM) 所需的内存可按如下进行计算。  
Tez AM 充当查询协调器，并基于要提供的多个并发查询来配置 Tez AMs 的数量。 从理论上讲，我们可以考虑每个工作节点一个 Tez 点。 但是，它可能会在一个工作节点上看到多个 Tez AM。 为了进行计算，我们假定在所有 LLAP 守护程序节点/工作节点上统一分布 Tez AMs。
建议为每个 Tez AM 配置 4 GB 内存。  

Hive config * hive. Tez _ 指定的 Tez Ams = 值的数目（以 **秒为单位**）。  
在 Ambari UI 中，由 env variable _*_num_llap_nodes_for_llap_daemons_*_ 指定的 LLAP daemon 节点数。  
Tez AM container size = Tez config _*_Tez_*_ 指定的值。  

每个节点 Tez AM memory = _ *(** **Ceil (** Tez AMS **/** **)** **x** Tez AM 容器大小的 LLAP 守护程序节点数 **)**  
对于 D14 v2，默认配置有四个 Tez AM 和四个 LLAP 守护程序节点。  
每个节点的 Tez AM 内存 = (ceil(4/4) x 4 GB) = 4 GB

可按如下所示计算每个工作节点的 LLAP 队列可用的总内存：  
此值取决于节点 (*YARN*) 上的所有 YARN 容器可用的总内存量，以及为 llap 队列配置的容量的百分比 (*YARN*) 的容量的百分比的值。  
工作器节点上的 LLAP 队列的总内存 = 节点上所有 YARN 容器可用的总内存 x LLAP 队列容量百分比。  
对于 D14 v2，此值为 (100 GB x 0.85) = 85 GB。

LLAP 守护程序容器大小可按如下进行计算；

**LLAP daemon container size = workernode) 上的 LLAP queue (总计内存– (每个节点 Tez AM 内存) - (Service Master 容器大小)**  
在其中一个辅助节点上生成的群集上只有一个服务主机 (应用程序主机用于 LLAP 服务) 。 为了进行计算，我们考虑每个工作节点一个服务主节点。  
对于 D14 v2 辅助角色节点，HDI 4.0-建议的值 (为 85 GB-4 gb-1 GB) # A2 = **80 GB**   
 (HDI 3.6，建议值为 **79 GB** ，因为对于滑块 AM，你应保留额外 ~ 2 GB。 )   

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8.确定每个 LLAP 守护程序的执行程序数**  
配置：**_hive.llap.daemon.num.executors_* _、_*_hive.llap.io.threadpool.size_*_

_*_hive.llap.daemon.num.executors_*_：   
此配置控制每个 LLAP 守护程序可以并行执行任务的执行程序数。 此值取决于 vcore 数、每个执行器使用的内存量，以及 LLAP 守护程序容器可用的总内存量。    对于每个辅助角色节点，可以将执行器的数量超额订阅为可用 vcore 的120%。 但是，如果它不满足每个执行器所需的内存和 LLAP 守护程序容器大小的内存要求，则应进行调整。

每个执行器都等效于 Tez 容器，并可使用 4GB (Tez 容器大小) 内存。 LLAP 后台中的所有执行器共享相同的堆内存。 假设并非所有执行程序同时运行内存密集型操作，你可以将 Tez 容器大小的75% 视为每个执行程序 (4 GB) 。 这样一来，你可以通过为每个执行器提供更少内存 (例如，3 GB) 增加并行度来增加执行器的数目。 但建议优化目标工作负荷的此设置。

D14 v2 VM 上有 16 个 vCore。
对于 D14 v2，为每个执行器考虑3GB 的每个工作节点上 (16 vcore x 120% ) ~ = _ *19**。

**_llap_*_：此值指定执行器的线程池大小。由于执行器是按指定固定的，因此它将与每个 LLAP 后台程序的执行器数相同。对于 D14 v2，推荐值为 _* 19**。

#### <a name="9-determining-llap-daemon-cache-size"></a>**9.确定 LLAP 守护程序缓存大小**  
配置：**_hive.llap.io.memory.size_* _

LLAP 守护程序容器内存包含以下组件：_  余量
*  执行程序使用的堆内存 (Xmx)
*  每个守护程序的内存中缓存（其堆外内存大小，启用 SSD 缓存时不适用）
*  内存中缓存元数据大小（仅当启用 SSD 缓存时适用）

**余量大小**：此大小表示有一部分堆外内存用于 Java VM 开销（元空间、线程堆栈、GC 数据结构等）。 通常，此开销大约是堆大小 (Xmx) 的 6%。 为了安全起见，此值可计算为总 LLAP 守护程序内存大小的 6%。  
对于 D14 v2，推荐值为 ceil (80 GB x 0.06) ~ = **4 gb**。  

**堆大小 (Xmx)** ：它是可供所有执行程序使用的堆内存量。
堆大小总计 = 执行器的数目 x 3 GB  
对于 D14 v2，此值为 19 x 3 GB = **57 gb**  

`Ambari environment variable for LLAP heap size:`

!["LLAP 堆大小"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "LLAP 堆大小")

禁用 SSD 缓存后，内存中缓存是指从 LLAP 守护程序容器大小中取出空间大小和堆大小后剩余的内存量。

启用 SSD 缓存后，缓存大小计算会有所不同。
设置 hive.llap.io.allocator.mmap = true 将启用 SSD 缓存。
启用 SSD 缓存后，内存的某些部分将用于存储 SSD 缓存的元数据。 元数据存储在内存中，并且预计大约为 SSD 缓存大小的8%。   
SSD 缓存内存中的元数据大小 = LLAP daemon 容器大小- (Head 房间 + 堆大小)   
对于 D14 v2，HDI 4.0，SSD 缓存内存中的元数据大小 = 80 GB- (4 GB + 57 GB) = **19 gb**  
对于 D14 v2，HDI 3.6，SSD 缓存内存中的元数据大小 = 79 GB- (4 GB + 57 GB) = **18 gb**

如果给定用于存储 SSD 缓存元数据的可用内存大小，我们可以计算可支持的 SSD 缓存大小。  
SSD 缓存的内存中元数据大小 = LLAP daemon 容器大小- (头房间 + 堆大小) = 19 GB     
SSD cache 的大小 = SSD 缓存的内存中元数据的大小 (19 GB) /0.08 (8%)   

对于 D14 v2 和 HDI 4.0，推荐的 SSD 缓存大小 = 19 GB/0.08 ~ = **237 gb**  
对于 D14 v2 和 HDI 3.6，推荐的 SSD 缓存大小 = 18 GB/0.08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10.调整映射联接内存**   
配置：**_hive.auto.convert.join.noconditionaltask.size_* _

确保已启用 hive.auto.convert.join.noconditionaltask，以便此参数生效。
此配置通过 Hive 优化器确定 MapJoin 选择的阈值，该优化器会考虑来自其他执行器的内存过度订阅，以便为内存中哈希表腾出更多空间，以允许更多的映射联接转换。 考虑每个执行器的3GB，此大小可能会过度订阅3GB，但某些堆内存还可以用于排序缓冲区、无序缓冲区等其他操作。   
因此对于 D14 v2，每个执行器的内存为 3 GB，建议将此值设置为 **2048 MB**。  

 (注意：此值可能需要适合你的工作负荷的调整。 将此值设置得太低可能无法使用自动转换功能。 如果将它设置得过高，则可能导致内存不足异常或 GC 暂停，从而导致性能不利。 )   

#### <a name="11-number-of-llap-daemons"></a>**11. LLAP 守护程序的数目**
Ambari 环境变量： **_num_llap_nodes，num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes**-指定 Hive llap 服务使用的节点数，这包括运行 Llap 守护程序的节点、Llap service Master 和 Tez Application Master (Tez AM) 。  

!["LLAP 服务的节点数"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "LLAP 服务的节点数")  

**num_llap_nodes_for_llap_daemons** 指定的节点数，仅用于 llap 守护程序。 LLAP daemon 容器大小设置为 "最大大小" 节点，因此在每个节点上都有一个 LLAP 守护程序。

!["LLAP 守护程序的节点数"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "LLAP 守护程序的节点数")

建议将两个值与交互式查询群集中的辅助角色节点数相同。

### <a name="considerations-for-workload-management"></a>**工作负荷管理注意事项**  
如果要为 LLAP 启用工作负荷管理，请确保保留足够的容量以便工作负荷管理按预期方式工作。 除了 queue 外，工作负荷管理还需要配置自定义 YARN 队列 `llap` 。 请确保根据工作负荷要求，在 llap 队列与工作负荷管理队列之间划分总体群集资源容量。
激活资源计划时，工作负荷管理会生成 Tez 应用程序主机 (Tez AMs) 。
请注意：

* 通过激活资源计划生成的 Tez AMs 使用由指定的工作负荷管理队列中的资源 `hive.server2.tez.interactive.queue` 。  
* Tez AMs 的数量取决于 `QUERY_PARALLELISM` 资源计划中指定的值。  
* 工作负荷管理处于活动状态后，将不会使用 Tez AMs in llap 队列。 仅工作负荷管理队列中的 Tez AMs 用于查询协调。 `llap`禁用工作负荷管理后，将使用队列中的 Tez AMs。
 
例如：总群集容量 = 100 GB 内存，分为 LLAP、工作负荷管理和默认队列，如下所示：
 - llap 队列容量 = 70 GB
 - 工作负荷管理队列容量 = 20 GB
 - 默认队列容量 = 10 GB

使用工作负荷管理队列容量中的 20 GB，资源计划可将 `QUERY_PARALLELISM` 值指定为5，这意味着工作负荷管理可以启动五个 Tez AMs，每个容器大小分别为 4 GB。 如果 `QUERY_PARALLELISM` 高于容量，可能会看到某些 Tez AMs 挂起 `ACCEPTED` 状态。 Hiveserver2 Interactive 无法将查询片段提交到未处于状态的 Tez AMs `RUNNING` 。


#### <a name="next-steps"></a>**后续步骤**
如果设置这些值并不能解决问题，请访问以下其中一个…

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区与适当的资源（解答、支持人员和专家）相关联来改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。  

* ##### <a name="other-references"></a>**其他参考：**
  * [配置其他 LLAP 属性](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [配置 Hive 服务器堆大小](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [LLAP 的映射联接内存大小](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez 执行引擎属性](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Hive LLAP 深入探讨](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)