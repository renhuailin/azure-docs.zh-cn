---
title: Azure IoT Central 中的客户数据派驻服务 |Microsoft Docs
description: 本文介绍 Azure IoT Central 应用程序中的客户数据驻留。
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280676"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central 客户数据派驻服务

IoT Central 不会将客户数据存储在除以下情况以外的客户指定地理位置：

- 将新用户添加到现有 IoT Central 应用程序时，该用户的电子邮件 ID 可能存储在地理位置之外，直到受邀用户首次访问应用程序。

- IoT Central 仪表板地图图块使用 [Azure Maps](../../azure-maps/about-azure-maps.md)。 将地图磁贴添加到现有 IoT Central 应用程序时，可能会根据 Azure Maps 服务的地理位置规则处理或存储位置数据。
