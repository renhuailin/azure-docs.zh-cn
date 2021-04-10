---
title: HDInsight Interactive Query 群集 (LLAP) 大小调整指南
description: LLAP 大小调整指南
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: ca3ba61de13e0e451b43dc9c8ea40db33fed859a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869661"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight Interactive Query 群集 (Hive LLAP) 大小调整指南

本文档介绍了 HDInsight Interactive Query 群集（Hive LLAP 群集）典型工作负载的大小调整，以实现合理的性能。 注意，本文档中提供的建议是通用准则，特定的工作负载可能需要进行特定的调整。

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**HDInsight Interactive Query 群集 (LLAP) 的 Azure 默认 VM 类型**

| 节点类型      | 实例 | 大小     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 个 VCPU，56 GB RAM，400 GB SSD   |
| 辅助角色   | **D14 v2**        | **16 个 VCPU，112 GB RAM，800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 个 VCPU，8-GB RAM，40 GB SSD       |

***注意：所有建议的配置值都基于 D14 v2 类型的工作器节点***  

### <a name="configuration"></a>**配置：**    
| 配置密钥      | 建议的值  | 说明 |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | 给定节点上所有 YARN 容器的总内存 (MB) | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | RM 上每个容器请求的最大分配 (MB)。 大于此值的内存请求数将不会生效 |
| yarn.scheduler.maximum-allocation-vcores | 12 |资源管理器中每个容器请求的最大 CPU 核心数。 大于此值的请求数将不会生效。 |
| yarn.nodemanager.resource.cpu-vcores | 12 | 可分配给容器的每个 NodeManager 的 CPU 内核数。 |
| yarn.scheduler.capacity.root.llap.capacity | 85 (%) | LLAP 队列的 YARN 容量分配  |
| tez.am.resource.memory.mb | 4096 (MB) | tez AppMaster 使用的内存量 (MB) |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |hive.server2.tez.default.queues 中命名的每个队列的会话数。 这个数字对应于查询协调器的数量 (Tez AM) |
| hive.tez.container.size | 4096 (MB) | 指定的 Tez 容器大小 (MB) |
| hive.llap.daemon.num.executors | 19 | LLAP 守护程序的执行程序数 | 
| hive.llap.io.threadpool.size | 19 | 执行程序的线程池大小 |
| hive.llap.daemon.yarn.container.mb | 81920 (MB) | 单个 LLAP 守护程序使用的总内存（每个守护程序的内存），以 MB 为单位
| hive.llap.io.memory.size | 242688 (MB) | 在启用了 SSD 缓存的情况下每个 LLAP 守护程序的缓存大小 (MB) |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | 要进行映射联接的内存大小 (MB) |

### <a name="llap-architecturecomponents"></a>**LLAP 体系结构/组件：**  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png " alt-text="`LLAP Architecture/Components`" border="true":::

### <a name="llap-daemon-size-estimations"></a>**LLAP 守护程序大小估计：** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1.确定节点上所有容器的总 YARN 内存分配**    
配置：yarn.nodemanager.resource.memory-mb  

此值表示每个节点上的 YARN 容器可以使用的最大内存总和 (MB)。 指定的值应小于该节点上的物理内存总量。   
节点上所有 YARN 容器的总内存 = （总物理内存 - OS 内存 + 其他服务）  
建议将此值设置为可用 RAM 大小的约 90%。  
对于 D14 v2，建议的值为 **102400 MB**。 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2.确定每个 YARN 容器请求的最大内存量**  
配置：yarn.scheduler.maximum-allocation-mb

此值指示资源管理器上每个容器请求的最大分配 (MB)。 高于此指定值的内存请求将不会生效。 资源管理器可以按“yarn.scheduler.minimum-allocation-mb”的增量为容器提供内存，并且不能超过“yarn.scheduler.maximum-allocation-mb”指定的大小 。 指定的值不应超过由“yarn.nodemanager.resource.memory-mb”指定的节点上所有容器的给定总内存。    
对于 D14 v2 工作器节点，建议的值为 102400 MB

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3.确定每个 YARN 容器请求的最大 vCore 量**  
配置：yarn.scheduler.maximum-allocation-vcores  

此值指示资源管理器中每个容器请求的最大虚拟 CPU 内核数。 请求的 vCore 数大于此值将不会生效。 这是 YARN 计划程序的全局属性。 对于 LLAP 守护程序容器，此值可以设置为总可用 vCore 的 75%。 剩余的 25% 应保留给在工作器节点上运行的 NodeManager、DataNode 和其他服务。  
D14 v2 VM 上总共有 16 个 vCore，在这 16 个 vCore 中，LLAP 守护程序容器可使用 75% 的 vCore。  
对于 D14 v2，建议的值为“12”。  

#### <a name="4-number-of-concurrent-queries"></a>**4.并发查询数**  
配置：hive.server2.tez.sessions.per.default.queue

此配置值确定可以并行启动的 Tez 会话数。 将为“hive.server2.tez.default.queues”指定的每个队列启动这些 Tez 会话。 它对应于 Tez-AM（查询协调器）的数量。 建议使用与工作器节点数相同的值。 Tez AM 的数量可以大于 LLAP 守护程序节点的数量。 Tez AM 的主要职责是协调查询执行，并将查询计划片段分配给相应的 LLAP 守护程序执行。 将此值保持为多个 LLAP 守护程序节点的倍数，以实现更高的吞吐量。  

默认 HDInsight 群集有四个 LLAP 守护程序在四个工作器节点上运行，因此建议的值为“4”。  

**Hive 配置变量的 Ambari UI 滑块`hive.server2.tez.sessions.per.default.queue`：**

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png " alt-text="`LLAP maximum concurrent queries`" border="true":::

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5.Tez 容器和 Tez 应用程序主机大小**    
配置：tez.am.resource.memory.mb, hive.tez.container.size  

tez.am.resource.memory.mb - 定义 Tez 应用程序主机大小。  
建议的值为 4096 MB。
   
hive.tez.container.size - 定义分配给 Tez 容器的内存量。 必须将此值设置为介于 YARN 最小容器大小 (yarn.scheduler.minimum-allocation-mb) 和 YARN 最大容器大小 (yarn.scheduler.maximum-allocation-mb) 之间的值 。 LLAP 守护程序执行程序使用此值来限制每个执行器的内存使用量。  
建议的值为 4096 MB。  

#### <a name="6-llap-queue-capacity-allocation"></a>**6.LLAP 队列容量分配**   
配置：yarn.scheduler.capacity.root.llap.capacity  

此值表示分配给 LLAP 队列的容量百分比。 根据 YARN 队列的配置方式，对于不同的工作负载，容量分配的值可能会有所不同。 如果工作负载是只读操作，那么将其设置为高达 90% 的容量应该是可以的。 但是，如果工作负载是使用托管表的更新/删除/合并操作的混合，建议为 llap 队列提供 85% 的容量。 剩余 15% 的容量可用于其他任务（如压缩等），以此从默认队列分配容器。 这样，默认队列中的任务就不会剥夺 YARN 资源。    

对于 D14v2 工作器节点，llap 队列的建议值为“85”。     
（对于只读工作负载，可以适当地将其增加到 90。）  

#### <a name="7-llap-daemon-container-size"></a>**7.LLAP 守护程序容器大小**    
配置：hive.llap.daemon.yarn.container.mb  
   
LLAP 守护程序作为 YARN 容器在每个工作器节点上运行。 LLAP 守护程序容器的总内存大小取决于以下因素，    
*  YARN 容器大小的配置（yarn.scheduler.minimum-allocation-mb、yarn.scheduler.maximum-allocation-mb、yarn.nodemanager.resource.memory-mb）
*  节点上的 Tez AM 数量
*  为节点上的所有容器配置的总内存和 LLAP 队列容量  

Tez Application Master (Tez AM) 所需的内存可按如下进行计算。  
Tez AM 充当查询协调器，应根据要提供的并发查询数来配置 Tez-AM 的数量。 从理论上讲，我们可以考虑为每个工作器节点配置一个 Tez AM。 但是，你可能会在一个工作器节点上看到多个 Tez AM。 为了进行计算，我们假定在所有 LLAP 守护程序节点/工作器节点上均匀分布 Tez AM。
建议为每个 Tez AM 配置 4 GB 内存。  

Tez Am 数 = 由 Hive 配置“hive.server2.tez.sessions.per.default.queue”指定的值。  
LLAP守护程序的节点数 = Ambari UI 中环境节点 ***num_llap_nodes_for_llap_daemons*** 指定的值。  
Tez AM 容器大小 = Tez 配置 ***tez.am.resource.memory.mb*** 指定的值。  

每个节点的 Tez AM 内存 =（ceil (Tez AM 数/LLAP 守护程序节点数) x Tez AM 容器大小）       
对于 D14 v2，默认配置有四个 Tez AM 和四个 LLAP 守护程序节点。  
每个节点的 Tez AM 内存 = (ceil(4/4) x 4 GB) = 4 GB

每个工作器节点的 LLAP 队列可用的总内存可按如下进行计算：  
此值取决于节点上所有 YARN 容器的可用内存总量 (yarn.nodemanager.resource.memory-mb) 以及为 LLAP 队列配置的容量百分比 (yarn.scheduler.capacity.root.llap.capacity) 。  
工作器节点上的 LLAP 队列的总内存 = 节点上所有 YARN 容器可用的总内存 x LLAP 队列容量百分比。  
对于 D14 v2，该值为 (100 GB x 0.85) = 85 GB。

LLAP 守护程序容器大小可按如下进行计算；

**LLAP 守护程序容器大小 =（工作器节点上 LLAP 队列的总内存）-（每个节点的 Tez AM 内存）-（服务主机容器大小）**  
在其中一个工作器节点上生成的群集上只有一个服务主机（LLAP 服务的应用程序主机）。 为了进行计算，我们假定每个工作器节点上有一个服务主机。  
对于 D14 v2 工作器节点 HDI 4.0，建议的值为 (85 GB - 4 GB - 1 GB) = 80 GB   
（对于 HDI 3.6，建议的值为 79 GB，因为你应为滑块 AM 保留额外约 2 GB。）  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8.确定每个 LLAP 守护程序的执行程序数**  
配置：hive.llap.daemon.num.executors、hive.llap.io.threadpool.size*

***hive.llap.daemon.num.executors***：   
此配置控制每个 LLAP 守护程序可以并行执行任务的执行程序数。 此值取决于 vCore 的数量、每个执行程序使用的内存量以及 LLAP 守护程序容器可用的总内存量。    每工作器节点的执行程序数可以超额订阅可用 vcore 的 120%。 但是，如果它不满足每个执行程序所需的内存和 LLAP 守护程序容器大小的内存要求，则应进行调整。

每个执行程序都等效于一个 Tez 容器，可以使用 4 GB（Tez 容器大小）的内存。 LLAP 守护程序中的所有执行程序共用相同的堆内存。 假设并非所有执行程序都同时运行内存密集型操作，你可以考虑使每个执行程序使用 Tez 容器大小 (4 GB) 的 75%。 这样，你可以通过为每个执行程序分配较少的内存（例如 3 GB）来增加并行度，从而增加执行程序数。 但是，建议针对你的目标工作负载优化此设置。

D14 v2 VM 上有 16 个 vCore。
对于 D14 v2，每个工作器节点上的执行程序数的建议值为（16 个 vcore x 120%）~= 19，每个执行程序 3 GB。

hive.llap.io.threadpool.size：   
此值指定执行程序的线程池大小。 由于根据指定，执行程序数量是固定的，此值与每个 LLAP 守护程序的执行程序数量相同。    
对于 D14 v2，建议的值为“19”。

#### <a name="9-determining-llap-daemon-cache-size"></a>**9.确定 LLAP 守护程序缓存大小**  
配置：hive.llap.io.memory.size

LLAP 守护程序容器内存包含以下部分；
*  余量
*  执行程序使用的堆内存 (Xmx)
*  每个守护程序的内存中缓存（其堆外内存大小，启用 SSD 缓存时不适用）
*  内存中缓存元数据大小（仅当启用 SSD 缓存时适用）

**余量大小**：此大小表示有一部分堆外内存用于 Java VM 开销（元空间、线程堆栈、GC 数据结构等）。 通常，此开销大约是堆大小 (Xmx) 的 6%。 为了安全起见，此值可计算为总 LLAP 守护程序内存大小的 6%。  
对于 D14 v2，建议的值为 ceil(80 GB x 0.06) ~= 4 GB。  

**堆大小 (Xmx)** ：它是可供所有执行程序使用的堆内存量。
堆大小总计 = 执行程序数 x 3 GB  
对于 D14 v2，该值为 19 x 3 GB = 57 GB  

`Ambari environment variable for LLAP heap size:`

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png " alt-text="`LLAP heap size`" border="true":::

禁用 SSD 缓存后，内存中的缓存是从 LLAP 守护程序容器大小中取出余量大小和堆大小后剩余的内存量。

启用 SSD 缓存后，缓存大小计算会有所不同。
设置 hive.llap.io.allocator.mmap = true 将启用 SSD 缓存。
启用 SSD 缓存后，内存的某些部分将用于存储 SSD 缓存的元数据。 元数据存储在内存中，预计约为 SSD 缓存大小的 8%。   
SSD 缓存内存中元数据大小 = LLAP 守护程序容器大小 -（余量 + 堆大小）  
对于 D14 v2 和 HDI 4.0，SSD 缓存内存中元数据大小 = 80 GB - (4 GB + 57 GB) = 19 GB  
对于 D14 v2 和 HDI 3.6，SSD 缓存内存中元数据大小 = 79 GB - (4 GB + 57 GB) = 18 GB

如果给定用于存储 SSD 缓存元数据的可用内存大小，我们可以计算可支持的 SSD 缓存大小。  
SSD 缓存的内存中元数据大小 = LLAP 守护程序容器大小 -（余量 + 堆大小）= 19 GB     
SSD 缓存大小 = SSD 缓存的内存中元数据大小 (19 GB)/0.08 (8%)  

对于 D14 v2 和 HDI 4.0，建议的 SSD 缓存大小为 19 GB/0.08 ~= 237 GB  
对于 D14 v2 和 HDI 3.6，建议的 SSD 缓存大小为 18 GB/0.08 ~= 225 GB

#### <a name="10-adjusting-map-join-memory"></a>**10.调整映射联接内存**   
配置：hive.auto.convert.join.noconditionaltask.size

确保已启用 hive.auto.convert.join.noconditionaltask，以便此参数生效。
此配置确定 Hive 优化器为 MapJoin 选择的阈值，该优化器会考虑其他执行程序的内存超额订阅，从而为内存中的哈希表留出更多空间，以允许更多的映射联接转换。 假定每个执行器 3 GB，此大小可超额订阅 3 GB，但其他操作还可将某些堆内存用于对缓冲区、无序缓冲区排序等。   
因此对于 D14 v2，每个执行程序有 3 GB 内存，建议将此值设置为 2048 MB。  

（注意：此值可能需要根据你的工作负载进行调整。 将此值设置得太低可能无法使用自动转换功能。 设置得过高可能会导致内存不足异常或 GC 暂停，从而导致性能下降。）  

#### <a name="11-number-of-llap-daemons"></a>**11.LLAP 守护程序数**
Ambari 环境变量：num_llap_nodes、num_llap_nodes_for_llap_daemons  

num_llap_nodes - 指定 Hive LLAP 服务使用的节点数，这包括运行 LLAP 守护程序、LLAP 服务主机和 Tez 应用程序主机 (Tez AM) 的节点。  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png " alt-text="`Number of Nodes for LLAP service`" border="true":::  

**num_llap_nodes_for_llap_daemons** - 仅用于 LLAP 守护程序的指定节点数。 LLAP 守护程序容器大小设置为“最大适用”节点，因此在每个节点上都有一个 LLAP 守护程序。

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png " alt-text="`Number of Nodes for LLAP daemons`" border="true":::

建议使两个值都与 Interactive Query 群集中的工作器节点数相同。

### <a name="considerations-for-workload-management"></a>**工作负载管理注意事项**  
如果要为 LLAP 启用工作负载管理，请确保保留足够的容量以便工作负载管理按预期方式工作。 工作负载管理需要配置自定义 YARN 队列，该队列是 `llap` 队列的补充。 请确保根据工作负载要求，在 llap 队列与工作负载管理队列之间划分总群集资源容量。
激活资源计划后，工作负载管理会生成 Tez 应用程序主机 (Tez AM)。
请注意：

* 激活资源计划而生成的 Tez AM 使用 `hive.server2.tez.interactive.queue` 指定的工作负载管理队列中的资源。  
* Tez AM 的数量将取决于资源计划中指定的 `QUERY_PARALLELISM` 的值。  
* 工作负载管理激活后，将不使用 llap 队列中的 Tez AM。 仅工作负载管理队列中的 Tez AM 用于查询协调。 禁用工作负载管理后，将使用 `llap` 队列中的 Tez AM。
 
例如：总群集容量 = 100 GB 内存，在 LLAP、工作负载管理和默认队列之间划分，如下所示：
 - llap 队列容量 = 70 GB
 - 工作负载管理队列容量 = 20 GB
 - 默认队列容量 = 10 GB

使用工作负载管理队列容量中的 20 GB，资源计划可将 `QUERY_PARALLELISM` 值指定为 5，这意味着工作负载管理可以启动五个 Tez AM，每个容器大小分别为 4 GB。 如果 `QUERY_PARALLELISM` 高于该容量，某些 Tez AM 可能会在 `ACCEPTED` 状态下停止响应。 Hiveserver2 Interactive 无法将查询片段提交到不处于 `RUNNING` 状态的 Tez AM。


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