---
title: 基于代理的解决方案体系结构
description: 了解基于 IoT 代理的体系结构和信息流的 Azure Defender。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 3a7dabfed86dab463b38ae45278ead7f5acb5ef9
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521945"
---
# <a name="agent-based-solution-for-device-builders"></a>适用于设备构建者的基于代理的解决方案

本文介绍基于 IoT 代理的解决方案的 Defender 的功能系统体系结构。 Azure Defender for IoT 提供了两组功能，可满足你的环境需求、组织的无代理解决方案以及设备构建者的基于代理的解决方案。

## <a name="iot-hub-built-in-security"></a>IoT 中心内置安全性

默认情况下，在创建的每个新 IoT 中心中启用了用于 IoT 的 Defender。 Defender for IoT 提供实时监视、建议和警报，无需在任何设备上安装代理，并对已记录的 IoT 中心元数据使用高级分析来分析和保护现场设备和 IoT 中心。 

## <a name="defender-for-iot-micro-agent"></a>用于 IoT 微代理的 Defender 

用于 IoT 微代理的 Defender 为设备行为提供深度安全保护和可见性。 从设备收集、聚合和分析原始安全事件。 原始安全事件可能包括 IP 连接、进程创建、用户登录和其他安全相关的信息。 用于 IoT 设备代理的 Defender 还处理事件聚合，以帮助避免高网络吞吐量。 这些代理具有高度可自定义性，使你可以将它们用于特定的任务（例如以最快的 SLA 仅发送重要信息），或者用于将大量安全信息和上下文聚合到更大的段中，从而避免更高的服务成本。

设备代理和其他应用程序使用 **azure 发送安全消息 SDK** 将安全信息发送到 azure IoT 中心。 IoT 中心获取此信息，并将其转发到 IoT 服务的 Defender。

一旦启用了用于 IoT 服务的 Defender，IoT 中心还将发送其所有内部数据，供 Defender 用于分析。 此数据包括设备云操作日志、设备标识和中心配置。 所有这些信息都有助于创建用于 IoT 分析管道的 Defender。

用于 IoT 分析的 Defender 还可以从 Microsoft 和 Microsoft 合作伙伴的各种来源接收其他威胁情报流。 适用于 IoT 整个分析管道的 Defender 适用于在服务 (上进行的每个客户配置，例如自定义警报和发送安全消息 SDK) 的使用。

使用分析管道，将所有信息流结合起来，以生成可操作的建议和警报。 该管道包含由安全研究人员和专家创建的自定义规则，以及用于搜索标准设备行为偏差和风险分析的机器学习模型。

用于 IoT 的 Defender 建议和警报 (分析管道输出) 写入每个客户的 Log Analytics 工作区。 如果工作区中包含原始事件和警报和建议，则可以使用检测到的可疑活动的确切详细信息来深入探讨调查和查询。

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="微代理体系结构。":::

## <a name="next-steps"></a>后续步骤

[Defender for IoT 常见问题](resources-frequently-asked-questions.md)

[系统先决条件](quickstart-system-prerequisites.md)
