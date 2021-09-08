---
title: Azure IoT Central 中的体系结构概念 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 的体系结构相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 37e4224ae1347647d15959a55d19d2c6487935d7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273206"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 体系结构

本文概述了 Azure IoT Central 体系结构的重要概念。

## <a name="devices"></a>设备

设备与 Azure IoT Central 应用程序交换数据。 设备可以：

- 发送度量，例如遥测。
- 与应用程序同步设置。

在 Azure IoT Central 中，设备可以与应用程序交换的数据在设备模板中指定。 有关设备模板的详细信息，请参阅[设备模板](concepts-device-templates.md)。

若要详细了解设备如何连接到 Azure IoT Central 应用程序，请参阅[设备连接](concepts-get-connected.md)。

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge 设备

除了使用 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 创建的设备之外，还可以将 [Azure IoT Edge 设备](../../iot-edge/about-iot-edge.md)连接到 IoT Central 应用程序。 使用 IoT Edge 可以直接在 IoT Central 管理的 IoT 设备上运行云智能和自定义逻辑。 还可以使用 IoT Edge 作为网关，使其他下游设备能够连接到 IoT Central。

若要了解详细信息，请参阅[将 Azure IoT Edge 设备连接到 Azure IoT Central 应用程序](concepts-iot-edge.md)。

## <a name="cloud-gateway"></a>云网关

Azure IoT Central 使用 Azure IoT 中心作为启用设备连接的云网关。 IoT 中心允许：

- 在云中进行大规模数据引入。
- 设备管理。
- 安全的设备连接。

若要详细了解 IoT 中心，请参阅 [Azure IoT 中心](../../iot-hub/index.yml)。

若要详细了解 Azure IoT Central 中的设备连接，请参阅[设备连接](concepts-get-connected.md)。

## <a name="data-stores"></a>数据存储

Azure IoT Central 在云中存储应用程序数据。 存储的应用程序数据包括：

- 设备模板。
- 设备标识。
- 设备元数据。
- 用户和角色数据。

Azure IoT Central 将时序存储用于从设备发送的度量数据。 设备提供的时序数据供分析服务使用。

## <a name="data-export"></a>数据导出

在 Azure IoT Central 应用程序中，可以[连续将数据导出](howto-export-data.md)到自己的 Azure 事件中心和 Azure 服务总线实例。 还可以定期将数据导出到 Azure Blob 存储帐户。 IoT Central 可以导出度量、设备和设备模板。

## <a name="batch-device-updates"></a>设备批量更新

在 Azure IoT Central 应用程序中，可以[创建并运行作业](howto-manage-devices-in-bulk.md)来管理连接的设备。 使用这些作业可对设备属性或设置进行批量更新，或运行命令。 例如，可以创建一个作业来提高多个冷冻食品自动售货机的风扇速度。

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

每个 IoT Central 应用程序都有自己的内置 RBAC 系统。 [管理员可以使用预定义的角色之一或通过创建自定义角色来定义适用于 Azure IoT Central 应用程序的访问规则](howto-manage-users-roles.md)。 角色决定了用户有权访问哪些应用程序区域以及可执行哪些操作。

## <a name="security"></a>安全性

Azure IoT Central 中的安全功能包括：

- 对传输中的和静止时的数据加密。
- 通过 Azure Active Directory 或 Microsoft 帐户提供身份验证。 支持双重身份验证。
- 完全的租户隔离。
- 设备级别安全性。

## <a name="next-steps"></a>后续步骤

了解 Azure IoT Central 的体系结构后，建议接下来了解 Azure IoT Central 中的[设备连接](concepts-get-connected.md)。