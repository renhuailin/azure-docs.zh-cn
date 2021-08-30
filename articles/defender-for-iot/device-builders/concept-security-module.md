---
title: Defender-IoT-micro-agent 和设备孪生
description: 了解 Defender-IoT-micro-agent 孪生体的概念，以及如何在 Defender for IoT 中使用它们。
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: ca36ad03c8e21e1606b163940f8bc06c0b761dd5
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014533"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-micro-agent

本文介绍 Defender for IoT 如何使用设备孪生和模块。

## <a name="device-twins"></a>设备孪生

对于在 Azure 中生成的 IoT 解决方案，设备孪生在设备管理和流程自动化方面发挥着关键作用。

适用于 IoT 的 Defender 可与现有的 IoT 设备管理平台完全集成，使你能够管理设备的安全状态，以及利用现有的设备控制功能。 集成是使用 IoT 中心孪生机制实现的。

详细了解 Azure IoT 中心[设备孪生](../../iot-hub/iot-hub-devguide-device-twins.md#device-twins)的概念。

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-micro-agent 孪生体

Defender for IoT 为服务中每个设备维护一个 Defender-IoT-micro-agent 孪生体。
该 Defender-IoT-micro-agent 孪生体为解决方案中的每个特定设备保存与设备安全性相关的所有信息。
设备安全属性在一个专用的 Defender-IoT-micro-agent 孪生体中进行维护，以实现更安全的通信并启用需要更少资源的更新和维护。

请参阅[创建 Defender-IoT-micro-agent 孪生体](quickstart-create-security-twin.md)和[配置安全代理](how-to-agent-configuration.md)，了解如何创建、自定义和配置孪生。 请参阅[在 IoT 中心内了解并使用模块孪生](../../iot-hub/iot-hub-devguide-module-twins.md)以详细了解 IoT 中心内模块孪生的概念。

## <a name="see-also"></a>请参阅

- [Defender for IoT 概述](overview.md)
- [部署安全代理](how-to-deploy-agent.md)
- [安全代理身份验证方法](concept-security-agent-authentication-methods.md)
