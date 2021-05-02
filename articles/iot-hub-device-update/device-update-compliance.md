---
title: 了解 Device Update for Azure IoT Hub 符合性 | Microsoft Docs
description: 了解 Device Update for Azure IoT Hub 如何度量设备更新符合性。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662139"
---
# <a name="device-update-compliance"></a>设备更新符合性

在 Device Update for IoT Hub 中，符合性会度量已安装最高版本兼容更新的设备数。 如果设备已安装与其兼容的最高版本可用更新，则该设备符合要求。 

例如，请考虑具有以下更新的 Device Update 实例：

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

现在，请考虑以下设备及其组成员身份和已安装版本：

|DeviceId   |设备型号   |已安装更新版本|组 |合规性|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |有可用的新更新</span>|
|Device2    |Model1 |2.0    |Group3 |应用了最新更新|
|Device3    |Model2 |1.0    |Group2 |应用了最新更新|
|Device4    |Model1 |1.0    |Group3 |正在进行更新|

Device1 和 Device4 不符合要求，因为它们安装了版本 1.0，虽然在 Device Update 实例中有与它们的型号兼容的更高版本更新 Update3。 Device2 和 Device3 都符合要求，因为它们安装了与其型号兼容的最高版本更新。

符合性不考虑更新是否已部署到设备的组中；它查看已发布到 Device Update 的任何更新。 因此，在上面的示例中，即使 Device1 已安装了部署到它的更新，它也被认为不符合要求。 Device1 仍会被认为不符合要求，直到它成功安装 Update3 为止。 符合性状态可以帮助你确定是否需要新部署。 

如上所示，在 Device Update for IoT Hub 中有三种符合性状态：

*   应用了最新更新 - 设备已安装发布到 Device Update 的最高版本兼容更新。
*   正在更新 - 活动部署正在向设备传递最高版本兼容更新。
*   有可用的新更新 - 设备尚未安装最高版本兼容更新，尚未针对该更新进行积极的部署。
