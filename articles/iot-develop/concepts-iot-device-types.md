---
title: Azure IoT 设备类型概述
description: 了解 Azure IoT 支持的不同设备类型和可用工具。
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654067"
---
# <a name="overview-of-azure-iot-device-types"></a>Azure IoT 设备类型概述
IoT 设备在各种硬件平台上都存在。 在台式计算机中找到最新的 x86 Cpu 时，有一些小的8位 Mcu。 许多因素都考虑到为 IoT 设备选择的硬件的决定，本文介绍了一些主要区别。

## <a name="key-hardware-differentiators"></a>关键硬件区别
选择硬件时，有一些重要因素是成本、功率消耗、网络和可用的输入和输出。

* **成本：** 当批量生成最终产品时，通常使用较小的价格较低的设备。 不过，这种折衷是，对于高度受约束的设备，设备的开发可能更昂贵。 开发成本可以分布在所有生产的设备上，因此，每个单位的开发成本将会降低。

* **电源：** 如果设备将使用电池并且未连接到电源网格，则设备消耗的电量非常重要。 Mcu 通常适用于低能耗方案，可以是延长电池寿命的更好选择。

* **网络访问：** 可以通过多种方式将设备连接到云服务。 以太网、Wi-fi 和移动电话以及一些可用选项。 选择的连接类型将取决于部署设备的位置及其使用方式。 例如，在高范围内，手机网络可能是一个引人注目的选项，但对于高流量设备，这可能会消耗大量资源。 硬编码以太网提供的数据成本较低，但不易携带。

* **输入和输出：** 设备上可用的输入和输出会直接影响设备操作功能。 通常，微控制器会将许多 i/o 函数直接内置到芯片中，并提供可直接连接的大量传感器。

## <a name="microcontrollers-vs-microprocessors"></a>微控制器与微处理器
IoT 设备可以分为两大类，微控制器 (Mcu) 和微处理器 (MPUs) 。

与 MPUs 相比， **mcu** 成本更低，操作更简单。 MCU 将包含许多功能，如芯片本身中的内存、接口和 i/o。 MPU 将从支持芯片中的组件绘制此功能。 MCU 通常使用实时操作系统 (RTO) 或运行裸机 (不) 操作系统，并为外部事件提供实时响应和高度确定性的反应。

**MPUs** 通常会运行常规用途的操作系统（例如 Windows、Linux 或 MacOSX），以提供不确定的实时响应。 通常不保证任务完成的时间。 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU 与 MPU":::

下表显示了 MCU 与基于 MPU 的系统之间的一些定义差异：

||微控制器 (MCU) |微处理器 (MPU) |
|-|-|-|
|**CPU**| Less | 更多 |
|**RAM**| Less | 更多 |
|**Flash**| Less | 更多 |
|**OS**| 否或 RTO | 常规用途 |
|**开发难点**| 麻烦 |  更容易 |
|**功率消耗**| 较低 | 较高 |
|**成本**| 较低 | 较高 |
|**具有确定性**| 是 | 不例外|
|**设备大小**| 超过 | 较大 |
