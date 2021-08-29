---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: d6df060afb65814175b02137720f7a9f27a6c4eb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122263585"
---
如果要针对受限制设备进行开发，可以将 IoT 即插即用与以下内容一起使用：

- [Azure SDK for Embedded C IoT 客户端库](https://aka.ms/embeddedcsdk)。
- [Azure RTOS](/azure/rtos/overview-rtos)。

本文包括这些受限制方案的链接和资源。

## <a name="prerequisites"></a>先决条件

下面的许多示例都需要特定的硬件设备，每个示例的先决条件各不相同。 有关详细的先决条件、配置和构建说明，请访问相关示例的链接。

## <a name="use-the-sdk-for-embedded-c"></a>使用 SDK for Embedded C

SDK for Embedded C 提供了一种将受限制设备连接到 Azure IoT 服务的轻量级解决方案，包括使用 [IoT 即插即用约定](../articles/iot-develop/concepts-convention.md)。 以下链接包括基于 MCU 的设备的示例，用于教育和调试目的。

### <a name="use-an-mcu-based-device"></a>使用基于 MCU 的设备

有关在 MCU 上使用 SDK for Embedded C、设备预配服务和 IoT 即插即用的完整端到端教程，请参阅[改造 PIC-IoT Wx 开发板以通过 IoT 中心设备预配服务连接到 Azure](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)。

### <a name="introductory-samples"></a>介绍性示例

SDK for Embedded C 存储库包含[几个示例](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample)，演示如何使用 IoT 即插即用：

> [!NOTE]
> 这些示例在 Windows 和 Linux 上演示运行，用于教育和调试目的。 在生产方案中，这些示例仅适用于受限制设备。

- [采用 SDK for Embedded C 的恒温器示例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [采用 SDK for Embedded C 的温度控制器示例](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>使用 Azure RTOS

Azure RTOS 包含一个轻量层，可将本机连接添加到 Azure IoT 云服务。 该层提供一种简单的机制，用于将受限制设备连接到 Azure IoT，同时使用 Azure RTOS 的高级功能。 要了解更多信息，请参阅[什么是 Microsoft Azure RTOS](/azure/rtos/overview-rtos)。

### <a name="toolchains"></a>工具链

Azure RTOS 示例包含了各种类型的 IDE 和工具链组合，例如：

- IAR：IAR 的[嵌入式工作台](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake：基于开源 [CMake](https://cmake.org/) 内部版本系统和 [GNU Arm Embedded 工具链](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)构建。
- MCUExpresso：NXP 的 [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube：STMicroelectronic 的 [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB：Microchip 的 [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>示例

下表列出了有关演示采用 Azure RTOS 和 IoT 即插即用的不同设备的入门示例：

制造商 | 设备 | 示例 |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](../articles/iot-develop/quickstart-devkit-mxchip-az3166.md) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)