---
title: 排查 Azure IoT 中心错误 409001 DeviceAlreadyExists
description: 了解如何修复错误 409001 DeviceAlreadyExists
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: f6ef32537da22324ac2d4991fac9df785374e16f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829197"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

本文介绍 **409001 DeviceAlreadyExists** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

尝试在 IoT 中心注册设备时，请求失败且错误为 **409001 DeviceAlreadyExists**。

## <a name="cause"></a>原因

IoT 中心中已存在具有相同设备 ID 的设备。 

## <a name="solution"></a>解决方案

请使用其他设备 ID，然后重试。