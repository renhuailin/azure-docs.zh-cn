---
title: 了解 Device Update for Azure IoT Hub 代理 | Microsoft Docs
description: 了解 Device Update for Azure IoT Hub 代理。
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 14bc02c81e28e98d6d094cdfbde6095b7cd2d885
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403443"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>IoT 中心设备更新代理概述

Device Update 代理包含两个概念层：

* 接口层是在 [Azure IoT 即插即用 (PnP)](../iot-develop/overview-iot-plug-and-play.md) 的基础上构建的，可让消息在 Device Update 代理和 Device Update 服务之间流动。
* 平台层负责下载、安装和应用等高级更新操作（可能特定于平台或设备）。

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="代理实现。" lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>接口层

接口层由 [ADU Core 接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface)和[设备信息接口](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)组成。

这些接口的默认值依赖于配置文件。 默认值包括 aduc_manufacturer 和 aduc_model（对于 AzureDeviceUpdateCore 接口），以及模型和制造商（对于 DeviceInformation 接口）。 [详细了解](device-update-configuration-file.md)配置文件。

### <a name="adu-core-interface"></a>ADU Core 接口

“ADU Core”接口是 Device Update 代理和服务之间的主信道。 [详细了解](device-update-plug-and-play.md#adu-core-interface)此接口。

### <a name="device-information-interface"></a>设备信息接口

设备信息接口用于实现 `Azure IoT PnP DeviceInformation` 接口。 [详细了解](device-update-plug-and-play.md#device-information-interface)此接口。

## <a name="the-platform-layer"></a>平台层

平台层有两种实现。 模拟器平台层具有更新操作的普通实现，该层用于快速测试和评估 Device Update for IoT Hub 服务和设置。 如果 Device Update 代理是使用模拟器平台层构建的，我们将它称为 Device Update 模拟器代理（简称“模拟器”）。 [详细了解](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)如何使用模拟器代理。 Linux 平台层集成了[传递优化](https://github.com/microsoft/do-client)（用于下载），该层用于 Raspberry Pi 参考映像和 Linux 系统上运行的所有客户端。

### <a name="simulator-platform-layer"></a>模拟器平台层

你可在 `src/platform_layers/simulator_platform_layer` 中找到模拟器平台层实现，它用于测试和评估 Device Update for IoT Hub。  通过模拟“模拟器”实现中的许多操作，可减少进行 Device Update for IoT Hub 试验所需的实际更改。  可使用此平台层执行端到端的“模拟”更新。 [详细了解](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)如何运行模拟器代理。

### <a name="linux-platform-layer"></a>Linux 平台层

你可在 `src/platform_layers/linux_platform_layer` 中找到 Linux 平台层实现，它集成了[传递优化客户端](https://github.com/microsoft/do-client/releases)（用于下载），用于 Raspberry Pi 参考映像和 Linux 系统上运行的所有客户端。

该层可与不同的更新处理程序集成以实现安装程序。 例如，`SWUpdate` 更新处理程序通过调用 shell 脚本来调用 `SWUpdate` 可执行文件（用于执行更新）。

## <a name="update-handlers"></a>更新处理程序

更新处理程序是用于处理更新内容或其中特定于安装程序的部分的组件。 更新处理程序实现位于 `src/content_handlers` 中。

### <a name="simulator-update-handler"></a>模拟器更新处理程序

模拟器更新处理程序由模拟器平台层使用，可与 Linux 平台层一起用于模拟与内容处理程序的交互。 模拟器更新处理程序几乎无需运维即可实现更新处理程序 API。 你可在下面找到模拟器更新处理程序的实现：
* [映像更新模拟器](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [包更新 apt 模拟器](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

注意：AzureDeviceUpdateCore PnP 接口中的 InstalledCriteria 字段应为内容的 sha256 哈希。 这是[导入清单对象](import-update.md#create-a-device-update-import-manifest)中存在的相同哈希。 [详细了解](device-update-plug-and-play.md) `installedCriteria` 和 `AzureDeviceUpdateCore` 接口。

### <a name="swupdate-update-handler"></a>`SWUpdate` 更新处理程序

`SWUpdate` 更新处理程序与 `SWUpdate` 命令行可执行文件和其他 shell 命令相集成，专门为 Raspberry Pi 参考映像实现 A/B 更新。 你可在[此处](https://github.com/Azure/iot-hub-device-update/releases)找到最新的 Raspberry Pi 参考映像。 `SWUpdate` 更新处理程序在 [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler) 中实现。

### <a name="apt-update-handler"></a>APT 更新处理程序

APT 更新处理程序处理特定于 APT 的更新清单，并调用 APT 来安装或更新指定的 Debian 包。

## <a name="self-update-device-update-agent"></a>自行更新的 Device Update 代理

Device Update 代理及其依赖项可通过 Device Update for IoT Hub 管道进行更新。 如果使用的是基于映像的更新，请在新映像中包含最新的 Device Update 代理。 如果使用的是基于包的更新，请在 apt 清单中包含 Device Update 代理及其所需版本，就像对其他任何包所做的那样。 [详细了解](device-update-apt-manifest.md) apt 清单。 你可在 [IoT 设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)的“设备属性”部分中查看 Device Update 代理和传递优化代理的安装版本。 [详细了解 ADU Core 接口下的设备属性](device-update-plug-and-play.md#device-properties)。

## <a name="next-steps"></a>后续步骤
[了解 Device Update 代理配置文件](device-update-configuration-file.md)