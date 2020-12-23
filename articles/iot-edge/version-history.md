---
title: IoT Edge 版本导航和历史记录-Azure IoT Edge
description: 了解有关最新版本中的新特性和功能的信息 IoT Edge 的新增功能。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdb84f817f63e6401d17f18319e161f4c3477293
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024647"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge 版本和发行说明

Azure IoT Edge 是一种基于 GitHub 上托管的开源 IoT Edge 项目构建的产品。 所有新版本都在 [Azure IoT Edge 项目](https://github.com/Azure/azure-iotedge)中提供。 可以在 [开源 IoT Edge 项目](https://github.com/Azure/iotedge)上进行贡献和 bug 报告。

## <a name="documented-versions"></a>已记录版本

此站点上的 IoT Edge 文档适用于这两个不同版本的产品，因此你可以选择适用于你的 IoT Edge 环境的内容。 目前，两个受支持的版本是：

* **IoT Edge 1.0.10** 通过最新的公开发行版（ [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10)）涵盖了所有特性和功能。
* **IoT Edge 1.2 (预览版)** 包含最新预览版本中的特性和功能的其他内容： [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * IoT Edge 1.2 处于预览阶段，则需要安装候选发布版本。 有关详细信息，请参阅 [脱机或特定版本安装](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation)。

## <a name="version-history"></a>版本历史记录

此表提供 IoT Edge 包版本的最新版本历史记录，并突出显示针对每个版本所做的文档更新。

| 发行说明和资产 | 类型 | 日期 | 亮点 |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | 预览 | 2020 年 11 月 | [网关后 IoT Edge 设备](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT broker](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020 年 10 月 | [UploadSupportBundle 直接方法](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[上载运行时指标](how-to-access-built-in-metrics.md)<br>[路由优先级和生存时间](module-composition.md#priority-and-time-to-live)<br>[模块启动顺序](module-composition.md#configure-modules)<br>[X.509 手动预配](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020 年 3 月 | [X.509 用 DPS 自动预配](how-to-auto-provision-x509-certs.md)<br>[RestartModule 直接方法](how-to-edgeagent-direct-method.md#restart-module)<br>[支持-捆绑命令](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>后续步骤

* [查看所有 Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)
* [在反馈论坛中发出或查看功能请求](https://feedback.azure.com/forums/907045-azure-iot-edge)