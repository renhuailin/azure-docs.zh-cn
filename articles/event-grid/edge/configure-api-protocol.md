---
title: 配置 API 协议 - Azure 事件网格 IoT Edge | Microsoft Docs
description: 了解事件网格模块可能的协议配置。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 05/10/2021
ms.subservice: iot-edge
ms.topic: article
ms.openlocfilehash: 2bd533cb59fd661e3ac6dff9ff6aa3fa2d646889
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370712"
---
# <a name="configure-event-grid-api-protocols"></a>配置事件网格 API 协议

本指南给出了事件网格模块可能的协议配置的示例。 事件网格模块公开了用于其管理和运行时操作的 API。 下表捕获了协议和端口。

| 协议 | 端口 | 说明 |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 默认禁用。 仅在测试过程中有用。 不适用于生产工作负荷。
| HTTPS | 4438 | 默认

有关所有可能的配置，请参阅[安全性和身份验证](security-authentication.md)指南。

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>向同一边缘网络上的 IoT 模块公开 HTTPS

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>对其他 IoT 模块和非 IoT 工作负荷启用 HTTPS

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> PortBindings 节用于将内部端口映射到容器主机的端口。 如果 IoT Edge 设备可公开访问，则此功能可让你从 IoT Edge 容器网络外部访问事件网格模块。

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>向同一边缘网络上的 IoT 模块公开 HTTP 和 HTTPS

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>对其他 IoT 模块和非 IoT 工作负荷启用 HTTP 和 HTTPS

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> 默认情况下，每个 IoT 模块都是桥接网络创建的 IoT Edge 运行时的一部分。 它可让同一网络上的不同 IoT 模块相互通信。 PortBindings 可将容器内部端口映射到主计算机，从而使任何人都能够从外部访问事件网格模块的端口。

>[!IMPORTANT]
> 虽然可以在 IoT Edge 网络之外访问端口，但客户端身份验证会强制实施在实际情况下允许特定用户对模块进行调用的功能。
