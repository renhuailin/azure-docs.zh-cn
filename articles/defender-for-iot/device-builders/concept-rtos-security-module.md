---
title: 适用于 Azure RTOS 的 Defender-IoT-micro-agent 基础知识的概念性说明
description: 了解有关适用于 Azure RTOS 的 Defender-IoT-micro-agent 概念和工作流的基础知识。
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: 5555b7806f3cf2bfe685ab442b85d44d03e1f406
ms.sourcegitcommit: a2540262e05ffd4a4b059df0976940d60fabd125
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113139172"
---
# <a name="defender-iot-micro-agent-for-azure-rtos"></a>适用于 Azure RTOS 的 Defender-IoT-micro-agent

通过本文可更好地了解适用于 Azure RTOS 的 Defender-IoT-micro-agent（包括功能和优势），并可以获取指向相关配置和参考资源的链接。 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agent

作为 NetX Duo 产品/服务的一部分，适用于 Azure RTOS 的 Defender-IoT-micro-agent 提供了一个全面的 Azure RTOS 设备安全解决方案。 在 NetX Duo 产品/服务中，Azure RTOS 随附了内置的 Azure IoT Defender-IoT-micro-agent，并且会在激活后针对实时操作系统设备上的常见威胁提供全面的防范。

适用于 Azure RTOS 的 Defender-IoT-micro-agent 在后台运行，提供无缝的用户体验，同时使用每个客户与 IoT 中心之间的唯一连接发送安全消息。 默认启用适用于 Azure RTOS 的 Defender-IoT-micro-agent。  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX Duo 是一种高级的行业级 TCP/IP 网络堆栈，专门用于深度嵌入的实时应用程序和 IoT 应用程序。 Azure RTO NetX Duo 是一种双 IPv4 和 IPv6 网络堆栈，提供一组丰富的协议，包括安全和云。 请详细了解 [Azure RTO NetX Duo](/azure/rtos/netx-duo/) 解决方案。

该模块提供以下功能：

- **检测恶意网络活动**
- **基于自定义警报的设备行为基线**
- **改善设备安全机制**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>适用于 Azure RTOS 的 Defender-IoT-micro-agent 体系结构

适用于 Azure RTOS 的 Defender-IoT-micro-agent 由 Azure IoT 中间件平台初始化，并使用 IoT 中心客户端向中心发送安全遥测数据。

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-IoT-micro-agent 状态图和信息流":::

适用于 Azure RTOS 的 Defender-IoT-micro-agent 使用三个收集器监视以下设备活动和信息：
- 设备网络活动 TCP、UDP 和 ICM
- 系统信息，如 **Threadx** 和 **NetX Duo** 版本
- 检测信号事件

每个收集器都链接到一个优先级组，每个优先级组都有其自己的间隔，其可能的值为“低”、“中”和“高”。 间隔影响收集和发送数据的时间间隔。

每个时间间隔都是可配置的，并且 IoT 连接器可以启用和禁用，以便进一步[自定义你的解决方案](how-to-azure-rtos-security-module.md)。 

## <a name="supported-security-alerts-and-recommendations"></a>支持的安全警报和建议

适用于 Azure RTOS 的 Defender IoT 微代理支持特定的安全警报和建议。 完成初始配置后，请确保为你的服务[查看并自定义相关的警报和建议值](concept-rtos-security-alerts-recommendations.md)。

## <a name="ready-to-begin"></a>准备好开始了吗？

适用于 Azure RTOS 的 Defender-IoT-micro-agent 可供免费下载，以用于你的 IoT 设备。 每个 Azure 订阅都通过一个 30 天的试用版提供 Defender for IoT 云服务。 [立即下载 Defender-IoT-micro-agent](https://github.com/azure-rtos/azure-iot-preview/releases)，让我们开始吧。 

## <a name="next-steps"></a>后续步骤

- 从适用于 Azure RTOS 的 Defender-IoT-micro-agent [先决条件和设置](quickstart-azure-rtos-security-module.md)入手。
- 详细了解适用于 Azure RTOS 的 Defender-IoT-micro-agent [安全警报和建议支持](concept-rtos-security-alerts-recommendations.md)。 
- 使用适用于 Azure RTOS 的 Defender-IoT-micro-agent [参考 API](azure-rtos-security-module-api.md)。