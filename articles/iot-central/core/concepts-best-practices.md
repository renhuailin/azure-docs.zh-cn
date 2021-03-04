---
title: Azure IoT Central 中的设备开发最佳做法 |Microsoft Docs
description: 本文概述了 Azure 中设备连接的最佳做法 IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054461"
---
# <a name="best-practices-for-device-development"></a>设备开发的最佳做法

本文适用于设备开发人员。

这些建议显示了如何实现设备，以便充分利用 IoT Central 中内置的灾难恢复和自动缩放。

以下列表显示了当设备连接到 IoT Central 时的高级流程：

1. 使用 DPS 预配设备并获取设备连接字符串。

1. 使用连接字符串连接 IoT Central 的内部 IoT 中心终结点。 向 IoT Central 应用程序发送数据并从中接收数据。

1. 如果设备获取连接失败，请根据错误类型重试连接或重新设置设备。

## <a name="use-dps-to-provision-the-device"></a>使用 DPS 预配设备

若要使用 DPS 预配设备，请使用 IoT Central 应用程序中的作用域 ID、凭据和设备 ID。 若要了解有关凭据类型的详细信息，请参阅 [x.509 组注册](concepts-get-connected.md#x509-group-enrollment) 和 [SAS 组注册](concepts-get-connected.md#sas-group-enrollment)。 若要了解有关设备 Id 的详细信息，请参阅 [设备注册](concepts-get-connected.md#device-registration)。

成功时，DPS 返回一个连接字符串，设备可以使用该字符串连接到 IoT Central 应用程序。 若要解决预配错误，请参阅 [检查设备的设置状态](troubleshoot-connection.md#check-the-provisioning-status-of-your-device)。

设备可以缓存用于以后连接的连接字符串。 但是，必须准备好设备来 [处理连接故障](#handle-connection-failures)。

## <a name="connect-to-iot-central"></a>连接到 IoT Central

使用连接字符串连接 IoT Central 的内部 IoT 中心终结点。 通过此连接，你可以将遥测数据发送到 IoT Central 应用程序，将属性值与 IoT Central 应用程序同步，并对 IoT Central 应用程序发送的命令进行响应。

## <a name="handle-connection-failures"></a>处理连接失败

出于缩放或灾难恢复目的，IoT Central 可能会更新其底层 IoT 中心。 若要维护连接性，设备代码应通过建立与新 IoT 中心终结点的连接来处理特定连接错误。

如果设备在连接时获得以下任何错误，则它应使用 DPS 重做设置步骤来获取新的连接字符串。 这些错误意味着设备使用的连接字符串不再有效：

- IoT 中心终结点无法访问。
- 安全令牌已过期。
- 已在 IoT 中心禁用设备。

如果设备在连接时获得以下任何错误，则它应使用回退策略重试连接。 这些错误意味着设备使用的连接字符串仍然有效，但暂时性的情况是阻止设备连接：

- 操作员阻止的设备。
- 服务内部错误500。

若要了解有关设备错误代码的详细信息，请参阅 [设备连接故障排除](troubleshoot-connection.md)。

## <a name="next-steps"></a>后续步骤

如果你是设备开发人员，则建议执行以下后续步骤：

- 查看一些演示如何使用 SAS 令牌的示例代码，请参阅 [教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)
- 了解如何 [使用 Node.js 设备 SDK IoT Central 应用程序将设备连接到 x.509 证书](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
- 了解如何 [在 Azure IoT Central 应用程序中定义新的 IoT 设备类型](./howto-set-up-template.md)
- 了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)
