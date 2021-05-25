---
title: Azure IoT Central 中的客户数据驻留 | Microsoft Docs
description: 本文介绍了 Azure IoT Central 应用程序中的客户数据驻留。
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280676"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central 客户数据驻留

IoT Central 不会将客户数据存储在客户指定的地理位置，但以下场景除外：

- 将新用户添加到现有 IoT Central 应用程序时，该用户的电子邮件 ID 可能存储在地理位置之外，直到受邀用户首次访问应用程序。

- IoT Central 仪表板地图图块使用 [Azure Maps](../../azure-maps/about-azure-maps.md)。 将地图图块添加到现有 IoT Central 应用程序时，可能会根据 Azure Maps 服务的地理位置规则处理或存储位置数据。
