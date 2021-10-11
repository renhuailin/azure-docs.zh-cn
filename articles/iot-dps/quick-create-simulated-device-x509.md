---
title: 快速入门 - 将 X.509 证书模拟设备预配到 Microsoft Azure IoT 中心
description: 了解如何在 Azure IoT 中心设备预配服务 (DPS) 中预配使用 X.509 证书进行身份验证的设备
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/07/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: c0c3d486e2a886a49c51e7ed78ed935c1a1c5c22
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276599"
---
# <a name="quickstart-provision-an-x509-certificate-simulated-device"></a>快速入门 - 预配 X.509 证书模拟设备

在本快速入门中，你将在 Windows 计算机上创建一个模拟设备。 模拟设备将被配置为使用 [X.509 证书证明](concepts-x509-attestation.md)机制进行身份验证。 配置设备后，你将使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。

如果不熟悉预配过程，请查看[预配](about-iot-dps.md#provisioning-process)概述。  另外，在继续操作之前，请确保已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

本快速入门演示适用于基于 Windows 工作站的解决方案。 但是，你也可以在 Linux 上执行这些过程。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

以下先决条件适用于 Windows 开发环境。 对于 Linux 或 macOS，请参阅 SDK 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。

::: zone pivot="programming-language-ansi-c"

* 安装 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 并启用[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载。 Visual Studio 2015 和 Visual Studio 2017 也受支持。 对于 Linux 或 macOS，请参阅 SDK 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。

::: zone-end

::: zone pivot="programming-language-csharp"

* 在基于 Windows 的计算机上安装 [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download)。 可使用以下命令来检查你的版本。

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* 在计算机上安装 [Node.js v4.0 或更高版本](https://nodejs.org)。

* 在计算机上安装 [OpenSSL](https://www.openssl.org/) 并将其添加到可供命令窗口访问的环境变量。 此库可以从源生成和安装，也可以从[第三方](https://wiki.openssl.org/index.php/Binaries)（例如[这个第三方](https://sourceforge.net/projects/openssl/)）下载和安装。

::: zone-end

::: zone pivot="programming-language-python"

* 在计算机上安装 [Python 3.6 或更高版本](https://www.python.org/downloads/)。

* 在计算机上安装 [OpenSSL](https://www.openssl.org/) 并将其添加到可供命令窗口访问的环境变量。 此库可以从源生成和安装，也可以从[第三方](https://wiki.openssl.org/index.php/Binaries)（例如[这个第三方](https://sourceforge.net/projects/openssl/)）下载和安装。

::: zone-end

::: zone pivot="programming-language-java"

* 在计算机上安装 [Java SE 开发工具包 8](/azure/developer/java/fundamentals/java-support-on-azure) 或更高版本。

* 下载并安装 [Maven](https://maven.apache.org/install.html)。

::: zone-end

* [安装最新版本的 Git](https://git-scm.com/download/)。 确保将 Git 添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。

## <a name="prepare-your-development-environment"></a>准备开发环境

::: zone pivot="programming-language-ansi-c"

在本部分，你将准备用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 示例代码尝试在执行设备的启动序列期间预配设备。

1. 下载最新的 [CMake 生成系统](https://cmake.org/download/)。

    >[!IMPORTANT]
    >在进行 `CMake` 安装之前，请确认在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负载）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。 另请注意，旧版本的 CMake 生成系统无法生成本文中使用的解决方案文件。 请务必使用最新版本的 CMake。

2. 打开 Web 浏览器，转到 [Azure IoT C SDK 发布页](https://github.com/Azure/azure-iot-sdk-c/releases/latest)。

3. 选择页面顶部的“标记”选项卡。

4. 复制最新版 Azure IoT C SDK 的标记名称。

5. 打开命令提示符或 Git Bash shell。 运行以下命令以克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 （将 `<release-tag>` 替换为在上一步复制的名标记）。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    此操作可能需要几分钟才能完成。

6. 操作完成后，从 `azure-iot-sdk-c` 目录运行以下命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. 代码示例使用 X.509 证书通过 X.509 身份验证提供证明。 运行以下命令，以生成特定于你的开发平台（包括设备预配客户端）的 SDK 版本。 在 `cmake` 目录中生成模拟设备的 Visual Studio 解决方案。

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    >[!TIP]
    >如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。

8. 生成成功后，最后的几个输出行如下所示：

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. 打开 Git CMD 或 Git Bash 命令行环境。

2. 使用以下命令克隆 [C# 的 Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. 打开 Git CMD 或 Git Bash 命令行环境。

2. 使用以下命令克隆[适用于 Node.js 的 Azure IoT 示例](https://github.com/Azure/azure-iot-sdk-node.git) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. 打开 Git CMD 或 Git Bash 命令行环境。

2. 使用以下命令克隆[适用于 Pythons 的 Azure IoT 示例](https://github.com/Azure/azure-iot-sdk-node.git) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. 打开 Git CMD 或 Git Bash 命令行环境。

2. 使用以下命令克隆[适用于 Java 的 Azure IoT 示例](https://github.com/Azure/azure-iot-sdk-java.git) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. 转至 `azure-iot-sdk-`java` 根目录，并生成项目以下载全部所需的包。

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

4. 转至证书生成器项目，然后生成该项目。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

::: zone-end

## <a name="create-a-self-signed-x509-device-certificate"></a>创建自签名的 X.509 设备证书

在本部分，你将使用 Azure IoT SDK 中的示例代码来创建自签名 X.509 证书。 此证书必须上传到预配服务，并由该服务进行验证。

> [!CAUTION]
> 使用 SDK 工具创建的证书只能用于开发测试。
> 请不要在生产环境中使用这些证书。
> SDK 生成的证书包含硬编码密码，例如“1234”，且会在 30 天后过期。
> 若要了解如何获取适用于生产用途的证书，请参阅 Azure IoT 中心文档中的[如何获取 X.509 CA 证书](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate)。
>

::: zone pivot="programming-language-csharp"

IoT 设备的设备 ID 将是证书上的使用者共用名称。 确保使用符合[设备 ID 字符串要求](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)的使用者名称。

::: zone-end

创建 X.509 证书：

::: zone pivot="programming-language-ansi-c"

### <a name="clone-the-azure-iot-c-sdk"></a>克隆 Azure IoT C SDK

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 包含的测试工具可以帮助你创建 X.509 证书链、从该链上传根证书或中间证书，以及通过服务执行所有权证明操作，对证书进行验证。

如果已克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库，请跳到[下一部分](#create-a-test-certificate)。

1. 打开 Web 浏览器，转到 [Azure IoT C SDK 发布页](https://github.com/Azure/azure-iot-sdk-c/releases/latest)。

2. 复制最新版 Azure IoT C SDK 的标记名称。

3. 打开命令提示符或 Git Bash shell。 运行以下命令以克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 （将 `<release-tag>` 替换为在上一步复制的名标记）。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    此操作可能需要几分钟才能完成。

4. 现在，测试工具应位于克隆的存储库的 azure-iot-sdk-c/tools/CACertificates 中。

### <a name="create-a-test-certificate"></a>创建测试证书

根据[管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步骤进行操作。

除了使用 C SDK 中的工具之外，*用于 .NET 的 Microsoft Azure IoT SDK* 中的 [组证书验证示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)演示了如何使用现有的 X.509 中间或根 CA 证书采用 C# 执行所有权证明操作。

::: zone-end

::: zone pivot="programming-language-csharp"

1. 在 PowerShell 提示符处，将目录更改为 X.509 设备预配示例的项目目录。

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. 示例代码设置为使用在受密码保护的 PKCS12 格式文件 (`certificate.pfx`) 中存储的 X.509 证书。 此外，还会需要一个公钥证书文件 (`certificate.cer`)，用于在本快速入门的后面部分创建单个注册。 若要生成自签名证书及其关联的 `.cer` 和 `.pfx` 文件，请运行以下命令：

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. 脚本会提示输入 PFX 密码。 请记住此密码，稍后在运行示例时需要使用该密码。 可以选择运行 `certutil` 来转储证书并验证使用者名称。

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. 打开命令提示符，转到证书生成器脚本并生成项目：

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

2. 使用自己的 _certificate-name_ 运行脚本，创建 _叶_ X.509 证书。 叶证书的公用名称会成为[注册 ID](./concepts-service.md#registration-id)，因此请确保只使用小写的字母数字和连字符。

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. 在 Git Bash 提示符下运行以下命令：

    # <a name="windows"></a>[Windows](#tab/windows)

    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > 仅当需要在 Windows 平台上使用 Git 来转义字符串时，才需要为使用者名称 (`//CN=Python-device-01`) 提供额外的西文斜杠。

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```

    ---

2. 当系统要求“输入 PEM 通行短语:”时，请使用通行短语 `1234`。

3. 如果系统要求“验证 - 输入 PEM 通行短语:”，请再次使用通行短语 `1234`。

现在应该在运行 `openssl` 命令的目录中生成测试证书文件 (python-device.pem) 和私钥文件 (python-device.key.pem) 。

::: zone-end

::: zone pivot="programming-language-java"

1. 使用前述步骤中的命令提示符，转到 `target` 文件夹。

2. 运行上一部分中创建的 .jar 文件。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

3. 对于“是否需要输入公用名称”，请输入 **N**。  

4. 将 `Client Cert` 的输出（从 -----BEGIN CERTIFICATE----- 到 -----END CERTIFICATE-----）复制到剪贴板 。

   ![单个证书的生成器](./media/quick-create-simulated-device-x509/cert-generator-java.png)

5. 在 Windows 计算机上创建一个名为 X509individual.pem 的文件。

6. 在所选编辑器中打开 X509individual.pem，然后将剪贴板内容复制到该文件中。 

7. 保存该文件并关闭编辑器。

8. 在命令提示符中，对于“是否需要输入验证码”  ，请输入 **N**，并让程序输出保持打开状态，以便稍后在本快速入门中进行引用。 复制 `Client Cert` 和 `Client Cert Private Key` 值，以在下一部分中使用。

::: zone-end

## <a name="create-a-device-enrollment"></a>创建设备注册

Azure IoT 设备预配服务支持两类注册：

* [注册组](concepts-service.md#enrollment-group)：用于注册多个相关设备。
* [单个注册](concepts-service.md#individual-enrollment)：用于注册单个设备。

本文演示要使用 IoT 中心预配的单个设备的单个注册过程。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择设备预配服务。

4. 在“设置”菜单中，选择“管理注册” 。

5. 在页面顶部，选择“+ 添加单个注册”。

::: zone pivot="programming-language-ansi-c"

6. 在“添加注册”页中，输入以下信息。

    * **机制：** 选择“X.509”  作为标识证明机制  。
    * **主要证书 .pem 或 .cer 文件**：选择“选择文件”以选择在前述部分中创建的证书文件 X509testcert.pem。
    * **IoT 中心设备 ID：** 输入 test-docs-cert-device 作为设备的 ID  。

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="将设备添加为使用 X.509 证明的单个注册。":::

::: zone-end

::: zone pivot="programming-language-csharp"

6. 在“添加注册”页中，输入以下信息。

    * **机制：** 选择“X.509”  作为标识证明机制  。
    * **主要证书 .pem 或 .cer 文件**：选择“选择文件”以选择在前述部分中创建的证书文件 certificate.cer。
    * 将“IoT 中心设备 ID:”保留为空。 对设备进行预配时，其设备 ID 将设置为 X.509 证书 *iothubx509device1* 中的公用名称 (CN)。 此共用名称也将是用于单个注册项的注册 ID 的名称。
    * （可选）可以提供以下信息：
        * 选择与预配服务链接的 IoT 中心。
        * 使用设备所需的初始配置更新“初始设备孪生状态”  。

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="将设备添加为使用 X.509 证明的单个注册。":::

::: zone-end

::: zone pivot="programming-language-nodejs"

6. 在“添加注册”页中，输入以下信息。

    * **机制：** 选择“X.509”  作为标识证明机制  。
    * **主要证书 .pem 或 .cer 文件**，选择“选择文件”以选择在前述部分中创建的证书文件 {certificate-name}_cert.pem。
    * （可选）可以提供以下信息：
        * 选择与预配服务链接的 IoT 中心。
        * 输入唯一设备 ID。 为设备命名时，请确保避免使用敏感数据。
        * 使用设备所需的初始配置更新“初始设备孪生状态”  。
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="将设备添加为使用 X.509 证明的单个注册。":::

::: zone-end

::: zone pivot="programming-language-python"

6. 在“添加注册”页中，输入以下信息。

    * **机制：** 选择“X.509”  作为标识证明机制  。
    * **主要证书 .pem 或 .cer 文件**：如果使用之前创建的测试证书，则选择“选择文件”，并选择证书文件“python-device.pem”。
    * （可选）可以提供以下信息：
        * 选择与预配服务链接的 IoT 中心。
        * 使用设备所需的初始配置更新“初始设备孪生状态”  。

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="将设备添加为使用 X.509 证明的单个注册。":::

::: zone-end

::: zone pivot="programming-language-java"

6. 在“添加注册”面板中，输入以下信息：
   * 选择“X.509”  作为标识证明机制  。
   * 在“主要证书 .pem 或 .cer 文件”下，选择“选择文件”以选择在前述步骤中创建的证书文件 *X509individual.pem*。    
   * （可选）可以提供以下信息：
     * 选择与预配服务链接的 IoT 中心。
     * 输入唯一设备 ID。 为设备命名时，请确保避免使用敏感数据。 
     * 使用设备所需的初始配置更新“初始设备孪生状态”  。
    
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="将设备添加为使用 X.509 证明的单个注册。":::

::: zone-end
    
7. 选择“保存”  。 你将返回到“管理注册”。

8. 选择“单个注册”。 X.509 注册条目应显示在注册表中。

## <a name="prepare-and-run-the-device-provisioning-code"></a>准备和运行设备预配代码

::: zone pivot="programming-language-ansi-c"

在本部分中，我们将更新向设备预配服务实例发送设备启动序列的示例代码。 此启动序列使得设备可被识别并分配到与设备预配服务实例链接的 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡。

2. 复制“ID 范围”值。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="复制门户中的 ID 范围。":::

3. 在 Visual Studio 的“解决方案资源管理器”窗口中，导航到 **Provision\_Samples** 文件夹。  展开名为 **prov\_dev\_client\_sample** 的示例项目。 展开“源文件”，打开 **prov\_dev\_client\_sample.c**。 

4. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 在同一文件中找到 `main()` 函数的定义。 确保 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_X509` 而不是 `SECURE_DEVICE_TYPE_TPM`，如下所示。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。

7. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。  对于重新生成项目的提示，请选择“是”，以便在运行项目之前重新生成项目  。

    以下输出示例显示预配设备客户端示例成功启动，然后连接到预配服务实例来获取 IoT 中心信息并注册：

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡。

2. 复制“ID 范围”值。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="复制门户中的 ID 范围。":::

3. 打开命令提示符窗口。

4. 键入以下命令，生成并运行 X.509 设备预配示例（将 `<IDScope>` 值替换为你在上一部分中复制的 ID 范围。）。 证书文件将默认为 ./certificate.pfx，还将提示输入 .pfx 密码。 键入密码。

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    如果要将所有内容都作为参数传递，可采用以下示例格式。

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```

5. 设备此时将连接到 DPS 并分配给 IoT 中心。 随后设备将向中心发送遥测消息。

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡。 

2. 复制“ID 范围”和“全局设备终结点”值 。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="复制门户中的 ID 范围。":::

3. 将证书和密钥复制到示例文件夹。  

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

4. 导航到设备测试脚本，然后生成项目。

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

5. 使用以下更改编辑 register\_x509.js 文件：

    * 将 `provisioning host` 替换为在上面的“步骤 1”中注明的“全局设备终结点”。  
    * 将 `id scope` 替换为在上面的“步骤 1”中记下的“ID 范围”。   
    * 将 `registration id` 替换为在上一部分中记下的“注册 ID”  。
    * 将 `cert filename` 和 `key filename` 替换为在上面的“步骤 2”中复制的文件。 

6. 保存文件。

7. 执行脚本，验证该设备是否已成功预配。

    ```cmd/sh
    node register_x509.js
    ``` 

>[!TIP]
>[Azure IoT 中心 Node.js 设备 SDK](https://github.com/Azure/azure-iot-sdk-node) 提供了进行设备模拟的简单方式。 有关详细信息，请参阅[设备概念](./concepts-service.md)。

::: zone-end

::: zone pivot="programming-language-python"

Python 设置示例 [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) 可在 `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` 目录中找到。 此示例使用六个环境变量，通过 DPS 对 IoT 设备进行身份验证和预配。 这些环境变量包括：

| 变量名称              | 说明                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  此值是用于连接 DPS 资源的全局终结点 |    
| `PROVISIONING_IDSCOPE`     |  此值是 DPS 资源的 ID 范围 |    
| `DPS_X509_REGISTRATION_ID` |  此值为设备的 ID。 它还必须与设备证书上的使用者名称相符 |    
| `X509_CERT_FILE`           |  设备证书文件名 |    
| `X509_KEY_FILE`            |  设备证书的私钥文件名 |
| `PASS_PHRASE`              |  用于对证书和私钥文件进行加密的密码 (`1234`)。 |    

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡。

2. 复制“ID 范围”和“全局设备终结点”值 。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="复制门户中的 ID 范围。":::

3. 在 Git Bash 提示符下，使用以下命令为全局设备终结点和 ID 范围添加环境变量。

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

4. IoT 设备的注册 ID 必须与设备证书上的使用者名称相符。 如果生成了自签名测试证书，则 `Python-device-01` 是设备的使用者名称和注册 ID。

    如果已有设备证书，可以使用 `certutil` 来验证用于设备的使用者公用名，如下所示：

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

5. 在 Git Bash 提示符下，按如下所示设置注册 ID 的环境变量：

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

6. 在 Git Bash 提示符下，为证书文件、私钥文件和通行短语设置环境变量。

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

7. 查看 [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) 的代码。 如果未使用 Python 版本 3.7 或更高版本，请执行[此处所述的代码更改](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk)以替换 `asyncio.run(main())`。

8. 保存所做更改。

9. 运行该示例。 该示例将设备连接并预配到中心，并向中心发送一些测试消息。

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡。

2. 复制“ID 范围”和“全局设备终结点”值 。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="复制门户中的 ID 范围。":::

3. 打开命令提示符。 导航到 Java SDK 存储库的示例项目文件夹。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

4. 在代码中输入预配服务和 X.509 标识信息。 在预配期间，该信息在注册设备之前用于证明模拟设备：

   * 编辑文件 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`，使之包括前面提到的“ID 范围”和“预配服务全局终结点”。   另请包括上一部分中提到的“客户端证书”和“客户端证书私钥”。  

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   * 复制/粘贴证书和私钥时，请使用以下格式：
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

5. 生成示例，然后转到 `target` 文件夹并执行所创建的 .jar 文件。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>确认设备预配注册

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择设备分配到的 IoT 中心。

4. 在“资源管理器”菜单中选择“IoT 设备” 。

5. 如果设备预配成功，设备 ID 应显示在列表中，其中状态设为“已启用”。 如果看不到设备，请选择页面顶部的“刷新”。

   :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration.png" alt-text="设备注册到 IoT 中心":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-csharp.png" alt-text="CSharp 设备已注册到 IoT 中心":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-nodejs.png" alt-text="Node.js 设备已注册到 IoT 中心":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-python.png" alt-text="Python 设备已注册到 IoT 中心":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-java.png" alt-text="Java 设备已注册到 IoT 中心":::

    ::: zone-end


::: zone pivot="programming-language-csharp,programming-language-nodejs,programming-language-python,programming-language-java"

>[!IMPORTANT]
>如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)

::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请不要清理在本快速入门中创建的资源。 如果不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

### <a name="delete-your-device-enrollment"></a>删除设备注册

1. 关闭计算机上的设备客户端示例输出窗口。

2. 在 Azure 门户的左侧菜单中，选择“所有资源”。

3. 选择设备预配服务。

4. 在“设置”菜单中，选择“管理注册” 。

5. 选择“单个注册”选项卡。

6. 选中在本快速入门中注册的设备的“注册 ID”旁边的复选框。

7. 在页面顶部，选择“删除”。

### <a name="delete-your-device-registration-from-iot-hub"></a>从 IoT 中心删除设备注册

1. 在 Azure 门户的左侧菜单中，选择“所有资源”。

2. 选择 IoT 中心。

3. 在“资源管理器”菜单中选择“IoT 设备” 。

4. 选中在本快速入门中注册的设备的“设备 ID”旁边的复选框。

5. 在页面顶部，选择“删除”。

## <a name="next-steps"></a>后续步骤

了解如何以编程方式注册自己的 X.509 设备：

> [!div class="nextstepaction"]
> [Azure 快速入门 - 将 X.509 设备注册到 Azure IoT 中心设备预配服务](quick-enroll-device-x509.md)