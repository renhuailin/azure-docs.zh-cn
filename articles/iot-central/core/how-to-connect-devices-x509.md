---
title: 在 Azure IoT Central 应用程序中使用 X.509 证书连接设备
description: 如何使用用于 IoT Central 应用程序的 node.js 设备 SDK 通过 x.509 证书连接设备
author: dominicbetts
ms.author: dobett
ms.date: 06/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 308cd5186d97e87ff044db496809b04def41265b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667102"
---
# <a name="how-to-connect-devices-with-x509-certificates-to-iot-central-application"></a>如何使用 X.509 证书将设备连接到 IoT Central 应用程序

IoT Central 支持共享的访问签名 (SAS) 和 X.509 证书，以保护设备与应用程序之间的通信。 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程使用 SAS。 本文介绍如何修改代码示例以使用 X.509 证书。 在生产环境中，建议使用 X.509 证书。 有关详细信息，请参阅[连接到 Azure IoT Central](./concepts-get-connected.md)。

本指南介绍 X.509 证书的两种用法 - [组注册](how-to-connect-devices-x509.md#use-group-enrollment)（通常在生产环境中使用）和[单独注册](how-to-connect-devices-x509.md#use-individual-enrollment)（适用于测试）。 本文还将介绍如何在证书过期时[滚动更新设备证书](#roll-x509-device-certificates)，以便能够保持连接。

本指南是根据使用 C#、Java、JavaScript 和 Python 的[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)教程中演示的示例编写的。 有关使用 C 编程语言的示例，请参阅[使用注册组预配多个 X.509 设备](../../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md)。

## <a name="prerequisites"></a>先决条件

完成[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程。 这包括为你选择的编程语言安装必备组件。

在本操作指南中，你将生成一些测试 X.509 证书。 若要生成这些证书，需要：

- 安装了 [Node.js](https://nodejs.org/) 6 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 本教程中的说明假设在 Windows 命令提示符下运行 **node** 命令。 但是，可以在许多其他的操作系统上使用 Node.js。
- [用于 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存储库的本地副本，其中包含用于生成测试 X.509 证书的脚本。 使用此链接下载存储库副本：[下载 ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip)。 然后将该文件解压缩到本地计算机上的适当位置。

## <a name="use-group-enrollment"></a>使用组注册

在生产环境中通过组注册使用 X.509 证书。 在组注册中，将根或中间 X.509 证书添加到 IoT Central 应用程序。 具有派生自根证书或中间证书的叶证书的设备可以连接到你的应用程序。

### <a name="generate-root-and-device-certificates"></a>生成根证书和设备证书

在本部分，你将使用派生自 IoT Central 注册组证书的 X.509 证书来连接某个设备。

> [!WARNING]
> 这种生成 X.509 证书的方法仅用于测试。 对于生产环境，应使用官方的安全机制来生成证书。

1. 导航到你已下载的用于 Node.js 的 Microsoft Azure IoT SDK 中的证书生成器脚本。 安装所需的包：

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

这些命令生成以下根证书和设备证书

| filename | 内容 |
| -------- | -------- |
| mytestrootcert_cert.pem | 根 X509 证书的公共部分 |
| mytestrootcert_key.pem | 根 X509 证书的私钥 |
| mytestrootcert_fullchain.pem | 根 X509 证书的整个密钥链。 |
| sampleDevice01_cert.pem | 设备 X509 证书的公共部分 |
| sampleDevice01_key.pem | 设备 X509 证书的私钥 |
| sampleDevice01_fullchain.pem | 设备 X509 证书的整个密钥链。 |

请记下这些文件的位置。 稍后需要用到此信息。

### <a name="create-a-group-enrollment"></a>创建组注册

1. 打开 IoT Central 应用程序并导航到左侧窗格中的“管理”，然后选择“设备连接”。

1. 选择“+ 新建”，然后创建一个名为“MyX509Group”的新注册组，其证明类型为“证书(X.509)”。

1. 打开创建的注册组，并选择“管理主要证书”。

1. 选择“文件”选项，上传前面生成的名为 mytestrootcert_cert.pem 的根证书文件：

    ![上传证书](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 若要完成验证，请生成验证码并复制，然后在命令提示符下使用该验证码创建 X.509 验证证书：

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. 选择“验证”，上传已签名的验证证书 verification_cert.pem 以完成验证：

    ![已验证的证书](./media/how-to-connect-devices-x509/verified.png)

你现在可以连接具有派生自此主根证书的 X.509 证书的设备。

保存注册组后，记下 ID 范围。

### <a name="run-sample-device-code"></a>运行示例代码

:::zone pivot="programming-language-csharp"

如果使用的是 Windows，则 X.509 证书必须位于 Windows 证书存储中，这样才能正常运行该示例。 若要将证书添加到存储中，请执行以下操作：

1. 使用 `openssl` 从 PEM 文件创建 PFX 文件。 运行这些命令时，系统会提示你创建密码。 请记下该密码，因为在下一步骤中需要用到它：

    ```bash
    openssl pkcs12 -inkey sampleDevice001_key.pem -in sampleDevice001_cert.pem -export -out sampledevice001.pfx
    openssl pkcs12 -inkey mytestrootcert_key.pem -in mytestrootcert_cert.pem -export -out mytestrootcert.pfx
    ```

1. 在 Windows 资源管理器中，双击每个 PFX 文件。 在“证书导入向导”中，选择“当前用户”作为存储位置，输入在上一步骤中记下的密码，然后让向导自动选择证书存储 。 向导会将证书导入到当前用户的个人存储中。

若要修改示例代码以使用这些证书，请执行以下操作：

1. 在 IoTHubDeviceSamples Visual Studio 解决方案中，打开 TemperatureController 项目中的 Parameter.cs 文件。

1. 将以下两个参数定义添加到类中：

    ```csharp
    [Option(
        'x',
        &quot;CertificatePath&quot;,
        HelpText = &quot;(Required if DeviceSecurityType is \"dps\"). \nThe device PFX file to use during device provisioning." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_CERT\".")]
    public string CertificatePath { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_CERT");

    [Option(
        'p',
        "CertificatePassword",
        HelpText = "(Required if DeviceSecurityType is \"dps\"). \nThe password of the PFX certificate file." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_PASSWORD\".")]
    public string CertificatePassword { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_PASSWORD");
    ```

    保存更改。

1. 在 IoTHubDeviceSamples Visual Studio 解决方案中，打开 TemperatureController 项目中的 Program.cs 文件。

1. 添加以下 `using` 语句：

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.IO;
    ```

1. 将以下方法添加到该类：

    ```csharp
    private static X509Certificate2 LoadProvisioningCertificate(Parameters parameters)
    {
        var certificateCollection = new X509Certificate2Collection();
        certificateCollection.Import(
            parameters.CertificatePath,
            parameters.CertificatePassword,
            X509KeyStorageFlags.UserKeySet);

        X509Certificate2 certificate = null;

        foreach (X509Certificate2 element in certificateCollection)
        {
            Console.WriteLine($"Found certificate: {element?.Thumbprint} {element?.Subject}; PrivateKey: {element?.HasPrivateKey}");
            if (certificate == null && element.HasPrivateKey)
            {
                certificate = element;
            }
            else
            {
                element.Dispose();
            }
        }

        if (certificate == null)
        {
            throw new FileNotFoundException($"{parameters.CertificatePath} did not contain any certificate with a private key.");
        }

        Console.WriteLine($"Using certificate {certificate.Thumbprint} {certificate.Subject}");

        return certificate;
    }
    ```

1. 在 `SetupDeviceClientAsync` 方法中，将 `case "dps"` 的代码块替换为以下代码：

    ```csharp
    case "dps":
        s_logger.LogDebug($"Initializing via DPS");
        Console.WriteLine($"Loading the certificate...");
        X509Certificate2 certificate = LoadProvisioningCertificate(parameters);
        DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, certificate, cancellationToken);
        var authMethod = new DeviceAuthenticationWithX509Certificate(dpsRegistrationResult.DeviceId, certificate);
        deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
        break;
    ```

1. 将 `ProvisionDeviceAsync` 方法替换为以下代码：

    ```csharp
    private static async Task<DeviceRegistrationResult> ProvisionDeviceAsync(Parameters parameters, X509Certificate2 certificate, CancellationToken cancellationToken)
    {
        SecurityProvider security = new SecurityProviderX509Certificate(certificate);
        ProvisioningTransportHandler mqttTransportHandler = new ProvisioningTransportHandlerMqtt();
        ProvisioningDeviceClient pdc = ProvisioningDeviceClient.Create(parameters.DpsEndpoint, parameters.DpsIdScope, security, mqttTransportHandler);

        var pnpPayload = new ProvisioningRegistrationAdditionalData
        {
            JsonData = PnpConvention.CreateDpsPayload(ModelId),
        };
        return await pdc.RegisterAsync(pnpPayload, cancellationToken);
    }
    ```

    保存更改。

1. 将以下环境变量添加到项目中：

    - `IOTHUB_DEVICE_X509_CERT`: `<full path to folder that contains PFX files>sampleDevice01.pfx`
    - `IOTHUB_DEVICE_X509_PASSWORD`：创建 sampleDevice01.pfx 文件时使用的密码。

1. 生成并运行应用程序。 验证设备预配是否成功。

:::zone-end

:::zone pivot="programming-language-java"

1. 导航到包含 pom.xml 文件的 azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample 文件夹，以及用于温度控制器设备示例的 src 文件夹 。

1. 编辑 pom.xml 文件，在 `<dependencies>` 节点中添加以下依赖关系配置：

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.sdk.iot.provisioning.security</groupId>
        <artifactId>${x509-provider-artifact-id}</artifactId>
        <version>${x509-provider-version}</version>
    </dependency>
    ```

    保存更改。

1. 在文本编辑器中打开 src/main/java/samples/com/microsoft/azure/sdk/iot/device/TemperatureController.java 文件。

1. 将 `SecurityProviderSymmetricKey` import 语句替换为以下 import 语句：

    ```java
    import com.microsoft.azure.sdk.iot.provisioning.security.SecurityProvider;
    import com.microsoft.azure.sdk.iot.provisioning.security.hsm.SecurityProviderX509Cert;
    import com.microsoft.azure.sdk.iot.provisioning.security.exceptions.SecurityProviderException;
    ```

1. 添加以下 import 语句：

    ```java
    import java.nio.file.*;
    ```

1. 将 `SecurityProviderException` 添加到 `main` 方法引发的异常列表中：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, ProvisioningDeviceClientException, InterruptedException, SecurityProviderException {
    ```

1. 将 `initializeAndProvisionDevice` 方法替换为以下代码：

    ```java
    private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException, SecurityProviderException {
        String deviceX509Key = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_KEY"))));
        String deviceX509Cert = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_CERT"))));
        SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(deviceX509Cert, deviceX509Key, null);
        ProvisioningDeviceClient provisioningDeviceClient;
        ProvisioningStatus provisioningStatus = new ProvisioningStatus();

        provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityProviderX509);

        AdditionalData additionalData = new AdditionalData();
        additionalData.setProvisioningPayload(com.microsoft.azure.sdk.iot.provisioning.device.plugandplay.PnpHelper.createDpsPayload(MODEL_ID));

        provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

        while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
        {
            if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
            {
                provisioningStatus.exception.printStackTrace();
                System.out.println("Registration error, bailing out");
                break;
            }
            System.out.println("Waiting for Provisioning Service to register");
            Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
        }

        ClientOptions options = new ClientOptions();
        options.setModelId(MODEL_ID);

        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
            System.out.println("IotHUb Uri : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri());
            System.out.println("Device ID : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId());

            String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
            String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

            log.debug("Opening the device client.");
            deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityProviderX509, IotHubClientProtocol.MQTT, options);
            deviceClient.open();
        }
    }
    ```

    保存更改。

1. 在 shell 环境中，添加以下两个环境变量。 请务必提供 PEM 文件的完整路径，并为操作系统使用正确的路径分隔符：

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > 完成[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程后，可以设置其他所需的环境变量。

1. 生成并运行应用程序。 验证设备预配是否成功。

:::zone-end

:::zone pivot="programming-language-javascript"

1. 导航到包含 pnpTemperatureController.js 应用程序的 azure-iot-sdk-node/device/samples/pnp 文件夹，并运行以下命令以安装 X.509 包：

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. 在文本编辑器中打开 pnpTemperatureController.js 文件。

1. 编辑 `require` 语句以包含以下代码：

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

1. 通过将第一行替换为以下代码，编辑用于创建客户端的 `provisionDevice` 函数：

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

    保存更改。

1. 在 shell 环境中，添加以下两个环境变量。 请务必提供 PEM 文件的完整路径，并为操作系统使用正确的路径分隔符：

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > 完成[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程后，可以设置其他所需的环境变量。

1. 执行脚本，验证设备预配是否成功：

    ```cmd/sh
    node pnpTemperatureController.js
    ```

:::zone-end

:::zone pivot="programming-language-python"

1. 导航到 azure-iot-device/samples/pnp 文件夹，并在文本编辑器中打开 temp_controller_with_thermostats.py 文件。

1. 添加以下 `from` 语句以导入 X.509 功能：

    ```python
    from azure.iot.device import X509
    ```

1. 按下面所示修改 `provision_device` 函数的第一个部分：

    ```python
    async def provision_device(provisioning_host, id_scope, registration_id, x509, model_id):
        provisioning_device_client = ProvisioningDeviceClient.create_from_x509_certificate(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            x509=x509,
        )
    ```

1. 在 `main` 函数中，将用于设置 `symmetric_key` 变量的行替换为以下代码：

    ```python
    x509 = X509(
        cert_file=os.getenv("IOTHUB_DEVICE_X509_CERT"),
        key_file=os.getenv("IOTHUB_DEVICE_X509_KEY"),
    )
    ```

1. 在 `main` 函数中，将 `provision_device` 函数调用替换为以下代码：

    ```python
    registration_result = await provision_device(
        provisioning_host, id_scope, registration_id, x509, model_id
    )
    ```

1. 在 `main` 函数中，将 `IoTHubDeviceClient.create_from_symmetric_key` 函数调用替换为以下代码：

    ```python
    device_client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=registration_result.registration_state.assigned_hub,
        device_id=registration_result.registration_state.device_id,
        product_info=model_id,
    )
    ```

    保存更改。

1. 在 shell 环境中，添加以下两个环境变量。 请务必提供 PEM 文件的完整路径，并为操作系统使用正确的路径分隔符：

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > 完成[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)教程后，可以设置其他所需的环境变量。

1. 执行脚本，验证设备预配是否成功：

    ```cmd/sh
    python temp_controller_with_thermostats.py
    ```

:::zone-end

验证遥测数据是否出现在 IoT Central 应用程序的设备视图中：

![显示进入 IoT Central 应用程序的遥测数据的屏幕截图。](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-individual-enrollment"></a>使用单独注册

通过单独注册使用 X.509 证书来测试设备和解决方案。 在单独注册中，IoT Central 应用程序中没有根或中间 X.509 证书。 设备使用自签名的 X.509 证书连接到应用程序。

### <a name="generate-self-signed-device-certificate"></a>生成自签名设备证书

本部分将使用自签名的 X.509 证书连接用于单独注册的设备，单独注册用于注册单个设备。 自签名证书仅用于测试。

> [!WARNING]
> 这种生成 X.509 证书的方法仅用于测试。 对于生产环境，应使用官方的安全机制来生成证书。

通过运行以下命令创建自签名的 X.509 设备证书：

```cmd/sh
  cd azure-iot-sdk-node/provisioning/tools
  node create_test_cert.js device mytestselfcertprimary
  node create_test_cert.js device mytestselfcertsecondary 
```

> [!TIP]
> 设备 ID 中可以包含字母、数字和 `-` 字符。

### <a name="create-individual-enrollment"></a>创建单独注册

1. 在 Azure IoT Central 应用程序中，选择“设备”，然后根据恒温器设备模板创建“设备 ID”为“mytestselfcertprimary”的新设备。 记下“ID 范围”以便稍后使用。

1. 打开所创建的设备，然后选择“连接”。

1. 选择“单独注册”作为“连接方法” ，选择“证书(X.509)”作为连接机制：

    ![单独注册](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. 选择“主要证书”下的文件选项，并上传之前生成的名为“mytestselfcertprimary_cert.pem”的证书文件。

1. 选择次要证书的文件选项，并上传名为“mytestselfcertsecondary_cert.pem”的证书文件。 然后选择“保存”：

    ![上传用于单独注册的证书](./media/how-to-connect-devices-x509/individual-enrollment.png)

设备现在已预配 X.509 证书。

### <a name="run-a-sample-individual-enrollment-device"></a>运行示例单独注册设备

1. 将 mytestselfcertprimary_key.pem 和 mytestselfcertprimary_cert.pem 文件复制到包含 pnpTemperatureController.js 应用程序的 azure-iot-sdk-node/device/samples/pnp 文件夹。 你在完成[连接设备 (JavaScript) 教程](./tutorial-connect-device.md)后使用过此应用程序。

1. 按如下所示修改上述示例中使用的环境变量：

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. 执行脚本，验证是否已成功预配设备：

    ```cmd/sh
    node environmentalSensor.js
    ```

    还可以验证遥测数据是否出现在设备视图中。

    ![遥测单独注册](./media/how-to-connect-devices-x509/telemetry-primary.png)

还可以对“mytestselfcertsecondary”证书重复上述步骤。

## <a name="connect-an-iot-edge-device"></a>连接 IoT Edge 设备

本部分假设使用组注册来连接 IoT Edge 设备。 遵循前面部分中所述的步骤完成以下操作：

- [生成根证书和设备证书](#generate-root-and-device-certificates)
- [创建组注册](#create-a-group-enrollment) <!-- No slightly different type of enrollment group - UPDATE!! -->

若要使用 X.509 设备证书将 IoT Edge 设备连接到 IoT Central，请执行以下操作：

- 将设备证书和密钥文件复制到 IoT Edge 设备。 在前面的组注册示例中，这些文件名为 sampleDevice01_key.pem 和 sampleDevice01_cert.pem 。
- 在 IoT Edge 设备上，按下面所示编辑 /etc/iotedge/config.yaml 配置文件中的 `provisioning` 节：

    ```yaml
    # DPS X.509 provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "<SCOPE_ID>"
      attestation:
        method: "x509"
    #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
        identity_cert: "file:///<path>/sampleDevice01_cert.pem"
        identity_pk: "file:///<path>/sampleDevice01_key.pem"
    #  always_reprovision_on_startup: true
    #  dynamic_reprovisioning: false
    ```

    > [!TIP]
    > 无需为 `registration_id` 添加值。 IoT Edge 可以使用 X.509 证书中的 CN 值。

- 运行以下命令重启 IoT Edge 运行时：

    ```bash
    sudo systemctl restart iotedge
    ```

有关详细信息，请参阅[使用 X.509 证书在 Linux 上大规模创建和预配 IoT Edge 设备](../../iot-edge/how-to-provision-devices-at-scale-linux-x509.md)。

## <a name="connect-an-iot-edge-leaf-device"></a>连接 IoT Edge 叶设备

IoT Edge 使用 X.509 证书来保护叶设备与充当网关的 IoT Edge 设备之间的连接。 若要详细了解如何配置此方案，请参阅[将下游设备连接到 Azure IoT Edge 网关](../../iot-edge/how-to-connect-downstream-device.md)。

## <a name="roll-x509-device-certificates"></a>滚动更新 X.509 设备证书

在 IoT Central 应用程序的生命周期内，需要滚动更新 X.509 证书。 例如：

- 如果出现安全违规，滚动更新证书是一种安全最佳做法，可帮助保护系统。
- X.509 证书具有过期日期。 滚动更新证书的频率取决于解决方案的安全需求。 其解决方案涉及到高度敏感数据的客户可以每日滚动更新证书，而其他客户则可以每隔数年滚动更新其证书。

IoT Central 允许配置主要和次要 X.509 证书，以确保连接不会中断。 如果主要证书和次要证书的过期日期不同，你可以滚动更新已过期的证书，而设备仍可使用另一个证书进行连接。

有关详细信息，请参阅[“假定漏洞”方法](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)。

本部分介绍如何在 IoT Central 中滚动更新证书。 在 IoT Central 中滚动更新证书时，还需要将新设备证书复制到设备。

### <a name="obtain-new-x509-certificates"></a>获取新的 X.509 证书

从证书提供者获取新的 X.509 证书。 可以使用 OpenSSL 之类的工具创建自己的 X.509 证书。 此方法适合用于测试 X.509 证书，但提供的安全保障较低。 除非你已准备好充当自己的 CA 提供者，否则请仅将此方法用于测试。

### <a name="enrollment-groups-and-security-breaches"></a>注册组和安全违规

若要为了响应安全漏洞而更新组注册，应使用以下方法来立即更新当前证书。 如果主要证书和次要证书都已泄露，请针对这两个证书完成以下步骤：

1. 在左窗格中导航到“管理”，然后选择“设备连接”。

2. 选择“注册组”，然后在列表中选择组名称。

3. 对于证书更新，选择“管理主要证书”或“管理次要证书”。

4. 在注册组中添加并验证根 X.509 证书。

### <a name="individual-enrollments-and-security-breaches"></a>单独注册和安全违规

如果你是为了响应安全漏洞而滚动更新证书，请使用以下方法立即更新当前证书。 如果主要证书和次要证书都已泄露，请针对这两个证书完成以下步骤：

1. 选择“设备”，然后选择设备。

1. 选择“连接”，然后将连接方法选为“个人注册”

1. 选择“证书(X.509)”作为机制。

1. 对于证书更新，选择文件夹图标以选择注册条目的待上传新证书。 选择“保存”。

### <a name="enrollment-groups-and-certificate-expiration"></a>注册组和证书过期

若要处理证书过期问题，请使用以下方法立即更新当前证书：

1. 在左窗格中导航到“管理”，然后选择“设备连接”。

2. 选择“注册组”，然后在列表中选择组名称。

3. 对于证书更新，选择“管理主要证书”。

4. 在注册组中添加并验证根 X.509 证书。

5. 以后当次要证书过期时，请回来并更新次要证书。

### <a name="individual-enrollments-and-certificate-expiration"></a>单独注册和证书过期

如果滚动更新证书的目的是处理证书过期问题，则应按如下所述使用辅助证书配置，以减少设备尝试预配时造成的停机时间。

当次要证书即将过期，因此需要滚动更新时，可以换用主要证书配置。 以这种方式在主要证书与辅助证书之间轮换可以减少设备尝试预配时造成的停机时间。

1. 选择“设备”，然后选择设备。

2. 选择“连接”，然后将连接方法选为“个人注册”

3. 选择“证书(X.509)”作为机制。

4. 对于次要证书更新，选择文件夹图标以选择注册条目的待上传新证书。 选择“保存”。

5. 以后当主要证书过期时，请回来并更新主要证书。

## <a name="next-steps"></a>后续步骤

了解如何使用 X.509 证书连接设备后，接下来建议了解如何[使用 Azure CLI 监视设备连接](howto-monitor-devices-azure-cli.md)。
