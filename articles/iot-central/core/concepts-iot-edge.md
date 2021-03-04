---
title: Azure IoT Edge 和 Azure IoT Central |Microsoft Docs
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
ms.openlocfilehash: 1a464b9e039f256fae52c32d828b1ec39a20a228
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123269"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>将 Azure IoT Edge 设备连接到 Azure IoT Central 应用程序

本文适用于解决方案构建者和设备开发人员。 

Azure IoT Edge 将云分析和自定义业务逻辑移动到设备上，使你的组织能够将精力集中在业务洞察力上，而不是数据管理。 通过将业务逻辑打包到标准容器来扩展 IoT 解决方案，将这些容器部署到设备，并从云中监视它们。

本文介绍：

* IoT Edge 设备如何连接到 IoT Central 应用程序。
* 如何使用 IoT Central 管理 IoT Edge 设备。

若要了解有关 IoT Edge 的详细信息，请参阅 [什么是 Azure IoT Edge？](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge 包含三个组件：

* IoT Edge 模块是容器，可以运行 Azure 服务、合作伙伴服务或者你自己的代码。 这些模块部署到 IoT Edge 设备，在设备上以本地方式运行。 若要了解详细信息，请参阅 [了解 Azure IoT Edge 模块](../../iot-edge/iot-edge-modules.md)。
* *IoT Edge 运行时* 在每个 IoT Edge 设备上运行，并管理部署到每个设备的模块。 运行时包括两个 IoT Edge 模块： *IoT Edge 代理* 和 *IoT Edge 中心*。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](../../iot-edge/iot-edge-runtime.md)。
* 可以通过基于云的界面远程监视和管理 IoT Edge 设备。 IoT Central 是云接口的示例。

IoT Edge 设备可以是：

* 由模块组成的独立设备。
* 一个 *网关设备*，其中的下游设备连接到该设备。

## <a name="iot-edge-as-a-gateway"></a>作为网关 IoT Edge

IoT Edge 设备可以作为网关，在网络上的其他下游设备与 IoT Central 应用程序之间提供连接。

有两种网关模式：

* 在 *透明网关* 模式下，IoT Edge 集线器模块的行为类似于 IoT Central 并处理 IoT Central 中注册的设备的连接。 消息从下游设备传递到 IoT Central，就好像它们之间没有任何网关。

* 在 *翻译网关* 模式下，无法连接到 IoT Central 的设备会改为连接到自定义 IoT Edge 模块。 IoT Edge 设备中的模块处理传入的下游设备消息，然后将其转发到 IoT Central。

透明和平移网关模式并不相互排斥。 单个 IoT Edge 设备既可以充当透明网关，也可以充当转换网关。

若要详细了解 IoT Edge 网关模式，请参阅 [如何将 IoT Edge 设备用作网关](../../iot-edge/iot-edge-as-gateway.md)。

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>下游设备与网关和模块的关系

下游设备可以通过 *IoT Edge 中心*  模块连接到 IoT Edge 网关设备。 在此方案中，IoT Edge 设备是透明网关：

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="透明网关关系图" border="false":::

下游设备也可通过自定义模块连接到 IoT Edge 网关设备。 在以下方案中，下游设备通过 *Modbus* 自定义模块进行连接。 在此方案中，IoT Edge 设备是一个翻译网关：

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="自定义模块连接关系图" border="false":::

下图显示了通过这两种类型的模块连接到 IoT Edge 网关设备的情况。 在此方案中，IoT Edge 设备既是透明网关，又是转换网关：

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="使用这两个连接模块进行连接的关系图" border="false":::

下游设备可以通过多个自定义模块连接到 IoT Edge 网关设备。 下图显示了通过 Modbus 自定义模块、BLE 自定义模块和 *IoT Edge 中心*  模块连接的下游设备：

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="使用多个自定义模块进行连接的关系图" border="false":::

<!-- To do: add link to how to configure gateway article? -->

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge 设备和 IoT Central

IoT Edge 设备可以使用 *共享访问签名* 令牌或 x.509 证书对 IoT Central 进行身份验证。 您可以在 IoT Central 中手动注册 IoT Edge 设备，才能首次连接，或使用设备预配服务来处理注册。 有关详细信息，请参阅[连接到 Azure IoT Central](concepts-get-connected.md)。

IoT Central 使用 [设备模板](concepts-device-templates.md) 来定义 IoT Central 与设备进行交互的方式。 例如，设备模板指定：

* 设备发送的遥测和属性的类型，以便 IoT Central 可以对其进行解释，并创建可视化效果。
* 设备响应的命令，以便 IoT Central 可以显示用于调用命令的操作员的 UI。

IoT Edge 设备可以发送遥测，同步属性值，并以与标准设备相同的方式响应命令。 因此，IoT Edge 设备需要 IoT Central 中的设备模板。

### <a name="iot-edge-device-templates"></a>IoT Edge 设备模板

IoT Central 设备模板使用模型来描述设备的功能。 下图显示了 IoT Edge 设备的模型的结构：

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="连接到 IoT Central IoT Edge 设备的模型结构" border="false":::

IoT Central 对 IoT Edge 设备建模，如下所示：

* 每个 IoT Edge 设备模板都有一个功能模型。
* 对于部署清单中列出的每个自定义模块，都将生成一个模块功能模型。
* 在每个模块功能模型和设备模型之间建立关系。
* 模块功能模型实现一个或多个模块接口。
* 每个模块接口都包含遥测、属性和命令。

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge 部署清单和 IoT Central 设备模板

在 IoT Edge 中，你将以模块的形式部署和管理业务逻辑。 IoT Edge 模块是 IoT Edge 管理的计算的最小单位，可包含 azure 流分析等 Azure 服务，或者你自己的特定于解决方案的代码。

IoT Edge *部署清单* 列出要在设备上部署的 IoT Edge 模块以及如何对其进行配置。 若要了解详细信息，请参阅 [了解如何部署模块和在 IoT Edge 中建立路由](../../iot-edge/module-composition.md)。

在 Azure IoT Central 中，会导入部署清单，为 IoT Edge 设备创建设备模板。

下面的代码片段演示 IoT Edge 部署清单：

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

* 有三个模块。 每个部署清单中都存在的 *IoT Edge 代理* 和 *IoT Edge 中心* 系统模块。 自定义 **SimulatedTemperatureSensor** 模块。
* 公用模块映像从 Azure 容器注册表存储库中提取，无需任何凭据即可进行连接。 对于专用模块映像，请设置要在 " `registryCredentials` *IoT Edge 代理* " 模块的设置中使用的容器注册表凭据。
* 自定义 **SimulatedTemperatureSensor** 模块有两个属性 `"SendData": true` 和 `"SendInterval": 10` 。

将此部署清单导入 IoT Central 应用程序时，它将生成以下设备模板：

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="显示从部署清单创建的设备模板的屏幕截图。":::

在前面的屏幕截图中，可以看到：

* 名为 **SimulatedTemperatureSensor** 的模块。 模板中未显示 *IoT Edge 代理* 和 *IoT Edge 中心* 系统模块。
* 称为 **管理** 的接口，它包含两个称为 **a** 和 **SendInterval** 的可写属性。

部署清单不包含 **SimulatedTemperatureSensor** 模块发送的遥测或它所响应的命令的相关信息。 在发布设备模板之前，请将这些定义手动添加到设备模板中。

若要了解详细信息，请参阅 [教程：将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序](tutorial-add-edge-as-leaf-device.md)。

### <a name="update-a-deployment-manifest"></a>更新部署清单

如果创建新 [版本](howto-version-device-template.md) 的设备模板，则可以使用新版本替换部署清单：

替换部署清单时，任何连接的 IoT Edge 设备都会下载新的清单并更新其模块。 但是，IoT Central 不会更新设备模板中的接口以及对模块配置所做的任何更改。 例如，如果将上一代码片段中显示的清单替换为以下清单，则不会在设备模板的 **管理** 界面中自动看到 **SendUnits** 属性。 手动将新属性添加到 **管理** 界面，以便 IoT Central 识别它：

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

若要了解可以在何处运行 IoT Edge 运行时，请参阅 [Azure IoT Edge 支持的系统](../../iot-edge/support.md)。

你还可以在以下环境中安装 IoT Edge 运行时：

* [安装或卸载适用于 Linux 的 Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md)
* [在 Windows 设备上安装和预配 Azure IoT Edge for Linux（预览版）](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [在 Azure 中的 Ubuntu 虚拟机上运行 Azure IoT Edge](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge 网关设备

如果选择 IoT Edge 设备作为网关设备，则可以为要连接到网关设备的设备将下游关系添加到设备型号。

<!-- TODO - add link to Edge Gateway how-to -->

## <a name="next-steps"></a>后续步骤

如果你是一名设备开发人员，建议下一步是了解如何 [开发自己的 IoT Edge 模块](../../iot-edge/module-development.md)。
