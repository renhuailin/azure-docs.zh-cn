---
title: 发布和订阅 Azure IoT Edge |Microsoft Docs
description: 使用 IoT Edge MQTT broker 发布和订阅消息
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: ecb034ae621c935c3ebcd5b480e116c2cb1d864f
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435529"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>发布和订阅 Azure IoT Edge

可以使用 Azure IoT Edge MQTT broker 发布和订阅消息。 本文介绍如何连接到此代理，如何通过用户定义的主题发布和订阅消息，以及如何使用 IoT 中心消息传递基元。 IoT Edge MQTT broker 内置 IoT Edge 中心。 有关详细信息，请参阅 [IoT Edge 中心的协调功能](iot-edge-runtime.md)。

> [!NOTE]
> IoT Edge MQTT broker 目前为公共预览版。

## <a name="pre-requisites"></a>先决条件

- 具有有效订阅的 Azure 帐户
- [Azure CLI](/cli/azure/) `azure-iot` 安装了 CLI 扩展的 Azure CLI。 有关详细信息，请参阅 [适用于 azure Azure CLI 的 Azure IoT 扩展安装步骤](/cli/azure/azure-cli-reference-for-iot)。
- SKU 的 **IoT 中心** ： F1、S1、S2 或 S3。
- 具有 **版本1.2 或更高版本的 IoT Edge 设备**。 由于 IoT Edge MQTT broker 目前为公共预览版，因此请在 edgeHub 容器上将以下环境变量设置为 true，以启用 MQTT broker：

   | 名称 | 值 |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- IoT Edge 设备上安装了 **Mosquitto 客户端**。 本文使用常见的 Mosquitto 客户端 [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) 和 [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html)。 可以改为使用其他 MQTT 客户端。 若要在 Ubuntu 设备上安装 Mosquitto 客户端，请运行以下命令：

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    请勿安装 Mosquitto 服务器，因为这可能会导致 MQTT 端口 (8883 和 1883) 并与 IoT Edge 集线器发生冲突。

## <a name="connect-to-iot-edge-hub"></a>连接到 IoT Edge 集线器

若要连接到 IoT Edge 集线器，请遵循 [使用一般 MQTT 客户端连接到 IoT 中心一文](../iot-hub/iot-hub-mqtt-support.md) 中所述的相同步骤，或 [IoT Edge 中心的概念说明一](iot-edge-runtime.md)文中所述。 这些步骤包括：

1. （可选） MQTT 客户端使用传输层安全性 (TLS 与 IoT Edge 集线器建立 *安全连接*) 
2. MQTT 客户端向 IoT Edge 中心进行 *身份验证*
3. IoT Edge 中心按其授权策略 *授权* MQTT 客户端

### <a name="secure-connection-tls"></a>安全连接 (TLS) 

传输层安全 (TLS) 用于在客户端与 IoT Edge 中心之间建立加密的通信。

若要禁用 TLS，请使用端口 1883 (MQTT) 并将 edgeHub 容器绑定到端口1883。

若要启用 TLS，如果客户端连接到端口 8883 (MQTTS) 连接到 MQTT broker，将启动 TLS 通道。 代理发送其证书链，客户端需要对其进行验证。 为了验证证书链，MQTT broker 的根证书必须作为受信任的证书安装在客户端上。 如果根证书不受信任，MQTT broker 将拒绝客户端库，并出现证书验证错误。 若要在客户端上安装代理的此根证书，请执行以下步骤，这与在 ["](how-to-create-transparent-gateway.md) [准备下游设备](how-to-connect-downstream-device.md#prepare-a-downstream-device) " 文档中介绍的步骤相同。

### <a name="authentication"></a>身份验证

要使 MQTT 客户端对其自身进行身份验证，首先需要将连接数据包发送到 MQTT broker，以在其名称中启动连接。 此数据包提供三部分身份验证信息： `client identifier` 、、 `username` 和 `password` ：

- 此 `client identifier` 字段是 IoT 中心中的设备名称或模块名称。 它使用以下语法：

  - 对于设备： `<device_name>`

  - 对于模块： `<device_name>/<module_name>`

   为了连接到 MQTT broker，设备或模块必须在 IoT 中心内注册。

   代理不允许使用相同的凭据从多个客户端连接。 如果第二个客户端使用相同的凭据进行连接，则代理将断开已连接的客户端。

- 此 `username` 字段派生自设备或模块名称，以及设备所属的 IoTHub 名称，使用以下语法：

  - 对于设备： `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - 对于模块： `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- `password`连接数据包的字段取决于身份验证模式：

  - 使用 [对称密钥身份验证](how-to-authenticate-downstream-device.md#symmetric-key-authentication)时，该 `password` 字段为 SAS 令牌。
  - 使用 [x.509 自签名身份验证](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)时，该 `password` 字段不存在。 在此身份验证模式下，需要 TLS 通道。 客户端需要连接到端口8883以建立 TLS 连接。 在 TLS 握手期间，MQTT broker 请求客户端证书。 此证书用于验证客户端的身份，因此， `password` 稍后在发送连接数据包时不需要该字段。 同时发送客户端证书和密码字段将导致错误，并且连接将关闭。 MQTT 库和 TLS 客户端库通常可以在启动连接时发送客户端证书。 你可以在 [使用用于客户端身份验证的 X509 证书](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)部分中查看分步示例。

通过 IoT Edge 部署的模块使用 [对称密钥身份验证](how-to-authenticate-downstream-device.md#symmetric-key-authentication) ，并可以调用本地 [IOT EDGE 工作负荷 API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) 以编程方式获取 SAS 令牌（即使在脱机时）。

### <a name="authorization"></a>授权

将 MQTT 客户端 IoT Edge 集线器身份验证后，需要对其进行授权才能连接。 连接后，需要获得授权才能发布或订阅特定主题。 这些授权由 IoT Edge 中心根据其授权策略授予。 授权策略是一组语句，表示为通过其克隆发送到 IoT Edge 集线器的 JSON 结构。 编辑 IoT Edge 中心克隆以配置其授权策略。

> [!NOTE]
> 对于公共预览版，只能通过 Visual Studio、Visual Studio Code 或 Azure CLI 来编辑 MQTT broker 的授权策略。 Azure 门户当前不支持编辑 IoT Edge 中心克隆及其授权策略。

每个授权策略语句都包含 `identities` 、 `allow` 或效果的组合 `deny` `operations` `resources` ：

- `identities` 描述策略的主题。 它必须映射到 `client identifier` 客户端在其连接数据包中发送的。
- `allow` 或 `deny` 效果定义是否允许或拒绝操作。
- `operations` 定义要授权的操作。 `mqtt:connect`， `mqtt:publish` 和 `mqtt:subscribe` 现在是三个受支持的操作。
- `resources` 定义策略的对象。 它可以是使用 [MQTT 通配符](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)定义的主题或主题模式。

下面是显式不允许 "rogue_client" 客户端连接的授权策略的一个示例，它允许任何 Azure IoT 客户端进行连接，并允许 "sensor_1" 发布到主题 `events/alerts` 。

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
                     "rogue_client"
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
                     "sensor_1"
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

编写授权策略时需要注意以下几点：

- 它需要不限的 `$edgeHub` 架构版本1。2
- 默认情况下，所有操作都将被拒绝。
- 授权语句将按照它们在 JSON 定义中的显示顺序进行评估。 首先，查看 `identities` 并选择与请求匹配的第一个 allow 或 deny 语句。 如果 allow 和 deny 语句之间发生冲突，deny 语句入选。
- 例如， (多个变量，可以在授权策略中使用替换) ：
    - `{{iot:identity}}` 表示当前连接的客户端的标识。 例如，像这样的设备标识 `myDevice` 或模块标识 `myEdgeDevice/SampleModule` 。
    - `{{iot:device_id}}` 表示当前连接的设备的标识。 例如，像这样的设备标识 `myDevice` 或运行模块的设备标识 `myEdgeDevice` 。
    - `{{iot:module_id}}` 表示当前连接的模块的标识。 对于连接的设备，此变量为空白; 对于的模块标识，此变量为空 `SampleModule` 。
    - `{{iot:this_device_id}}` 表示运行授权策略 IoT Edge 设备的标识。 例如 `myIoTEdgeDevice`。

与用户定义的主题相比，处理 IoT 中心主题的授权的方式略有不同。 以下是要记住的要点：

- Azure IoT 设备或模块需要显式授权规则才能连接到 IoT Edge hub MQTT broker。 下面提供了默认连接授权策略。
- 默认情况下，Azure IoT 设备或模块可以访问自己的 IoT 中心主题，无需任何显式授权规则。 但是，在这种情况下，授权源自父/子关系，必须设置这些关系。 IoT Edge 模块会自动设置为其 IoT Edge 设备的子级，但设备需要显式设置为其 IoT Edge 网关的子网。
- Azure IoT 设备或模块可以访问其他设备或模块的主题，这些主题提供了相应的显式授权规则。

下面是可用于启用所有 Azure IoT 设备或模块 **连接** 到 broker 的默认授权策略：

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

现在，你已了解如何连接到 IoT Edge MQTT broker，接下来让我们了解如何使用它在用户定义的主题上首先发布和订阅消息，然后将其用于 IoT 中心主题和其他 MQTT broker。

## <a name="publish-and-subscribe-on-user-defined-topics"></a>发布和订阅用户定义的主题

本文将使用一个名为 **sub_client** 的客户端，该客户端订阅一个主题，另一个名为 **pub_client** 的客户端发布到主题。 我们将使用 [对称密钥身份验证](how-to-authenticate-downstream-device.md#symmetric-key-authentication) ，但也可以使用 [x.509 自签名身份](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) 验证或 [x.509 CA 签名](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)的身份验证来实现相同的身份验证。

### <a name="create-publisher-and-subscriber-clients"></a>创建发布服务器和订阅服务器客户端

在 IoT 中心创建两个 IoT 设备并获取其密码。 使用终端中的 Azure CLI 执行以下操作：

1. 在 IoT 中心创建两个 IoT 设备，并将它们作为 IoT Edge 设备的父级：

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. 通过生成 SAS 令牌获取其密码：

    - 对于设备：
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       其中3600是 SAS 令牌的持续时间（以秒为单位） (例如，3600 = 1 小时) 。
    
    - 对于模块：
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       其中3600是 SAS 令牌的持续时间（以秒为单位） (例如，3600 = 1 小时) 。

3. 复制 SAS 令牌，它是与输出中的 "SAS" 键相对应的值。 下面是上述 Azure CLI 命令的示例输出：

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>授权发布服务器和订阅服务器客户端

若要对发布服务器和订阅服务器授权，请通过 Azure CLI、Visual Studio 或 Visual Studio code 编辑 IoT Edge 中心克隆，以包含以下授权策略：

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
                     "sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               },
               {
                  "identities": [
                     "pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>不带 TLS 的对称密钥身份验证

#### <a name="subscribe"></a>订阅

**sub_client** `test_topic` 通过在 IoT Edge 设备上运行以下命令，将 sub_client MQTT 客户端连接到 MQTT broker，并订阅：

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

`<edge_device_address>`  =  `localhost` 在此示例中，由于客户端在与 IoT Edge 相同的设备上运行。

请注意，在此第一个示例中，将使用端口 1883 (MQTT) ，不包含 TLS。 接下来的部分将显示端口 8883 (MQTTS) 的另一个示例（已启用 TLS）。

**Sub_client** MQTT 客户端现已启动，正在等待传入消息 `test_topic` 。

#### <a name="publish"></a>发布

通过从另一终端运行以下命令，将 **pub_client** MQTT 客户端连接到 MQTT broker，并 `test_topic` 通过在你的 IoT Edge 设备上运行以下命令，将消息发布到上面：

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

`<edge_device_address>`  =  `localhost` 在此示例中，由于客户端在与 IoT Edge 相同的设备上运行。

执行命令时， **sub_client** MQTT 客户端将收到 "hello" 消息。

### <a name="symmetric-keys-authentication-with-tls"></a>对称密钥用 TLS 进行身份验证

若要启用 TLS，端口必须从 1883 (MQTT) 改为 8883 (MQTTS) ，并且客户端必须具有 MQTT broker 的根证书才能验证 MQTT broker 发送的证书链。 为此，可以按照 [安全连接 (TLS) ](#secure-connection-tls)部分中提供的步骤操作。

由于客户端在与上面的示例中的 MQTT broker 相同的设备上运行，因此，通过将端口号从 1883 (MQTT) 改为 8883 (MQTTS) ，相同的步骤仅适用于启用 TLS。

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>发布和订阅 IoT 中心主题

[Azure IoT 设备 sdk](https://github.com/Azure/azure-iot-sdks)已允许客户端执行 IoT 中心操作，但不允许在用户定义的主题上发布/订阅。 只要遵守 IoT 中心基元协议，就可以使用任何 MQTT 客户端使用发布和订阅语义执行 IoT 中心操作。 我们将通过 specificities 来了解这些协议的工作原理。

### <a name="send-telemetry-data-to-iot-hub"></a>将遥测数据发送到 IoT 中心

将遥测数据发送到 IoT 中心类似于发布用户定义的主题，但使用特定的 IoT 中心主题：

- 对于设备，将在主题中发送遥测： `devices/<device_name>/messages/events`
- 对于模块，将在主题中发送遥测： `devices/<device_name>/<module_name>/messages/events`

此外，请创建一个路由，例如将 `FROM /messages/* INTO $upstream` 遥测从 IOT EDGE MQTT broker 发送到 IoT 中心。 若要了解有关路由的详细信息，请参阅 [声明路由](module-composition.md#declare-routes)。

### <a name="get-twin"></a>获取克隆

获取设备/模块克隆不是典型的 MQTT 模式。 客户端需要为 IoT 中心要服务的克隆发出请求。

若要接收孪生，客户端需要订阅 IoT 中心特定主题 `$iothub/twin/res/#` 。 此主题名称继承自 IoT 中心，并且所有客户端都需要订阅同一主题。 这并不意味着设备或模块会接收彼此之间的不同。 IoT 中心和 IoT Edge 集线器知道应该将哪个克隆传递到何处，即使所有设备都侦听相同的主题名称。 

完成订阅后，客户端需要将消息发布到 IoT 中心特定主题， `$iothub/twin/GET/?rid=<request_id>/#` 其中  `<request_id>` 是任意标识符。 然后，IoT 中心会向客户端订阅的主题发送请求的数据 `$iothub/twin/res/200/?rid=<request_id>` 。 这是客户端可以如何将其请求与响应配对。

### <a name="receive-twin-patches"></a>接收克隆修补程序

若要接收克隆修补程序，客户端需要订阅特殊的 IoTHub 主题 `$iothub/twin/PATCH/properties/desired/#` 。 完成订阅后，客户端会收到本主题中的 IoT 中心发送的克隆修补程序。 

### <a name="receive-direct-methods"></a>接收直接方法

接收直接方法类似于接收完整的孪生，客户端需要确认是否已收到呼叫。 首先，客户端订阅 IoT 中心特殊主题 `$iothub/methods/POST/#` 。 然后，在本主题中收到直接方法后，客户端需要 `rid` 从收到直接方法的子主题中提取请求标识符，最后在 IoT 中心的特殊主题上发布确认消息 `$iothub/methods/res/200/<request_id>` 。

### <a name="send-direct-methods"></a>发送直接方法

发送直接方法是 HTTP 调用，因此不会通过 MQTT broker。 若要将直接方法发送到 IoT 中心，请参阅 [了解和调用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。 若要在本地将直接方法发送到其他模块，请参阅 [Azure IoT c # SDK 直接方法调用示例](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)。

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>在 MQTT 代理之间发布和订阅

若要连接两个 MQTT 代理，IoT Edge 中心包含一个 MQTT 桥。 MQTT 桥通常用于将运行的 MQTT broker 连接到另一 MQTT broker。 通常仅将本地流量的一个子集推送到另一个代理。

> [!NOTE]
> 当前只能在嵌套的 IoT Edge 设备之间使用 IoT Edge 中心桥。 由于 IoT 中心不是功能完备的 MQTT broker，因此不能用于将数据发送到 IoT 中心。 若要了解有关 IoT 中心 MQTT broker 功能支持的详细信息，请参阅 [使用 MQTT 协议与 iot 中心通信](../iot-hub/iot-hub-mqtt-support.md)。 若要详细了解如何嵌套 IoT Edge 设备，请参阅 [将下游 IoT Edge 设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

在嵌套配置中，IoT Edge 集线器 MQTT 桥作为父 MQTT broker 的客户端，因此必须在父 EdgeHub 上设置授权规则，以允许子 EdgeHub 发布和订阅配置了桥的特定用户定义的主题。

IoT Edge MQTT 桥是通过通过其克隆发送到 IoT Edge 中心的 JSON 结构配置的。 编辑 IoT Edge 中心克隆以配置其 MQTT 桥。

> [!NOTE]
> 对于公共预览版，只能通过 Visual Studio、Visual Studio Code 或 Azure CLI 使用 MQTT 桥的配置。 Azure 门户当前不支持编辑 IoT Edge 中心克隆及其 MQTT 桥配置。

可以将 MQTT 桥配置为将 IoT Edge 集线器 MQTT broker 连接到多个外部代理。 对于每个外部代理，需要以下设置：

- `endpoint` 要连接到的远程 MQTT 代理的地址。 目前仅支持父 IoT Edge 设备，并由变量定义 `$upstream` 。
- `settings` 定义要为终结点桥接哪些主题。 每个终结点可以有多个设置，并使用以下值对其进行配置：
    - `direction`： `in` 订阅远程代理的主题，或 `out` 发布到远程代理的主题
    - `topic`：要匹配的核心主题模式。 [MQTT 通配符](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) 可用于定义此模式。 可以在本地 broker 和远程代理上对此主题模式应用不同的前缀。
    - `outPrefix`：应用于 `topic` 远程代理上模式的前缀。
    - `inPrefix`：应用于 `topic` 本地代理上模式的前缀。

下面是一个 IoT Edge MQTT 桥接配置的示例，该配置将父 IoT Edge 设备的主题上收到的所有消息重新发布 `alerts/#` 为同一主题上的子 IoT Edge 设备，并将子 IoT Edge 设备主题中发送的所有消息重新发布 `/local/telemetry/#` 到主题的父 IoT Edge 设备上 `/remote/messages/#` 。

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
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
IoT Edge 中心 MQTT 桥中的其他说明：
- 使用 MQTT broker 时，MQTT 协议将自动用作上游协议，并在嵌套配置中使用 IoT Edge （例如，使用指定的） `parent_hostname` 。 若要了解有关上游协议的详细信息，请参阅 [云通信](iot-edge-runtime.md#cloud-communication)。 若要了解有关嵌套配置的详细信息，请参阅 [将下游 IoT Edge 设备连接到 Azure IoT Edge 的网关](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)。

## <a name="next-steps"></a>后续步骤

[了解 IoT Edge 中心](iot-edge-runtime.md#iot-edge-hub)
