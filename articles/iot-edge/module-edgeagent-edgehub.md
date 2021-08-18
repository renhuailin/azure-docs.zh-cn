---
title: 代理和中心模块孪生的属性 - Azure IoT Edge
description: 查看 edgeAgent 和 edgeHub 模块孪生的特定属性及其值
author: kgremban
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 18a1114c3253a9a8debb9289a3749df10ade7d54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726510"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge 代理和 IoT Edge 中心模块孪生的属性

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 代理和 IoT Edge 中心是构成 IoT Edge 运行时的两个模块。 有关每个运行时模块的职责的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文提供运行时模块孪生的所需属性和报告属性。 有关如何在 IoT Edge 设备上部署模块的详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。

模块孪生包括：

* **所需的属性**。 解决方案后端可设置所需的属性，而模块可以读取这些属性。 此外，模块还可以收到有关所需的属性发生更改的通知。 将所需的属性与报告的属性结合使用，可以同步模块配置或条件。

* **报告的属性**。 模块可以设置报告的属性，而解决方案后端可以读取和查询这些属性。 报告的属性与所需的属性结合使用，可同步模块配置或状况。

## <a name="edgeagent-desired-properties"></a>EdgeAgent 所需属性

IoT Edge 代理的模块孪生被称为 `$edgeAgent`，用于协调在设备上运行的与在 IoT 中心运行的 IoT Edge 代理之间的通信。 在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。

| 属性 | 说明 | 必须 |
| -------- | ----------- | -------- |
| schemaVersion | “1.0”或“1.1”。 版本 1.1 随 IoT Edge 版本 1.0.10 引入，推荐使用。 | 是 |
| runtime.type | 必须为“docker” | 是 |
| runtime.settings.minDockerVersion | 设置为此部署清单所需的最小 Docker 版本 | 是 |
| runtime.settings.loggingOptions | 字符串化的 JSON 包含 IoT Edge 代理容器的日志记录选项。 [Docker 日志记录选项](https://docs.docker.com/engine/admin/logging/overview/) | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.username | 容器注册表的用户名。 对于 Azure 容器注册表，用户名通常是注册表名称。<br><br> 注册表凭据对于任何专用模块映像都是必需的。 | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.password | 容器注册表的密码。 | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.address | 容器注册表的地址。 对于 Azure 容器注册表，地址通常为 {registry name}.azurecr.io。 | 否 |  
| systemModules.edgeAgent.type | 必须为“docker” | 是 |
| systemModules.edgeAgent.settings.image | IoT Edge 代理的映像 URI。 目前，IoT Edge 代理不能自行更新。 | 是 |
| systemModules.edgeAgent.settings<br>.createOptions | 字符串化的 JSON 包含 IoT Edge 代理容器的创建选项。 [Docker 创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| systemModules.edgeAgent.configuration.id | 部署此模块的部署 ID。 | 此属性是在使用部署来应用清单时由 IoT 中心设置。 不是部署清单的一部分。 |
| systemModules.edgeHub.type | 必须为“docker” | 是 |
| systemModules.edgeHub.status | 必须为“running” | 是 |
| systemModules.edgeHub.restartPolicy | 必须为“always” | 是 |
| systemModules.edgeHub.startupOrder | 一个整数值，指示某个模块在启动顺序中所处的位置。 0 表示第一个，最大整数 (4294967295) 表示最后一个。 如果未提供值，则默认值为最大整数。  | 否 |
| systemModules.edgeHub.settings.image | IoT Edge 中心的映像 URI。 | 是 |
| systemModules.edgeHub.settings<br>.createOptions | 字符串化的 JSON 包含 IoT Edge 中心容器的创建选项。 [Docker 创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| systemModules.edgeHub.configuration.id | 部署此模块的部署 ID。 | 此属性是在使用部署来应用清单时由 IoT 中心设置。 不是部署清单的一部分。 |
| modules.{moduleId}.version | 用户定义的字符串，表示此模块的版本。 | 是 |
| modules.{moduleId}.type | 必须为“docker” | 是 |
| modules.{moduleId}.status | {"running" \| "stopped"} | 是 |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | 是 |
| modules.{moduleId}.startupOrder | 一个整数值，指示某个模块在启动顺序中所处的位置。 0 表示第一个，最大整数 (4294967295) 表示最后一个。 如果未提供值，则默认值为最大整数。  | 否 |
| modules.{moduleId}.imagePullPolicy | {"on-create" \| "never"} | 否 |
| modules.{moduleId}.env | 要传递给模块的环境变量的列表。 采用格式 `"<name>": {"value": "<value>"}` | 否 |
| modules.{moduleId}.settings.image | 模块映像的 URI。 | 是 |
| modules.{moduleId}.settings.createOptions | 字符串化的 JSON 包含模块容器的创建选项。 [Docker 创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| modules.{moduleId}.configuration.id | 部署此模块的部署 ID。 | 此属性是在使用部署来应用清单时由 IoT 中心设置。 不是部署清单的一部分。 |

## <a name="edgeagent-reported-properties"></a>EdgeAgent 报告属性

IoT Edge 代理报告属性包括三个主要信息：

1. 上一次出现的所需属性的应用程序状态；
2. IoT Edge 代理报告的目前在设备上运行的模块的状态；以及
3. 当前在设备上运行的所需属性的副本。

当前所需属性的副本有助于判断设备是应用了最新部署，还是仍在运行以前的部署清单。

> [!NOTE]
> IoT Edge 代理报告的属性非常有用，因为可以使用 [IoT 中心查询语言](../iot-hub/iot-hub-devguide-query-language.md)对其进行查询，进而调查大规模部署的状态。 若要详细了解如何使用 IoT Edge 代理属性来获取状态，请参阅[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)。

下表不包括从所需属性中复制的信息。

| 属性 | 说明 |
| -------- | ----------- |
| lastDesiredVersion | 此整数指的是由 IoT Edge 代理处理的所需属性的最后一个版本。 |
| lastDesiredStatus.code | 此状态代码指的是 IoT Edge 代理上次看到的所需属性。 允许的值：`200` 成功、`400` 配置无效、`412` 架构版本无效、`417` 所需属性为空、`500` 失败 |
| lastDesiredStatus.description | 状态的文本说明 |
| configurationHealth.{deploymentId}.health | 如果部署 {deploymentId} 设置的所有模块的运行时状态为 `running` 或 `stopped`，则为 `healthy`，否则为 `unhealthy` |
| runtime.platform.OS | 报告在设备上运行的 OS |
| runtime.platform.architecture | 报告设备上的 CPU 体系结构 |
| systemModules.edgeAgent.runtimeStatus | IoT Edge 代理的报告状态：{"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | IoT Edge 代理报告状态的文本说明。 |
| systemModules.edgeHub.runtimeStatus | IoT Edge 中心的状态：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 运行不正常情况下 IoT Edge 中心状态的文本说明。 |
| systemModules.edgeHub.exitCode | 由 IoT Edge 中心容器报告的退出代码（如果容器存在） |
| systemModules.edgeHub.startTimeUtc | 上一次启动 IoT Edge 中心的时间 |
| systemModules.edgeHub.lastExitTimeUtc | IoT Edge 中心上一次退出的时间 |
| systemModules.edgeHub.lastRestartTimeUtc | 上一次重启 IoT Edge 中心的时间 |
| systemModules.edgeHub.restartCount | 作为重启策略的一部分，此模块重新启动的次数。 |
| modules.{moduleId}.runtimeStatus | 模块的状态：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 运行不正常情况下模块状态的文本说明。 |
| modules.{moduleId}.exitCode | 由模块容器报告的退出代码（如果容器存在） |
| modules.{moduleId}.startTimeUtc | 模块的上次启动时间 |
| modules.{moduleId}.lastExitTimeUtc | 模块的上次退出时间 |
| modules.{moduleId}.lastRestartTimeUtc | 模块的上次重启时间 |
| modules.{moduleId}.restartCount | 作为重启策略的一部分，此模块重新启动的次数。 |

## <a name="edgehub-desired-properties"></a>EdgeHub 所需属性

IoT Edge 中心的模块孪生被称为 `$edgeHub`，用于协调在设备上运行的与在 IoT 中心运行的 IoT Edge 中心之间的通信。 在特定设备上将部署清单作为单一设备或规模部署的一部分应用时，将会设置所需的属性。

| 属性 | 说明 | 部署清单中的必备项 |
| -------- | ----------- | -------- |
| schemaVersion | “1.0”或“1.1”。 版本 1.1 随 IoT Edge 版本 1.0.10 引入，推荐使用。 | 是 |
| routes.{routeName} | 表示 IoT Edge 中心路由的字符串。 有关详细信息，请参阅[声明路由](module-composition.md#declare-routes)。 | `routes` 元素可以存在但为空。 |
| storeAndForwardConfiguration.timeToLiveSecs | 在断开连接路由终结点（无论是 IoT 中心还是本地模块）的情况下，IoT Edge 中心保留消息的时长（以秒为单位）。 该值可以是任何正整数。 | 是 |

## <a name="edgehub-reported-properties"></a>EdgeHub 报告属性

| 属性 | 说明 |
| -------- | ----------- |
| lastDesiredVersion | 此整数指的是由 IoT Edge 中心处理的所需属性的最后一个版本。 |
| lastDesiredStatus.code | 该状态代码指的是 IoT Edge 中心上次看到的所需属性。 允许的值：`200` 成功、`400` 配置无效、`500` 失败 |
| lastDesiredStatus.description | 状态的文本说明。 |
| clients.{device or moduleId}.status | 此设备或模块的连接状态。 可能值：{"connected" \| "disconnected"}。 仅模块标识可以处于断开连接状态。 仅在连接时才会显示连接到 IoT Edge 中心的下游设备。 |
| clients.{device or moduleId}.lastConnectTime | 上次连接设备或模块的时间。 |
| clients.{device or moduleId}.lastDisconnectTime | 上次断开连接设备或模块的时间。 |

## <a name="next-steps"></a>后续步骤

若要了解如何使用这些属性构建部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。
