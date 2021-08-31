---
title: 将 IoT Edge 透明网关连接到 Azure IoT Central 应用程序
description: 如何通过 IoT Edge 透明网关将设备连接到 IoT Central 应用程序
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: c0e63d0aee60ae81c14fa8971369c3a50f9b2bde
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461393"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>如何通过 IoT Edge 透明网关连接设备

IoT Edge 设备可以充当网关，提供本地网络上其他设备与 IoT Central 应用程序之间的连接。 当设备无法直接访问 IoT Central 应用程序时，使用网关。

IoT Edge 支持[透明网关模式和转换网关模式](../../iot-edge/iot-edge-as-gateway.md) 。 本文总结了如何实现透明网关模式。 在此模式中，网关将消息从下游设备传递到 IoT Central 应用程序中的 IoT 中心终结点。

本文使用虚拟机来托管下游设备和网关。 在实际情况中，下游设备和网关将在本地网络上的物理设备上运行。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要：

- 一个有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 根据自定义应用程序模板[创建的 IoT Central 应用程序](howto-create-iot-central-application.md)。 若要了解详细信息，请参阅[创建 IoT Central 应用程序](howto-create-iot-central-application.md)。

若要执行本文中的步骤，请将以下文件下载到计算机：

- [恒温器设备模型](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [透明网关清单](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>添加设备模板

下游设备和网关设备都需要 IoT Central 的设备模板。 IoT Central 允许你对下游设备和网关之间的关系进行建模，以便在它们连接后可以查看和管理它们。

若要为下游设备创建设备模板，请创建一个标准设备模板，该模板对设备的功能进行建模。 本文所示的示例使用恒温器设备模型。

为下游设备创建设备模板：

1. 创建设备模板，然后选择“IoT 设备”作为模板类型。

1. 在向导的“自定义”页上，输入设备模板的名称，例如“恒温器”。

1. 创建设备模板后，选择“导入模型”。 选择一个模型，比如之前下载的 thermostat-1.json 文件。

1. 若要为恒温器生成一些默认视图，请选择“视图”，然后选择“生成默认视图”。

1. 发布设备模板。

为透明 IoT Edge 网关创建设备模板：

1. 创建设备模板，然后选择“Azure IoT Edge”作为模板类型。

1. 在向导的“自定义”页上，输入设备模板的名称，例如“Edge 网关”。

1. 在向导的“自定义”页上，勾选“具有下游设备的网关设备” 。

1. 在向导的“自定义”页面上，选择“浏览” 。 上传之前下载的 EdgeTransparentGatewayManifest.json 文件。

1. 将“关系”中的条目添加到下游设备模板。

以下屏幕截图显示了 IoT Edge 网关设备的“关系”页面，该网关设备具有使用“恒温器”设备模板的下游设备 ：

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="显示 IoT Edge 网关设备模板与恒温器下游设备模板关系的屏幕截图。":::

上一个屏幕截图显示了没有定义模块的 IoT Edge 网关设备模板。 透明网关不需要任何模块，因为 IoT Edge 运行时将消息从下游设备转发到 IoT Central。 如果网关本身需要发送遥测、同步属性或处理命令，则可以在根组件或模块中定义这些功能。

在发布网关和下游设备模板之前，添加任何所需的云属性和视图。

## <a name="add-the-devices"></a>添加设备

将设备添加到 IoT Central 应用程序时，可以定义下游设备与透明网关之间的关系。

添加设备：

1. 导航到 IoT Central 应用程序中的“设备”页。

1. 添加透明网关 IoT Edge 设备的实例。 在本文中，网关设备 ID 是 `edgegateway`。

1. 添加下游设备的一个或多个实例。 在本文中，下游设备是 ID 为 `thermostat1` 和 `thermostat2` 的恒温器。

1. 在设备列表中，选择每个下游设备并选择“附加到网关”。

以下屏幕截图显示你可以在“下游设备”页面上查看附加到网关的设备列表：

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="显示连接到透明网关的下游设备列表的屏幕截图。":::

在透明网关中，下游设备连接到网关本身，而不是网关托管的自定义模块。

在部署设备之前，你需要：

- IoT Central 应用程序的“ID 范围”。
- 网关和下游设备的“设备 ID”值。
- 网关和下游设备的“主密钥”值。

若要查找这些值，请导航到设备列表中的每个设备并选择“连接”。 在继续之前，请记下这些值。

## <a name="deploy-the-gateway-and-devices"></a>部署网关和设备

为了使你能够尝试此场景，以下步骤将向你展示如何将网关和下游设备部署到 Azure 虚拟机。 在实际场景中，下游设备和网关会在本地网络上的物理设备上运行。

若要尝试透明网关场景，请选择下面的按钮以部署两个 Linux 虚拟机。 一个虚拟机是透明的 IoT Edge 网关，另一个是模拟恒温器的下游设备：

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

部署并运行这两个虚拟机时，请验证 IoT Edge 网关设备是否在 `edgegateway` 虚拟机上运行：

1. 转到 IoT Central 应用程序中的“设备”页。 如果 IoT Edge 网关设备连接到 IoT Central，则其状态为“已预配”。

1. 打开 IoT Edge 网关设备并在“模块”页面上验证模块的状态。 如果 IoT Edge 运行时启动成功，“$edgeAgent”和“$edgeHub”模块的状态为“正在运行”  ：

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="屏幕截图显示 $edgeAgent 和 $edgeHub 模块正在 IoT Edge 网关上运行。":::

> [!TIP]
> 当虚拟机启动且设备在 IoT Central 应用程序中进行预配时，你可能需要等待几分钟。

## <a name="configure-the-gateway"></a>配置网关

若要使 IoT Edge 设备充当透明网关，它需要一些证书来向任何下游设备证明其身份。 本文使用演示证书。 在生产环境中，使用证书颁发机构的证书。

生成演示证书并将其安装到网关设备上：

1. 使用 SSH 连接并登录网关设备虚拟机。

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

如果 IoT Edge 运行时在更改后成功启动，则在 IoT Central 网关设备的“模块”页面上，“$edgeAgent”和“$edgeHub”模块的状态更改为“运行”   。

如果运行时未启动，请检查在 config.yaml 中所做的更改，并参阅[对 IoT Edge 设备进行故障排除](../../iot-edge/troubleshoot.md)。

透明网关现已配置好，可以开始从下游设备转发遥测。

## <a name="provision-a-downstream-device"></a>预配下游设备

目前，IoT Edge 无法自动为 IoT Central 应用程序预配下游设备。 以下步骤向你展示了如何配置 `thermostat1` 设备。 若要完成这些步骤，需要一个安装了 Python 3.6（或更高版本）且具有 Internet 连接的环境。 [Azure Cloud Shell](https://shell.azure.com/) 预装了 Python 3.7：

1. 运行以下命令以安装 `azure.iot.device` 模块：

    ```bash
    pip install azure.iot.device
    ```

1. 运行以下命令，下载执行预配的 Python 脚本：

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. 若要在 IoT Central 应用程序中预配 `thermostat1` 下游设备，请运行以下命令，替换 `{your application id scope}` 和 `{your device primary key}`：

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

在 IoT Central 应用程序中，验证 thermostat1 设备的“设备状态”现在为“已预配” 。 

## <a name="configure-a-downstream-device"></a>配置下游设备

在上一部分中，你使用演示证书配置了 `edgegateway` 虚拟机，使其能够作为网关运行。 `leafdevice` 虚拟机已经准备好安装恒温器模拟器，该模拟器使用网关连接到 IoT Central。

`leafdevice` 虚拟机需要在 `edgegateway` 虚拟机上创建的根 CA 证书的副本。 将 /home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem 文件从 `edgegateway` 虚拟机复制到 `leafdevice` 虚拟机上的主目录。 可使用“scp”命令向/从 Linux 虚拟机复制文件。

若要了解如何检查从下游设备到网关的连接，请参阅[测试网关连接](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection)。

在 `leafdevice` 虚拟机上运行恒温器模拟器：

1. 将 Python 示例下载到主目录：

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. 安装 Azure IoT 设备 Python 模块：

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. 设置环境变量以配置示例。 用前面记录的 `thermostat1` 的主密钥替换 `{your device shared key}`。 这些变量假定网关虚拟机的名称为 `edgegateway`，恒温器设备的 ID 为 `thermostat1`：

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    请注意连接字符串使用网关设备的名称（而不是 IoT 中心的名称）的方式。

1. 若要运行代码，请使用以下命令：

    ```bash
    python3 simple_thermostat.py
    ```

    此命令的输出如下所示：

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. 若要查看 IoT Central 中的遥测，请导航到“thermostat1”设备的“概述”页 ：

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="显示下游设备遥测的屏幕截图。":::

    在“关于”页上，你可以查看从下游设备发送的属性值；在“命令”页上，你可以调用下游设备上的命令 。

## <a name="next-steps"></a>后续步骤

现在你已经了解了如何使用 IoT Central 配置透明网关，建议的下一步是进一步了解 [IoT Edge](../../iot-edge/about-iot-edge.md)。
