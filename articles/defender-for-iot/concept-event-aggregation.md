---
title: '事件聚合 (预览) '
titleSuffix: Azure Defender for IoT
description: 用于 IoT 的 Defender 安全代理从你的本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 9ce24a44e48f090a0dc7d355952b3cf50dd4318c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809742"
---
# <a name="event-aggregation-preview"></a>事件聚合 (预览) 

用于 IoT 的 Defender 安全代理从你的本地设备收集数据和系统事件，并将数据发送到 Azure 云进行处理和分析。 用于 IoT 微代理的 Defender 收集许多类型的设备事件，包括新进程和所有新连接事件。 新进程和新连接事件可能会在一秒钟内发生在设备上。 此功能对于全面的安全性非常重要，但安全代理发送的消息数可能会很快达到或超出 IoT 中心配额和成本限制。 不过，这些事件包含非常重要的安全信息，这对于保护你的设备至关重要。 

为了降低额外的配额，并在使设备受到保护的同时降低成本，IoT 代理的 Defender 聚合了以下类型的事件： 

- 仅限 (Linux) ProcessCreate 

- ConnectionCreate (仅限 Azure RTO)  

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？ 

用于 IoT 代理的 Defender 聚合间隔时间段或时间范围内的事件。 间隔时间段过后，代理会将聚合事件发送到 Azure 云进行进一步分析。 在发送到 Azure 云之前，聚合事件一直存储在内存中。 

当代理将相同的事件收集到已保留在内存中的事件时，代理会增加此特定事件的命中计数，以减少代理的内存占用量。 当聚合时间窗口通过时，代理将发送所发生事件的每种类型的命中次数。 事件聚合其实就是收集的每种事件类型的命中次数的聚合。 

## <a name="process-events"></a>处理事件 

目前只有 Linux 操作系统支持处理事件。 

当 *命令行* 和  *userid* 完全相同时，处理事件被视为相同   。 

进程事件的默认缓冲区为32个进程，在此过程中，缓冲区将循环，并丢弃最旧的进程事件以便为新的进程事件腾出空间。  

## <a name="network-connection-events"></a>网络连接事件 

目前仅支持 Azure RTO 的网络连接事件。 

当 *本地端口*、 *远程端口*、 *传输协议*、 *本地地址* 和  *远程地址* 相同时，网络连接事件将视为相同。 

网络连接事件的默认缓冲区为64。 直到下一个收集周期，才会缓存新的网络事件。 将记录要增加缓存大小的警告。

## <a name="next-steps"></a>后续步骤

检查你 [的 Defender 是否有 IoT 安全警报](concept-security-alerts.md)。
