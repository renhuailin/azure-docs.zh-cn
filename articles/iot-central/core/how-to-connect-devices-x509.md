---
title: 使用 Azure IoT Central 应用程序中的 x.509 证书连接设备
description: 如何使用 Node.js 设备 SDK IoT Central 应用程序将设备连接到 x.509 证书
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 33d837f63fca2062ec930fcf0d64ee01ea822c99
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989524"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>如何使用 Node.js 设备 SDK IoT Central 应用程序将设备连接到 x.509 证书

IoT Central 支持共享访问签名 (SAS) 和 x.509 证书，以保护设备与应用程序之间的通信。 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device-nodejs.md)教程使用 SAS。 本文介绍如何修改代码示例以使用 x.509。  在生产环境中，建议使用 x.509 证书。 有关详细信息，请参阅 [连接到 Azure IoT Central](./concepts-get-connected.md)。

本文介绍了在生产环境 [中通常使用的 x.509 的两](how-to-connect-devices-x509.md#use-a-group-enrollment) 种使用方式的方法，以及用于测试的 [单个注册](how-to-connect-devices-x509.md#use-an-individual-enrollment) 。

## <a name="prerequisites"></a>先决条件

- 完成 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序 ( # A0) ](./tutorial-connect-device-nodejs.md) 教程。
- [Git](https://git-scm.com/download/)。
- 下载并安装 [OpenSSL](https://www.openssl.org/)。 如果使用的是 Windows，则可以使用 [SourceForge 上的 OpenSSL 页面](https://sourceforge.net/projects/openssl/)中的二进制文件。

## <a name="use-a-group-enrollment"></a>使用组注册

在生产环境中将 x.509 证书与组注册一起使用。 在组注册中，将根或中间的 x.509 证书添加到 IoT Central 应用程序。 具有派生自根证书或中间证书的叶证书的设备可以连接到你的应用程序。

## <a name="generate-root-and-device-cert"></a>生成根证书和设备证书

在本部分中，将使用 x.509 证书将设备与派生自注册组的证书的证书连接，该证书可以连接到 IoT Central 应用程序。

> [!WARNING]
> 此方法生成 x.509 证书仅用于测试。 对于生产环境，应该使用官方的安全机制来生成证书。

1. 打开命令提示符。 克隆证书生成脚本的 GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. 导航到证书生成器脚本并安装所需的包：

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. 创建根证书，然后通过运行脚本派生设备证书：

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > 设备 ID 中可以包含字母、数字和 `-` 字符。

这些命令为根和设备证书生成三个文件

filename | 内容
-------- | --------
\<name\>_cert pem | X509 证书的公共部分
\<name\>_key pem | X509 证书的私钥
\<name\>_fullchain pem | X509 证书的整个密钥链。

## <a name="create-a-group-enrollment"></a>创建组注册

1. 打开 IoT Central 应用程序并导航到左侧窗格中的 " **管理**  "，然后选择 " **设备连接**"。

1. 选择 " **+ 创建注册组**"，然后创建一个名为 _MyX509Group_ 的新注册组，其证明类型为 **证书 (x.509)**。

1. 打开创建的注册组，并选择 " **管理主要**"。

1. 选择 "文件" 选项，然后上传之前生成的名为 _mytestrootcert_cert_ 的根证书文件：

    ![证书上传](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 若要完成验证，请生成验证代码，复制它，然后在命令提示符下使用它创建 x.509 验证证书：

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. 将签名的验证证书上传 _verification_cert_ 以完成验证：

    ![验证的证书](./media/how-to-connect-devices-x509/verified.png)

你现在可以连接具有派生自此主根证书的 x.509 证书的设备。

保存注册组后，记下 "ID" 范围。

## <a name="run-sample-device-code"></a>运行示例设备代码

1. 将 **sampleDevice01_key** 和 **sampleDevice01_cert pem** 文件复制到包含 **simple_thermostat.js** 应用程序的 _azure iot sdk 节点/设备/示例/pnp_ 文件夹中。 完成 " [连接设备 ( # A0) 教程](./tutorial-connect-device-nodejs.md)时使用此应用程序。

1. 导航到包含 **simple_thermostat.js** 应用程序的 _azure iot sdk 节点/设备/示例/pnp_ 文件夹，并运行以下命令安装 x.509 包：

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. 在文本编辑器中打开 **simple_thermostat.js** 文件。

1. 编辑 `require` 语句，使其包含以下内容：

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. 将以下四行代码添加到 "DPS 连接信息" 部分以初始化 `deviceCert` 变量：

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. `provisionDevice`通过将第一行替换为以下内容，编辑创建客户端的函数：

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. 在同一函数中，按如下所示修改设置 `deviceConnectionString` 变量的行：

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. 在 `main` 函数中，在调用的行后添加以下行 `Client.fromConnectionString` ：

    ```javascript
    client.setOptions(deviceCert);
    ```

1. 在 shell 环境中，设置以下两个环境变量：

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > 完成 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device-nodejs.md) 教程时，可以设置其他所需的环境变量。

1. 执行脚本并验证是否已成功设置设备：

    ```cmd/sh
    node simple_thermostat.js
    ```

    你还可以验证遥测显示在仪表板上。

    ![验证设备遥测](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>使用单个注册

使用 x.509 证书和单个注册来测试设备和解决方案。 在单个注册中，IoT Central 应用程序中没有根或中间的 x.509 证书。 设备使用自签名的 x.509 证书连接到应用程序。

## <a name="generate-self-signed-device-cert"></a>生成自签名设备证书

在本部分中，将使用自签名的 x.509 证书来连接用于注册单个设备的单个注册设备。 自签名证书仅用于测试。

通过运行脚本创建自签名 x.509 设备证书。 请确保仅对证书名称使用小写字母数字和连字符：

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>创建单个注册

1. 在 Azure IoT Central 应用程序中，选择 " **设备**"，然后从恒温器设备模板创建 **设备 ID** 为 _mytestselfcertprimary_ 的新设备。 记下 **ID 范围**，稍后使用。

1. 打开所创建的设备，然后选择 " **连接**"。

1. 选择 " **单独注册** " 作为 **Connect 方法** ， **(的证书)** 作为机制：

    ![单独注册](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. 选择 "主文件" 下的 "文件" 选项，并上传之前生成的名为 _mytestselfcertprimary_cert_ 的证书文件。

1. 选择辅助证书的文件选项，并上传名为 mytestselfcertsecondary_cert 的证书文件 _。_ 然后选择 " **保存**"：

    ![上传单个注册证书](./media/how-to-connect-devices-x509/individual-enrollment.png)

设备现在预配了 x.509 证书。

## <a name="run-a-sample-individual-enrollment-device"></a>运行单个注册设备示例

1. 将 _mytestselfcertprimary_key_ 和 _mytestselfcertprimary_cert pem_ 文件复制到包含 **simple_thermostat.js** 应用程序的 _azure iot sdk 节点/设备/示例/pnp_ 文件夹中。 完成 " [连接设备 ( # A0) 教程](./tutorial-connect-device-nodejs.md)时使用此应用程序。

1. 修改中使用的环境变量，如下所示：

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. 执行脚本并验证是否已成功设置设备：

    ```cmd/sh
    node environmentalSensor.js
    ```

    你还可以验证遥测显示在仪表板上。

    ![遥测单个注册](./media/how-to-connect-devices-x509/telemetry-primary.png)

还可以对 _mytestselfcertsecondary_ 证书重复上述步骤。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 x.509 证书连接设备，接下来建议的下一步是了解如何 [使用监视设备连接性 Azure CLI](howto-monitor-devices-azure-cli.md)
