---
title: 教程 - 使用自定义硬件安全模块 (HSM) 向 Azure IoT 中心预配 X.509 设备
description: 本教程使用注册组。 在本教程中，你将了解如何使用自定义硬件安全模块 (HSM) 和适用于 Azure IoT 中心设备预配服务 (DPS) 的 C 设备 SDK 预配 X.509 设备。
author: wesmc7777
ms.author: wesmc
ms.date: 05/24/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f2e6918e6cdc6eb87fe80e9110183ed5a0a3693b
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106688"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>教程：使用注册组预配多个 X.509 设备

在本教程中，你将了解如何预配使用 X.509 证书进行身份验证的 IoT 设备组。 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的示例设备代码将在开发计算机上执行，以模拟 x.509 设备的预配。 在实际设备上，将从 IoT 设备部署并运行设备代码。

在继续学习本教程之前，请确保至少已完成[通过 Azure 门户设置 IoT 中心设备预配服务](quick-setup-auto-provision.md)中的步骤。 此外，如果不熟悉自动预配过程，请查看[预配](about-iot-dps.md#provisioning-process)概述。 

Azure IoT 设备预配服务支持两种类型的预配设备注册：

* [注册组](concepts-service.md#enrollment-group)：用于注册多个相关设备。
* [单独注册](concepts-service.md#individual-enrollment)：用于注册单个设备。

本教程与前面演示如何使用注册组预配设备集的教程类似。 但是，本教程将使用 X.509 证书而不是对称密钥。 查看本部分前面的教程，了解使用[对称密钥](./concepts-symmetric-key-attestation.md)的简单方法。

本教程将演示[自定义 HSM 示例](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example)，该示例提供了用于与基于硬件的安全存储连接的存根实现。 [硬件安全模块 (HSM)](./concepts-service.md#hardware-security-module) 用于安全、基于硬件的设备机密存储。 HSM 可以与对称密钥、X.509 证书或 TPM 证明配合使用，为机密提供安全存储。 虽然不要求对设备机密进行基于硬件的存储，但强烈建议使用它来帮助保护敏感信息，如设备证书的私钥。


在本教程中，你将完成以下目标：

> [!div class="checklist"]
> * 创建一个信任证书链以整理一组使用 X.509 证书的设备。
> * 使用签名证书和证书链完成所有权证明。
> * 创建使用证书链的新组注册
> * 使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的代码设置用于预配设备的开发环境
> * 使用 SDK 中的自定义硬件安全模块 (HSM) 示例通过证书链预配设备。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

以下先决条件适用于用于模拟设备的 Windows 开发环境。 对于 Linux 或 macOS，请参阅 SDK 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019，已启用[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载。 Visual Studio 2015 和 Visual Studio 2017 也受支持。 

    本文使用 Visual Studio 来生成将部署到 IoT 设备的设备示例代码。  这并不意味着设备本身需要 Visual Studio。

* 已安装最新版本的 [Git](https://git-scm.com/download/)。

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 SDK 包括 X.509 设备利用 DPS 进行预配时所用的示例代码和工具。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装之前，必须在计算机上安装 Visual Studio 必备组件（[Visual Studio](https://visualstudio.microsoft.com/vs/) 和[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

2. 找到[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) Azure IoT C SDK 的标记名称。

3. 打开命令提示符或 Git Bash shell。 运行以下命令以克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 使用在上一步找到的标记作为 `-b` 参数的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

4. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 创建的 `cmake` 目录将包含自定义 HSM 示例和使用自定义 HSM 提供 X.509 身份验证的示例设备预配代码。 

    在 `cmake` 目录中运行以下命令，以生成特定于开发平台的 SDK 版本。 该生成将包含对自定义 HSM 示例的引用。 

    在指定下面与 `-Dhsm_custom_lib` 一起使用的路径时，请确保使用先前创建的 `cmake` 目录的相对路径。 下面所示的相对路径只是一个示例。

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。

    生成成功后，将在 `cmake` 目录中生成一个 Visual Studio 解决方案。 最后的几个输出行如下所示：

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>创建 X.509 证书链

在本部分中，将生成一个包含三个证书的 X.509 证书链，以便使用本教程测试每台设备。 证书将具有以下层次结构。

![教程设备证书链](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[根证书](concepts-x509-attestation.md#root-certificate)：你将完成[所有权证明](how-to-verify-certificates.md)来验证根证书。 此验证将使 DPS 能够信任该证书并验证由其签名的证书。

[中间证书](concepts-x509-attestation.md#intermediate-certificate)：中间证书通常用于按产品线、公司部门或其他标准对设备进行逻辑分组。 本教程将使用由一个中间证书组成的证书链。 中间证书将由根证书签名。 此证书还将用于 DPS 中创建的注册组，以逻辑方式对一组设备进行分组。 此配置允许管理设备证书由同一中间证书签名的整组设备。 你可以创建注册组以启用或禁用一组设备。 有关禁用一组设备的详细信息，请参阅[使用注册组禁用 X.509 中间或根 CA 证书](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)

[设备证书](concepts-x509-attestation.md#end-entity-leaf-certificate)：设备（叶）证书将由中间证书签名，并与其私钥一起存储在设备上。 理想情况下，这些敏感项将通过 HSM 安全地存储。 尝试预配时，每台设备都将提供此证书、私钥以及证书链。 

#### <a name="create-root-and-intermediate-certificates"></a>创建根证书和中间证书

若要创建证书链的根部分和中间部分，请执行以下操作：

> [!IMPORTANT]
> 对于本文，请仅使用 Bash shell 方法。 可以使用 PowerShell，但本文中未介绍此方法。


1. 打开 Git Bash 命令提示符。 使用位于[管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials)中的 Bash shell 指令完成步骤 1 和步骤 2。

    此步骤为证书脚本创建一个工作目录，并使用 openssl 为证书链生成示例根证书和中间证书。 
    
2. 请注意输出中显示了自签名根证书的位置。 此证书稍后将通过[所有权证明](how-to-verify-certificates.md)来验证所有权。

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        
    
3. 请注意输出中显示了由根证书签名/颁发的中间证书的位置。 此证书将用于稍后创建的注册组。

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    
    
#### <a name="create-device-certificates"></a>创建设备证书

若要创建由证书链中的中间证书签名的设备证书，请执行以下操作：

1. 运行以下命令创建一个新的设备/叶证书，该证书具有你作为参数提供的使用者名称。 使用为本教程提供的示例使用者名称 `custom-hsm-device-01`。 此使用者名称将是 IoT 设备的设备 ID。 

    > [!WARNING]
    > 请勿使用带有空格的使用者名称。 此使用者名称是正在预配的 IoT 设备的设备 ID。 它必须遵循设备 ID 的规则。 有关详细信息，请参阅[设备标识属性](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)。

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    请注意以下输出显示了新设备证书的位置。 设备证书由中间证书签名（颁发）。

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
2. 运行以下命令以创建包含用于 `custom-hsm-device-01` 的新设备证书的完整证书链 .pem 文件。

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-01-full-chain.cert.pem && cd ..
    ```

    使用文本编辑器并打开证书链文件 ./certs/new-device-01-full-chain.cert.pem。 证书链文本包含所有三个证书的完整链。 在本教程后面的 `custom-hsm-device-01` 的自定义 HSM 设备代码中，你将使用此文本作为证书链。

    完整链文本采用以下格式：
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

3. 请注意，新设备证书的私钥会写入 ./private/new-device.key.pem。 重命名 `custom-hsm-device-01` 设备的密钥文件 ./private/new-device-01.key.pem。 设备在预配期间需要此密钥的文本。 该文本稍后将添加到自定义 HSM 示例中。

    ```bash
    $ mv private/new-device.key.pem private/new-device-01.key.pem
    ```

    > [!WARNING]
    > 证书的文本仅包含公钥信息。 
    >
    > 但是，设备还必须能够访问设备证书的私钥。 这是必需的，因为设备在尝试预配时必须在运行时使用该密钥执行验证。 此密钥的敏感性是建议在实际 HSM 中使用基于硬件的存储来帮助保护私钥的主要原因之一。

4. 删除 ./certs/new-device.cert.pem，并对设备 ID 为 `custom-hsm-device-02` 的第二台设备重复步骤 1-3。 对于第二台设备，必须删除 ./certs/new-device.cert.pem，否则将无法生成证书。 本文仅使用完整链证书文件。 对于第二台设备，请使用以下值：

    |   说明                 |  值  |
    | :---------------------------- | :--------- |
    | 使用者名称                  | `custom-hsm-device-02` |
    | 完整的证书链文件   | ./certs/new-device-02-full-chain.cert.pem |
    | 私钥文件名          | private/new-device-02.key.pem |
    

## <a name="verify-ownership-of-the-root-certificate"></a>验证根证书的所有权

> [!NOTE]
> 从 2021 年 7 月 1 日起，可以通过[自动验证](how-to-verify-certificates.md#automatic-verification-of-intermediate-or-root-ca-through-self-attestation)来执行证书的自动验证
>

1. 使用[注册 X.509 证书的公共部分并获取验证码](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code)中的说明，上传根证书 (`./certs/azure-iot-test-only.root.ca.cert.pem`) 并从 DPS 获取验证码。

2. 从 DPS 获取根证书的验证码后，从证书脚本工作目录运行以下命令以生成验证证书。
 
    此处提供的验证码只是一个示例。 使用从 DPS 生成的代码。    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    此脚本将创建由根证书签名的证书，并将使用者名称设置为验证码。 此证书允许 DPS 验证你是否有权访问根证书的私钥。 请注意验证证书在脚本输出中的位置。 此证书以 `.pfx` 格式生成。

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. 如[上传已签名的验证证书](how-to-verify-certificates.md#upload-the-signed-verification-certificate)中所述，上传验证证书，然后单击 DPS 中的“验证”完成根证书的所有权证明。


## <a name="update-the-certificate-store-on-windows-based-devices"></a>在基于 Windows 的设备上更新证书存储

在非 Windows 设备上，可以将代码中的证书链作为证书存储传递。

在基于 Windows 的设备上，必须将签名证书（根证书和中间证书）添加到 Windows [证书存储](/windows/win32/secauthn/certificate-stores)。 否则，签名证书将不会通过具有传输层安全性 (TLS) 的安全通道传输到 DPS。

> [!TIP]
> 还可以将 OpenSSL 与 C SDK 一起使用，而不是使用安全通道 (Schannel)。 有关使用 OpenSSL 的详细信息，请参阅[在 SDK 中使用 OpenSSL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk)。

若要将签名证书添加到基于 Windows 的设备中的证书存储，请执行以下操作：

1. 在 Git bash 提示符中，导航到包含签名证书的 `certs` 子目录，并将其转换为 `.pfx`，如下所示。

    根证书：

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    中间证书：   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. 右键单击 Windows“开始”按钮。 然后，左键单击“运行”。 输入 certmgr.msc 并单击“确定”以启动证书管理器 MMC 管理单元。

3. 在证书管理器的“证书 - 当前用户”下，单击“受信任的根证书颁发机构” 。 然后在菜单上，单击“操作” > “所有任务” > “导入”以导入 `root.pfx`  。

    * 请确保按“个人信息交换 (.pfx)”进行搜索
    * 使用 `1234` 作为密码。
    * 将证书放在“受信任的根证书颁发机构”证书存储中。

4. 在证书管理器的“证书 - 当前用户”下，单击“中间证书颁发机构” 。 然后在菜单上，单击“操作” > “所有任务” > “导入”以导入 `intermediate.pfx`  。

    * 请确保按“个人信息交换 (.pfx)”进行搜索
    * 使用 `1234` 作为密码。
    * 将证书放入“中间证书颁发机构”证书存储中。

你的签名证书现在在基于 Windows 的设备上受信任，且整个链可以传输到 DPS。



## <a name="create-an-enrollment-group"></a>创建注册组

1. 登录到 Azure 门户，选择左侧菜单上的“所有资源”按钮，打开设备预配服务  。

2. 选择“管理注册”选项卡，然后选择顶部的“添加注册组”按钮 。

3. 在“添加注册组”面板中输入以下信息，然后按“保存”按钮 。

      ![在门户中添加 X.509 证明的注册组](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | 字段        | 值           |
    | :----------- | :-------------- |
    | **组名** | 对于本教程，输入“custom-hsm-x509-devices” |
    | **证明类型** | 选择“证书” |
    | **IoT Edge 设备** | 选择“False” |
    | **证书类型** | 选择“中间证书” |
    | **主要证书 .pem 或 .cer 文件** | 导航到前面创建的中间证书 (./certs/azure-iot-test-only.intermediate.cert.pem)。 此中间证书由已经上传并经过验证的根证书签名。 DPS 会在该根证书通过验证后信任该根证书。 DPS 可以验证为此注册组提供的中间证书是否确实由受信任的根证书签名。 DPS 会信任每一个确实由该根证书签名的中间证书，因此能够验证和信任由中间证书签名的叶证书。  |


## <a name="configure-the-provisioning-device-code"></a>配置预配设备代码

在本部分中，通过设备预配服务实例信息更新示例代码。 如果设备经过身份验证，将会被分配到与本部分中配置的设备预配服务实例链接的 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值   。

    ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 启动 Visual Studio 并打开在 azure-iot-sdk-c git 存储库根目录中创建的 `cmake` 目录中创建的新解决方案文件。 解决方案文件命名为 `azure_iot_sdks.sln`。

3. 在 Visual Studio 的解决方案资源管理器中，导航到“Provisioning_Samples”>“prov_dev_client_sample”>“源文件”，然后打开 prov_dev_client_sample.c。

4. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. 在同一文件中找到 `main()` 函数的定义。 确保 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_X509`，如下所示。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。


## <a name="configure-the-custom-hsm-stub-code"></a>配置自定义 HSM 存根代码

与实际的基于硬件的安全存储交互的细节因硬件而异。 因此，本教程中模拟设备使用的证书链将硬编码到自定义 HSM 存根代码中。 在实际情况中，证书链将存储在实际的 HSM 硬件中，以便为敏感信息提供更好的安全性。 然后将实现与此示例中使用的存根方法类似的方法，以从基于硬件的存储中读取机密。 

尽管并未要求使用 HSM 硬件，但建议保护敏感信息（如证书的私钥）。 如果该示例调用了实际的 HSM，则私钥不会出现在源代码中。 将密钥包含在源代码中会将密钥公开给可以查看代码的任何人。 本文中这样做只是为了帮助学习。

若要更新自定义 HSM 存根代码以模拟 ID 为 `custom-hsm-device-01` 的设备的标识，请执行以下步骤：

1. 在 Visual Studio 的解决方案资源管理器中，导航到“Provisioning_Samples”>“custom_hsm_example”>“源文件”，然后打开 custom_hsm_example.c。

2. 使用生成设备证书时使用的公用名称更新 `COMMON_NAME` 字符串常量的字符串值。

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

3. 在同一文件中，需使用在生成证书后保存在 ./certs/new-device-01-full-chain.cert.pem 中的证书链文本更新 `CERTIFICATE` 常量字符串的字符串值。

    证书文本的语法必须遵循下面的模式，不使用额外的空格或由 Visual Studio 进行解析。

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    在本步骤中正确更新此字符串值可能非常繁琐，且容易出错。 若要在 Git Bash 提示符中生成正确的语法，请将以下 bash shell 命令复制并粘贴到 Git Bash 命令提示符中，然后按 Enter。 这些命令将生成 `CERTIFICATE` 字符串常量值的语法。

    ```Bash
    input="./certs/new-device-01-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    复制并粘贴新常量值的输出证书文本。 


4. 在同一文件中，还必须使用设备证书的私钥更新 `PRIVATE_KEY` 常量的字符串值。

    私钥文本的语法必须遵循下面的模式，不使用额外的空格或由 Visual Studio 进行解析。

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    在本步骤中正确更新此字符串值也可能非常繁琐，且容易出错。 若要在 Git Bash 提示符中生成正确的语法，请复制并粘贴以下 bash shell 命令，然后按 Enter。 这些命令将生成 `PRIVATE_KEY` 字符串常量值的语法。

    ```Bash
    input="./private/new-device-01.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    复制并粘贴新常量值的输出私钥文本。 

5. 保存 custom_hsm_example.c。

6. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。  系统提示重新生成项目时，请选择“是”，以便在运行项目之前重新生成项目。

    以下输出是模拟设备 `custom-hsm-device-01` 成功启动并连接到预配服务的示例。 设备已分配到 IoT 中心并注册：

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. 在门户中导航到已链接到预配服务的 IoT 中心，然后选择“IoT 设备”选项卡。将 X.509 设备成功预配到中心以后，设备 ID 会显示在“IoT 设备”边栏选项卡上，“状态”为“已启用”。 你可能需要按顶部的“刷新”按钮  。 

    ![自定义 HSM 设备已注册到 IoT 中心](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

8. 对于设备 ID 为 `custom-hsm-device-02` 的第二台设备重复步骤 1-7。 为该设备使用以下值：

    |   说明                 |  值  |
    | :---------------------------- | :--------- |
    | `COMMON_NAME`                 | `"custom-hsm-device-02"` |
    | 完整的证书链        | 使用 `input="./certs/new-device-02-full-chain.cert.pem"` 生成文本 |
    | 私钥                   | 使用 `input="./private/new-device-02.key.pem"` 生成文本 |

    以下输出是模拟设备 `custom-hsm-device-02` 成功启动并连接到预配服务的示例。 设备已分配到 IoT 中心并注册：

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-02
    Press enter key to exit:
    ```


## <a name="clean-up-resources"></a>清理资源

完成此设备客户端示例的测试和研究后，使用以下步骤删除本教程创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择设备预配服务。 打开服务的“管理注册”，然后选择“注册组”选项卡 。选中在本教程中创建的设备组的“组名称”旁边的复选框，然后按窗格顶部的“删除”按钮。 
1. 在 DPS 中单击“证书”。 对于在本教程中上传和验证的每个证书，请单击证书并单击“删除”按钮将其删除。
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择 IoT 中心  。 为中心打开“IoT 设备”。 选中在本教程中注册的设备的“设备 ID”旁边的复选框。 单击窗格顶部的“删除”按钮。

## <a name="next-steps"></a>后续步骤

在本教程中，你使用自定义 HSM 为 IoT 中心预配了一个 X.509 设备。 若要了解如何为多个中心提供 IoT 设备，请继续进行下一个教程。 

> [!div class="nextstepaction"]
> [教程：跨负载均衡的 IoT 中心预配设备](tutorial-provision-multiple-hubs.md)
