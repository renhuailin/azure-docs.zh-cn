---
title: Azure 文件共享性能故障排除指南
description: 排查 Azure 文件共享的已知性能问题。 遇到这些问题时，找出潜在的原因和相关解决方法。
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 90942e4deebdc65fe26ce94f04a15fe2b8c0684c
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512063"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>排查 Azure 文件共享性能问题

本文列出了与 Azure 文件共享相关的一些常见问题。 当遇到这些问题时，它将提供可能的原因和解决方法。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延迟、低吞吐量和一般性能问题

### <a name="cause-1-share-was-throttled"></a>原因 1：共享受限

当达到文件共享的每秒 i/o 操作数 (IOPS) 、入口或出口限制时，将会限制请求。 若要了解标准文件共享和高级文件共享的限制，请参阅[文件共享和文件缩放目标](./storage-files-scale-targets.md#file-share-and-file-scale-targets)。

若要确认共享是否受到限制，可以访问和使用门户中的 Azure 指标。

1. 在 Azure 门户中转到自己的存储帐户。

1. 在左窗格中的 " **监视**" 下，选择 " **指标**"。

1. 选择 " **文件** " 作为存储帐户范围的 "指标命名空间"。

1. 选择“事务”作为指标。

1. 添加 **响应类型** 的筛选器，然后查看是否有任何请求具有以下响应代码之一：
   * **SuccessWithThrottling**：对于服务器消息块 (SMB) 
   * **ClientThrottlingError**：适用于 REST

   ![高级文件共享的指标选项的屏幕截图，其中显示了 "响应类型" 属性筛选器。](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > 若要接收警报，请参阅本文后面的 ["如何在文件共享受到限制时创建警报"](#how-to-create-an-alert-if-a-file-share-is-throttled) 一节。

### <a name="solution"></a>解决方案

- 如果使用的是标准文件共享，请在存储帐户上启用 [大型文件](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) 共享。 大型文件共享支持每个共享最多 10,000 IOPS。
- 如果使用的是高级文件共享，请增加预配的文件共享大小，以增加 IOPS 限制。 若要了解详细信息，请参阅 [Azure 文件规划指南](./storage-files-planning.md#understanding-provisioning-for-premium-file-shares)中的 "了解高级文件共享的预配" 部分。

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>原因2：元数据或命名空间繁重的工作负荷

如果大多数请求都是以元数据为中心的 (例如 createfile、openfile、对应 closefile、queryinfo 或 querydirectory) ，则延迟将比读取/写入操作更糟。

若要确定你的大多数请求是否以元数据为中心，请先按照原因1中前面所述的步骤1-4 进行操作。 对于步骤5，请为 **API 名称** 添加属性筛选器，而不是添加 **响应类型** 的筛选器。

![高级文件共享的指标选项的屏幕截图，显示 "API 名称" 属性筛选器。](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>解决方法

- 检查以确定是否可以修改应用程序以减少元数据操作的数量。
- 在文件共享上添加虚拟硬盘 (VHD) ，并从客户端装载 VHD，以便对数据执行文件操作。 此方法适用于单个编写器和多个读取器方案，并允许元数据操作为本地。 安装程序的性能与本地直接连接的存储的性能类似。

### <a name="cause-3-single-threaded-application"></a>原因 3：单线程应用程序

如果你使用的应用程序是单线程的，则此设置可能会使 IOPS 吞吐量明显低于最大可能吞吐量，具体取决于你的预配共享大小。

### <a name="solution"></a>解决方案

- 通过增加线程数来提高应用程序的并行度。
- 切换到支持并行度的应用程序。 例如，对于复制操作，可以使用 Windows 客户端中的 AzCopy 或 RoboCopy，或从 Linux 客户端使用 **parallel** 命令。

## <a name="very-high-latency-for-requests"></a>请求的延迟很高

### <a name="cause"></a>原因

 (VM) 的客户端虚拟机与文件共享位于不同的区域。 高延迟的其他原因可能是由于客户端或网络造成的延迟。

### <a name="solution"></a>解决方案

- 从与文件共享位于同一区域的 VM 运行该应用程序。
- 对于存储帐户，请通过 Azure 门户中的 **Azure Monitor** 查看事务度量值 **SuccessE2ELatency** 和 **SuccessServerLatency** 。 "SuccessE2ELatency" 和 "SuccessServerLatency" 度量值的高差异是指可能由网络或客户端引起的延迟。 请参阅 Azure 文件中的 [事务度量值](storage-files-monitoring-reference.md#transaction-metrics) 监视数据参考。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>客户端无法实现网络支持的最大吞吐量

### <a name="cause"></a>原因
一个可能的原因是缺少标准文件共享的 SMB 多通道支持。 目前，Azure 文件仅支持单个通道，因此从客户端 VM 到服务器的连接只有一个。 此单一连接限定为客户端 VM 上的单一核心，因此，可从 VM 实现的最大吞吐量受限于单个核心。

### <a name="workaround"></a>解决方法

- 对于高级文件共享，请 [在 FileStorage 帐户上启用 SMB 多通道](storage-files-enable-smb-multichannel.md)。
- 获取更大核心的 VM 可能会有助于提高吞吐量。
- 从多个 VM 运行客户端应用程序会提高吞吐量。
- 尽可能地使用 REST API。

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Linux 客户端的吞吐量明显低于 Windows 客户端的吞吐量

### <a name="cause"></a>原因

这是在 Linux 上实现 SMB 客户端的已知问题。

### <a name="workaround"></a>解决方法

- 跨多个 VM 分散负载。
- 在同一 VM 上，使用具有 **nosharesock** 选项的多个装入点，并将负载分散到这些装入点。
- 在 Linux 上，尝试装载 **nostrictsync** 选项，以避免在每个 **fsync** 调用上强制执行 SMB 刷新。 对于 Azure 文件，此选项不会影响数据一致性，但可能会导致目录列表中的文件元数据陈旧 (**ls-l** 命令) 。 使用 **stat** 命令直接查询文件元数据将返回最新的文件元数据。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>涉及大量打开/关闭操作的元数据工作负荷的高延迟

### <a name="cause"></a>原因

缺少目录租约支持。

### <a name="workaround"></a>解决方法

- 如果可能，请避免在较短的时间内对同一目录使用过多的打开/关闭句柄。
- 对于 Linux VM，请指定“actimeo=\<sec>”作为装载选项，以增大目录条目缓存超时。 默认情况下，超时值为1秒，因此较大的值（例如3或5秒）可能会有所帮助。
- 对于 CentOS Linux 或 Red Hat Enterprise Linux (RHEL) Vm，请将系统升级到 CentOS Linux 8.2 或 RHEL 8.2。 对于其他 Linux Vm，请将内核升级到5.0 或更高版本。

## <a name="low-iops-on-centos-linux-or-rhel"></a>CentOS Linux 或 RHEL 上的低 IOPS

### <a name="cause"></a>原因

在 CentOS Linux 或 RHEL 上，不支持大于1的 i/o 深度。

### <a name="workaround"></a>解决方法

- 升级到 CentOS Linux 8 或 RHEL 8。
- 改用 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>在 Linux 中将文件复制到 Azure 文件共享和从其复制慢速文件

如果遇到文件复制缓慢的问题，请参阅 [linux 故障排除指南](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)中的 "将文件复制到 linux 中的文件共享速度缓慢" 部分。

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>IOPS 或呈现锯齿模式

### <a name="cause"></a>原因

客户端应用程序始终超过基线 IOPS。 目前，请求负载没有服务端平滑处理。 如果客户端超过基线 IOPS，则会受到服务的限制。 此限制可能会导致客户端遇到抖动或呈现锯齿的 IOPS 模式。 在这种情况下，客户端实现的平均 IOPS 可能低于基线 IOPS。

### <a name="workaround"></a>解决方法
- 减少客户端应用程序的请求负载，以便不会限制共享。
- 增加共享配额，以便不会限制共享。

## <a name="excessive-directoryopendirectoryclose-calls"></a>过多的 DirectoryOpen/DirectoryClose 调用

### <a name="cause"></a>原因

如果 **DirectoryOpen/DirectoryClose** 调用数是排名靠前的 API 调用，并且您不希望客户端发出多次调用，则问题可能是由 Azure 客户端 VM 上安装的防病毒软件引起的。

### <a name="workaround"></a>解决方法

- [Windows 四月平台更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)中提供了此问题的修复措施。

## <a name="file-creation-is-slower-than-expected"></a>文件创建速度慢于预期

### <a name="cause"></a>原因

依赖于创建大量文件的工作负荷不会在高级文件共享和标准文件共享之间出现明显的性能差异。

### <a name="workaround"></a>解决方法

- 无。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>在 Windows 8.1 或 Server 2012 R2 中的性能不佳

### <a name="cause"></a>原因

对于 i/o 密集型工作负荷的 Azure 文件共享，延迟时间比预期的要高。

### <a name="workaround"></a>解决方法

- 安装可用的[修补程序](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>SMB 多通道选项在 "文件共享设置" 下不可见。 

### <a name="cause"></a>原因

没有为该功能注册订阅，或者不支持区域和帐户类型。

### <a name="solution"></a>解决方案

确保已为 SMB 多通道功能注册订阅。 请 [参阅 "](storage-files-enable-smb-multichannel.md#getting-started) 帐户概述" 页中的 "入门" 确保帐户类型为 "FileStorage (premium file account) 。 

## <a name="smb-multichannel-is-not-being-triggered"></a>不会触发 SMB 多通道。

### <a name="cause"></a>原因

最近对 SMB 多通道配置设置的更改，无需重新装入。

### <a name="solution"></a>解决方案
 
-   对 Windows SMB 客户端或帐户 SMB 多通道配置设置进行任何更改后，你必须卸载该共享，等待60秒，并重新装载该共享以触发多通道。
-   对于 Windows 客户端操作系统，生成高队列深度的 IO 负载假设 QD = 8，例如，复制文件以触发 SMB 多通道。  对于服务器操作系统，SMB 多通道是通过 QD = 1 触发的，这意味着，一旦向共享启动任何 IO，就会出现这种情况。

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>文件共享上托管的网站上的高延迟 

### <a name="cause"></a>原因  

文件共享上的大文件更改通知可能会导致严重的高延迟。 这通常发生在具有深层嵌套目录结构的文件共享上托管的网站上。 典型方案是 IIS 托管的 web 应用程序，其中，为默认配置中的每个目录设置文件更改通知。 对 SMB 客户端注册的共享上 ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) 的每个更改都会将更改通知从文件服务推送到客户端，这会占用系统资源，并发出更为恶化的更改次数。 这可能会导致共享限制，进而导致更高的客户端延迟。 

若要确认，可以在门户中使用 Azure 指标- 

1. 在 Azure 门户中转到自己的存储帐户。 
1. 在左侧菜单中的 "监视" 下，选择 "指标"。 
1. 选择 "文件" 作为存储帐户范围的 "指标命名空间"。 
1. 选择 "事务" 作为指标。 
1. 为 ResponseType 添加筛选器并检查是否有任何请求的 SuccessWithThrottling (的响应代码为 SMB) 或 ClientThrottlingError (为 REST) 。

### <a name="solution"></a>解决方案 

- 如果未使用文件更改通知，请禁用 (首选) 的文件更改通知。
    - 通过更新 FCNMode[禁用文件更改通知](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto)。 
    - 通过在注册表中设置来将 IIS 工作进程 (W3WP.EXE) 轮询间隔更新为 0 `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` ，并重新启动 w3wp.exe 进程。 若要了解有关此设置的详细信息，请参阅 [IIS 的许多部分使用的常见注册表项](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp)。
- 增加文件更改通知轮询间隔的频率以减少卷。
    - 根据要求，将 W3WP.EXE 工作进程轮询间隔更新 (例如10分钟入门或 30mins) 。 `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds `[在注册表中](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp)设置并重新启动 w3wp.exe 进程。
- 如果网站的映射物理目录具有嵌套的目录结构，可以尝试限制文件更改通知的范围，以减少通知量。 默认情况下，IIS 使用虚拟目录映射到的物理目录中 Web.config 文件中的配置，以及该物理目录中的任何子目录。 如果你不希望在子目录中使用 Web.config 文件，请为虚拟目录上的了 allowsubdirconfig 属性指定 false。 [此处](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)提供了更多详细信息。 
    - 将 Web.Config 中的 IIS 虚拟目录 "了 allowsubdirconfig" 设置设置为 " *false* "，以从作用域中排除映射的物理子目录。  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如何创建文件共享受到限制时的警报

1. 在 Azure 门户中转到自己的存储帐户。
1. 在 " **监视** " 部分中，选择 " **警报**"，然后选择 " **新建警报规则**"。
1. 选择 " **编辑资源**"，选择存储帐户的 **文件资源类型** ，然后选择 " **完成**"。 例如，如果存储帐户名称为 " *contoso*"，则选择 "contoso/文件" 资源。
1. 选择 " **选择条件** " 添加条件。
1. 在存储帐户支持的信号列表中，选择 " **事务** " 指标。
1. 在 " **配置信号逻辑** " 窗格的 " **维度名称** " 下拉列表中，选择 " **响应类型**"。
1. 在 " **维度值** " 下拉列表中，选择 "适用于 SMB) 的 **SuccessWithThrottling** (" 或 " **ClientThrottlingError** (为 REST) "。

   > [!NOTE]
   > 如果 " **SuccessWithThrottling** " 和 " **ClientThrottlingError** " 维度值都未列出，则表示资源尚未被限制。 若要添加维度值，请在 " **维度值** " 下拉列表旁边，选择 " **添加自定义值**"，输入 **SuccessWithThrottling** 或 **ClientThrottlingError**，选择 **"确定**"，然后重复步骤7。

1. 在 " **维度名称** " 下拉列表中，选择 " **文件共享**"。
1. 在 " **维度值** " 下拉列表中，选择要对其发出警报的文件共享或共享。

   > [!NOTE]
   > 如果文件共享是标准文件共享，请选择“所有当前值和将来值”。 "维度值" 下拉列表不列出文件共享，因为 "每个共享" 指标不适用于标准文件共享。 如果存储帐户中的任何文件共享受到限制，并且警报未确定限制了哪个文件共享，则会触发标准文件共享的限制警报。 由于每个共享指标不适用于标准文件共享，因此建议对每个存储帐户使用一个文件共享。

1. 通过输入 **阈值**、 **运算符**、 **聚合粒度** 和 **计算频率** 来定义警报参数，然后选择 " **完成**"。

    > [!TIP]
    > 如果使用的是静态阈值，则在当前限制文件共享的情况下，度量值图表可帮助您确定合理的阈值。 如果使用的是动态阈值，则指标图表将根据最近的数据显示计算出的阈值。

1. 选择 " **选择操作组**"，然后将 "操作组" (例如，通过选择现有操作组或通过创建新的操作组，将) 发送到警报。
1. 输入警报详细信息，如 **警报规则名称**、 **描述** 和 **严重性**。
1. 选择“创建警报规则”可以创建警报  。

若要详细了解如何在 Azure Monitor 中配置警报，请参阅 [Microsoft Azure 中的警报概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>如果高级文件共享的趋势达到限制，如何创建警报

1. 在 Azure 门户中转到自己的存储帐户。
1. 在 " **监视** " 部分中，选择 " **警报**"，然后选择 " **新建警报规则**"。
1. 选择 " **编辑资源**"，选择存储帐户的 **文件资源类型** ，然后选择 " **完成**"。 例如，如果存储帐户名称为 " *contoso*"，则选择 "contoso/文件" 资源。
1. 选择 " **选择条件** " 添加条件。
1. 在存储帐户支持的信号列表中，选择 **出口** 指标。

   > [!NOTE]
   > 必须创建三个单独的警报，以便在入口、出口或事务值超过设置的阈值时发出警报。 这是因为仅当满足所有条件时才会触发警报。 例如，如果你将所有条件都放入一个警报，则仅当入口、出口和交易超出其阈值时，才会发出警报。

1. 向下滚动。 在 " **维度名称** " 下拉列表中，选择 " **文件共享**"。
1. 在 " **维度值** " 下拉列表中，选择要对其发出警报的文件共享或共享。
1. 通过在 " **运算符**、 **阈值**、 **聚合粒度** 和 **计算频率** " 下拉列表中选择值来定义警报参数，然后选择 " **完成**"。

   出口、入口和交易指标每分钟表示一次，不过，每秒预配的是出口、入口和 i/o。 因此，例如，如果预配的出口为 90 &nbsp; mebibytes/秒 (MiB/s) 并且您希望阈值为 &nbsp; 预配出口的80%，请选择以下警报参数： 
   - 对于 **阈值**： *75497472* 
   - 对于 **运算符**： *大于或等于*
   - 用于 **聚合类型**： *average*
   
   你还可以选择 **聚合粒度** 和 **计算频率的** 值，具体取决于你希望你的警报的干扰。 例如，如果你希望你的警报查看在1小时时间段内的平均入口，并且你希望每隔一小时运行一次警报规则，请选择以下内容：
   - 用于 **聚合粒度**： *1 小时*
   - 对于 **评估频率**： *1 小时*

1. 选择 " **选择操作组**"，然后将操作组 (例如，通过选择现有操作组或通过创建新操作组，将) 发送到警报。
1. 输入警报详细信息，如 **警报规则名称**、 **描述** 和 **严重性**。
1. 选择“创建警报规则”可以创建警报  。

    > [!NOTE]
    > - 若要通知你的高级文件共享 *由于预配入口* 而接近阻止，请按照前面的说明进行操作，但需要进行以下更改：
    >    - 在步骤5中，选择 " **入口** " 指标而不是 " **出口**"。
    >
    > - 若要通知你的高级文件共享 *由于预配的 IOPS* 而接近限制，请按照前面的说明进行操作，但需要进行以下更改：
    >    - 在步骤5中，选择 " **事务** " 指标而不是 " **出口**"。
    >    - 在步骤10中， **聚合类型** 的唯一选项为 *Total*。 因此，阈值取决于所选的聚合粒度。 例如，如果希望将阈值设置为 80 &nbsp; % 的预配基线 IOPS，并选择 *1 小时* 的 **聚合粒度**，则 **阈值** 为基线 IOPS (以字节为单位) &times; &nbsp; 0.8 &times; &nbsp; 3600。 

若要详细了解如何在 Azure Monitor 中配置警报，请参阅 [Microsoft Azure 中的警报概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="see-also"></a>另请参阅
- [在 Windows 中排查 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)  
- [在 Linux 中排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)  
- [Azure 文件常见问题解答](storage-files-faq.md)
