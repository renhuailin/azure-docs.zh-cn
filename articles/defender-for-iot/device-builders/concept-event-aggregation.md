---
title: 微代理事件集合（预览版）
description: Defender for IoT 安全代理从本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。
ms.date: 10/11/2021
ms.topic: conceptual
ms.openlocfilehash: 1868df35c05560fd80f6fa619b2d612ba6325d3c
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740429"
---
# <a name="micro-agent-event-collection-preview"></a>微代理事件集合（预览版）

Defender for IoT 安全代理从本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。 Defender for IoT 微代理收集许多类型的设备事件，包括新进程和所有新连接事件。 设备上可能会频繁发生新进程和新连接事件。 此功能对于全面的安全性非常重要，但安全代理发送的消息数可能会很快达到或超出 IoT 中心配额和成本限制。 这些消息和事件都包含非常有价值的安全信息，这些信息对于保护设备至关重要。

为了减少消息数量和成本，同时保持设备安全，Defender for IoT 代理将聚合了以下类型的事件：

- ProcessCreate（仅限 Linux）

- 登录活动（仅限 Linux）

- 网络 ConnectionCreate

- 系统信息

- 基线

基于事件的收集器是基于设备内的相应活动触发的收集器。 例如 ``a process was started in the device``。  

基于触发器的收集器是基于客户的配置以计划方式触发的收集器。

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？

Defender for IoT 代理聚合间隔时间段或时间范围内的事件。 间隔时间段过后，代理会将聚合事件发送到 Azure 云进行进一步分析。 在发送到 Azure 云之前，聚合事件一直存储在内存中。

代理会收集与内存中已存储的事件相同的事件。 此收集会使代理增加此特定事件的命中计数，以减少代理的内存占用。 聚合时间范围过后，代理会发送已发生的每种事件类型的命中次数。 事件聚合其实就是收集的每种事件类型的命中次数的聚合。

## <a name="process-events-event-based"></a>进程事件（基于事件）

进程事件在 Linux 操作系统上受支持。

当 *命令行* 和  *userid*   相同时，处理事件被视为相同。

进程事件的默认缓冲区可容纳 256 个进程。 达到此限制后，缓冲区会进行循环，丢弃最旧的进程事件，为最新处理的事件提供空间。 将记录增加缓存大小的警告。

针对每个事件收集的数据为：

| 参数 | 说明|
|--|--|
| **Timestamp** | 首次观察到此进程的时间。 |
| process_id | Linux PID。 |
| parent_process_id | Linux 父 PID（如果存在）。 |
| Commandline | 命令行。 |
| 类型 | 可以为 `fork` 或 `exec`。 |
| hit_count | 聚合计数。 在事件被发送到云之前，同一进程在同一时间范围内的执行次数。 |

## <a name="network-connection-events-event-based-collector"></a>网络连接事件（基于事件的收集器）

当本地端口、远程端口、传输协议、本地地址和远程地址相同时，各个网络连接事件会被视为同一事件。

网络连接事件的默认缓冲区为 256。 在缓存已满的情况下：

- Azure RTOS 设备：不会缓存新的网络事件，直到下一个收集周期开始。  

- Linux 设备：最旧的事件会被每个新事件替代。 将记录增加缓存大小的警告。

对于 Linux 设备，仅支持 IPv4。

针对每个事件收集的数据为：

| 参数 | 说明|
|--|--|
| **本地地址** | 连接的源地址。 |
| 远程地址 | 连接的目标地址。 |
| **本地端口** | 连接的源端口。 |
| **远程端口** | 连接的目标端口。 |
| Bytes_in | 连接的聚合 RX 字节总数。 |
| Bytes_out | 连接的聚合 TX 字节总数。 |
| Transport_protocol | 可以是 TCP、UDP 或 ICMP。 |
| 应用程序协议 | 与连接关联的应用程序协议。 |
| **扩展属性** | 连接的其他详细信息。 例如 `host name`。 |

## <a name="login-collector-event-based-collector"></a>登录收集器（基于事件的收集器）

登录收集器可收集用户登录名、注销和失败的登录尝试。

当前完全支持 SSH 和 Telnet。  

将收集下列数据：

| 参数 | 说明|
|--|--|
| **operation** | Login、Logout 或 LoginFailed。 |
| process_id | Linux PID。 |
| user_name | Linux 用户。 |
| executable | 终端设备。 例如 tty1..6 或 pts/n。 |
| remote_address | 连接的源，IPv6 或 IPv4 格式的远程 IP，或 127.0.0.1/0.0.0.0 表示本地连接。 |

> [!Note]
> 在用户实际登录之前，在设备上打开终端时会捕获登录事件。 此事件具有 TTY 进程、登录事件类型和用户名。 例如 `LOGIN`。

## <a name="system-information-trigger-based-collector"></a>系统信息（基于触发器的收集器）

针对每个事件收集的数据为：

| 参数 | 说明|
|--|--|
| hardware_vendor | 设备供应商的名称。 |
| hardware_model | 设备的型号。 |
| os_dist | 操作系统的分发版。 例如 `Linux`。 |
| os_version | 操作系统的版本。 例如，`Windows 10` 或 `Ubuntu 20.04.1`。 |
| os_platform | 设备的 OS。 |
| os_arch | 操作系统的体系结构。 例如 `x86_64`。 |
| nics | 网络接口控制器。 下面列出了属性的完整列表。 |

nics 属性由以下各项组成：

| 参数 | 说明|
|--|--|
|type | 以下值之一：`UNKNOWN`、`ETH`、`WIFI`、`MOBILE` 或 `SATELLITE`。 |
| vlans | 与网络接口关联的虚拟局域网。 |
| **vendor** | 网络控制器的供应商。 |
| info | IPS 以及与网络控制器关联的 MAC。 包括以下字段： <br> - ipv4_address：IPv4 地址。 <br> - ipv6_address：IPv6 地址。 <br> - mac：MAC 地址。|

## <a name="baseline-trigger-based"></a>基线（基于触发器）

基线收集器定期执行 CIS 检查。 只会将失败的结果发送到云。 云将对结果进行聚合并提供建议。

### <a name="data-collection"></a>数据收集

针对每个事件收集的数据为：

| 参数 | 说明|
|--|--|
| 检查 ID | 采用 CIS 格式。 例如 `CIS-debian-9-Filesystem-1.1.2`。 |
| 检查结果 | 可以是 `Error` 或 `Fail`。 例如，无法运行检查时为 `Error`。 |
| **错误** | 错误的信息和说明。 |
| **描述** | 来自 CIS 的检查说明。 |
| **修正** | 来自 CIS 的修正建议。 |
| **严重性** | 严重性级别。 |

## <a name="next-steps"></a>后续步骤

请检查 [Defender for IoT 安全警报](concept-security-alerts.md)。
