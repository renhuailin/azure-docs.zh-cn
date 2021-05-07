---
title: 事件聚合（预览）
description: Defender for IoT 安全代理从本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779318"
---
# <a name="event-aggregation-preview"></a>事件聚合（预览）

Defender for IoT 安全代理从本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。 Defender for IoT 微代理收集许多类型的设备事件，包括新进程和所有新连接事件。 新进程和新连接事件可能会在一秒钟内频繁发生在设备上。 此功能对于全面的安全性非常重要，但安全代理发送的消息数可能会很快达到或超出 IoT 中心配额和成本限制。 但是，这些事件包含非常有价值的安全信息，这些信息对于保护设备至关重要。 

为了减少额外的配额和成本，同时保护你的设备，Defender for IoT 代理聚合了这些类型的事件： 

- ProcessCreate（仅限 Linux） 

- ConnectionCreate（仅限 Azure RTOS） 

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？ 

Defender for IoT 代理聚合间隔时间段或时间范围内的事件。 间隔时间段过后，代理会将聚合事件发送到 Azure 云进行进一步分析。 在发送到 Azure 云之前，聚合事件一直存储在内存中。 

当代理收集与已保存在内存中的事件相同的事件时，代理都会增加此特定事件的命中次数，以降低代理的内存占用情况。 聚合时间范围过后，代理会发送已发生的每种事件类型的命中次数。 事件聚合其实就是收集的每种事件类型的命中次数的聚合。 

## <a name="process-events"></a>处理事件 

目前只有 Linux 操作系统支持处理事件。 

当 *命令行* 和  *userid*   相同时，处理事件被视为相同。 

处理事件的默认缓冲区为 32 个进程，在此之后缓冲区将循环，并丢弃最旧的处理事件，以便为新的处理事件腾出空间。  

## <a name="network-connection-events"></a>网络连接事件 

目前只有 Azure RTO 支持网络连接事件。 

当 *本地端口*、 *远程端口*、 *传输协议*、 *本地地址* 和 *远程地址* 相同时，网络连接事件被视为相同。 

网络连接事件的默认缓冲区为 64。 直到下一个集合周期，才会缓存新的网络事件。 将记录增加缓存大小的警告。

## <a name="next-steps"></a>后续步骤

请检查 [Defender for IoT 安全警报](concept-security-alerts.md)。
