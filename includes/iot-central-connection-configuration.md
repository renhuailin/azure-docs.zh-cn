---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491028"
---
在本教程的后面部分中运行示例设备应用程序时，需要以下配置值：

* ID 范围：在 IoT Central 应用程序中，导航到“管理”>“设备连接”。 记下“ID 范围”值。
* 组主键：在 IoT Central 应用程序中，导航到“管理”>“设备连接”>“SAS-IoT-Devices”。 记下共享访问签名主键值。

使用 Cloud Shell 从刚刚检索的组主键生成设备密钥：

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

记下生成的设备密钥，本教程的后面部分中将用到它。
