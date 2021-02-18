---
title: 快速入门：配置并启用 Azure RTOS 安全模块
description: 了解如何在 Azure IoT 中心为 Azure RTOS 服务加入和启用安全模块。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: a11a8ec2d8eb82950cf0ab55eb6ca1913b41c84a
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522931"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>快速入门：Azure RTOS 安全模块（预览版）

本文介绍了入门的先决条件，并说明了如何在 IoT 中心为 Azure RTOS 服务启用安全模块。 如果当前没有 IoT 中心，请参阅[使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)以开始操作。

## <a name="prerequisites"></a>先决条件 

### <a name="supported-devices"></a>支持的设备

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

从 [Azure RTOS 安全模块 GitHub 资源](https://github.com/azure-rtos/azure-iot-preview/releases)下载、编译和运行你选择的特定看板和工具（IAR、semi's IDE 或 PC）的其中一个 .zip 文件。

### <a name="azure-resources"></a>Azure 资源

入门的下一个阶段是准备 Azure 资源。 需要一个 IoT 中心，建议使用 Log Analytics 工作区。 对于 IoT 中心，需要使用 IoT 中心连接字符串来连接设备。 
  
### <a name="iot-hub-connection"></a>IoT 中心连接

需要使用 IoT 中心连接才能开始使用。 

1. 在 Azure 门户中打开你的 **IoT 中心**。

1. 导航到“IoT 设备”。

1. 选择“创建”。

1. 将 IoT 连接字符串复制到[配置文件](how-to-azure-rtos-security-module.md)中。

连接凭据来自用户应用程序配置 HOST_NAME、DEVICE_ID 和 DEVICE_SYMMETRIC_KEY  。

Azure RTOS 安全模块根据 MQTT 协议使用 Azure IoT 中间件连接。

## <a name="next-steps"></a>后续步骤

转到下一篇文章，完成配置和自定义解决方案的操作。

> [!div class="nextstepaction"]
> [配置 Azure RTOS 安全模块](how-to-azure-rtos-security-module.md)
