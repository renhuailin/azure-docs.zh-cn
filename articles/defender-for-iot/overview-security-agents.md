---
title: 安全代理
description: 开始在 IoT 设备上了解、配置、部署和使用适用于 IoT 的 Azure Defender 安全服务代理。
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
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: a40b64dd3b8f898c961863c1d78a2a56642c44b9
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521605"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Azure Defender for IoT 设备微代理入门

Defender for IoT security agent 提供增强的安全功能，如监视操作系统配置的最佳实践。 通过单一服务控制设备现场威胁防护和安全态势。

防守 for IoT security agent 处理来自设备操作系统的原始事件收集、事件聚合以降低成本，并通过设备模块进行配置。 通过 IoT 中心将安全消息发送到 Defender for IoT 分析服务。

使用以下工作流来部署和测试 Defender for IoT 安全代理：

1. [为 Iot 中心启用用于 iot 服务的 Defender](quickstart-onboard-iot-hub.md)。

1. 如果 IoT 中心没有已注册的设备，则[注册新设备](../iot-accelerators/iot-accelerators-device-simulation-overview.md)。

1. 为设备[创建 DefenderIotMicroAgent 模块](quickstart-create-micro-agent-module-twin.md)。

1. 若要在 Azure 模拟设备上安装代理而不是在实际设备上安装，请在可用区域中[启动新的 Azure 虚拟机 (VM)](../virtual-machines/linux/quick-create-portal.md)。

1. 在 IoT 设备或新 VM 上[部署用于 iot security agent 的 Defender](how-to-deploy-linux-cs.md) 。

1. 按照 [trigger_events](https://aka.ms/iot-security-github-trigger-events) 的说明运行操作系统基准事件。

1. 验证 Defender 以获取 IoT 建议，以响应上一步骤中的模拟操作系统基线检查失败。 运行脚本30分钟后开始验证。

## <a name="next-steps"></a>后续步骤

配置[解决方案](quickstart-configure-your-solution.md) 
 [创建安全模块](quickstart-create-security-twin.md)配置[自定义警报](quickstart-create-custom-alerts.md) 
 [部署安全代理](how-to-deploy-agent.md)
