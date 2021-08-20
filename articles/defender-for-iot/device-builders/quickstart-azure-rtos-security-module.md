---
title: 快速入门：为 Azure RTOS 配置和启用 Defender-IoT-micro-agent
description: 本快速入门介绍了如何在 Azure IoT 中心为 Azure RTOS 服务加入和启用 Defender-IoT-micro-agent。
services: defender-for-iot
ms.topic: quickstart
ms.date: 07/01/2021
ms.openlocfilehash: 11705b33efcd594296dea087e0aaf914cb494a0c
ms.sourcegitcommit: a2540262e05ffd4a4b059df0976940d60fabd125
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113138506"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos"></a>快速入门：适用于 Azure RTOS 的 Defender-IoT-micro-agent

本文介绍了入门的先决条件，并说明了如何在 IoT 中心为 Azure RTOS 服务启用 Defender-IoT-micro-agent。 如果当前没有 IoT 中心，请参阅[使用 Azure 门户创建 IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)。

## <a name="prerequisites"></a>先决条件 

### <a name="supported-devices"></a>支持的设备

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

从[适用于 Azure RTOS 的 Defender-IoT-micro-agent GitHub 资源](https://github.com/azure-rtos/azure-iot-preview/releases)下载、编译和运行你选择的特定版块和工具（IAR、semi 的 IDE 或 PC）的其中一个 .zip 文件。

### <a name="azure-resources"></a>Azure 资源

入门的下一个阶段是准备 Azure 资源。 需要一个 IoT 中心，建议使用 Log Analytics 工作区。 对于 IoT 中心，需要使用 IoT 中心连接字符串来连接设备。 
  
### <a name="iot-hub-connection"></a>IoT 中心连接

需要使用 IoT 中心连接才能开始使用。 

1. 在 Azure 门户中打开你的 **IoT 中心**。

1. 导航到“IoT 设备”。

1. 选择“创建”。

1. 将 IoT 连接字符串复制到[配置文件](how-to-azure-rtos-security-module.md)中。

连接凭据来自用户应用程序配置 HOST_NAME、DEVICE_ID 和 DEVICE_SYMMETRIC_KEY  。

适用于 Azure RTOS 的 Defender-IoT-micro-agent 根据 MQTT 协议使用 Azure IoT 中间件连接。

## <a name="next-steps"></a>后续步骤

转到下一篇文章，完成配置和自定义解决方案的操作。

> [!div class="nextstepaction"]
> [配置和自定义适用于 Azure RTOS 的 Defender-IoT-micro-agent（预览版）](how-to-azure-rtos-security-module.md)
