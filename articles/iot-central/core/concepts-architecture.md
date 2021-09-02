---
title: Azure IoT Central 中的体系结构概念 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 的体系结构相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 46b8cdc7fa33c8ddd382decb49eaa148093c99fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742263"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 体系结构

本文概述了 Azure IoT Central 体系结构的重要概念。

## <a name="devices"></a>设备

设备与 Azure IoT Central 应用程序交换数据。 设备可以：

- 发送度量，例如遥测。
- 与应用程序同步设置。

在 Azure IoT Central 中，设备可以与应用程序交换的数据在设备模板中指定。 有关设备模板的详细信息，请参阅[设备模板](concepts-device-templates.md)。

若要详细了解设备如何连接到 Azure IoT Central 应用程序，请参阅[设备连接](concepts-get-connected.md)。

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge 设备

除了使用 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 创建的设备之外，还可以将 [Azure IoT Edge 设备](../../iot-edge/about-iot-edge.md)连接到 IoT Central 应用程序。 使用 IoT Edge 可以直接在 IoT Central 管理的 IoT 设备上运行云智能和自定义逻辑。 IoT Edge 运行时使你能够：

- 在设备上安装和更新工作负荷。
- 维护设备上的 IoT Edge 安全标准。
- 确保 IoT Edge 模块始终处于运行状态。
- 将模块运行状况报告给云以进行远程监视。
- 管理下游叶设备与 IoT Edge 设备之间、IoT Edge 设备上的模块之间以及 IoT Edge 设备与云之间的通信。

![包含 Azure IoT Edge 的 Azure IoT Central](./media/concepts-architecture/iotedge.png)

IoT Central 为 IoT Edge 设备启用以下功能：

- 设备模板，用于描述 IoT Edge 设备的功能，例如：
  - 部署清单上传功能，可帮助管理设备群的清单。
  - 在 IoT Edge 设备上运行的模块。
  - 每个模块发送的遥测数据。
  - 每个模块报告的属性。
  - 每个模块响应的命令。
  - IoT Edge 网关设备与下游设备之间的关系。
  - 不存储在 IoT Edge 设备上的云属性。
  - 更改 UI 如何显示设备功能的自定义项。
  - 设备视图和窗体。

  有关详细信息，请参阅[将 Azure IoT Edge 设备连接到 Azure IoT Central 应用程序](./concepts-iot-edge.md)一文。

- 使用 Azure IoT 设备预配服务大规模预配 Azure IoT Edge 设备的功能
- 规则和操作。
- 自定义仪表板和分析。
- 从 Azure IoT Edge 设备连续导出遥测数据。

### <a name="iot-edge-device-types"></a>IoT Edge 设备类型

IoT Central 将 IoT Edge 设备类型分类为：

- 叶设备。 IoT Edge 设备可以包含下游叶设备，但这些设备不会在 IoT Central 中预配。
- 包含下游设备的网关设备。 网关设备和下游设备均在 IoT Central 中进行预配

![包含 IoT Edge 的 IoT Central 概述](./media/concepts-architecture/gatewayedge.png)

> [!NOTE]
> IoT Central 目前不支持将 IoT Edge 设备作为下游设备连接到 IoT Edge 网关。 这是因为连接到 IoT Central 的所有设备均使用设备预配服务 (DPS) 进行预配，而 DPS 不支持嵌套的 IoT Edge 方案。

### <a name="iot-edge-patterns"></a>IoT Edge 模式

IoT Central 支持以下 IoT Edge 设备模式：

#### <a name="iot-edge-as-leaf-device"></a>用作叶设备的 IoT Edge

![用作叶设备的 IoT Edge](./media/concepts-architecture/edgeasleafdevice.png)

IoT Edge 设备在 IoT Central 中预配，任何下游设备及其遥测均表示为来自 IoT Edge 设备。 已连接到 IoT Edge 设备的下游设备不会在 IoT Central 中预配。

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>已连接到有标识下游设备的 IoT Edge 网关设备

![有下游设备标识的 IoT Edge](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

IoT Edge 设备连同已连接到该设备的下游设备一起在 IoT Central 中预配。 用于通过网关预配下游设备的运行时支持当前不受支持。

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>已连接到具有 IoT Edge 网关所提供标识的下游设备的 IoT Edge 网关设备

![包含无标识下游设备的 IoT Edge](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

IoT Edge 设备连同已连接到该设备的下游设备一起在 IoT Central 中预配。 用于为下游设备提供标识和预配下游设备的网关运行时支持当前不受支持。 如果你引入自己的标识转换模块，则 IoT Central 可以支持此模式。

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