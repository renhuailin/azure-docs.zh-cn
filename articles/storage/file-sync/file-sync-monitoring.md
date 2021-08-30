---
title: 监视 Azure 文件同步 | Microsoft Docs
description: 查看如何使用 Azure Monitor、存储同步服务和 Windows Server 来监视你的 Azure 文件同步部署。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb1f793e9abad7c28b91da5c3028483637c53740
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114722039"
---
# <a name="monitor-azure-file-sync"></a>监视 Azure 文件同步

使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文介绍如何使用 Azure Monitor、存储同步服务和 Windows Server 来监视你的 Azure 文件同步部署。

本指南介绍了以下方案： 
- 查看 Azure Monitor 中的 Azure 文件同步指标。
- 在 Azure Monitor 中创建警报，以主动通知关键情况。
- 使用 Azure 门户查看 Azure 文件同步部署的运行状况。
- 如何使用 Windows Server 上的事件日志和性能计数器来监视 Azure 文件同步部署的运行状况。 

## <a name="azure-monitor"></a>Azure Monitor

使用 [Azure Monitor](../../azure-monitor/overview.md) 查看指标，并为同步、云分层和服务器连接配置警报。  

### <a name="metrics"></a>指标

默认情况下，启用 Azure 文件同步指标，并每隔 15 分钟将指标发送到 Azure Monitor。

**如何查看 Azure Monitor 中的 Azure 文件同步指标**
1. 转到“Azure 门户”中的“存储同步服务”，然后单击“指标”  。
2. 单击“指标”下拉列表，然后选择要查看的指标。

![Azure 文件同步指标的屏幕截图](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

Azure Monitor 提供了下列 Azure 文件同步指标：

| 指标名称 | 说明 |
|-|-|
| 同步的字节数 | 传输数据大小（上传和下载）。<br><br>单元：字节<br>聚合类型：Sum<br>适用的维度：服务器终结点名称、同步方向、同步组名称 |
| 云分层缓存命中率 | 从缓存中提供与从云中召回的字节百分比，不是整个文件。<br><br>单位：百分比<br>聚合类型：平均值<br>适用的维度：服务器终结点名称、服务器名称、同步组名称 |
| 云分层重调大小 | 回调的数据大小。<br><br>单元：字节<br>聚合类型：Sum<br>适用的维度：服务器名称、同步组名称 |
| 应用程序的云分层重调大小 | 应用程序召回的数据大小。<br><br>单元：字节<br>聚合类型：Sum<br>适用的维度：应用程序名称、服务器名称、同步组名称 |
| 云分层重调成功率 | 成功的召回请求的百分比。<br><br>单位：百分比<br>聚合类型：平均值<br>适用的维度：服务器终结点名称、服务器名称、同步组名称 |
| 云分层重调吞吐量 | 数据召回吞吐量大小。<br><br>单元：字节<br>聚合类型：Sum<br>适用的维度：服务器名称、同步组名称 |
| 未同步的文件 | 未能同步的文件数。<br><br>单位：计数<br>聚合类型：Average、Sum<br>适用的维度：服务器终结点名称、同步方向、同步组名称 |
| 同步的文件 | 已传输的文件数（上传和下载）。<br><br>单位：计数<br>聚合类型：Sum<br>适用的维度：服务器终结点名称、同步方向、同步组名称 |
| 服务器缓存大小 | 服务器上缓存的数据大小。<br><br>单元：字节<br>聚合类型：平均值<br>适用的维度：服务器终结点名称、服务器名称、同步组名称 |
| 服务器联机状态 | 从服务器接收的检测信号数。<br><br>单位：计数<br>聚合类型：最大值<br>适用的维度：服务器名称 |
| 同步会话结果 | 同步会话结果（1=成功同步会话；0=失败同步会话）<br><br>单位：计数<br>聚合类型：Maximum<br>适用的维度：服务器终结点名称、同步方向、同步组名称 |

### <a name="alerts"></a>警报

在监视数据中发现重要情况时，警报会以主动的方式通知你。 若要详细了解如何在 Azure Monitor 中配置警报，请参阅 [Microsoft Azure 中的警报概述](../../azure-monitor/alerts/alerts-overview.md)。

**如何为 Azure 文件同步创建警报**

1. 转到“Azure 门户”中的“存储同步服务” 。 
2. 在“监视”部分中单击“警报”，然后单击“+ 新建警报规则” 。
3. 单击“选择条件”，并为警报提供以下信息： 
    - **指标**
    - **维度名称**
    - **警报逻辑**
4. 单击“选择操作组”，并通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
5. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
6. 单击“创建警报规则”以创建警报。  

下表列出了一些要监视的示例场景和用于警报的适当指标：

| 场景 | 用于警报的指标 |
|-|-|
| 服务器终结点运行状况在门户中显示错误 | 同步会话结果 |
| 文件未能同步到服务器或云终结点 | 未同步的文件 |
| 已注册的服务器未能与存储同步服务进行通信 | 服务器联机状态 |
| 云分层召回大小在一天内超过 500GiB  | 云分层重调大小 |

有关如何为这些场景创建警报的说明，请参阅[警报示例](#alert-examples)部分。

## <a name="storage-sync-service"></a>存储同步服务

若要在“Azure 门户”中查看 Azure 文件同步部署的运行状况，请导航到“存储同步服务”，并使用以下信息 ：

- 已注册的服务器运行状况
- 服务器终结点运行状况
    - 未同步的文件
    - 同步活动
    - 云分层效率
    - 文件未分层
    - 召回错误
- 指标

### <a name="registered-server-health"></a>已注册的服务器运行状况

若要在门户中查看“已注册的服务器运行状况”，请导航到“存储同步服务”的“已注册的服务器”部分  。

![已注册的服务器运行状况的屏幕截图](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- 如果“已注册的服务器”状态为“联机”，则服务器已成功与服务进行通信 。
- 如果“已注册的服务器”状态为“脱机”，则存储同步监视器进程 (AzureStorageSyncMonitor.exe) 未运行，或服务器无法访问 Azure 文件同步服务 。 请参阅[故障排除文档](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity)获取指南。

### <a name="server-endpoint-health"></a>服务器终结点运行状况

若要在门户中查看“服务器终结点”的运行状况，请导航到“存储同步服务”的“同步组”部分，然后选择“同步组”   。

![服务器终结点运行状况的屏幕截图](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- 门户中的“服务器终结点运行状况”和“同步活动”取决于服务器上遥测事件日志中记录的同步事件（ID 9102 和 9302） 。 如果同步会话由于暂时性错误（例如错误已取消）而失败，则只要当前同步会话正在进行（已应用文件），服务器终结点就会在门户中依旧显示为“运行正常”。 事件 ID 9302 是同步进度事件，而事件 ID 9102 是同步会话完成后被记录的。  有关详细信息，请参阅[同步运行状况](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync)和[同步进度](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。 如果服务器终结点运行状况显示“错误”或“无活动”，请参阅[故障排除文档](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors)以获取指南 。
- 门户中的未同步的文件计数基于在服务器的遥测事件日志中记录的事件 ID 9121。 同步会话完成后，每项的错误都会记录在此事件中。 若要解决每项错误，请参阅[如何确定是否有特定的文件或文件夹未同步？](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。
- 若要在门户中查看“云分层效率”，请转到“服务器终结点属性”，然后导航到“云分层”部分  。 为云分层效率提供的数据基于服务器的遥测事件日志中记录的事件 ID 9071。 若要了解详细信息，请参阅[监视云分层](file-sync-monitor-cloud-tiering.md)。
- 若要在门户中查看“未分层文件”和“召回错误”，请转到“服务器终结点属性”，然后导航到“云分层”部分   。 “未分层文件”基于服务器的遥测事件日志中记录的事件 ID 9003，“召回错误”基于事件 ID 9006 。 若要调查无法进行分层或召回的文件，请参阅[如何对未能分层的文件进行故障排除](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier)和[如何对未能召回的文件进行故障排除](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)。

### <a name="metric-charts"></a>指标图表

- 可以在“存储同步服务”门户中查看以下指标图表：

  | 指标名称 | 说明 | 边栏选项卡名称 |
  |-|-|-|
  | 同步的字节数 | 已传输数据的大小（上传和下载） | 同步组，服务器终结点 |
  | 云分层回调 | 回调的数据的大小 | 已注册的服务器 |
  | 未同步的文件 | 未能同步的文件数 | 服务器终结点 |
  | 同步的文件 | 已传输的文件数（上传和下载） | 同步组，服务器终结点 |
  | 服务器联机状态 | 从服务器接收的检测信号数 | 已注册的服务器 |

- 若要了解详细信息，请参阅 [Azure Monitor](#azure-monitor)。

  > [!Note]  
  > “存储同步服务”门户中的图表的时间范围为 24 小时。 若要查看不同时间范围或维度，请使用 Azure Monitor。

## <a name="windows-server"></a>Windows Server

在安装了 Azure 文件同步代理的 Windows Server 上，你可以使用事件日志和性能计数器查看该服务器上的服务器终结点的运行状况  。

### <a name="event-logs"></a>事件日志

使用服务器上的遥测事件日志来监视已注册的服务器、同步和云分层运行状况。 遥测事件日志位于事件查看器中的“Applications and Services\Microsoft\FileSync\Agent”下。

同步运行状况

- 同步会话完成后，将记录事件 ID 9102。 使用此事件来确定同步会话是否已成功 (HResult = 0)，以及是否存在每项同步错误 (PerItemErrorCount) 。 有关详细信息，请参阅[同步运行状况](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync)和[每项错误](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)文档。

  > [!Note]  
  > 有时同步会话会整体失败或具有非零值 PerItemErrorCount。 但是，它们仍将继续进行，并且某些文件会同步成功。 可以在应用的字段（如 AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）中查看此项。 这些字段将显示会话成功的次数。 如果在一行中发现多个同步会话失败，并且它们的应用的计数已增加，请在创建支持工单之前，提供同步时间以重试。

- 同步会话完成后，每项的错误都会记录在事件 ID 9121 中。 使用此事件以确定由于此错误（PersistentCount 和 TransientCount）而未能同步的文件数 。 应调查永久性每项错误，请参阅[如何确定是否有特定的文件或文件夹未同步？](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

- 如果存在活动同步会话，则每 5 到 10 分钟记录一次事件 ID 9302。 使用此事件确定要同步多少项 (TotalItemCount)，到目前为止已同步的项数 (AppliedItemCount) 和由于每项错误而未能同步的项数 (PerItemErrorCount)  。 如果没有进行同步 (AppliedItemCount=0)，同步会话最终将失败并记录包含错误的事件 ID 9102。 有关详细信息，请参阅[同步进度文档](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已注册的服务器运行状况

- 当服务器查询作业服务时，每 30 秒记录一次事件 ID 9301。 如果 GetNextJob 已完成且状态为 0，服务器就可以与服务进行通信。 如果 GetNextJob 已完成但出现错误，请查看[故障排除文档](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity)以获得指导。

云分层运行状况

- 若要监视服务器上的分层活动，请使用遥测事件日志（位于“事件查看器”中的“Applications and Services\Microsoft\FileSync\Agent”下）中的事件 ID 9003、9016 和 9029。

  - 事件 ID 9003 提供服务器终结点的错误分布情况。 例如：错误总数和错误代码。 将为每个错误代码记录一个事件。
  - 事件 ID 9016 提供卷的副本创建结果。 例如：可用空间百分比、会话中创建的文件副本数和无法创建副本的文件数。
  - 事件 ID 9029 提供服务器终结点的副本创建会话信息。 例如：会话中尝试的文件数、会话分层的文件数和已分层的文件数。

- 若要监视服务器上的召回活动，请使用遥测事件日志（位于“事件查看器”中的“Applications and Services\Microsoft\FileSync\Agent”下）中的事件 ID 9005、9006、9009、9059 和 9071。

  - 事件 ID 9005 提供服务器终结点的重新调用可靠性。 例如：访问的唯一文件总数和访问失败的唯一文件总数。
  - 事件 ID 9006 提供服务器终结点的重新调用错误分布情况。 例如：失败的请求总数和错误代码。 将为每个错误代码记录一个事件。
  - 事件 ID 9009 提供服务器终结点的回调会话信息。 例如：DurationSeconds、CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件 ID 9059 提供服务器终结点的应用程序回调分布情况。 例如：ShareId、应用程序名称和 TotalEgressNetworkBytes。
  - 事件 ID 9071 为服务器终结点提供云分层效率。 例如：TotalDistinctFileCountCacheHit、TotalDistinctFileCountCacheMiss、TotalCacheHitBytes 和 TotalCacheMissBytes。

### <a name="performance-counters"></a>性能计数器

使用服务器上的 Azure 文件同步性能计数器来监视同步活动。

若要在服务器上查看 Azure 文件同步性能计数器，请打开性能监视器 (Perfmon.exe)。 可以在“已传输的 AFS 字节数”和“AFS 同步操作”对象下找到计数器 。

性能监视器提供了下列 Azure 文件同步性能计数器：

| 性能对象\计数器名称 | 说明 |
|-|-|
| AFS 传输的字节数\下载的字节数/秒 | 每秒下载的字节数。 |
| AFS 传输的字节数\上传的字节数/秒 | 每秒上传的字节数。 |
| AFS 传输的字节数\总字节数/秒 | 每秒（上传和下载）的总字节数。 |
| AFS 同步操作数\下载的同步文件数/秒 | 每秒下载的文件数。 |
| AFS 同步操作数\上传的同步文件数/秒 | 每秒上传的文件数。 |
| AFS 同步操作数\同步文件总操作数/秒 | 同步的文件总数（上传和下载）。 |

## <a name="alert-examples"></a>警报示例
本部分提供了有关 Azure 文件同步的一些示例警报。

  > [!Note]  
  > 如果创建了警报，但警报导致过多的干扰，请调整阈值和警报逻辑。

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>如果服务器终结点运行状况在门户中显示错误应如何创建警报

1. 在“Azure 门户”中，导航到相应的“存储同步服务” 。 
2. 转到“监视”部分并单击“警报” 。 
3. 单击“+ 新建警报规则”，创建新的警报规则。 
4. 通过单击“选择条件”配置条件。
5. 在“配置信号逻辑”边栏选项卡中，单击信号名称下的“同步会话结果” 。  
6. 选择以下维度配置： 
     - 维度名称：服务器终结点名称  
     - 运算符：= 
     - 维度值：所有当前值和未来值  
7. 导航到“警报逻辑”，然后完成以下操作： 
     - 阈值设置为“静态” 
     - 运算符：小于 
     - 聚合类型：Maximum  
     - 阈值：1 
     - 计算依据：聚合粒度 = 24 小时 | 计算频率 = 每小时  
     - 单击“完成”。 
8. 单击“选择操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
9. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
10. 单击“创建警报规则”。 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>如果文件未能同步到服务器或云终结点应如何创建警报

1. 在“Azure 门户”中，导航到相应的“存储同步服务” 。 
2. 转到“监视”部分并单击“警报” 。 
3. 单击“+ 新建警报规则”，创建新的警报规则。 
4. 通过单击“选择条件”配置条件。
5. 在“配置信号逻辑”边栏选项卡中，单击信号名称下的“未同步的文件” 。  
6. 选择以下维度配置： 
     - 维度名称：服务器终结点名称  
     - 运算符：= 
     - 维度值：所有当前值和未来值  
7. 导航到“警报逻辑”，然后完成以下操作： 
     - 阈值设置为“静态” 
     - 运算符：大于 
     - 聚合类型：Average  
     - 阈值：100 
     - 计算依据：聚合粒度 = 5 分钟 | 计算频率 = 每 5 分钟  
     - 单击“完成”。 
8. 单击“选择操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
9. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
10. 单击“创建警报规则”。 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>如果已注册的服务器未能与存储同步服务进行通信应如何创建警报

1. 在“Azure 门户”中，导航到相应的“存储同步服务” 。 
2. 转到“监视”部分并单击“警报” 。 
3. 单击“+ 新建警报规则”，创建新的警报规则。 
4. 通过单击“选择条件”配置条件。
5. 在“配置信号逻辑”边栏选项卡中，单击信号名称下的“服务器联机状态” 。  
6. 选择以下维度配置： 
     - 维度名称：服务器名称  
     - 运算符：= 
     - 维度值：所有当前值和未来值  
7. 导航到“警报逻辑”，然后完成以下操作： 
     - 阈值设置为“静态” 
     - 运算符：小于 
     - 聚合类型：Maximum  
     - 阈值(字节)：1 
     - 计算依据：聚合粒度 = 1 小时 | 计算频率 = 每 30 分钟  
         - 请注意，指标每 15 到 20 分钟发送到 Azure Monitor 一次。 请勿将“计算频率”设置为小于 30 分钟（将生成错误警报）。
     - 单击“完成”。 
8. 单击“选择操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
9. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
10. 单击“创建警报规则”。 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>如果云分层召回大小在一天内超过 500GiB 应如何创建警报

1. 在“Azure 门户”中，导航到相应的“存储同步服务” 。 
2. 转到“监视”部分并单击“警报” 。 
3. 单击“+ 新建警报规则”，创建新的警报规则。 
4. 通过单击“选择条件”配置条件。
5. 在“配置信号逻辑”边栏选项卡中，单击信号名称下的“云分层召回大小” 。  
6. 选择以下维度配置： 
     - 维度名称：服务器名称  
     - 运算符：= 
     - 维度值：所有当前值和未来值  
7. 导航到“警报逻辑”，然后完成以下操作： 
     - 阈值设置为“静态” 
     - 运算符：大于 
     - 聚合类型：Total  
     - 阈值(字节)：67108864000 
     - 计算依据：聚合粒度 = 24 小时 | 计算频率 = 每小时  
     - 单击“完成”。 
8. 单击“选择操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
9. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
10. 单击“创建警报规则”。 

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](file-sync-planning.md)
- [考虑防火墙和代理设置](file-sync-firewall-and-proxy.md)
- [部署 Azure 文件同步](file-sync-deployment-guide.md)
- [Azure 文件同步排除故障](file-sync-troubleshoot.md)
