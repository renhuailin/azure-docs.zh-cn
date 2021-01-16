---
title: 安全代理概述
description: 开始在 IoT 设备上了解、配置、部署和使用 Azure Defender for IoT 安全服务代理。
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
ms.date: 12/27/2019
ms.author: shhazam
ms.openlocfilehash: 2b1cd131e578b1d16fabee99b8de536e4a48ece0
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247295"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Azure Defender for IoT 设备安全代理入门

Defender for IoT 安全代理提供增强的安全功能，例如监视远程连接、活动应用程序、登录事件和操作系统配置最佳做法。 通过单一服务控制设备现场威胁防护和安全态势。

同时采用 C# 和 C 为 Linux 和 Windows 安全代理提供参考体系结构。

Defender for IoT 安全代理处理从设备操作系统进行的原始事件收集、用于降低成本的事件聚合，以及通过设备模块孪生进行的配置。 通过 IoT 中心将安全消息发送到 Defender for IoT 分析服务。

使用以下工作流来部署和测试 Defender for IoT 安全代理：

1. [为 IoT 中心启用 Defender for IoT 服务](quickstart-onboard-iot-hub.md)
1. 如果 IoT 中心没有已注册的设备，则[注册新设备](../iot-accelerators/iot-accelerators-device-simulation-overview.md)。
1. 为设备[创建 azureiotsecurity 安全模块](quickstart-create-security-twin.md)。
1. 若要在 Azure 模拟设备上安装代理而不是在实际设备上安装，请在可用区域中[启动新的 Azure 虚拟机 (VM)](../virtual-machines/linux/quick-create-portal.md)。
1. 在 IoT 设备或新 VM 上[部署 Defender for IoT 安全代理](how-to-deploy-linux-cs.md)。
1. 按照 [trigger_events](https://aka.ms/iot-security-github-trigger-events) 的说明运行攻击的无害模拟。
1. 验证 Defender for IoT 警报，以响应上一步中的模拟攻击。 在运行脚本 5 分钟后开始验证。
1. 通过 IoT 中心浏览[警报](concept-security-alerts.md)、[建议](concept-recommendations.md)并[使用 Log Analytics 进行深入探讨](how-to-security-data-access.md)。

## <a name="next-steps"></a>后续步骤

- 配置[解决方案](quickstart-configure-your-solution.md)
- [创建安全模块](quickstart-create-security-twin.md)
- 配置[自定义警报](quickstart-create-custom-alerts.md)
- [部署安全代理](how-to-deploy-agent.md)