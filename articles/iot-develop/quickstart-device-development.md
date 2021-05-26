---
title: Azure IoT 嵌入式设备开发快速入门
description: 介绍如何使用 Azure RTOS 和 Azure IoT 进行嵌入式设备开发的快速入门指南。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 613a537dc01262a111c8db70a5013b3346c7aab0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110091605"
---
# <a name="getting-started-with-azure-iot-embedded-device-development"></a>Azure IoT 嵌入式设备开发入门

**适用于**：[嵌入式设备开发](about-iot-develop.md#embedded-device-development)

本入门指南包含一系列快速入门，其中介绍了如何开始使用嵌入式设备和 Azure IoT。 

在每篇快速入门中，你将完成以下基本任务：
* 安装一组嵌入式开发工具，用于以 C 语言编写特定设备的程序
* 生成包含 Azure RTOS 组件和示例的映像，然后闪烁设备指示灯
* 将设备安全连接到 Azure IoT
* 查看设备遥测数据、查看属性，然后调用云到设备的方法

## <a name="quickstarts"></a>快速入门
本入门指南包含以下教程：

|快速入门|设备|
|---------------|-----|
|[开始使用 ST Microelectronics B-L475E-IOT01 发现工具包](https://go.microsoft.com/fwlink/p/?linkid=2129536) |[ST Microelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)|
|[开始使用 ST Microelectronics B-L4S5I-IOT01 发现工具包](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+) |[ST Microelectronics B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html)|
|[开始使用 NXP MIMXRT1060-EVK 评估工具包](https://go.microsoft.com/fwlink/p/?linkid=2129821) |[NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)|
|[开始使用 NXP MIMXRT1050-EVKB 评估工具包](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1050-EVKB) |[NXP MIMXRT1050-EVKB](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/i-mx-rt1050-evaluation-kit:MIMXRT1050-EVK)|
|[开始使用微芯片 ATSAME54-XPRO 评估工具包](https://go.microsoft.com/fwlink/p/?linkid=2129537) |[微芯片 ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)|
|[开始使用 Renesas Starter Kit+ for RX65N-2MB](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RSK_RX65N_2MB) |[Renesas Starter Kit+ for RX65N-2MB](https://www.renesas.com/us/en/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-2mb-starter-kit-plus-renesas-starter-kit-rx65n-2mb)|

## <a name="next-steps"></a>后续步骤
完成本指南中特定于设备的快速入门后，请浏览 Azure RTOS 入门存储库中其他特定于设备的文章和示例：
* [Azure RTOS 和 Azure IoT 入门](https://github.com/azure-rtos/getting-started#getting-started-with-azure-rtos-and-azure-iot)