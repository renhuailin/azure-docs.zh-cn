---
title: 了解 Azure IoT 中心符合性的设备更新 |Microsoft Docs
description: 了解 Azure IoT 中心的设备更新如何测量设备更新符合性。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662139"
---
# <a name="device-update-compliance"></a>设备更新符合性

在 IoT 中心的设备更新中，符合性会衡量已安装最高版本兼容更新的设备数。 如果设备安装了可兼容的最高版本更新，则该设备符合要求。 

例如，请考虑具有以下更新的设备更新实例：

|更新名称|更新版本|兼容设备型号|
|-----------|--------------|-----------------------|
|Update1    |1.0    |Model1|
|Update2    |1.0    |Model2|
|Update3    |2.0    |Model1|

假设已创建以下部署：

|部署名称    |更新名称    |目标组|
|-----------|--------------|-------------------|
|Deployment1    |Update1    |Group1|
|Deployment2    |Update2    |Group2|
|Deployment3    |Update3    |Group3|

现在，请考虑以下设备及其组成员身份和已安装的版本：

|DeviceId   |设备型号   |已安装更新版本|组 |合规性|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |可用的新更新</span>|
|Device2    |Model1 |2.0    |Group3 |更新时|
|Device3    |Model2 |1.0    |Group2 |更新时|
|-4    |Model1 |1.0    |Group3 |正在进行更新|

Device1 和-4 不合规，因为它们安装了1.0 版，即使在设备更新实例中其模型的版本更新为 "Update3"，也是如此。 设备2和设备3都是相容的，因为它们安装了其模型的最高版本更新。

合规性不考虑是否将更新部署到设备的组;它会查看发布到设备更新的所有更新。 因此，在上述示例中，尽管 Device1 已安装了部署到它的更新，但它被视为不符合。 在成功安装 Update3 之前，Device1 将继续被视为不符合。 相容性状态可帮助你确定是否需要新的部署。 

如上所示，IoT 中心的设备更新中有三种符合性状态：

*   **最新更新** –设备安装了发布到设备更新的版本兼容最高的更新。
*   **正在进行更新** -活动部署正在向设备提供最高版本的兼容更新。
*   **可用的新更新** –设备尚未安装兼容最高版本的更新，并且不是针对该更新的活动部署。
