---
title: IoT Edge 版本导航和历史记录 - Azure IoT Edge
description: 了解 IoT Edge 中的新功能以及有关最新版本中新特性和功能的信息。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e7b85f3e01a1c9a9c354599cfed8359b4a56d549
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631448"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge 版本和发行说明

Azure IoT Edge 是基于 GitHub 上托管的开源 IoT Edge 项目构建的产品。 [Azure IoT Edge 项目](https://github.com/Azure/azure-iotedge)中提供了所有新版本。 可在[开源 IoT Edge 项目](https://github.com/Azure/iotedge)上作出贡献以及提供 Bug 报告。

## <a name="documented-versions"></a>记录的版本

此站点上的 IoT Edge 文档可用于产品的两个不同版本，因此你可以选择适用于你的 IoT Edge 环境的内容。 当前支持的两个版本为：

* IoT Edge 1.0.10，涵盖最新正式发布版中的所有特性和功能：[1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10)。
* IoT Edge 1.2（预览版），包含最新预览版本中的特性和功能的附加内容：[1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * IoT Edge 1.2 处于预览状态，需要安装候选发布版本。 有关详细信息，请参阅[脱机或特定版本安装](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation-optional)。

## <a name="version-history"></a>版本历史记录

下表提供了 IoT Edge 包版本的最新版本历史记录，并重点介绍了针对每个版本所做的文档更新。

| 发行说明和资产 | 类型 | Date | 亮点 |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | 预览 | 2020 年 11 月 | [网关后 IoT Edge 设备](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT 代理](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020 年 10 月 | [UploadSupportBundle 直接方法](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[上传运行时指标](how-to-access-built-in-metrics.md)<br>[路由优先级和生存时间](module-composition.md#priority-and-time-to-live)<br>[模块启动顺序](module-composition.md#configure-modules)<br>[X.509 手动预配](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020 年 3 月 | [使用 DPS 的 X.509 自动预配](how-to-auto-provision-x509-certs.md)<br>[RestartModule 直接方法](how-to-edgeagent-direct-method.md#restart-module)<br>[support-bundle 命令](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>后续步骤

* [查看所有 Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)
* [在反馈论坛中发送或查看功能请求](https://feedback.azure.com/forums/907045-azure-iot-edge)