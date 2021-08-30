---
title: Azure IoT Edge 和 Azure IoT Central | Microsoft Docs
description: 了解如何在 IoT Central 应用程序中使用 Azure IoT Edge。
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 0b1cf7d0dbf7456d01f6530355e6943c8ead54db
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014922"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>将 Azure IoT Edge 设备连接到 Azure IoT Central 应用程序

Azure IoT Edge 将云分析和自定义业务逻辑移到设备，这样你的组织就可以专注于业务见解而非数据管理。 通过将业务逻辑打包到标准容器中，横向扩展 IoT 解决方案，然后将这些容器部署到设备，并从云中监视所有这些设备。

本文介绍：

* 如何将 IoT Edge 设备连接到 IoT Central 应用程序。
* 如何使用 IoT Central 管理 IoT Edge 设备。

若要了解有关 IoT Edge 的详细信息，请参阅[什么是 Azure IoT Edge？](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge 包含三个组件：

* IoT Edge 模块是容器，可以运行 Azure 服务、合作伙伴服务或者你自己的代码。 这些模块部署到 IoT Edge 设备，在设备上以本地方式运行。 有关详细信息，请参阅[了解 Azure IoT Edge 模块](../../iot-edge/iot-edge-modules.md)。
* IoT Edge 运行时在每个 IoT Edge 设备上运行，并管理部署到每个设备的模块。 运行时包括两个 IoT Edge 模块：IoT Edge 代理和 IoT Edge 中心。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](../../iot-edge/iot-edge-runtime.md)。
* 可以通过基于云的界面远程监视和管理 IoT Edge 设备。 IoT Central 是云界面的一个示例。

IoT Edge 设备可以为：

* 由模块组成的独立设备。
* 连接了下游设备的网关设备。

## <a name="iot-edge-as-a-gateway"></a>使用 IoT Edge 作为网关

IoT Edge 设备可以充当网关，提供网络上的其他下游设备与 IoT Central 应用程序之间的连接。

有两种网关模式：

* 在“透明网关”模式下，IoT Edge 中心模块的行为类似于 IoT Central，并处理在 IoT Central 中注册的设备的连接。 消息从下游设备传递到 IoT Central，就好像它们之间没有任何网关。

    > [!NOTE]
    > IoT Central 目前不支持将 IoT Edge 设备作为下游设备连接到 IoT Edge 透明网关。 这是因为连接到 IoT Central 的所有设备均使用设备预配服务 (DPS) 进行预配，而 DPS 不支持嵌套的 IoT Edge 方案。

* 在“转换网关”模式下，无法自行连接到 IoT Central 的设备会改为连接到自定义 IoT Edge 模块。 IoT Edge 设备中的模块处理传入的下游设备消息，然后将其转发到 IoT Central。

透明和转换网关模式并不相互排斥。 单个 IoT Edge 设备既可以充当透明网关，也可以充当转换网关。

若要了解有关 IoT Edge 网管模式的详细信息，请参阅[如何将 IoT Edge 设备用作网关](../../iot-edge/iot-edge-as-gateway.md)。

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>下游设备与网关和模块的关系

下游设备可通过 IoT Edge 中心模块连接到 IoT Edge 网关设备。 在此方案中，IoT Edge 设备是透明网关：

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="透明网关关系图" border="false":::

下游设备也可通过自定义模块连接到 IoT Edge 网关设备。 在以下方案中，下游设备通过 Modbus 自定义模块进行连接。 在此方案中，IoT Edge 设备是转换网关：

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="自定义模块连接关系图" border="false":::

下图显示了通过两种类型的模块与 IoT Edge 网关设备的连接。 在此方案中，IoT Edge 设备既是透明网关，又是转换网关：

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="通过两种连接模块连接的关系图" border="false":::

下游设备可通过多个自定义模块连接到 IoT Edge 网关设备。 下图显示了通过 Modbus 自定义模块、BLE 自定义模块和 IoT Edge 中心模块连接的下游设备：

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="通过多个自定义模块连接的关系图" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge 设备和 IoT Central

IoT Edge 设备可以使用“共享访问签名”令牌或 X.509 证书对 IoT Central 进行身份验证。 你可以在首次连接之前先在 IoT Central 中手动注册 IoT Edge 设备，或使用设备预配服务来处理注册。 有关详细信息，请参阅[连接到 Azure IoT Central](concepts-get-connected.md)。

IoT Central 使用[设备模板](concepts-device-templates.md)来定义 IoT Central 与设备进行交互的方式。 例如，设备模板指定：

* 设备发送的遥测和属性的类型，以便 IoT Central 可以对其进行解释，并创建可视化效果。
* 设备响应的命令，以便 IoT Central 可以显示用于调用命令的运算符的 UI。

IoT Edge 设备可以发送遥测，同步属性值，并以与标准设备相同的方式响应命令。 因此，IoT Edge 设备需要 IoT Central 中的设备模板。

### <a name="iot-edge-device-templates"></a>IoT Edge 设备模板

IoT Central 设备模板使用模型来描述设备的功能。 下图显示了 IoT Edge 设备的模型的结构：

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="连接到 IoT Central 的 IoT Edge 设备的模型结构" border="false":::

IoT Central 对 IoT Edge 设备建模的方式如下：

* 每个 IoT Edge 设备模板都具有一个功能模型。
* 对于部署清单中列出的每个自定义模块，都会生成模块功能模型。
* 在每个模块功能模型和设备模型之间建立关系。
* 模块功能模型可实现一个或多个模块接口。
* 每个模块接口都包含遥测、属性和命令。

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge 部署清单和 IoT Central 设备模板

在 IoT Edge 中，你以模块的形式来部署和管理业务逻辑。 IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务（如 Azure 流分析）或你自己特定于解决方案的代码。

IoT Edge 部署清单列出要在设备上部署的 IoT Edge 模块以及如何对其进行配置的信息。 若要了解详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](../../iot-edge/module-composition.md)。

在 Azure IoT Central 中，导入部署清单，为 IoT Edge 设备创建设备模板。

下面的代码片段显示 IoT Edge 部署清单的示例：

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

在前面的代码片段中，可以看到：

* 有三种模块。 每个部署清单中都存在的 IoT Edge 代理和 IoT Edge 中心系统模块。 自定义 SimulatedTemperatureSensor 模块。
* 从无需任何凭据即可连接的 Azure 容器注册表存储库中拉取公用模块映像。 对于专用模块映像，请为 IoT Edge 代理模块设置要在 `registryCredentials` 设置中使用的容器注册表凭据。
* 自定义 SimulatedTemperatureSensor 模块有两个属性：`"SendData": true` 和 `"SendInterval": 10`。

将此部署清单导入 IoT Central 应用程序时，它将生成以下设备模板：

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="显示从部署清单创建的设备模板的屏幕截图。":::

在上一屏幕截图中，可以看到：

* 名为 SimulatedTemperatureSensor 的模块。 模板中未显示 IoT Edge 代理和 IoT Edge 中心系统模块。
* 一个称为“管理”的界面，它包含两个可写属性：SendData 和 SendInterval。

部署清单不包含 SimulatedTemperatureSensor 模块发送的遥测或它所响应的命令的相关信息。 在发布之前，请将这些定义手动添加到设备模板中。

若要了解详细信息，请参阅[教程：将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序](tutorial-add-edge-as-leaf-device.md)。

### <a name="update-a-deployment-manifest"></a>更新部署清单

替换部署清单时，任何连接的 IoT Edge 设备都会下载新的清单并更新其模块。 但是，IoT Central 不会使用对模块配置所做的任何更改更新设备模板中的接口。 例如，如果将上一代码片段中显示的清单替换为以下清单，则不会在设备模板的“管理”界面中自动看到 SendUnits 属性。 手动将新属性添加到“管理”界面，以便 IoT Central 识别它：

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>部署 IoT Edge 运行时

若要了解可以运行 IoT Edge 运行时的环境，请参阅 [Azure IoT Edge 支持的系统](../../iot-edge/support.md)。

你还可以在以下环境中安装 IoT Edge 运行时：

* [安装或卸载适用于 Linux 的 Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md)
* [在 Windows 设备上安装和预配 Azure IoT Edge for Linux（预览版）](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [在 Azure 中在 Ubuntu 虚拟机上运行 Azure IoT Edge](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge 网关设备

如果选择 IoT Edge 设备作为网关设备，则可以将下游关系添加到将连接到网关设备的设备的设备模型。

若要了解详细信息，请参阅[如何通过 IoT Edge 透明网关连接设备](how-to-connect-iot-edge-transparent-gateway.md)。

## <a name="monitor-your-iot-edge-devices"></a>监视 IoT Edge 设备

若要了解如何使用 Azure Monitor 和内置指标集成远程监视 IoT Edge 队列，请参阅[收集和传输指标](../../iot-edge/how-to-collect-and-transport-metrics.md)。

## <a name="next-steps"></a>后续步骤

建议的下一步是了解如何[开发自己的 IoT Edge 模块](../../iot-edge/module-development.md)。
