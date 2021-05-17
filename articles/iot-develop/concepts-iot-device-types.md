---
title: Azure IoT 设备类型改善
description: 了解 Azure IoT 支持的不同设备类型和可用工具。
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654067"
---
# <a name="overview-of-azure-iot-device-types"></a>Azure IoT 设备类型改善
IoT 设备在各种硬件平台上都存在。 台式计算机中一直有小型 8 位 MCU，即使最新款的 x86 CPU 中也存在。 许多变量会影响你为 IoT 设备选择硬件的决定，本文概述了一些关键差异。

## <a name="key-hardware-differentiators"></a>关键硬件区别
选择硬件时，一些重要因素包括成本、功耗、网络以及可用的输入和输出。

* 成本：当批量生成最终产品时，通常使用体积更小，价格更低的设备。 但需要取舍的是，鉴于设备高度受限，该设备的开发费用可能会更加高昂。 开发成本可以分摊到所有生产的设备上，因此，每台装置的开发成本将会降低。

* 功率：如果设备将使用电池并且未连接到电源插座，则设备的功耗非常重要。 MCU 通常设计用于低功耗场景，可能是延长电池寿命的更好选择。

* 网络访问：可以通过多种方式将设备连接到云服务。 以太网、Wi-fi 和移动电话以及一些可用选项。 选择的连接类型将取决于设备的部署位置和使用方式。 例如，由于覆盖范围巨大，蜂窝可能是一个有吸引力的选择，但是对于高流量设备，费用可能会很高。 硬编码以太网降低了数据成本，但便携性很差。

* 输入和输出：设备上可用的输入和输出会直接影响设备操作功能。 通常，微控制器会将许多 I/O 功能直接内置到芯片中，并提供可直接连接的大量传感器。

## <a name="microcontrollers-vs-microprocessors"></a>微控制器与微处理器
IoT 设备可以分为两大类：微控制器 (MCU) 和微处理器 (MPU)。

与 MPU 相比，MCU 成本更低，操作更简单。 MCU 将在芯片自身中包含许多功能，如内存、接口和 I/O。 MPU 将从支持芯片中的组件获取此功能。 MCU 通常将使用实时操作系统 (RTOS) 或运行裸机（无操作系统），并提供对外部事件的实时响应和高度确定的反应。

**MPU** 通常会运行通用操作系统（例如 Windows、Linux 或 MacOSX），提供不确定的实时响应。 通常无法保证何时完成任务。 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU 与 MPU 对比":::

下表显示了基于 MCU 的系统和基于 MPU 的系统之间的一些明确差异：

||微控制器 (MCU)|微处理器 (MPU)|
|-|-|-|
|**CPU**| Less | 更多 |
|**RAM**| Less | 更多 |
|**Flash**| Less | 更多 |
|**OS**| 无操作系统还是 RTOS | 常规用途 |
|开发难点| 更困难 |  更容易 |
|功耗| 较低 | 较高 |
|**节约成本**| 较低 | 较高 |
|**具有确定性**| 是 | 否 - 有异常|
|设备大小| 较小 | 较大 |
