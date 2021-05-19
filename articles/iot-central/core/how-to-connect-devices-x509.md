---
title: 在 Azure IoT Central 应用程序中使用 X.509 证书连接设备
description: 如何使用用于 IoT Central 应用程序的 node.js 设备 SDK 通过 x.509 证书连接设备
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: cf0db71600c9350b4d70e6375f509a6e88709f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378326"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>如何使用用于 IoT Central 应用程序的 node.js 设备 SDK 通过 x.509 证书连接设备

IoT Central 支持共享的访问签名 (SAS) 和 X.509 证书，以保护设备与应用程序之间的通信。 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程使用 SAS。 本文介绍如何修改代码示例以使用 X.509。  在生产环境中，建议使用 X.509 证书。 有关详细信息，请参阅[连接到 Azure IoT Central](./concepts-get-connected.md)。

本文介绍了使用 X.509 的两种方法 - [组注册](how-to-connect-devices-x509.md#use-a-group-enrollment)（通常在生产环境中使用）和[单独注册](how-to-connect-devices-x509.md#use-an-individual-enrollment)（适用于测试）。

本文中的代码片段使用 JavaScript。 有关其他语言的代码示例，请参阅：

- [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_ll_client_x509_sample)
- [C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/X509DeviceCertWithChainSample)
- [Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
- [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)

## <a name="prerequisites"></a>先决条件

- 完成[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程。
- [Git](https://git-scm.com/download/)。
- 下载并安装 [OpenSSL](https://www.openssl.org/)。 如果使用的是 Windows，则可以使用 [SourceForge 上的 OpenSSL 页面](https://sourceforge.net/projects/openssl/)中的二进制文件。

## <a name="use-a-group-enrollment"></a>使用组注册

在生产环境中通过组注册使用 X.509 证书。 在组注册中，将根或中间 X.509 证书添加到 IoT Central 应用程序。 具有派生自根证书或中间证书的叶证书的设备可以连接到你的应用程序。

## <a name="generate-root-and-device-cert"></a>生成根证书和设备证书

本部分将使用 X.509 证书将设备连接到派生自注册组证书的证书，该证书可以连接到 IoT Central 应用程序。

> [!WARNING]
> 这种生成 X.509 证书的方法仅用于测试。 对于生产环境，应使用官方的安全机制来生成证书。

1. 打开命令提示符。 克隆证书生成脚本的 GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. 导航到证书生成器脚本，然后安装所需的包。

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

这些命令为根和设备证书各生成三个文件

filename | 内容
-------- | --------
\<name\>_cert pem | X509 证书的公共部分
\<name\>_key pem | X509 证书的私钥
\<name\>_fullchain pem | X509 证书的整个密钥链。

## <a name="create-a-group-enrollment"></a>创建组注册

1. 打开 IoT Central 应用程序并导航到左侧窗格中的“管理”，然后选择“设备连接”。

1. 选择“+ 创建注册组”，然后创建一个名为“MyX509Group”的新注册组，其证明类型为“证书 (X.509)”。

1. 打开创建的注册组，并选择“管理主要证书”。

1. 选择“文件”选项，然后上传之前生成的名为“mytestrootcert_cert.pem”的根证书文件：

    ![上传证书](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 若要完成验证，请生成验证码并复制，然后在命令提示符下使用该验证码创建 X.509 验证证书：

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. 上传已签名的验证证书“verification_cert.pem”以完成验证：

    ![已验证的证书](./media/how-to-connect-devices-x509/verified.png)

你现在可以连接具有派生自此主根证书的 X.509 证书的设备。

保存注册组后，记下 ID 范围。

## <a name="run-sample-device-code"></a>运行示例代码

1. 将“sampleDevice01_key.pem”和“sampleDevice01_cert.pem”文件复制到包含“simple_thermostat.js”应用程序的“azure-iot-sdk-node/device/samples/pnp”文件夹中  。 你在完成[连接设备 (JavaScript) 教程](./tutorial-connect-device.md)后使用过此应用程序。

1. 导航到包含“simple_thermostat.js”应用程序的“azure-iot-sdk-node/device/samples/pnp”文件夹，并运行以下命令以安装 X.509 包：

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. 在文本编辑器中打开“simple_thermostat.js”文件。

1. 编辑 `require` 语句，使其包括以下内容：

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. 将以下四行代码添加到“DPS 连接信息”部分以初始化 `deviceCert` 变量：

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. 通过将第一行替换为以下内容，编辑用于创建客户端的 `provisionDevice` 函数：

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. 在同一函数中，按如下所示修改设置 `deviceConnectionString` 变量的行：

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. 在 `main` 函数中，在调用 `Client.fromConnectionString` 的行后添加以下行：

    ```javascript
    client.setOptions(deviceCert);
    ```

1. 在 shell 环境中，设置以下两个环境变量：

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > 完成[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程后，可以设置其他所需的环境变量。

1. 执行脚本，验证该设备是否已成功预配。

    ```cmd/sh
    node simple_thermostat.js
    ```

    你还可以验证遥测数据是否显示在仪表板上。

    ![验证设备遥测数据](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>使用单独注册

通过单独注册使用 X.509 证书来测试设备和解决方案。 在单独注册中，IoT Central 应用程序中没有根或中间 X.509 证书。 设备使用自签名的 X.509 证书连接到应用程序。

## <a name="generate-self-signed-device-cert"></a>生成自签名设备证书

本部分将使用自签名的 X.509 证书连接用于单独注册的设备，单独注册用于注册单个设备。 自签名证书仅用于测试。

通过运行脚本创建自签名的 X.509 设备证书。 请确保仅对证书名称使用小写字母数字和连字符：

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>创建单独注册

1. 在 Azure IoT Central 应用程序中，选择“设备”，然后根据恒温器设备模板创建“设备 ID”为“mytestselfcertprimary”的新设备。 记下“ID 范围”以便稍后使用。

1. 打开所创建的设备，然后选择“连接”。

1. 选择“单独注册”作为“连接方法” ，选择“证书(X.509)”作为连接机制：

    ![单独注册](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. 选择“主要证书”下的文件选项，并上传之前生成的名为“mytestselfcertprimary_cert.pem”的证书文件。

1. 选择次要证书的文件选项，并上传名为“mytestselfcertsecondary_cert.pem”的证书文件。 然后选择“保存”：

    ![上传用于单独注册的证书](./media/how-to-connect-devices-x509/individual-enrollment.png)

设备现在已预配 X.509 证书。

## <a name="run-a-sample-individual-enrollment-device"></a>运行示例单独注册设备

1. 将“mytestselfcertprimary_key.pem”和“mytestselfcertprimary_cert.pem”文件复制到包含“simple_thermostat.js”应用程序的“azure-iot-sdk-node/device/samples/pnp”文件夹中 。 你在完成[连接设备 (JavaScript) 教程](./tutorial-connect-device.md)后使用过此应用程序。

1. 按如下所示修改上述示例中使用的环境变量：

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. 执行脚本，验证该设备是否已成功预配。

    ```cmd/sh
    node environmentalSensor.js
    ```

    你还可以验证遥测数据是否显示在仪表板上。

    ![遥测单独注册](./media/how-to-connect-devices-x509/telemetry-primary.png)

还可以对“mytestselfcertsecondary”证书重复上述步骤。

## <a name="next-steps"></a>后续步骤

了解如何使用 X.509 证书连接设备后，接下来建议了解如何[使用 Azure CLI 监视设备连接性](howto-monitor-devices-azure-cli.md)
