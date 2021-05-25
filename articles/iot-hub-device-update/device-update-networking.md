---
title: Device Update for IoT Hub 网络要求 | Microsoft Docs
description: Device Update for IoT Hub 将各种网络端口用于不同目的。
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558374"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>用于 Device Update for IoT Hub 的端口
ADU 将各种网络端口用于不同目的。

## <a name="default-ports"></a>默认端口

目标|端口号 |
---|---
从网络/CDN 下载  | 80（HTTP 协议）
从 MCC/CDN 下载 | 80（HTTP 协议）
ADU 代理连接到 Azure IoT 中心  | 8883（MQTT 协议）

## <a name="use-azure-iot-hub-supported-protocols"></a>使用 Azure IoT 中心支持的协议
可以将 ADU 代理修改为使用任何支持的 Azure IoT 中心协议。

[详细了解](../iot-hub/iot-hub-devguide-protocols.md)当前支持的协议列表。
