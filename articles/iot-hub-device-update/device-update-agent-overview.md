---
title: 了解 Azure IoT 中心代理的设备更新 |Microsoft Docs
description: 了解 Azure IoT 中心代理的设备更新。
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e932238849baf267983fb3ca1ebb082db169d9fd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679121"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>IoT 中心代理的设备更新概述

设备更新代理包括两个概念层：

* 接口层在 Azure IoT 即插即用的基础上构建， [ (PnP) ](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play) 允许消息在设备更新代理和设备更新服务之间流动。
* 平台层负责下载、安装和应用可能是平台或特定于设备的高级更新操作。

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="代理实现。" lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>接口层

接口层由 [ADU 核心接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) 和 [设备信息接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)组成。

这些接口依赖于默认值的配置文件。 默认值包括 AzureDeviceUpdateCore 接口的 aduc_manufacturer 和 aduc_model，以及 DeviceInformation 接口的型号和制造商。 [了解更多](device-update-configuration-file.md) 配置文件。

### <a name="adu-core-interface"></a>ADU 核心接口

"ADU Core" 接口是设备更新代理和服务之间的主信道。 [了解](device-update-plug-and-play.md#adu-core-interface) 有关此接口的详细信息。

### <a name="device-information-interface"></a>设备信息接口

设备信息接口用于实现 `Azure IoT PnP DeviceInformation` 接口。 [了解](device-update-plug-and-play.md#device-information-interface) 有关此接口的详细信息。

## <a name="the-platform-layer"></a>平台层

平台层有两种实现。 模拟器平台层有更新操作的普通实现，并用于快速测试和评估 IoT 中心服务和设置的设备更新。 当设备更新代理是用模拟器平台层构建的时，我们将它称为设备更新模拟器代理或仅模拟器。 [了解](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) 有关如何使用模拟器代理的详细信息。 Linux 平台层集成了下载的 [传递优化](https://github.com/microsoft/do-client) ，并用于我们的 Raspberry Pi 参考图像和在 Linux 系统上运行的所有客户端。

### <a name="simulator-platform-layer"></a>模拟器平台层

模拟器平台层实现可在中找到 `src/platform_layers/simulator_platform_layer` ，可用于测试和评估 IoT 中心的设备更新。  "模拟器" 实现中的许多操作都模拟，以减少对 IoT 中心的设备更新进行试验的实际更改。  可以使用此平台层执行端到端 "模拟" 更新。 [了解](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) 有关运行模拟器代理的详细信息。

### <a name="linux-platform-layer"></a>Linux 平台层

可以在中找到 Linux 平台层实现 `src/platform_layers/linux_platform_layer` ，并将其与用于下载的 [传递优化客户端](https://github.com/microsoft/do-client/releases) 集成，并用于我们的 Raspberry Pi 参考图像和在 Linux 系统上运行的所有客户端。

此层可与不同的更新处理程序集成以实现安装程序。 例如， `SWUpdate` 更新处理程序调用 shell 脚本来调用 `SWUpdate` 可执行文件以执行更新。

## <a name="update-handlers"></a>更新处理程序

更新处理程序是处理内容或特定于安装程序的更新部分的组件。 更新处理程序实现在中 `src/content_handlers` 。

### <a name="simulator-update-handler"></a>模拟器更新处理程序

模拟器更新处理程序由模拟器平台层使用，可与 Linux 平台层一起使用，以与内容处理程序进行伪交互。 模拟器更新处理程序实现了几乎无 ops 的更新处理程序 Api。 可在下面找到模拟器更新处理程序的实现：
* [映像更新模拟器](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [包更新 apt 模拟器](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

注意： AzureDeviceUpdateCore PnP 接口中的 InstalledCriteria 字段应为内容的 sha256 哈希。 这是 [导入清单对象](import-update.md#create-device-update-import-manifest)中存在的相同哈希。 [详细了解](device-update-plug-and-play.md) `installedCriteria` 和 `AzureDeviceUpdateCore` 界面。

### <a name="swupdate-update-handler"></a>`SWUpdate` 更新处理程序

`SWUpdate`更新处理程序与 `SWUpdate` 命令行可执行文件和其他 shell 命令集成，以便为 Raspberry Pi 参考图像专门实现 A/B 更新。 [在此处](https://github.com/Azure/iot-hub-device-update/releases)找到最新的 Raspberry Pi 参考映像。 `SWUpdate`更新处理程序在[src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler)中实现。

### <a name="apt-update-handler"></a>APT 更新处理程序

APT 更新处理程序处理特定于 APT 的更新清单，并调用 APT 来安装或更新)  (的指定 Debian 包。

## <a name="self-update-device-update-agent"></a>自我更新设备更新代理

设备更新代理及其依赖项可以通过 IoT 中心管道的设备更新进行更新。 如果使用的是基于映像的更新，请在新映像中包含最新的设备更新代理。 如果使用的是基于包的更新，请在 apt 清单中包括设备更新代理及其所需版本，如任何其他包。 [了解](device-update-apt-manifest.md) 有关 apt 清单的详细信息。 你可以在 [IoT 设备](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)克隆的 "设备属性" 部分中查看设备更新代理和传递优化代理的安装版本。 [详细了解 ADU Core Interface 下的设备属性](device-update-plug-and-play.md#device-properties)。

## <a name="next-steps"></a>后续步骤
[了解设备更新代理配置文件](device-update-configuration-file.md)

