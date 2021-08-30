---
title: 了解 Device Update for IoT Hub 导入 | Microsoft Docs
description: 将新的更新导入 Device Update for IoT Hub 时涉及的关键概念。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39a1acb596f4d56915aabfa0a64fc69d6e55aec2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769108"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>将更新导入 Device Update for IoT Hub
若要将更新从 Device Update for IoT Hub 部署到设备，首先必须将该更新导入 Device Update 服务。 下面概述了一些在导入更新时要了解的重要概念。

## <a name="limits-on-importing-updates"></a>有关导入更新的限制
对于 Device Update for IoT Hub 实例，强制实施以下限制：

* 每个 ADU 实例 5 个提供程序
* 每个提供程序 5 个名称
* 每个名称 25 个版本

还限制了更新版本总共 100 个（跨所有提供程序和名称）。

对于更新提交中的任何单个文件，文件大小不得超过 800 MB。 一次更新提交中所有文件的总大小也不得超过 800 MB。

## <a name="import-manifest"></a>导入清单

导入清单是一个 JSON 文件，它定义有关要导入的更新的重要信息。 在导入过程中，你需要提交导入清单和关联的更新文件（如固件更新包）。 在导入清单中定义的元数据用于引入更新。 有些元数据还会在部署时用到，例如，用于验证是否正确安装了某个更新。

导入清单包含多个项，它们表示重要的 Device Update for IoT Hub 概念。 下面简要介绍这些概念。

### <a name="update-identity-update-id"></a>更新标识（更新 ID）

更新标识表示某个更新的独一无二的标识符。 它定义有关要导入的更新的重要属性。 该更新标识由三部分组成：
* 提供者：这是指创建或直接负责该更新的实体。 它通常会是公司名称。
* 名称：某一类更新的标识符。 该类可以是你选择的任何内容。 它通常会是设备或型号名称。
* 版本：它是一个版本号，将此更新与其他具有相同“提供者”和“名称”的更新区分开。 此版本由 Device Update for IoT Hub 服务使用，可能与设备上单个软件组件的版本相符，也可能不相符。 

### <a name="compatibility"></a>兼容性

为了简化更新部署，Device Update for IoT Hub 会将某个更新的兼容性属性（在导入清单中定义）与相应的设备属性进行比较。 只有属性匹配的更新才会返回，可用于部署。

### <a name="installedcriteria"></a>InstalledCriteria

设备上的更新代理使用 InstalledCriteria 来确定是否已成功安装更新。


## <a name="next-steps"></a>后续步骤

如果已准备就绪，请尝试按[导入操作指南](./import-update.md)操作，它将逐步引导你完成导入过程。


