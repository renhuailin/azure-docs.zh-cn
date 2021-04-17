---
title: 什么是基于代理的解决方案体系结构
description: 了解基于 Azure Defender for IoT 代理的体系结构和信息流。
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: e08c3f151c3d3bc4fe08e61d960874b07f5b63e8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383518"
---
# <a name="what-is-agent-based-solution-for-device-builders"></a>什么是基于代理的设备构建者解决方案

本文介绍了基于 Defender for IoT 代理的解决方案的功能系统体系结构。 Azure Defender for IoT 提供了两组功能，可满足你的环境需求,适用于组织的无代理解决方案，以及设备构建者的基于代理的解决方案。

## <a name="iot-hub-built-in-security"></a>IoT 中心内置安全性

默认情况下，在创建的每个新 IoT 中心中启用了 Defender for IoT。 Defender for IoT 可提供实时监视、建议和警报，而无需在任何设备上安装代理，并对已记录的 IoT 中心元数据使用高级分析来分析和保护现场设备和 IoT 中心。 

## <a name="defender-for-iot-micro-agent"></a>Defender for IoT 微代理 

Defender for IoT 微代理为设备行为提供深度安全保护和可见性。 从设备收集、聚合和分析原始安全事件。 原始安全事件可能包括 IP 连接、进程创建、用户登录和其他安全相关的信息。 Defender for IoT 设备代理还可处理事件聚合，帮助避免高网络吞吐量。 这些代理具有高度可自定义性，使你可以将它们用于特定的任务（例如以最快的 SLA 仅发送重要信息），或者用于将大量安全信息和上下文聚合到更大的段中，从而避免更高的服务成本。

设备代理和其他应用程序使用“Azure 发送安全消息 SDK”将安全信息发送到 Azure IoT 中心。 IoT 中心获取此信息，并将其转发到 Defender for IoT 服务。

启用了 Defender for IoT 服务后，除了转发的数据之外，IoT 中心还会发送其所有内部数据，供 Defender for IoT 进行分析。 这些数据包括设备-云操作日志、设备标识和中心配置。 所有这些信息都有助于创建 Defender for IoT 分析管道。

Defender for IoT 分析管道还可以从 Microsoft 和 Microsoft 合作伙伴的各种来源接收其他威胁情报流。 整个 Defender for IoT 分析管道可与针对服务进行的每个客户配置配合使用（如自定义警报和“发送安全消息 SDK”的使用）。

通过使用分析管道，Defender for IoT 将所有信息流结合起来，生成可操作的建议和警报。 该管道包含由安全研究人员和专家创建的自定义规则，以及用于搜索标准设备行为偏差和风险分析的机器学习模型。

Defender for IoT 建议和警报（分析管道输出）会写入到每个客户的 Log Analytics 工作区。 如果将工作区中的原始事件以及警报和建议包括在内，则可以使用检测到的可疑活动的确切详细信息进行深入调查和查询。

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="微代理体系结构。":::

## <a name="next-steps"></a>后续步骤

[Defender for IoT 常见问题解答](resources-frequently-asked-questions.md)

[系统先决条件](quickstart-system-prerequisites.md)
