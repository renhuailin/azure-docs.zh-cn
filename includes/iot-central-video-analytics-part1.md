---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763385"
---
该示例应用程序包括两个模拟设备和一个 IoT Edge 网关。 以下教程介绍了两种方法来试验和了解网关的功能：

* 在 Azure VM 中创建 IoT Edge 网关，并连接模拟相机。
* 在真实设备（如 Intel NUC）上创建 IoT Edge 网关，并连接到真实相机。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 使用 Azure IoT Central 视频分析应用程序模板来创建零售商店应用程序
> * 自定义应用程序设置
> * 为 IoT Edge 网关设备创建设备模板
> * 将网关设备添加到 IoT Central 应用程序

## <a name="prerequisites"></a>先决条件

若要完成本教程系列，需要：

* Azure 订阅。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。
* 如果使用的是真实相机，则需要在 IoT Edge 设备和相机之间建立连接，并且需要“实时流式处理协议”通道。

## <a name="initial-setup"></a>初始设置

在这些教程中，你将更新并使用多个配置文件。 这些文件的初始版本在 [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub 存储库中提供。 该存储库包括一个 [scratchpad](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) 文本文件，你可下载该文件并将其用于记录部署的服务中的配置值。 此文件可帮助你完成教程中的后续步骤。

在本地计算机上创建一个名为 lva-configuration 的文件夹以保存这些文件的副本。 然后右键单击以下每个链接，并选择“另存为”将文件保存到 lva-configuration 文件夹中：
