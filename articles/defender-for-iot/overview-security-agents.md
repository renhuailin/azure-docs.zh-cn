---
title: 安全代理
description: 开始在 IoT 设备上了解、配置、部署和使用 Azure Defender for IoT 安全服务代理。
ms.topic: conceptual
ms.date: 1/24/2021
ms.openlocfilehash: a1d74de9824b9e825f87754da21070d4e7f1ee33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783483"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Azure Defender for IoT 设备微代理入门

Defender for IoT 安全代理提供增强的安全功能，例如监视操作系统配置最佳做法。 通过单一服务控制设备现场威胁防护和安全态势。

Defender for IoT 安全代理处理从设备操作系统进行的原始事件收集、用于降低成本的事件聚合，以及通过设备模块孪生进行的配置。 通过 IoT 中心将安全消息发送到 Defender for IoT 分析服务。

使用以下工作流来部署和测试 Defender for IoT 安全代理：

1. [为 IoT 中心启用 Defender for IoT 服务](quickstart-onboard-iot-hub.md)。

1. 如果 IoT 中心没有已注册的设备，则[注册新设备](../iot-accelerators/iot-accelerators-device-simulation-overview.md)。

1. 为设备[创建 DefenderIotMicroAgent 模块孪生](quickstart-create-micro-agent-module-twin.md)。

1. 若要在 Azure 模拟设备上安装代理而不是在实际设备上安装，请在可用区域中[启动新的 Azure 虚拟机 (VM)](../virtual-machines/linux/quick-create-portal.md)。

1. 在 IoT 设备或新 VM 上[部署 Defender for IoT 安全代理](how-to-deploy-linux-cs.md)。

1. 按照 [trigger_events](https://aka.ms/iot-security-github-trigger-events) 中的说明运行 OS 基准事件。

1. 为响应上一步中失败的模拟 OS 基线检查，请验证 Defender for IoT 建议。 在运行脚本 30 分钟后开始验证。

## <a name="next-steps"></a>后续步骤

- 配置[解决方案](quickstart-configure-your-solution.md)
- [创建 Defender-IoT-micro-agent](quickstart-create-security-twin.md)
- 配置[自定义警报](quickstart-create-custom-alerts.md)
- [部署安全代理](how-to-deploy-agent.md)