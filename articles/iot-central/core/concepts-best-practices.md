---
title: Azure IoT Central 中的设备开发最佳做法 | Microsoft Docs
description: 本文概述了 Azure IoT Central Azure 中设备连接的最佳做法
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: a3cbfa17d3b063ddcef90820dc31a080a768cbcd
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493756"
---
# <a name="best-practices-for-device-development"></a>设备开发最佳做法

这些建议说明了如何实现设备，以便充分利用 IoT Central 中内置的灾难恢复和自动缩放。

以下列表显示了设备连接到 IoT Central 时的高级流程：

1. 使用 DPS 预配设备并获取设备连接字符串。

1. 使用连接字符串连接 IoT Central 的内部 IoT 中心终结点。 向 IoT Central 应用程序发送数据并从中接收数据。

1. 如果设备获取连接失败，请根据错误类型重试连接或重新预配设备。

## <a name="use-dps-to-provision-the-device"></a>使用 DPS 预配设备

若要使用 DPS 预配设备，请使用 IoT Central 应用程序中的作用域 ID、凭据和设备 ID。 若要了解有关凭据类型的详细信息，请参阅 [X.509 组注册](concepts-get-connected.md#x509-group-enrollment)和 [SAS 组注册](concepts-get-connected.md#sas-group-enrollment)。 若要了解有关设备 ID 的详细信息，请参阅[设备注册](concepts-get-connected.md#device-registration)。

成功后，DPS 将返回一个连接字符串，设备可以使用该字符串连接到 IoT Central 应用程序。 若要解决预配错误，请参阅[检查设备的预配状态](troubleshoot-connection.md#check-the-provisioning-status-of-your-device)。

设备可以缓存该连接字符串，用于以后的连接。 但是，必须准备好设备来[处理连接故障](#handle-connection-failures)。

## <a name="connect-to-iot-central"></a>连接到 IoT Central

使用连接字符串连接 IoT Central 的内部 IoT 中心终结点。 通过此连接，可以将遥测数据发送到 IoT Central 应用程序，将属性值与 IoT Central 应用程序同步，并对 IoT Central 应用程序发送的命令进行响应。

## <a name="handle-connection-failures"></a>处理连接失败

出于缩放或灾难恢复目的，IoT Central 可能会更新其底层 IoT 中心。 若要保持连接性，设备代码应通过建立与新 IoT 中心终结点的连接来处理特定连接错误。

如果设备在连接时收到以下任何错误，则应对 DPS 重新执行预配步骤来获取新的连接字符串。 这些错误意味着设备使用的连接字符串不再有效：

- 不可访问的 IoT 中心终结点。
- 已过期的安全令牌。
- 设备在 IoT 中心被禁用。

如果设备在连接时收到以下任何错误，则应使用回退策略重试连接。 这些错误意味着设备使用的连接字符串仍然有效，但暂时情况阻止设备连接：

- 操作员阻止的设备。
- 服务内部错误 500。

若要了解有关设备错误代码的详细信息，请参阅[设备连接故障排除](troubleshoot-connection.md)。

## <a name="test-failover-capabilities"></a>测试故障转移功能

通过 Azure CLI，可以测试设备客户端代码的故障转移功能。 CLI 命令的工作原理是将设备注册临时切换到不同的内部 IoT 中心。 可以通过检查设备是否仍在发送遥测数据并响应 IoT Central 应用程序中的命令来验证设备故障转移是否有效。

若要为设备运行故障转移测试，请运行以下命令：

```azurecli
az iot central device manual-failover \
    --app-id {Application ID of your IoT Central application} \
    --device-id {Device ID of the device you're testing} \
    --ttl-minutes {How to wait before moving the device back to it's original IoT hub}
```

> [!TIP]
> 若要查找“应用程序 ID”，请导航到 IoT Central 应用程序中的“管理”>“应用程序” 。

如果命令成功，则会看到如下所示的输出：

```output
Command group 'iot central device' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
{
  "hubIdentifier": "6bd4...bafa",
  "message": "Success! This device is now being failed over. You can check your device'’'s status using 'iot central device registration-info' command. The device will revert to its original hub at Tue, 18 May 2021 11:03:45 GMT. You can choose to failback earlier using device-manual-failback command. Learn more: https://aka.ms/iotc-device-test"
}
```

若要详细了解 CLI 命令，请参阅 [az iot central device manual-failover](/cli/azure/iot/central/device#az_iot_central_device_manual_failover)。

现在，可以检查设备中的遥测数据是否仍到达 IoT Central 应用程序。

若要查看以各种编程语言处理故障转移的示例设备代码，请参阅 [IoT 高可用性客户端](https://github.com/iot-for-all/iot-central-high-availability-clients)。

## <a name="next-steps"></a>后续步骤

一些建议的后续步骤如下：

- 在[教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)中查看一些介绍如何使用 SAS 令牌的示例代码
- 了解[如何使用用于 IoT Central 应用程序的 Node.js 设备 SDK 通过 X.509 证书连接设备](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
- 了解如何[在 Azure IoT Central 应用程序中定义新的 IoT 设备类型](./howto-set-up-template.md)
- 了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)
