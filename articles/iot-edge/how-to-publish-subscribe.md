---
title: 使用 Azure IoT Edge 发布和订阅 | Microsoft Docs
description: 使用 IoT Edge MQTT 中转站发布和订阅消息
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 899a6c1f0e5f2eb7441ea2035cdb12bed13a4b85
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457309"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>使用 Azure IoT Edge 发布和订阅（预览版）

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

可以使用 Azure IoT Edge MQTT 中转站发布和订阅消息。 本文介绍如何连接到此代理，如何针对用户定义的主题发布和订阅消息，以及如何使用 IoT 中心消息传送基元。 IoT Edge MQTT 中转站内置于 IoT Edge 中心。 有关详细信息，请参阅 [IoT Edge 中心的中转站功能](iot-edge-runtime.md)。

> [!NOTE]
> IoT Edge MQTT 中转站目前提供公开预览版。

## <a name="pre-requisites"></a>先决条件

- 含有效订阅的 Azure 帐户
- [安装了 `azure-iot` CLI 扩展的 Azure CLI](/cli/azure/)。 有关详细信息，请参阅 [Azure Azure CLI 的 Azure IoT 扩展安装步骤](/cli/azure/azure-cli-reference-for-iot)。
- SKU 的 IoT 中心（F1、S1、S2 或 S3）。
- 具有版本为 1.2 或更高的 IoT Edge 设备，包括已部署的版本为 1.2 或更高的 edgeAgent 和 edgeHub 模块，其中的 MQTT 中转站功能处于启用状态，edgeHub 端口 1883 绑定到主机，可启用非 TLS 连接。 可以按照[此文所述步骤](how-to-install-iot-edge-ubuntuvm.md)，在 Azure VM 中自动部署 IoT Edge 1.2。 由于 IoT Edge MQTT 中转站当前为公共预览版，因此你还需要在 edgeHub 模块上将以下环境变量设置为 true 以启用 MQTT 中转站：

   | 名称 | Value |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

   若要快速创建一个满足这些条件并包含一个有关 `test_topic` 的开放授权策略的 IoT Edge 部署，可以使用附录中的此[示例部署清单](#appendix---sample-deployment-manifest)：

   - 将部署文件保存在工作文件夹中

   - 使用以下 Azure CLI 命令将此部署应用到 IoT Edge 设备。 有关此命令的详细信息，请参阅[使用 Azure CLI 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)。

    ```azurecli
    az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [deployment file path]
    ```

- 已在 IoT Edge 设备上安装 Mosquitto 客户端。 本文使用常用的 Mosquitto 客户端 [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) 和 [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html)。 可以改用其他 MQTT 客户端。 若要在 Ubuntu 设备上安装 Mosquitto 客户端，请运行以下命令：

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    请勿安装 Mosquitto 服务器，因为它可能会导致 MQTT 端口（8883 和 1883）阻塞并与 IoT Edge 中心发生冲突。

## <a name="connect-to-iot-edge-hub"></a>连接到 IoT Edge 中心

连接到 IoT Edge 中心遵循与[使用通用 MQTT 客户端连接到 IoT 中心一文](../iot-hub/iot-hub-mqtt-support.md)或 [IoT Edge 中心的概念说明一文](iot-edge-runtime.md)中描述的相同步骤。 这些步骤包括：

1. 或者，MQTT 客户端使用传输层安全性 (TLS) 与 IoT Edge 中心建立安全连接
2. MQTT 客户端向 IoT Edge 中心进行自身验证
3. IoT Edge 中心根据其授权策略对 MQTT 客户端进行授权

### <a name="secure-connection-tls"></a>安全连接 (TLS)

传输层安全性 (TLS) 用于在客户端和 IoT Edge 中心之间建立加密通信。

若要禁用 TLS，请使用端口 1883 (MQTT) 并将 edgeHub 容器绑定到端口 1883。

若要启用 TLS，如果客户端通过端口 8883 (MQTTS) 连接到 MQTT 代理，则将会启动一个 TLS 通道。 中转站发送客户端需要验证的证书链。 为了验证证书链，MQTT 中转站的根证书必须作为受信任的证书安装在客户端上。 如果根证书不受信任，则 MQTT 中转站将拒绝客户端库，并出现证书验证错误。 在客户端上安装中转站的该根证书的步骤与[透明网关](how-to-create-transparent-gateway.md)案例中的步骤相同，并在[准备下游设备](how-to-connect-downstream-device.md#prepare-a-downstream-device)文档中进行了描述。

### <a name="authentication"></a>身份验证

若要使 MQTT 客户端进行自身身份验证，它首先需要向 MQTT 代理发送 CONNECT 数据包，以便用它的名称来启动连接。 此数据包提供三条身份验证信息：`client identifier`、`username` 和 `password`：

- `client identifier` 字段是 IoT 中心内设备的名称或模块名称。 它使用以下语法：

  - 对于设备：`<device_name>`

  - 对于模块：`<device_name>/<module_name>`

   为了连接到 MQTT 中转站，必须在 IoT 中心中注册设备或模块。

   该代理不允许来自使用相同凭据的多个客户端的连接。 如果第二个客户端使用相同的凭据进行连接，则中转站将断开已连接的客户端。

- `username` 字段使用以下语法从设备/模块名称和设备所属的 IoTHub 名称派生：

  - 对于设备：`<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - 对于模块：`<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- CONNECT 数据包的 `password` 字段取决于身份验证模式：

  - 在使用[对称密钥身份验证](how-to-authenticate-downstream-device.md#symmetric-key-authentication)时，`password` 字段是 SAS 令牌。
  - 在使用 [X.509 自签名身份验证](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)时，`password` 字段不存在。 在此身份验证模式下，需要一个 TLS 通道。 客户端需要连接到端口 8883 以建立 TLS 连接。 在 TLS 握手期间，MQTT 中转站请求客户端证书。 此证书用于验证客户端的身份，因此在发送 CONNECT 数据包时不需要 `password` 字段。 同时发送客户端证书和密码字段将导致错误，且连接将关闭。 MQTT 库和 TLS 客户端库通常可以在启动连接时发送客户端证书。 你可以在 [使用 X509 证书进行客户端身份验证](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)部分中查看分步示例。

通过 IoT Edge 部署的模块使用[对称密钥身份验证](how-to-authenticate-downstream-device.md#symmetric-key-authentication)，并且可以调用本地 [IoT Edge 工作负载 API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) 以编程方式获取 SAS 令牌，即使脱机时也是如此。

### <a name="authorization"></a>授权

在 MQTT 客户端向 IoT Edge 中心进行身份验证后，它需要获得授权才能进行连接。 连接后，它需要获得授权才可针对特定主题进行发布或订阅。 这些授权由 IoT Edge 中心根据其授权策略授予。 授权策略是一组以 JSON 结构表示的语句，通过其孪生体发送到 IoT Edge 中心。 编辑 IoT Edge 中心孪生体以配置其授权策略。

> [!NOTE]
> 对于公共预览版，只有 Azure CLI 支持包含 MQTT 代理授权策略的部署。 Azure 门户目前不支持编辑 IoT Edge 中心孪生体及其授权策略。

每个授权策略语句由 `identities`、`allow` 或 `deny` 效果、`operations` 和 `resources` 的组合构成：

- `identities` 描述策略的主题。 它必须映射到客户端在其 CONNECT 数据包中发送的 `username`，并采用 `<iot_hub_name>.azure-devices.net/<device_name>` 或 `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>` 格式。
- `allow` 或 `deny` 效果定义是允许还是拒绝操作。
- `operations` 定义要授权的操作。 `mqtt:connect`、`mqtt:publish` 和 `mqtt:subscribe` 是目前支持的三种操作。
- `resources` 定义策略的对象。 它可以是主题，也可以是用 [MQTT 通配符](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)定义的主题模式。

下面是授权策略的示例，它明确不允许“rogue_client”客户端进行连接，允许任何 Azure IoT 客户端进行连接，并允许“sensor_1”发布到主题 `events/alerts`。

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "<iot_hub_name>.azure-devices.net/rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "<iot_hub_name>.azure-devices.net/sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

在编写授权策略时要记住以下几点：

- 它需要 `$edgeHub` 孪生体架构版本 1.2
- 默认情况下，所有操作都将被拒绝。
- 授权语句会按照它们在 JSON 定义中出现的顺序接受评估。 它首先查看 `identities`，然后选择与请求匹配的第一个允许或拒绝语句。 如果允许和拒绝语句之间发生冲突，则以拒绝语句为准。
- 授权策略中可以使用多个变量（例如替换）：

  - `{{iot:identity}}` 表示当前连接的客户端的标识。 例如，`<iot_hub_name>.azure-devices.net/myDevice` 等设备标识或 `<iot_hub_name>.azure-devices.net/myEdgeDevice/SampleModule` 等模块标识。
  - `{{iot:device_id}}` 表示当前连接的设备的标识。 例如，`myDevice` 等设备标识或 `myEdgeDevice` 等运行模块的设备标识。
  - `{{iot:module_id}}` 表示当前连接的模块的标识。 此变量对于已连接的设备为空，或者是模块标识（如 `SampleModule`）。
  - `{{iot:this_device_id}}` 表示运行授权策略的 IoT Edge 设备的标识。 例如，`myIoTEdgeDevice`。

与用户定义的主题相比，IoT 中心主题的授权处理方式略有不同。 以下是需要记住的要点：

- Azure IoT 设备或模块需要显式授权规则才能连接到 IoT Edge 中心 MQTT 中转站。 下面提供了默认的连接授权策略。
- 默认情况下，Azure IoT 设备或模块可以访问自己的 IoT 中心主题，而无需任何显式授权规则。 但是，在这种情况下，授权源于父/子关系，必须设置这些关系。 IoT Edge 模块自动设置为其 IoT Edge 设备的子项，但设备需要显式设置为其 IoT Edge 网关的子项。

以下是默认授权策略，可用于使所有 Azure IoT 设备或模块能够连接到中转站：

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

现在，你已经了解如何连接到 IoT Edge MQTT 中转站，接下来让我们依次了解如何使用它针对用户定义的主题发布和订阅消息，针对 IoT 中心主题发布和订阅消息，以及向另一个 MQTT 中转站发布和订阅消息。

## <a name="publish-and-subscribe-on-user-defined-topics"></a>针对用户定义的主题进行发布和订阅

在本文中，你将使用一个名为 sub_client 的客户端（它针对一个主题进行订阅）和另一个名为 pub_client 的客户端（它针对一个主题进行发布） 。 我们将使用[对称密钥身份验证](how-to-authenticate-downstream-device.md#symmetric-key-authentication)，但使用 [X.509 自签名身份验证](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)或 [X.509 CA 签名身份验证](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)可实现同样的操作。

### <a name="create-publisher-and-subscriber-clients"></a>创建发布服务器和订阅服务器客户端

在 IoT 中心创建两个 IoT 设备并获取其密码。 使用终端中的 Azure CLI 执行以下操作：

1. 在 IoT 中心创建两个 IoT 设备：

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name>
   ```

2. 将其父级设置为你的 IoT Edge 设备：

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity parent set --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```


3. 通过生成 SAS 令牌获取其密码：

   - 对于设备：

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

     其中 3600 是 SAS 令牌的持续时间（以秒为单位，例如 3600 = 1 小时）。

   - 对于模块：

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

     其中 3600 是 SAS 令牌的持续时间（以秒为单位，例如 3600 = 1 小时）。

4. 复制 SAS 令牌，即输出中与“sas”键对应的值。 以下是上述 Azure CLI 命令的输出示例：

   ```output
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>对发布服务器和订阅服务器客户端授权

若要对发布服务器和订阅服务器进行授权，请通过包含以下授权策略的 IoT Edge 部署来编辑 IoT Edge 中心孪生体。

>[!NOTE]
>目前，包含 MQTT 授权属性的部署只能使用 Azure CLI 应用于 IoT Edge 设备。

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>无 TLS 的对称密钥身份验证

#### <a name="subscribe"></a>订阅

通过在 IoT Edge 设备上运行以下命令，将 MQTT 客户端 sub_client 连接到 MQTT 中转站，并针对 `test_topic` 进行订阅：

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

在本例中为 `<edge_device_address>` = `localhost`，因为客户端与 IoT Edge 在同一设备上运行。

请注意，在这第一个示例中使用了未启用 TLS 的端口 1883 (MQTT)。 为此，edgeHub 端口 1883 需要通过其 create 选项绑定到主机。 在先决条件部分提供了一个示例。 下一部分中将显示另一个示例，其中使用了已启用 TLS 的端口 8883 (MQTTS)。

MQTT 客户端 sub_client 现在已启动，正在等待有关 `test_topic` 的传入消息。

#### <a name="publish"></a>发布

将 MQTT 客户端 pub_client 连接到 MQTT 中转站，并通过从另一个终端在 IoT Edge 设备上运行以下命令，针对与上面相同的 `test_topic` 发布消息：

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

在本例中为 `<edge_device_address>` = `localhost`，因为客户端与 IoT Edge 在同一设备上运行。

执行该命令时，MQTT 客户端 sub_client 接收到“hello”消息。

### <a name="symmetric-keys-authentication-with-tls"></a>带 TLS 的对称密钥身份验证

若要启用 TLS，端口必须从 1883 (MQTT) 更改为 8883 (MQTTS)，并且客户端必须具有 MQTT 中转站的根证书才能验证 MQTT 中转站发送的证书链。 这可以通过遵循[安全连接 (TLS)](#secure-connection-tls) 部分中提供的步骤来完成。

在上面的示例中，因为客户端与 MQTT 中转站在同一设备上运行，所以可以使用相同的步骤来启用 TLS，方法是：

- 将端口号从 1883 (MQTT) 更改为 8883 (MQTTS)
- 使用类似于 `--cafile /certs/certs/azure-iot-test-only.root.ca.cert.pem` 的参数将 CA 根证书传递到 mosquitto_pub 和 mosquitto_sub 客户端
- 通过传递到 mosquitto_pub 和 mosquitto_sub 客户端的主机名参数来传递在 IoT Edge 中设置的实际主机名而非 `localhost`，以便启用证书链验证

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>针对 IoT 中心主题进行发布和订阅

[Azure IoT 设备 SDK](https://github.com/Azure/azure-iot-sdks) 已经允许客户端执行 IoT 中心操作，但不允许针对用户定义的主题进行发布/订阅。 只要遵守 IoT 中心基元协议，就可以使用任何采用发布和订阅语义的 MQTT 客户端执行 IoT 中心操作。 我们将探讨这些协议的特性，了解这些协议的工作方式。

### <a name="send-telemetry-data-to-iot-hub"></a>将遥测数据发送到 IoT 中心

向 IoT 中心发送遥测数据类似于针对用户定义的主题进行发布，只不过使用的是特定的 IoT 中心主题：

- 对于设备，遥测按主题发送：`devices/<device_name>/messages/events/`
- 对于模块，遥测按主题发送：`devices/<device_name>/modules/<module_name>/messages/events/`

此外，创建一个诸如 `FROM /messages/* INTO $upstream` 的路由，将遥测从 IoT Edge MQTT 中转站发送到 IoT 中心。 若要了解有关路由的详细信息，请参阅[声明路由](module-composition.md#declare-routes)。

### <a name="get-twin"></a>获取孪生体

获取设备/模块孪生不是典型的 MQTT 模式。 客户端需要对 IoT 中心将要服务的孪生体发出请求。

若要接收孪生体，客户端需要针对一个 IoT 中心特定的主题 `$iothub/twin/res/#` 进行订阅。 此主题名称继承自 IoT 中心，所有客户端都需要针对相同的主题进行订阅。 这并不意味着设备或模块接收彼此的孪生体。 IoT 中心和 IoT Edge 中心知道哪个孪生体应在何处交付（即使所有设备都侦听相同的主题名）。

订阅完成后，客户端需要针对 IoT 中心特定的主题 `$iothub/twin/GET/?rid=<request_id>/#`（其中 `<request_id>` 是任意标识符）发布消息以请求孪生体。 然后，IoT 中心将发送响应，其中包含有关主题 `$iothub/twin/res/200/?rid=<request_id>`（客户端针对其进行订阅）的请求数据。 这就是客户端如何将其请求与响应配对。

### <a name="receive-twin-patches"></a>接收孪生体修补程序

若要接收孪生体修补程序，客户端需要针对特定的 IoT 中心主题 `$iothub/twin/PATCH/properties/desired/#` 进行订阅。 订阅完成后，客户端将收到 IoT 中心发送的关于此主题的孪生体修补程序。

### <a name="receive-direct-methods"></a>接收直接方法

接收直接方法与接收完整孪生体类似，但客户端还需要发送回有关它已收到调用的确认信息。 首先，客户端订阅 IoT 中心特殊主题 `$iothub/methods/POST/#`。 然后，在收到关于该主题的直接方法后，客户端需要从针对其接收到直接方法的子主题中提取请求标识符 `rid`，最终针对 IoT 中心特定主题 `$iothub/methods/res/200/<request_id>` 发布确认消息。

### <a name="send-direct-methods"></a>发送直接方法

发送直接方法是 HTTP 调用，因此不需要通过 MQTT 中转站。 若要将直接方法发送到 IoT 中心，请参阅[了解并调用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。 若要将直接方法本地发送到其他模块，请参阅这一 [Azure IoT C# SDK 直接方法调用示例](https://github.com/Azure/azure-iot-sdk-csharp/blob/main/iothub/device/src/ModuleClient.cs#L597)。

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>在 MQTT 中转站之间发布和订阅

为了连接两个 MQTT 代理，IoT Edge 中心会包含一个 MQTT 桥。 MQTT 桥通常用于将正在运行的 MQTT 代理连接到另一个 MQTT 代理。 通常只将一部分的本地流量推送到其他中转站。

> [!NOTE]
> IoT Edge 中心桥目前只能在嵌套的 IoT Edge 设备之间使用。 它不能用于向 IoT 中心发送数据，因为 IoT 中心不是功能齐全的 MQTT 中转站。 若要了解 IoT 中心 MQTT 中转站功能支持的详细信息，请参阅[使用 MQTT 协议与 IoT 中心通信](../iot-hub/iot-hub-mqtt-support.md)。 若要详细了解如何嵌套 IoT Edge 设备，请参阅[将下游 IoT Edge 设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)。

在嵌套配置中，IoT Edge 中心 MQTT 桥充当父 MQTT 中转站的客户端，因此必须对父 EdgeHub 设置授权规则，以允许子 EdgeHub 针对为其配置桥的特定用户定义主题进行发布和订阅。

IoT Edge MQTT 桥通过 JSON 结构进行配置，JSON 结构通过其孪生体发送到 IoT Edge 中心。 编辑 IoT Edge 中心孪生体以配置其 MQTT 桥。

> [!NOTE]
> 对于公共预览版，只有 Azure CLI 支持包含 MQTT 桥配置的部署。 Azure 门户目前不支持编辑 IoT Edge 中心孪生体及其 MQTT 桥配置。

可配置 MQTT 桥以将 IoT Edge 中心 MQTT 中转站连接到多个外部中转站。 对于每个外部中转站，需要进行以下设置：

- `endpoint` 是要连接到的远程 MQTT 中转站的地址。 当前仅支持父 IoT Edge 设备，且该设备由变量 `$upstream` 定义。
- `settings` 定义要为终结点桥接的主题。 每个终结点可以有多个设置，以下值用于配置它：
  - `direction`：`in`（针对远程中转站的主题进行订阅）或 `out`（针对远程中转站的主题进行发布）
  - `topic`：要匹配的核心主题模式。 [MQTT 通配符](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)可用于定义此模式。 在本地中转站和远程中转站上，可以将不同的前缀应用于此主题模式。
  - `outPrefix`：在远程代理上应用于 `topic` 模式的前缀。
  - `inPrefix`：在本地代理上应用于 `topic` 模式的前缀。

以下是 IoT Edge MQTT 桥配置的示例，该配置将父 IoT Edge 设备收到的所有关于主题 `alerts/#` 的消息重新发布到关于相同主题的子 IoT Edge 设备，并将子 IoT Edge 设备发送的所有关于主题 `/local/telemetry/#` 的消息重新发布到关于主题 `/remote/messages/#` 的父 IoT Edge 设备。

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```
关于 IoT Edge 中心 MQTT 桥的其他说明：
- 当使用 MQTT 中转站并且 IoT Edge 在嵌套配置（例如指定了 `parent_hostname`）中使用时，MQTT 协议将自动用作上游协议。 若要了解有关上游协议的详细信息，请参阅[云通信](iot-edge-runtime.md#cloud-communication)。 若要详细了解配套配置，请参阅[将下游 IoT Edge 设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)。

## <a name="next-steps"></a>后续步骤

[了解 IoT Edge 中心](iot-edge-runtime.md#iot-edge-hub)

## <a name="appendix---sample-deployment-manifest"></a>附录 - 示例部署清单

下面是可用于在 IoT Edge 中启用 MQTT 中转站的完整部署清单。 它部署 IoT Edge 1.2 版，该版本启用了 MQTT 中转站功能和 edgeHub 端口 1883，并有一个有关 `test_topic` 的开放授权策略。

```json
{
   "modulesContent":{
      "$edgeAgent":{
         "properties.desired":{
            "schemaVersion":"1.1",
            "runtime":{
               "type":"docker",
               "settings":{
                  "minDockerVersion":"v1.25",
                  "loggingOptions":"",
                  "registryCredentials":{
                     
                  }
               }
            },
            "systemModules":{
               "edgeAgent":{
                  "type":"docker",
                  "settings":{
                     "image":"mcr.microsoft.com/azureiotedge-agent:1.2",
                     "createOptions":"{}"
                  }
               },
               "edgeHub":{
                  "type":"docker",
                  "status":"running",
                  "restartPolicy":"always",
                  "settings":{
                     "image":"mcr.microsoft.com/azureiotedge-hub:1.2",
                     "createOptions":"{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"1883/tcp\":[{\"HostPort\":\"1883\"}]}}}"
                  },
                  "env":{
                     "experimentalFeatures__mqttBrokerEnabled":{
                        "value":"true"
                     },
                     "experimentalFeatures__enabled":{
                        "value":"true"
                     },
                     "RuntimeLogLevel":{
                        "value":"debug"
                     }
                  }
               }
            },
            "modules":{
               
            }
         }
      },
      "$edgeHub":{
         "properties.desired":{
            "schemaVersion":"1.2",
            "routes":{
               "Upstream":"FROM /messages/* INTO $upstream"
            },
            "storeAndForwardConfiguration":{
               "timeToLiveSecs":7200
            },
            "mqttBroker":{
               "authorizations":[
                  {
                     "identities":[
                        "{{iot:identity}}"
                     ],
                     "allow":[
                        {
                           "operations":[
                              "mqtt:connect"
                           ]
                        }
                     ]
                  },
                  {
                     "identities":[
                        "{{iot:identity}}"
                     ],
                     "allow":[
                        {
                           "operations":[
                              "mqtt:publish",
                              "mqtt:subscribe"
                           ],
                           "resources":[
                              "test_topic"
                           ]
                        }
                     ]
                  }
               ]
            }
         }
      }
   }
}
```
