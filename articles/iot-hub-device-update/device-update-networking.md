---
title: IoT 中心网络要求的设备更新 |Microsoft Docs
description: IoT 中心的设备更新出于不同目的使用各种网络端口。
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679118"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>用于 IoT 中心的设备更新的端口
ADU 出于不同目的使用各种网络端口。

## <a name="default-ports"></a>默认端口

目的|端口号 |
---|---
从网络下载/Cdn  | 80 (HTTP 协议) 
从 MCC/Cdn 下载 | 80 (HTTP 协议) 
ADU 代理与 Azure IoT 中心的连接  | 8883 (MQTT 协议) 

## <a name="use-azure-iot-hub-supported-protocols"></a>使用 Azure IoT 中心支持的协议
可以修改 ADU 代理以使用任何受支持的 Azure IoT 中心协议。

[详细了解](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) 受支持的协议的当前列表。
