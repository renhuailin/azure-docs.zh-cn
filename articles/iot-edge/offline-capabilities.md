---
title: 脱机操作设备 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 设备和模块如何能够长时间在无 Internet 连接的情况下操作，以及 IoT Edge 如何使常规 IoT 设备也能脱机操作。
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 41610bb956273fa69119d6b87a016072a5e4faa2
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108749872"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>了解有关 IoT Edge 设备、模块和子设备的扩展脱机功能

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge 支持 IoT Edge 设备上的扩展脱机操作，并且也会在子设备上启用脱机操作。 只要 IoT Edge 设备有机会连接到 IoT 中心，该设备和任何子设备就可以在间歇性或无 Internet 连接的情况下继续运作。

## <a name="how-it-works"></a>工作原理

当 IoT Edge 设备进入脱机模式，IoT Edge 中心将扮演三个角色。 首先，它将存储任何向上游发送的消息并保存它们，直到设备重新连接。 其次，它代表 IoT 中心对模块和子设备进行身份验证，以便它们可以继续运行。 第三，它会在子设备之间启用通常通过 IoT 中心的通信。

下面的示例展示了 IoT Edge 方案如何在脱机模式下运行：

1. **配置设备**

   IoT Edge 设备自动启用脱机功能。 若要将此功能扩展到其他设备，需要将子设备配置为信任其分配的父设备，并通过作为网关的父设备路由设备到云的通信。

2. **与 IoT 中心同步**

   在安装 IoT Edge 运行时后，IoT Edge 设备至少要有一次处于联机状态，以便与 IoT 中心同步。 在此同步中，IoT Edge 设备将获取有关任何分配给它的子设备的详细信息。 IoT Edge 设备还可以安全更新本地缓存以启用脱机操作，并检索本地存储遥测消息的设置。

3. **脱机**

   从 IoT 中心断开连接时，IoT Edge 设备及其部署的模块和任何子设备都可以无限期运行。 模块和子设备可以在脱机状态下通过在 IoT Edge 中心进行身份验证来启动和重新启动。 上游绑定到 IoT 中心的遥测存储在本地。 模块之间或子设备之间的通信通过直接方法或消息来维护。

4. **与 IoT 中心重新连接和重新同步**

   一旦还原与 IoT 中心的连接，IoT Edge 设备会再次同步。 本地存储的消息会立即传递到 IoT 中心，但取决于连接速度、IoT 中心延迟和相关因素。 这些消息按照它们存储的相同顺序传递。

   模块和设备的所需属性和报告属性之间的差异已得到协调。 IoT Edge 设备更新对其分配的子设备集所做的任何更改。

## <a name="restrictions-and-limits"></a>约束和限制

本文所述的扩展脱机功能可在 [IoT Edge 1.0.7 版或更高版本](https://github.com/Azure/azure-iotedge/releases)中获得。 早期版本有一个脱机功能子集。 不具备扩展脱机功能的现有 IoT Edge 设备不能通过更改运行时版本进行升级，但是必须用新的 IoT Edge 设备标识重新配置才能获得这些功能。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

只能添加非 IoT Edge 设备作为子设备。

:::moniker-end
<!-- end 1.1 -->

IoT Edge 设备及其分配的子设备可以在初始一次性同步之后无限期脱机运行。但是，消息存储取决于生存时间 (TTL) 设置和存储消息的可用磁盘空间。

## <a name="set-up-parent-and-child-devices"></a>设置父设备和子设备

父设备可以有多个子设备，但一个子设备只能有一个父设备。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

子设备可以是注册到同一个 IoT 中心的任何非 IoT Edge 设备。

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range="iotedge-2020-11"

子设备可以是注册到同一个 IoT 中心的任何 IoT Edge 设备或非 IoT Edge 设备。

:::moniker-end
<!-- end 1.2 -->

如果你不知道如何在 IoT Edge 设备与 IoT 设备之间创建父子关系，请参阅[通过 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)。 对称密钥、自签名 X.509 和 CA 签名的 X.509 部分提供了示例来演示了在创建设备时如何使用 Azure 门户和 Azure CLI 来定义父子关系。 对于现有设备，可以从父或子设备的设备详细信息页声明关系。

<!-- 1.2 -->
:::moniker range="iotedge-2020-11"

如果你不知道如何在两个 IoT Edge 设备之间创建父子关系，请参阅[将下游 IoT Edge 设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-iot-edge-device.md)。

:::moniker-end
<!-- end 1.2 -->

### <a name="set-up-the-parent-device-as-a-gateway"></a>将父设备设为网关

可将父/子关系视为一种透明网关，其中的子设备在 IoT 中心具有自身的标识，但通过其父设备在云中通信。 若要安全通信，子设备需能够验证父设备来自受信任的源。 否则，第三方可能会设置恶意设备来模拟父设备并截获通信。

以下文章详细介绍了建立这种信任关系的一种方法：

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [将下游（子）设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>指定 DNS 服务器

为了提高可靠性，强烈建议指定在环境中使用的 DNS 服务器地址。 若要为 IoT Edge 设置 DNS服务器，请参阅故障排除文章中 [Edge 代理模块不断报告“空配置文件”且设备上没有模块启动](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)的解决方案。

## <a name="optional-offline-settings"></a>可选脱机设置

如果设备已脱机，IoT Edge 父设备将一直存储所有设备到云的消息，直到重新建立连接为止。 IoT Edge 中心模块将管理脱机消息的存储和转发。 对于长时间脱机的设备，可以通过配置两项 IoT Edge 中心设置来优化性能。

首先，增大活动设置的时间，以便在设备重新建立连接之前，IoT Edge 中心将消息保留足够长的时间。 然后，为消息存储添加更多磁盘空间。

### <a name="time-to-live"></a>生存时间

生存时间设置是指在过期之前消息可以等待传递的时间量（以秒为单位）。 默认为 7200 秒（两个小时）。 此最大值仅受整数变量的最大值（约为 20 亿）限制。

此设置是 IoT Edge 中心的所需属性，它存储在模块孪生中。 可以在 Azure 门户中或直接在部署清单中进行配置。

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>系统模块的主机存储

默认情况下，消息和模块状态信息存储在 IoT Edge 中心的本地容器文件系统中。 为了提高可靠性，尤其是在脱机操作时，还可以在主机 IoT Edge 设备上专门使用存储。 有关详细信息，请参阅[向模块授予对设备本地存储的访问权限](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>后续步骤

详细了解如何为父/子设备连接设置透明网关：

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
