---
title: 适用于 IoT Edge 的 Defender for IoT azureiotsecurity
description: 了解适用于 IoT Edge 的 Azure Defender for IoT azureiotsecurity 的体系结构和功能。
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: dddd45c857537cbb1af8032c6e53590e9a8ff4cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604885"
---
# <a name="azure-defender-for-iot-edge-azureiotsecurity"></a>Azure Defender for IoT Edge azureiotsecurity

[Azure IoT Edge](../../iot-edge/index.yml) 提供了用于在边缘管理和执行业务工作流的强大功能。
IoT Edge 在 IoT 环境中发挥的关键作用使其对于恶意行动者尤其有吸引力。

Defender for IoT azureiotsecurity 为 IoT Edge 设备提供了一个全面的安全解决方案。
Defender for IoT 模块收集、聚合和分析来自你的操作系统和容器系统的原始安全数据，从而生成可操作的安全建议和警报。

与用于 IoT 设备的 Defender for IoT 安全代理类似，Defender for IoT Edge 模块可通过其模块孪生进行高度自定义。
请参阅[配置代理](how-to-agent-configuration.md)以了解详细信息。

适用于 IoT Edge 的 Defender for IoT azureiotsecurity 提供以下功能：

- 从基础操作系统 (Linux) 和 IoT Edge 容器系统收集原始安全事件。

  请参阅 [Defender for IoT 代理配置](how-to-agent-configuration.md)来详细了解可用的安全数据收集器。

- IoT Edge 部署清单分析。

- 将原始安全事件聚合到通过 [IoT Edge 中心](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)发送的消息中。

- 通过使用 azureiotsecurity 孪生来删除配置。

  若要了解详细信息，请参阅[配置 Defender for IoT 代理](how-to-agent-configuration.md)。

适用于 IoT Edge 的 Defender for IoT azureiotsecurity 在 IoT Edge 下以特权模式运行。
要使模块能够监视操作系统和其他 IoT Edge 模块，需要使用特权模式。

## <a name="module-supported-platforms"></a>模块支持的平台

适用于 IoT Edge 的 Defender for IoT azureiotsecurity 目前仅适用于 Linux。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解了适用于 IoT Edge 的 Defender for IoT azureiotsecurity 的体系结构和功能。

若要继续开始使用 Defender for IoT 部署，请使用以下文章：

- 部署[适用于 IoT Edge 的 azureiotsecurity](how-to-deploy-edge.md)
- 了解如何[配置 Defender-IoT-micro-agent](how-to-agent-configuration.md)
- 了解如何[在 IoT 中心启用 Defender for IoT 服务](quickstart-onboard-iot-hub.md)
- 通过 [Defender for IoT 常见问题解答](resources-agent-frequently-asked-questions.md)详细了解该服务
