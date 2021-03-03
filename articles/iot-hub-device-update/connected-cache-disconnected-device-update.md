---
title: 了解使用 Microsoft 连接缓存对已断开连接的设备更新的支持 |Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: 了解使用 Microsoft 连接缓存对已断开连接的设备更新的支持
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679110"
---
# <a name="understand-support-for-disconnected-device-updates"></a>了解对断开连接的设备更新的支持

在透明网关方案中，一个或多个设备可以通过连接到 Azure IoT 中心的单个网关设备传递其消息。 在这些情况下，子设备可能未建立 internet 连接，或者可能不允许从 internet 下载内容。 Microsoft 连接缓存预览 IoT Edge 模块将为具有智能网络内缓存功能的 Azure IoT 中心客户提供设备更新，这将支持基于映像的和基于包的设备更新，IoT Edge 并将基于 Linux OS 的设备用于 (下游 IoT) 设备，并且还有助于节省 Azure IoT 中心客户的设备更新带宽。

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>适用于 Azure IoT 中心的设备更新的 Microsoft 连接缓存预览版如何工作？

Microsoft 连接缓存是适用于 Azure IoT 中心内容的设备更新发布内容的智能透明缓存，可对其进行自定义以缓存来自其他源（如包存储库）的内容。 Microsoft 连接缓存是一种冷缓存，该缓存由客户端对传递优化客户端请求的确切文件范围的请求准备好，不会预植入内容。 以下关系图和分步说明介绍了 Microsoft 连接缓存在 Azure IoT 中心基础结构的设备更新中的工作原理。

>[!Note]
>定义此流时，假定 IoT Edge 网关具有 internet 连接。 对于下游 IoT Edge 网关 (嵌套边缘) 方案中，可将 "内容交付网络" (CDN) 视为父 IoT Edge 网关上托管的 MCC。

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="断开连接的设备更新" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft 连接缓存作为 IoT Edge 模块部署到本地服务器。
2. Azure IoT 中心客户端的设备更新已配置为从 Microsoft 连接缓存下载内容，因为 IoT 叶设备的设备连接字符串的 GatewayHostName 属性 **或** IoT Edge 子设备在 toml 中设置 parent_hostname。
3. 在这两种情况下，适用于 Azure IoT 中心客户端的设备更新会从适用于 Azure IoT 中心服务的设备更新接收更新内容下载命令，并请求更新内容到 Microsoft 连接缓存而不是 CDN。 默认情况下，Microsoft 连接缓存配置为侦听 http 端口80，并且传递优化客户端在端口80上发出内容请求，因此必须将父对象配置为侦听此端口。  目前仅支持 http 协议。
4. Microsoft 连接缓存服务器从 CDN 下载内容，将其本地缓存设定为本地缓存，并将内容传递到 Azure IoT 中心客户端的设备更新。
   
>[!Note]
>使用基于包的更新时，管理员将使用所需的包主机名来配置 Microsoft 连接缓存服务器。

5. 来自适用于 Azure IoT 中心客户端的来自其他设备更新的后续请求现在来自缓存，Microsoft 连接缓存将不会向 CDN 请求相同内容的请求。

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>支持工业 IoT (IIoT) 与父/子托管方案

当下游或子 IoT Edge 网关托管 Microsoft 连接缓存服务器时，它将被配置为从父 IoT Edge 网关（托管 Microsoft 连接缓存服务器）请求更新内容。 这是所需的尽可能多级别，然后才能到达托管有 internet 访问权限的 Microsoft 连接缓存服务器的父 IoT Edge 网关。 从连接 internet 的服务器上，请求内容时，会将内容传递回最初请求内容的子 IoT Edge 网关。 内容将存储在磁盘上的每个级别。

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>针对 Azure IoT 中心的设备更新访问 Microsoft 连接缓存预览版

对于使用 Azure IoT 中心的设备更新部署解决方案的客户，Microsoft 连接缓存 IoT Edge 模块作为预览发布。 通过邀请访问预览。 [请求访问](https://aka.ms/MCCForDeviceUpdateForIoT) 适用于 IoT 关机的设备更新的 Microsoft 连接缓存预览版，并提供所需的信息（如果你想要访问该模块）。
