---
title: 微代理事件集合（预览版）
description: Defender for IoT 安全代理从本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 6836c12f625645c5b9e0913f9d2f3f36fc06843e
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397499"
---
# <a name="micro-agent-event-collection-preview"></a>微代理事件集合（预览版）

Defender for IoT 安全代理从本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。 Defender for IoT 微代理收集许多类型的设备事件，包括新进程和所有新连接事件。 新进程和新连接事件可能会在一秒钟内频繁发生在设备上。 此功能对于全面的安全性非常重要，但安全代理发送的消息数可能会很快达到或超出 IoT 中心配额和成本限制。 在任何情况下，这些事件都包含非常有价值的安全信息，这些信息对于保护设备至关重要。 

为了减少额外的配额和成本，同时保护你的设备，Defender for IoT 代理聚合了这些类型的事件： 

- ProcessCreate（仅限 Linux） 

- 网络 ConnectionCreate

- 系统信息

- 基线

基于事件的收集器是基于设备内的相应活动触发的收集器。 例如，一个进程在设备中启动。  

基于触发器的收集器是基于客户的配置以计划方式触发的收集器。

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？ 

Defender for IoT 代理聚合间隔时间段或时间范围内的事件。 间隔时间段过后，代理会将聚合事件发送到 Azure 云进行进一步分析。 在发送到 Azure 云之前，聚合事件一直存储在内存中。 

代理会收集与内存中已存储的事件相同的事件。 此收集会使代理增加此特定事件的命中计数，以减少代理的内存占用。 聚合时间范围过后，代理会发送已发生的每种事件类型的命中次数。 事件聚合其实就是收集的每种事件类型的命中次数的聚合。 

## <a name="process-events-event-based"></a>进程事件（基于事件）

进程事件在 Linux 操作系统上受支持。 

当 *命令行* 和  *userid*   相同时，处理事件被视为相同。 

进程事件的默认缓冲区可容纳 256 个进程。 达到此限制后，缓冲区会进行循环，丢弃最旧的事件，为最新处理的事件提供空间。 将记录增加缓存大小的警告。

### <a name="data-collection"></a>数据收集

针对每个事件收集的数据：

- Timestamp - 首次观察到此进程的时间。

- Process_id - Linux PID。

- Parent_process_id - Linux 父 PID（如果存在）。

- Commandline - 命令行。 

- Type - 可以是 `fork` 或 `exec`。

- hit_count - 聚合计数。 在事件被发送到云之前，同一进程在同一时间范围内的执行次数。 

## <a name="network-connection-events-event-based-collector"></a>网络连接事件（基于事件的收集器）

当本地端口、远程端口、传输协议、本地地址和远程地址相同时，各个网络连接事件会被视为同一事件。

网络连接事件的默认缓冲区为 256。 在缓存已满的情况下： 

- Azure RTOS 设备：不会缓存新的网络事件，直到下一个收集周期开始。  

- Linux 设备：最旧的事件会被每个新事件替代。 将记录增加缓存大小的警告。

对于 Linux 设备，仅支持 IPv4。

### <a name="data-collection"></a>数据收集

针对每个事件收集的数据：

- 本地地址 - 连接的源地址。

- 远程地址 - 连接的目标地址。

- 本地端口 - 连接的源端口。

- 远程端口 - 连接的目标端口。

- Bytes_in - 连接的聚合 RX 字节总数。

- Bytes_out - 连接的聚合 TX 字节总数。

- Transport_protocol - 可以是 TCP、UDP 或 ICMP。

- 应用程序协议 - 与连接关联的应用程序协议。

- 扩展的属性 - 连接的其他详细信息。 例如 `host name`。 

## <a name="baseline-trigger-based"></a>基线（基于触发器） 

基线收集器定期执行 CIS 检查。 只会将失败的结果发送到云。 云将对结果进行聚合并提供建议。 

### <a name="data-collection"></a>数据收集

针对每个事件收集的数据：

- 检查 ID - 采用 CIS 格式 CIS-debian-9-Filesystem-1.1.2。

- 检查结果 -可以是 `Error` 或 `Fail`。 例如，无法运行检查时为 `Error`。

- 错误 - 错误的信息和说明。

- 说明 - 来自 CIS 的检查说明。

- 修正 - 来自 CIS 的修正建议。

## <a name="next-steps"></a>后续步骤

请检查 [Defender for IoT 安全警报](concept-security-alerts.md)。
