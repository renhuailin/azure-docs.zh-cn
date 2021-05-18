---
title: 预配 Device Update for Azure IoT Hub 代理 | Microsoft Docs
description: 预配 Device Update for Azure IoT Hub 代理
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 01ce213f71fc2ac070ca0b09780bd45ede4e61c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097748"
---
# <a name="device-update-agent"></a>Device Update 代理

Device Update for IoT Hub 支持两种形式的更新：基于映像的更新和基于包的更新。 

* 映像更新在设备的最终状态中提供更高级别的置信度。 通常可以更轻松地在预生产环境和生产环境之间复制映像更新的结果，因为它不会带来与包及其依赖项相同的挑战。 由于其原子性质，还可以轻松采用 A/B 故障转移模型。 
* 基于包的更新是有针对性的更新，只改变设备上的特定组件或应用程序。 因此将降低带宽的消耗，并有助于减少下载和安装更新的时间。 包更新通常可以在应用更新时减少设备的停机时间，并避免创建图像的开销。 

请访问以下链接，了解如何生成、运行和修改 Device Update 代理。

## <a name="build-the-device-update-agent"></a>生成 Device Update 代理

按照说明通过源[生成](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) Device Update 代理。

## <a name="run-the-device-update-agent"></a>运行 Device Update 代理

成功生成代理后，请[运行](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)该代理。

## <a name="modifying-the-device-update-agent"></a>修改 Device Update 代理

现在，请进行必要的更改，将代理合并到映像中。  查看如何[修改](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) Device Update 代理以获取指导。

### <a name="troubleshooting-guide"></a>故障排除指南

如果遇到问题，请查看 Device Update for IoT Hub [故障排除指南](troubleshoot-device-update.md)，以帮助解决所有可能的问题并收集要提供给 Microsoft 的必要信息。

## <a name="next-steps"></a>后续步骤

使用以下预生成的映像和二进制文件轻松演示 Device Update for IoT Hub。  

[映像更新：Raspberry Pi 3 B+ Reference Yocto Image 入门](device-update-raspberry-pi.md)

[映像更新：开始使用 Ubuntu (18.04 x64) 模拟器引用代理](device-update-simulator.md)

[包更新：开始使用 Ubuntu Server 18.04 x64 包代理](device-update-ubuntu-agent.md)

