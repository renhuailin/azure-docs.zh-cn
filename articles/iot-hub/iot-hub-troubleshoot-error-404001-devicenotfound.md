---
title: 排查 Azure IoT 中心错误 404001 DeviceNotFound
description: 了解如何修复错误 404001 DeviceNotFound
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: 127f4546d674eb425440a8e0f395ca8ff0a6b6f4
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515517"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

本文介绍 **404001 DeviceNotFound** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

在云到设备 (C2D) 通信期间（例如，C2D 消息、孪生更新或直接方法），操作失败且错误为 **404001 DeviceNotFound**。

## <a name="cause"></a>原因

操作失败，因为 IoT 中心找不到设备。 设备未注册或已禁用。

## <a name="solution"></a>解决方案

注册所用的设备 ID，然后重试。