---
title: Azure 文件共享性能故障排除指南
description: 排查 Azure 文件共享的已知性能问题。 遇到这些问题时，找出潜在的原因和相关解决方法。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/06/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 65b703a4f193e6b1197c3c8f2cb03ffbc349471b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741857"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>排查 Azure 文件共享性能问题

本文列出了与 Azure 文件共享相关的一些常见问题。 其中提供了这些问题的潜在原因和解决方法。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延迟、低吞吐量和一般性能问题

### <a name="cause-1-share-was-throttled"></a>原因 1：共享受限

当达到文件共享的每秒 I/O 操作数 (IOPS)、流入量或流出量限制时，将会限制请求。 若要了解标准文件共享和高级文件共享的限制，请参阅[文件共享和文件缩放目标](./storage-files-scale-targets.md#azure-file-share-scale-targets)。

若要确认共享是否受到限制，可以访问并使用门户中的 Azure 指标。

1. 在 Azure 门户中转到自己的存储帐户。

1. 在左侧窗格中的“监视”下，选择“指标” 。

1. 选择“文件”作为存储帐户范围的指标命名空间。

1. 选择“事务”作为指标。

1. 添加一个“响应类型”筛选器，然后检查是否有任何请求被限制。 

    对于标准文件共享，如果某个请求被限制，则会记录以下响应类型：

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    对于高级文件共享，如果某个请求被限制，则会记录以下响应类型：

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

    若要详细了解每个响应类型，请参阅[指标维度](./storage-files-monitoring-reference.md#metrics-dimensions)。

    ![高级文件共享的指标选项的屏幕截图，其中显示了“响应类型”属性筛选器。](media/storage-troubleshooting-premium-fileshares/metrics.png)

    > [!NOTE]
    > 若要接收警报，请参阅本文后面的[“如何创建文件共享受到限制时的警报”](#how-to-create-an-alert-if-a-file-share-is-throttled)部分。

#### <a name="solution"></a>解决方案

- 如果使用的是标准文件共享，请在存储帐户上[启用大型文件共享](storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)，并[增大文件共享配额的大小以利用大型文件共享支持](storage-how-to-create-file-share.md#expand-existing-file-shares)。 大型文件共享支持很大的 IOPS 和带宽限制；有关详细信息，请参阅 [Azure 文件存储的可伸缩性和性能目标](storage-files-scale-targets.md)。
- 如果使用的是高级文件共享，请增加预配的文件共享大小，以便提高 IOPS 限制。 若要了解详细信息，请参阅[了解高级文件共享的预配](./understanding-billing.md#provisioned-model)。

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>原因 2：元数据或命名空间工作负载繁重

如果大多数请求是以元数据为中心（如 `createfile`、`openfile`、`closefile`、`queryinfo` 或 `querydirectory`），那么延迟情况将会比读取/写入操作更加严重。

若要确定你的大多数请求是否以元数据为中心，请先按照先前“原因 1”中概述的步骤 1-4 进行操作。 对于步骤 5，请不要添加“响应类型”筛选器，而是添加“API 名称”属性筛选器 。

![高级文件共享的指标选项的屏幕截图，其中显示了“API 名称”属性筛选器。](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

#### <a name="workaround"></a>解决方法

- 检查是否可以修改应用程序以减少元数据操作的数量。
- 在文件共享上添加虚拟硬盘 (VHD)，并从客户端通过 SMB 装载 VHD，以便对数据执行文件操作。 此方法适用于单个编写器/读取器方案或包含多个读取器且无编写器的方案。 由于文件系统由客户端而不是 Azure 文件存储拥有，因此元数据操作可在本地执行。 安装程序提供的性能与本地直连的存储的性能类似。

### <a name="cause-3-single-threaded-application"></a>原因 3：单线程应用程序

如果使用的应用程序是单线程的，则此安装程序可能会导致 IOPS 吞吐量明显低于最大可能的吞吐量，具体取决于预配的共享大小。

#### <a name="solution"></a>解决方案

- 通过增加线程数来提高应用程序的并行度。
- 切换到支持并行度的应用程序。 例如，对于复制操作，可以在 Windows 客户端中使用 AzCopy 或 RoboCopy，或者在 Linux 客户端中使用 parallel 命令。

### <a name="cause-4-number-of-smb-channels-exceeds-four"></a>原因 4：SMB 通道数超过 4 个

如果使用的是 SMB 多通道并且通道数超过 4 个，则会导致性能不佳。 若要确定连接计数是否超过 4 个，请使用 PowerShell cmdlet `get-SmbClientConfiguration` 查看当前的连接计数设置。

#### <a name="solution"></a>解决方案

为 SMB 设置单个 NIC 的 Windows 设置，使总通道数不超过 4 个。 例如，如果你有 2 个 NIC，则可以使用以下 PowerShell cmdlet 将单个 NIC 的最大值设置为 2：`Set-SmbClientConfiguration -ConnectionCountPerRssNetworkInterface 2`。

## <a name="very-high-latency-for-requests"></a>请求的延迟很高

### <a name="cause"></a>原因

客户端虚拟机 (VM) 所在的区域可能与文件共享所在的区域不同。 高延迟的其他原因可能是由于客户端或网络造成的延迟。

### <a name="solution"></a>解决方案

- 从与文件共享位于同一区域的 VM 运行应用程序。
- 对于存储帐户，通过 Azure 门户中的 Azure Monitor 查看事务指标 SuccessE2ELatency 和 SuccessServerLatency。 SuccessE2ELatency 和 SuccessServerLatency 指标值之间的较大差异表示可能由网络或客户端引起的延迟。 请参阅 Azure 文件存储监视数据参考中的[事务指标](storage-files-monitoring-reference.md#transaction-metrics)。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>客户端无法实现网络支持的最大吞吐量

### <a name="cause"></a>原因
一个可能原因是缺少用于标准文件共享的 SMB 多通道支持。 目前，Azure 文件存储仅支持单个通道，因此从客户端 VM 到服务器只有一个连接。 此单一连接限定为客户端 VM 上的单一核心，因此，可从 VM 实现的最大吞吐量受限于单个核心。

### <a name="workaround"></a>解决方法

- 对于高级文件共享，请[在 FileStorage 帐户上启用 SMB 多通道](storage-files-enable-smb-multichannel.md)。
- 获取核心更大的 VM 可能有助于提高吞吐量。
- 从多个 VM 运行客户端应用程序会提高吞吐量。
- 尽可能地使用 REST API。

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Linux 客户端上的吞吐量明显低于 Windows 客户端上的吞吐量

### <a name="cause"></a>原因

这是在 Linux 上实施 SMB 客户端的一个已知问题。

### <a name="workaround"></a>解决方法

- 跨多个 VM 分散负载。
- 在同一 VM 上，通过 `nosharesock` 选项使用多个装入点，并将负载分散到这些装入点上。
- 在 Linux 上，尝试使用 `nostrictsync` 选项进行装载，以避免每次调用 `fsync` 时都强制执行 SMB 刷新。 对于 Azure 文件存储，此选项不会影响数据一致性，但可能会导致目录列表（`ls -l` 命令）中出现过时的文件元数据。 使用 `stat` 命令直接查询文件元数据将会返回最新的文件元数据。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>涉及大量打开/关闭操作的元数据密集型工作负载的延迟较高

### <a name="cause"></a>原因

缺少目录租约支持。

### <a name="workaround"></a>解决方法

- 如果可能，请避免短时间内在同一目录中使用过多的打开/关闭句柄。
- 对于 Linux VM，请指定“`actimeo=<sec>`”作为装载选项，以延长目录条目缓存超时。 默认情况下，超时值为 1 秒，因此较大的值（例如 3 或 5 秒）可能会有所帮助。
- 对于 CentOS Linux 或 Red Hat Enterprise Linux (RHEL) VM，请将系统升级到 CentOS Linux 8.2 或 RHEL 8.2。 对于其他 Linux VM，请将内核升级到 5.0 或更高版本。

## <a name="low-iops-on-centos-linux-or-rhel"></a>CentOS Linux 或 RHEL 上的 IOPS 较低

### <a name="cause"></a>原因

CentOS Linux 或 RHEL 不支持大于 1 的 I/O 深度。

### <a name="workaround"></a>解决方法

- 升级到 CentOS Linux 8 或 RHEL 8。
- 改用 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>在 Linux 中将文件复制到 Azure 文件共享以及从中复制文件时速度缓慢

如果复制文件时速度缓慢，请查看 [Linux 故障排除指南](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)中的“在 Linux 中向/从 Azure 文件共享复制文件时速度缓慢”部分。

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>IOPS 出现抖动或锯齿模式

### <a name="cause"></a>原因

客户端应用程序始终超过基线 IOPS。 当前，尚无请求负载的服务端平滑处理。 如果客户端超过基线 IOPS，它将受到服务的限制。 该限制可能导致客户端出现抖动或锯齿 IOPS 模式。 在这种情况下，客户端实现的平均 IOPS 可能低于基线 IOPS。

### <a name="workaround"></a>解决方法
- 减少客户端应用程序的请求负载，以使共享不会受到限制。
- 提高共享配额，以使共享不会受到限制。

## <a name="excessive-directoryopendirectoryclose-calls"></a>过多的 DirectoryOpen/DirectoryClose 调用

### <a name="cause"></a>原因

如果最频繁的 API 调用中包括 DirectoryOpen/DirectoryClose 调用，而你预计客户端不会发出这么多的调用，则问题可能是 Azure 客户端 VM 上安装的防病毒软件引起的。

### <a name="workaround"></a>解决方法

- [Windows 四月平台更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)中提供了此问题的修复措施。

## <a name="file-creation-is-slower-than-expected"></a>文件创建速度慢于预期

### <a name="cause"></a>原因

依赖于创建大量文件的工作负载不会在高级文件共享和标准文件共享之间出现明显的性能差异。

### <a name="workaround"></a>解决方法

- 无。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>在 Windows 8.1 或 Server 2012 R2 中的性能不佳

### <a name="cause"></a>原因

对于 I/O 密集型工作负载，访问 Azure 文件共享时的延迟要高于预期。

### <a name="workaround"></a>解决方法

- 安装可用的[修补程序](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>SMB 多通道选项在“文件共享”设置下不可见。 

### <a name="cause"></a>原因

没有为该功能注册订阅，或者不支持该区域和帐户类型。

### <a name="solution"></a>解决方案

确保已为 SMB 多通道功能注册订阅。 请参阅[入门](storage-files-enable-smb-multichannel.md#getting-started)，确保帐户概述页中的帐户类型为 FileStorage（高级文件帐户）。 

## <a name="smb-multichannel-is-not-being-triggered"></a>SMB 多通道未触发。

### <a name="cause"></a>原因

最近对 SMB 多通道配置设置进行了更改，但没有重新装载。

### <a name="solution"></a>解决方案
 
-   对 Windows SMB 客户端或帐户 SMB 多通道配置设置进行任何更改后，必须卸载该共享，等待 60 秒，并重新装载该共享以触发多通道。
-   对于 Windows 客户端 OS，生成具有高队列深度（如 QD=8）的 IO 负载（例如复制文件）以触发 SMB 多通道。  对于服务器 OS，在 QD=1 时触发 SMB 多通道，这意味着一旦启动对共享的任何 IO，就会触发 SMB 多通道。

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>托管文件共享的网站上的高延迟 

### <a name="cause"></a>原因  

文件共享上的大量文件更改通知可能会导致严重的高延迟。 这通常发生在托管具有深层嵌套目录结构的文件共享的网站上。 一个典型场景是 IIS 托管的 Web 应用程序，其中在默认配置中为每个目录设置了文件更改通知。 已注册 SMB 客户端的共享上的每个更改 ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) 都会将更改通知从文件服务推送到客户端，这会占用系统资源，并且随着更改数量的增加问题会变得更加严重。 这会导致共享限制，从而导致更高的客户端延迟。 

若要进行确认，可以使用门户中的 Azure 指标 - 

1. 在 Azure 门户中转到自己的存储帐户。 
1. 在左侧菜单中的“监视”下，选择“指标”。 
1. 选择“文件”作为存储帐户范围的指标命名空间。 
1. 选择“事务”作为指标。 
1. 添加 ResponseType 的筛选器，并检查是否有任何请求的响应代码为 SuccessWithThrottling（适用于 SMB）或 ClientThrottlingError（适用于 REST）。

### <a name="solution"></a>解决方案 

- 如果未使用文件更改通知，请禁用文件更改通知（首选）。
    - 更新 FCNMode 以[禁用文件更改通知](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto)。 
    - 通过在注册表中设置 `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` 来将 IIS 工作进程 (W3WP) 轮询间隔更新为 0，然后重启 W3WP 进程。 若要了解有关此设置的详细信息，请参阅 [IIS 的许多部分使用的常见注册表项](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp)。
- 增加文件更改通知轮询间隔的频率以减少通知量。
    - 根据你的需求，将 W3WP 工作进程轮询间隔更新为更高的值（例如 10 分钟或 30 分钟）。 [在注册表中](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp)设置 `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds `，并重启 W3WP 进程。
- 如果网站的映射物理目录具有嵌套的目录结构，可以尝试限制文件更改通知的范围以减少通知量。 默认情况下，IIS 使用虚拟目录映射到的物理目录以及该物理目录下任何子目录中的 Web.config 文件中的配置。 如果你不希望使用子目录中的 Web.config 文件，请将虚拟目录上的 allowSubDirConfig 特性指定为 false。 [此处](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)提供了更多详细信息。 
    - 将 Web.Config 中的 IIS 虚拟目录“allowSubDirConfig”设置设置为 false，以从范围中排除映射的物理子目录。  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如何创建文件共享受到限制时的警报

1. 在 Azure 门户 中转到自己的存储帐户。
2. 在“监视”部分中单击“警报”，然后单击“+ 新建警报规则”。  
3. 单击“编辑资源”，为存储帐户选择“文件资源类型”，然后单击“完成”。 例如，如果存储帐户名称为 `contoso`，请选择 `contoso/file` 资源。
4. 单击“选择条件”以添加条件。
5. 你将看到存储帐户支持的信号列表，请选择“事务”指标。
6. 在“配置信号逻辑”边栏选项卡上，单击“维度名称”下拉列表，然后选择“响应类型”。
7. 单击“维度值”下拉列表，然后为你的文件共享选择适当的响应类型。

    对于标准文件共享，请选择以下响应类型：

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    对于高级文件共享，请选择以下响应类型：

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > 如果“维度值”下拉列表中未列出响应类型，这意味着资源未被限制。 若要添加维度值，请在“维度值”下拉列表旁边选择“添加自定义值”，输入响应类型类型（例如 **SuccessWithThrottling**），选择“确定”，然后重复上述步骤，为你的文件共享添加所有适用的响应类型。

8. 对于“高级文件共享”，请单击“维度名称”下拉列表，然后选择“文件共享”。 对于“标准文件共享”，请跳到“步骤 #10”。

   > [!NOTE]
   > 如果文件共享是标准文件共享，则“File Share”维度不会列出文件共享，因为每个共享指标对标准文件共享不可用。 如果存储帐户中的任何文件共享受到限制，则会触发标准文件共享的限制警报，并且警报不会识别哪个文件共享受到限制。 因为每共享指标不可用于标准文件共享，所以建议为每个存储帐户使用一个文件共享。

9. 单击“维度值”下拉列表，并选择要对其发出警报的文件共享。
10. 定义“警报参数”（阈值、运算符、聚合粒度和评估频率），然后单击“完成”。

    > [!TIP]
    > 如果你使用的是静态阈值，并且文件共享当前受到限制，则可通过指标图表来确定合理的阈值。 如果使用的是动态阈值，则指标图表将显示基于最新数据计算出的阈值。

11. 单击“添加操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
12. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
13. 单击“创建警报规则”以创建警报。

若要详细了解如何在 Azure Monitor 中配置警报，请参阅 [Microsoft Azure 中的警报概述](../../azure-monitor/alerts/alerts-overview.md)。

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>如果高级文件共享正在趋向于受限制，如何创建警报

1. 在 Azure 门户中转到自己的存储帐户。
2. 在“监视”部分中选择“警报”，然后选择“新建警报规则”  。
3. 选择“编辑资源”，为存储帐户选择“文件资源类型”，然后选择“完成”  。 例如，如果存储帐户名称为 contoso，则选择 contoso/文件资源。
4. 选择“选择条件”以添加条件。
5. 在存储帐户支持的信号列表中，选择“流出量”指标。

   > [!NOTE]
   > 必须创建三个单独的警报，以在流入量、流出量或事务值超过所设置的阈值时发出警报。 这是因为仅当满足所有条件时才会触发警报。 例如，如果将所有条件都放入一个警报，则仅当流入量、流出量和事务都超出其阈值量时才会发出警报。

6. 向下滚动。 在“维度名称”下拉列表中，选择“文件共享” 。
7. 在“维度值”下拉列表中，选择要对其发出警报的文件共享。
8. 通过选择“运算符”、“阈值”、“聚合粒度”和“评估频率”下拉列表中的值来定义警报参数，然后选择“完成”    。

   流出量、流入量和事务指标以每分钟表示，但预配的流出量、流入量和 I/O 以每秒表示。 因此，举例来说，如果预配的流出量为每秒 90&nbsp;兆字节 (MiB/s)，并且你希望阈值达到预配流出量的&nbsp;80%，请选择以下警报参数： 
   - 阈值：75497472 
   - 运算符：大于或等于
   - 聚合类型：平均
   
   根据所需的警报的干扰程度，你还可以选择“聚合粒度”和“评估频率”的值 。 例如，如果你希望警报查看在 1 小时内的平均流入量，并且希望警报规则每小时运行一次，请选择以下内容：
   - 聚合粒度：*1 小时*
   - 评估频率：*1 小时*

9. 选择“添加操作组”，然后通过选择现有操作组或创建新的操作组，将一个操作组（例如电子邮件或短信）添加到警报中。
10. 输入警报详细信息，例如“警报规则名称”、“说明”和“严重性”  。
11. 选择“创建警报规则”可以创建警报  。

    > [!NOTE]
    > - 若要接收有关高级文件共享由于预配的流入量而接近限制的通知，请按照前面的说明进行操作，但需要进行以下更改：
    >    - 在步骤 5 中，选择“流入量”指标，而不是“流出量” 。
    >
    > - 若要接收有关高级文件共享由于预配的 IOPS 而接近限制的通知，请按照前面的说明进行操作，但需要进行以下更改：
    >    - 在步骤 5 中，选择“事务”指标，而不是“流出量” 。
    >    - 在步骤 10 中，“聚合类型”的唯一选项是“总计”。 因此，阈值将取决于所选的聚合粒度。 例如，如果希望阈值为预配基线 IOPS 的&nbsp;80%，并且为“聚合粒度”选择了“1 小时”，则“阈值”将为基线 IOPS（以字节为单位）&times;&nbsp;0.8 &times;&nbsp;3600 。 

若要详细了解如何在 Azure Monitor 中配置警报，请参阅 [Microsoft Azure 中的警报概述](../../azure-monitor/alerts/alerts-overview.md)。

## <a name="see-also"></a>另请参阅
- [在 Windows 中排查 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)  
- [在 Linux 中排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)  
- [Azure 文件常见问题解答](storage-files-faq.md)
