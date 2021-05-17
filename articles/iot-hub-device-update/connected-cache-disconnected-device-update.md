---
title: 了解如何使用 Microsoft 联网缓存支持离线设备更新 |Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: 了解如何使用 Microsoft 联网缓存支持离线设备更新
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811897"
---
# <a name="understand-support-for-disconnected-device-updates"></a>了解对离线设备更新的支持

在透明网关方案中，一个或多个设备可以通过与 Azure IoT 中心保持连接的一个网关设备来传递其消息。 在这些情况下，子设备可能没有 Internet 连接，或者可能不允许这些子设备从 Internet 下载内容。 Microsoft 联网缓存预览版 IoT Edge 模块将为适用于 Azure IoT 中心的设备更新的客户提供智能的网络内缓存功能，从而能够对 IoT Edge 网关后面基于 Linux OS 的设备（下游 IoT 设备）以基于映像和基于包的方式进行更新，并且还会有助于为 Azure IoT 中心客户节省用于设备更新的带宽。

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>适用于 Azure IoT 中心的设备更新的 Microsoft 联网缓存预览版如何工作？

Microsoft 联网缓存预览版是一种智能的透明缓存，适用于为 Azure IoT 中心的设备更新发布的内容，并且可以进行自定义，以缓存来自其他源（如包存储库）的内容。 Microsoft 联网缓存是冷缓存，由针对确切文件范围的客户端请求（由“传递优化”客户端请求）加热，并且不会预植入内容。 下面的关系图和分步说明介绍了 Microsoft 联网缓存在适用于 Azure IoT 中心的设备更新基础结构中的工作原理。

>[!Note]
>在定义此流时，已假定 IoT Edge 网关具有 Internet 连接。 对于下游 IoT Edge 网关（嵌套边缘）方案，可以将“内容交付网络”(CDN) 视为在父 IoT Edge 网关上托管的 MCC。

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="离线的设备更新" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft 联网缓存会作为 IoT Edge 模块部署到本地服务器。
2. 由于 IoT 叶设备的设备连接字符串的 GatewayHostName 属性，或 IoT Edge 子设备的 config.toml 中设置的 parent_hostname，适用于 Azure IoT 中心的设备更新客户端配置为从 Microsoft 联网缓存下载内容。
3. 在这两种情况下，适用于 Azure IoT 中心的设备更新客户端都会从适用于 Azure IoT 中心的设备更新服务接收更新内容下载命令，并向 Microsoft 联网缓存（而不是 CDN）请求更新内容。 在默认情况下，Microsoft 联网缓存配置为在 http 端口 80 上侦听，并且传递优化客户端在端口 80 上发出内容请求，因此，必须将父级配置为在此端口上侦听。  目前只支持 http 协议。
4. Microsoft 联网缓存服务器会从 CDN 下载内容，种植磁盘上存储的它的本地缓存，并将内容传递到适用于 Azure IoT 中心的设备更新客户端。
   
>[!Note]
>在使用基于包的更新时，Microsoft 联网缓存服务器将由管理员来配置所需的包主机名。

5. 来自其他适用于 Azure IoT 中心的设备更新客户端的对于同一更新内容的后续请求现在将会来自缓存，并且 Microsoft 联网缓存将不会向 CDN 发出关于同一内容的请求。

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>利用父/子托管支持工业 IoT (IIoT) 的方案

在某个下游或子 IoT Edge 网关承载 Microsoft 联网缓存服务器时，该网关将会被配置为从承载 Microsoft 联网缓存服务器的父 IoT Edge 网关请求更新内容。 在到达承载着具有 Internet 访问权限的 Microsoft 联网缓存服务器的父 IoT Edge 网关之前，需要在必要的情况下为尽可能多的级别进行这样的配置。 从连接了 Internet 的服务器上，会从 CDN 请求内容，此时，内容会被传送回最初请求该内容的子 IoT Edge 网关。 内容将会存储在磁盘上的每个级别。

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>访问适用于 Azure IoT 中心的设备更新的 Microsoft 联网缓存预览版

Microsoft 联网缓存 IoT Edge 模块作为预览版发布，面向使用适用于 Azure IoT 中心的设备更新来部署解决方案的客户。 对该预览版的访问会通过邀请方式提供。 如果你想访问该模块，可以[请求访问](https://aka.ms/MCCForDeviceUpdateForIoT)适用于 IoT 中心的设备更新的 Microsoft 联网缓存预览版，并提供所需信息。
