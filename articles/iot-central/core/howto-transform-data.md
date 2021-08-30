---
title: 转换 Azure IoT Central 的数据 | Microsoft Docs
description: IoT 设备发送各种格式的数据，你可能需要转换这些数据的格式。 本文介绍如何在将数据输入和输出 IoT Central 的过程中转换数据。所描述的场景使用 IoT Edge 和 Azure Functions。
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0c087838029a5e7863359564682a6843238421c5
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112964583"
---
# <a name="transform-data-for-iot-central"></a>转换 IoT Central 的数据

IoT 设备发送各种格式的数据。 要将设备数据用于 IoT Central 应用程序，可以能需要使用转换实现以下操作：

- 使数据的格式与 IoT Central 应用程序兼容。
- 转换单位。
- 计算新指标。
- 从其他源扩充数据。

本文介绍如何在流入或流出期间在 IoT Central 外转换设备数据。

下图显示包含转换的数据的三种路由：

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="流入和流出期间数据转换路由的摘要" border="false":::

下表显示三个转换类型的示例：

| 转换 | 描述 | 示例  | 说明 |
|------------------------|-------------|----------|-------|
| 消息格式         | 转换为或操作 JSON 消息。 | CSV 转换为 JSON  | 在流入过程中。 IoT Central 仅接受值为 JSON 的消息。 有关详细信息，请参阅[遥测、属性和命令有效负载](concepts-telemetry-properties-commands.md)。 |
| 计算           | [Azure Functions](../../azure-functions/index.yml) 可以执行的数学函数。 | 从华氏到摄氏的单位换算。  | 使用流出模式进行转换，通过与 IoT Central 的直接连接利用可缩放的设备流入。 通过转换数据，可使用可视化效果和作业等 IoT Central 功能。 |
| 消息扩充     | 未在设备属性或遥测中找到的来自外部数据源的扩充。 有关内部扩充的详细信息，请参阅[使用数据导出将 IoT 数据导出到云目标](howto-export-data.md) | 使用设备中的[位置数据](howto-use-location-data.md)向消息添加天气信息。 | 使用流出模式进行转换，通过与 IoT Central 的直接连接利用可缩放的设备流入。 |

## <a name="prerequisites"></a>先决条件

若要完成本操作指南中的步骤，需要：

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="data-transformation-at-ingress"></a>流入期间的数据转换

要在流入期间转换设备数据，有以下两个选项：

- **IoT Edge**：将数据发送到 IoT Central 应用程序之前，使用 IoT Edge 模块转换来自下游设备的数据。

- **IoT Central 设备桥**：[IoT Central 设备桥](howto-build-iotc-device-bridge.md)将 Sigfox、Particle 和 The Things Network 等其他 IoT 设备云连接到 IoT Central。 设备桥使用 Azure 函数转发数据，并且你可以自定义转换设备数据的函数。

### <a name="use-iot-edge-to-transform-device-data"></a>使用 IoT Edge 转换设备数据

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="使用 IoT Edge 在流入期间转换数据" border="false":::

在此场景中，IoT Edge 模块先转换来自下游设备的数据，再将这些数据转发到 IoT Central 应用程序。 配置此场景的大致步骤如下：

1. **设置 IoT Edge 设备**：将 IoT Edge 设备安装并预配为网关，然后将该网关连接到 IoT Central 应用程序。

1. **将下游设备连接到 IoT Edge 设备：** 将下游设备连接到 IoT Edge 设备，然后将下游设备预配到 IoT Central 应用程序。

1. **在 IoT Edge 中转换设备数据：** 创建转换数据的 IoT Edge 模块。 将该模块部署到将转换的设备数据发送到 IoT Central 应用程序的 IoT Edge 网关设备。

1. **验证**：将来自下游设备的数据发送到网关，并验证转换的设备数据是否到达 IoT Central 应用程序。

在以下部分所述的示例中，下游设备将采用以下格式的 CSV 数据发送到 IoT Edge 网关设备：

```csv
"<temperature >, <pressure>, <humidity>"
```

在将数据发送到 IoT Central 之前，你希望使用 IoT Edge 模块将其转换为以下 JSON 格式：

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

[![浏览代码](media/common/browse-code.svg)](https://github.com/iot-for-all/iot-central-transform-with-iot-edge)

以下步骤介绍如何设置并配置此场景：

### <a name="build-the-custom-module"></a>构建自定义模块

在此场景中，IoT Edge 设备运行一个转换来自下游设备的数据的自定义模块。 在部署和配置 IoT Edge 设备之前，需要执行以下操作：

- 构建自定义模块。
- 将自定义模块添加到容器注册表。

IoT Edge 运行时从 Azure 容器注册表或 Docker Hub 等容器注册表下载自定义模块。 [Azure Cloud Shell](../../cloud-shell/overview.md)包含创建容器注册表，构建模块和将模块上传到注册表所需的所有工具：

创建容器注册表：

1. 打开 [Azure Cloud Shell](https://shell.azure.com/) 并登录到你的 Azure 订阅。

1. 运行以下命令。创建一个 Azure 容器注册表：

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    记下 `username` 和 `password` 的值供以后使用。

在 [Azure Cloud Shell](https://shell.azure.com/) 中构建自定义模块：

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中导航到适当的文件夹。
1. 要克隆包含模块源代码的 GitHub 存储库，请运行以下命令：

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. 要构建自定义模块，请在 Azure Cloud Shell 中运行以下命令：

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    运行以上命令可能要花费几分钟的时间。

### <a name="set-up-an-iot-edge-device"></a>设置 IoT Edge 设备

此场景使用 IoT Edge 网关设备转换来自任何下游设备的数据。 本部分介绍如何在 IoT Central 应用程序中为网关和下游设备创建 IoT Central 设备模块。 IoT Edge 设备使用部署清单配置其模块。

要为下游设备创建设备模板，此场景使用一个简单的恒温器设备模型：

1. 将[恒温器设备模型](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json)设备下载到本地计算机。

1. 登录到 IoT Central 应用程序，然后导航到“设备模板”页。

1. 依次选择“+ 新建”、“IoT 设备”和“下一步: 自定义”  。

1. 输入“恒温器”作为模板名称，然后选择“下一步: 查看”。 然后选择“创建”。

1. 选择“导入模型”，然后导入之前下载的 thermostat-2.json 文件。

1. 选择“发布”以发布新设备模板。

为 IoT Edge 网关设备创建设备模板：

1. 将部署清单 [moduledeployment.json](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json) 的副本保存到本地开发计算机。

1. 在文本编辑器中打开清单文件 moduledeployment.json 的本地副本。

1. 找到 `registryCredentials` 部分，并将占位符替换为创建 Azure 容器注册表时记下的值。 `address` 值类似于 `<username>.azurecr.io`。

1. 找到的 `transformmodule` 的 `settings` 部分。 将 `<acr or docker repo>` 替换为在上一步中使用的同一 `address` 值。 保存更改。

1. 在 IoT Central 应用程序中，导航到“设备模板”页。

1. 依次选择“+ 新建”、“Azure IoT Edge”和“下一步: 自定义”  。

1. 输入“IoT Edge 网关设备”作为设备模板名称。 选择“这是网关设备”。 选择“浏览”，上传之前编辑的部署清单文件 moduledeployment.json。

1. 验证部署清单后，依次选择“下一步: 查看”和“创建” 。

1. 在“模型”下，选择“关系” 。 选择“+ 添加关系”。  输入“下游设备”作为显示名称，然后选择“恒温器”作为目标。 选择“保存”。

1. 选择“发布”以发布设备模板。

现在，你在 IoT Central 应用程序中有两个设备模板。 “IoT Edge 网关设备”模板和作为下游设备的“恒温器”模板 。

在 IoT Central 中注册网关设备：

1. 在 IoT Central 应用程序中，导航到“设备”页。

1. 选择“IoT Edge 网关设备”，然后选择“创建设备” 。 输入“IoT Edge 网关设备”作为设备名称，输入“gateway-01”作为设备 ID，确保“IoT Edge 网关设备”被选为设备模板 。 选择“创建”。

1. 在设备列表中，单击“IoT Edge 网关设备”，然后选择“连接” 。

1. 记下 IoT Edge 网关设备的“ID 范围”、“设备 ID”和“主密钥”的值   。 稍后会使用到它们。

在 IoT Central 中注册下游设备：

1. 在 IoT Central 应用程序中，导航到“设备”页。

1. 选择“恒温器”，然后选择“创建设备” 。 输入“恒温器”作为设备名称，然后输入“downstream-01”作为设备 ID，确保“恒温器”被选为设备模板 。 选择“创建”。

1. 在设备列表中，选择“恒温器”，然后选择“附加到网关” 。 选择“IoT Edge 网关设备”模板和“IoT Edge 网关设备”实例 。 选择“附加”。

1. 在设备列表中，单击“恒温器”，然后选择“连接” 。

1. 记下恒温器设备的“ID 范围”、“设备 ID”和“主密钥”的值   。 稍后会使用到它们。

### <a name="deploy-the-gateway-and-downstream-devices"></a>部署网关和下游设备

为了方便起见，本文使用 Azure 虚拟机运行网关和下游设备。 要创建两个 Azure 虚拟机，请选择下面的“部署到 Azure”按钮，并使用下表中的信息完成“自定义部署”窗体 ：

| 字段 | 值 |
| ----- | ----- |
| 资源组 | `ingress-scenario` |
| 网关 DNS 标签前缀 | 此计算机的唯一 DNS 名称，如 `<your name>edgegateway` |
| 下游设备 DNS 标签前缀 | 此计算机的唯一 DNS 名称，如 `<your name>downstream` |
| 作用域 ID | 之前记下的 ID 范围 |
| IoT Edge 网关设备 ID | `gateway-01` |
| IoT Edge 网关设备密钥 | 之前记下的主密钥值 |
| 身份验证类型 | 密码 |
| 管理员密码或密钥 | 为两个虚拟机上的 AzureUser 帐户选择的密码。 |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

选择“查看 + 创建”，然后选择“创建” 。 在 ingress-scenario 资源组中创建虚拟机要花费几分钟的时间。

检查 IoT Edge 设备是否正常运行：

1. 打开 IoT Central 应用程序。 然后在设备页上，导航到设备列表上的“IoT Edge 网关设备” 。

1. 选择“模块”选项卡，并检查三个模块的状态。 在虚拟机中启动 IoT Edge 运行时要花费几分钟的时间。 启动后，三个模块的状态都将是“正在运行”。 如果 IoT Edge 运行时无法启动，请参阅[排除 IoT Edge 设备的故障](../../iot-edge/troubleshoot.md)。

要使 IoT Edge 设备充当网关，需要一些向任何下游设备证明其身份的证书。 本文使用演示证书。 在生产环境中，使用证书颁发机构的证书。

生成演示证书并将其安装到网关设备上：

1. 使用 SSH 连接并登录网关设备虚拟机。 可以在 Azure 门户中找到此虚拟机的 DNS 名称。 导航到 ingress-scenario 资源组中的 edgegateway 虚拟机 。

    > [!TIP]
    > 你可能需要在两个虚拟机上打开端口 22 以允许 SSH 访问，然后才能使用 SSH 从本地计算机或 Azure Cloud Shell 进行连接。

1. 运行以下命令克隆 IoT Edge 存储库并生成演示证书：

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    运行上述命令后，就可以在接下来的步骤中使用以下文件：

    - ~/certs/certs/azure-iot-test-only.root.ca.cert.pem - 根 CA 证书，用于生成所有其他演示证书以测试 IoT Edge 场景。
    - ~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem - 从 config.yaml 文件引用的设备 CA 证书 。 在网关场景中，该 CA 证书是 IoT Edge 设备向下游设备验证其身份的方式。
    - ~/certs/private/iot-edge-device-mycacert.key.pem - 与设备 CA 证书关联的私钥。

    若要了解有关这些演示证书的详细信息，请参阅[创建演示证书以测试 IoT Edge 设备功能](../../iot-edge/how-to-create-test-certificates.md)。

1. 在文本编辑器中打开 config.yaml 文件。 例如：

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. 找到 `Certificate settings` 设置。 取消注释并修改证书设置，如下所示：

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    上面显示的示例假设你以 AzureUser 身份登录并创建了一个名为“mycacert”的设备 CA 证书。

1. 保存更改并重启 IoT Edge 运行时：

    ```bash
    sudo systemctl restart iotedge
    ```

如果 IoT Edge 运行时在更改以后成功启动，则 $edgeAgent 和 $edgeHub 模块的状态都将更改为“正在运行”  。 可以在 IoT Central 中你的网关设备的“模块”页上查看状态值。

如果运行时未启动，请检查在 config.yaml 中所做的更改，并参阅[对 IoT Edge 设备进行故障排除](../../iot-edge/troubleshoot.md)。

### <a name="connect-downstream-device-to-iot-edge-device"></a>将下游设备连接到 IoT Edge 设备

将下游设备连接到 IoT Edge 网关设备：

1. 使用 SSH 连接并登录下游设备虚拟机。 可以在 Azure 门户中找到此虚拟机的 DNS 名称。 导航到 ingress-scenario 资源组中的 leafdevice 虚拟机 。

    > [!TIP]
    > 你可能需要在两个虚拟机上打开端口 22 以允许 SSH 访问，然后才能使用 SSH 从本地计算机或 Azure Cloud Shell 进行连接。

1. 要克隆包含示例下游设备源代码的 GitHub 存储库，请运行以下命令：

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. 要从网关设备复制所需的证书，请运行以下 `scp` 命令。 此 `scp` 命令使用主机名 `edgegateway` 识别网关虚拟机。 系统将提示你输入密码：

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. 导航到 leafdevice 文件夹，并安装所需包。 然后运行 `build` 和 `start` 脚本，将设备预配并连接到网关：

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. 输入之前创建的下游设备的设备 ID、范围 ID 和 SAS 密钥。 输入 `edgegateway` 作为主机名。 此命令的输出如下所示：

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>验证

要验证该场景是否正在运行，请导航到 IoT Central 中你的 IoT Edge 网关设备：

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="显示设备页上已转换的数据的屏幕截图。":::

- 选择“模块”。 验证 $edgeAgent、$edgeHub 和 transformmodule 这三个 IoT Edge 模块是否正在运行  。
- 选择“下游设备”，并验证该下游设备是否已进行预配。
- 选择“原始数据”。 “未建模的数据”列中的遥测数据如下所示：

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

因为 IoT Edge 设备要转换来自下游设备的数据，所以遥测与 IoT Central 中网关设备相关联。 要将遥测可视化，请创建新版 IoT Edge 网关设备模板，其中包含遥测类型的定义。

## <a name="data-transformation-at-egress"></a>流出期间的数据转换

你可以将设备连接到 IoT Central，将设备数据导出到计算引擎以进行转换，然后将已转换的数据发送回 IoT Central 用于设备管理和分析。 例如：

- 设备将位置数据发送到 IoT Central。
- IoT Central 将数据导出到计算引擎，该引擎使用天气信息完善了位置数据。
- 计算引擎将完善后数据发送回 IoT Central。

可以将 [IoT Central 设备](https://github.com/Azure/iotc-device-bridge)桥用作计算引擎，转换从 IoT Central 导出的数据。

在流出期间转换数据的一个优势在于，设备直接连接到 IoT Central，这样可以轻松地将命令发送到设备，或更新设备属性。 但是通过此方法，使用的消息会超出每月配额，并且 Azure IoT Central 的使用成本会提高。

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>使用 IoT Central 设备桥转换设备数据

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="使用 IoT Edge 在流出期间转换数据" border="false":::

在此场景中，计算引擎先转换从 IoT Central 导出的设备数据，再将这些数据发送回 IoT Central 应用程序。 配置此场景的大致步骤如下：

1. **设置计算引擎：** 创建一个 IoT Central 设备桥，使其作为转换数据的计算引擎。

1. **在设备桥中转换设备数据：** 在设备桥中通过修改数据转换用例的设备桥函数代码来转换数据。

1. **启用从 IoT Central 到设备桥的数据流：** 将数据从 IoT Central 导出到设备桥以进行转换。 然后，将转换后的数据转发回 IoT Central。 创建数据导出时，使用消息属性筛选器仅导出未转换的数据。

1. **验证**：将设备连接到 IoT Central 应用，并检查 IoT Central 中的原始设备数据和已转换的数据。

<!-- To Do - doesn't the device send JSON data? -->
在以下部分所述的示例中，设备将采用以下格式的 CSV 数据发送到 IoT Edge 网关设备：

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

使用设备桥通过以下方法转换设备数据：

- 将温度单位从摄氏更改为华氏。
- 通过根据纬度和经度值从 [Open Weather](https://openweathermap.org/) 拉取的天气数据扩充设备数据。

然后设备桥按照以下格式将转换后的数据发送到 IoT Central：

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

以下步骤介绍如何设置并配置此场景：

### <a name="retrieve-your-iot-central-connection-settings"></a>检索 IoT Central 连接设置

设置此场景之前，需要从 IoT Central 应用程序获取一些连接设置：

1. 登录到 IoT Central 应用程序。

1. 导航到“管理”>“设备连接”。

1. 记下“ID 范围”。 稍后会用到此值。

1. 选择“SaS-IoT-Devices”注册组。 记下共享访问签名主密钥。 稍后会用到此值。

### <a name="set-up-a-compute-engine"></a>设置计算引擎

此场景使用同一 Azure Functions 部署作为 IoT Central 设备桥。 要部署设备桥，请选择下面的“部署到 Azure”按钮，并使用下表中的信息完成“自定义部署”窗体 ：

| 字段 | 值 |
| ----- | ----- |
| 资源组 | 创建名为 `egress-scenario` 的新资源组 |
| 区域 | 选择离你最近的区域。 |
| 作用域 ID | 使用之前记下的 ID 范围。 |
| IoT Central SAS 密钥 | 使用 SaS-IoT-Devices 注册组的共享访问签名主密钥。 你之前已记下此值。 |

[![部署到 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)。

选择“查看 + 创建”，然后选择“创建” 。 在 egress-scenario 资源组中创建 Azure 函数和相关资源需要花费几分钟的时间。

### <a name="transform-device-data-in-the-device-bridge"></a>在设备桥中转换设备数据

配置转换导出的设备数据的设备桥：

1. 从 Open Weather 服务获取应用程序 API 密钥。 帐户是免费的，但对于服务的使用存在限制。 要创建应用程序 API 密钥，请在 [Open Weather 服务门户](https://openweathermap.org/)中创建一个帐户，并按照说明操作。 稍后会用到 Open Weather API 密钥。

1. 在 Azure 门户中，导航到 egress-scenario 资源组中的函数应用。

1. 在左侧导航栏的“开发工具”中，选择“应用服务编辑器(预览)” 。

1. 选择“开始”&rarr;打开“应用服务编辑器”页 。 进行以下更改：

    1. 打开 wwwroot/IoTCIntegration/index.js 文件。 将此文件中的所有代码替换为 [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js) 文件中的代码。

    1. 在新的 index.js 文件中，使用之前获取的 Open Weather API 密钥更新 `openWeatherAppId` 变量文件。

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. 向由函数发送到 IoT Central 的数据添加一个消息属性。 IoT Central 使用此属性防止导出转换后的数据。 要作出此更改，请打开 wwwroot/IoTCIntegration/lib/engine.js 文件。 找到下列代码：

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        就在上述代码片段中的代码之后，添加以下代码：

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        作为参考，你可以查看已完成的 [engine.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) 文件示例。

1. 在应用服务编辑器中，选择左侧导航栏中的“控制台” 。 运行以下命令安装所需的包：

    ```bash
    cd IoTCIntegration
    npm install
    ```

    此命令可能需要花费几分钟时间运行。

1. 返回到 Azure 函数概述页，然后重启函数：

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="重启函数":::

1. 选择左侧导航栏中的“函数”。 然后选择“IoTCIntegration”。 选择“代码 + 测试”。

1. 记下函数 URL，稍后需要此值：

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="获取函数 URL":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>启用从 IoT Central 到设备桥的数据流

本部分介绍如何设置 Azure IoT Central 应用程序。

首先，将[设备模型](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json)文件保存到本地计算机。

要将设备模板添加到 IoT Central 应用程序，请导航到 IoT Central 应用程序，然后：

1. 登录到 IoT Central 应用程序，然后导航到“设备模板”页。

1. 依次选择“+ 新建”、“IoT 设备”和“下一步: 自定义”，然后输入“计算模型”作为模板名称  。 在完成时选择“下一步:  查看”。 然后选择“创建”。

1. 选择“导入模型”，并浏览到之前下载的 model.json 文件。

1. 导入模型后，选择“发布”以发布“计算模型”设备模板 。

设置数据导出以将数据发送到设备桥：

1. 在 IoT Central 应用程序中，选择“数据导出”。

1. 选择“+ 新建目标”，创建与设备桥一起使用的目标。 调用目标“计算函数”，选择“Webhook”作为“目标类型” 。 选择粘贴之前记下的函数 URL，作为回调 URL。 将“授权”保留为“无授权” 。

1. 保存更改。

1. 选择“+ 新建导出”，并创建名为“计算导出”的数据导出。

1. 添加一个筛选器，仅导出正在使用的设备模板的设备数据。 依次选择“+ 筛选器”、“设备模板”项和“等于”运算符，然后选择刚创建的“计算模型”设备模板   。

1. 添加消息筛选器以区分已转换数据和未转换数据。 此筛选器防止将已转换的值发送回设备桥。 选择“+ 消息属性筛选器”，输入名称值“计算”，然后选择运算符“不存在”。 字符串 `computed` 用作设备桥示例代码中的关键字。

1. 选择之前创建的“计算函数”目标作为目标。

1. 保存更改。 一分钟左右，“导出状态”显示为“正常” 。

### <a name="verify"></a>验证

用于测试场景的示例设备以 Node.js 编写。 请确保已在本地计算机上安装 Node.js 和 NPM。 如果不想安装这些必备组件，请使用已预安装这些必备组件的 [Azure Cloud Shell](https://shell.azure.com/)。

运行测试场景的示例设备：

1. 运行以下命令，克隆包含示例代码的 GitHub 存储库：

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. 要将示例设备连接到 IoT Central 应用程序，请在 iot-central-compute/device/device.js 文件中编辑连接设置。 将范围 ID 和组 SAS 密钥替换为之前记下的值：

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    保存更改。

1. 使用以下命令安装所需的包，并运行设备：

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. 此命令的结果如以下输出所示：

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. 在 IoT Central 应用程序中，导航到名为“computeDevice”的设备。 在“原始数据”视图中，有两种约每五秒显示一次的不同遥测流。 具有未建模的数据的流是原始遥测，具有已建模的数据的流是该函数转换的数据：

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="显示原始数据和已转换的原始数据的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要在执行本指南中的步骤时创建的 Azure 资源，请删除 [Azure 门户中的资源组](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups)。

本指南中使用的两个资源组分别是 ingress-scenario 和 egress-scenario 。

## <a name="next-steps"></a>后续步骤

在本文中，你学习了在流入和流出期间为 IoT Central 转换设备数据的两个不同选项。 本文包含两个特定场景的演练：

- 先使用 IoT Edge 模块转换来自下游设备的数据，再将这些数据发送到 IoT Central 应用程序。
- 使用 Azure Functions 在 IoT Central 外转换数据。 在此场景中，IoT Central 使用数据导出将传入数据发送到 Azure 函数以进行转换。 该函数将转换后的数据发送回 IoT Central 应用程序。

现在，你了解了如何在 Azure IoT Central 应用程序外转换设备数据，接下来可以学习[如何使用分析来分析 IoT Central 中的设备数据](howto-create-analytics.md)。
