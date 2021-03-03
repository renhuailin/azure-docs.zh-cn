---
title: 了解 IoT 中心导入的设备更新 |Microsoft Docs
description: 用于将新的更新导入到 IoT 中心的设备更新的关键概念。
author: andbrown
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: c6c6707a74007898c3cd73250709f4df3fabb37d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662115"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>将更新导入到 IoT 中心的设备更新
若要将更新从 IoT 中心的设备更新部署到设备，你必须首先将该更新 _导入_ 到设备更新服务中。 下面概述了导入更新时要了解的一些重要概念。

## <a name="import-manifest"></a>导入清单

导入清单是一种 JSON 文件，用于定义有关要导入的更新的重要信息。 在导入过程中，你将导入清单和关联的更新文件 (如固件更新包) 。 导入清单中定义的元数据用于引入更新。 某些元数据还会在部署时使用-例如，用于验证是否正确安装了更新。

导入清单包含若干项，这些项表示 IoT 中心概念的重要设备更新。 下面概述了这些概念。

### <a name="update-identity-update-id"></a>更新 ID (标识) 

更新标识表示更新的唯一标识符。 它定义有关要导入的更新的重要属性。 更新标识由三个部分组成：
* 提供者：这是创建或直接负责更新的实体。 它通常是公司名称。
* Name：更新类的标识符。 类可以是你选择的任何内容。 它通常是一个设备或模型名称。
* 版本：这是一个与此更新具有相同提供程序和名称的版本号。 此版本由 IoT 中心服务的设备更新使用，并可能与设备上的单个软件组件的版本相匹配。 

### <a name="compatibility"></a>兼容性

为了简化更新部署，IoT 中心的设备更新会将导入清单中定义的更新的兼容性属性与相应的设备属性进行比较。 仅返回具有匹配属性的更新，并且可用于部署。

### <a name="installedcriteria"></a>InstalledCriteria

设备上的更新代理使用 InstalledCriteria 来确定是否已成功安装更新。


## <a name="next-steps"></a>后续步骤

如果已准备就绪，请尝试 [导入 How-To 指南](./import-update.md)，这将逐步引导你完成导入过程。


