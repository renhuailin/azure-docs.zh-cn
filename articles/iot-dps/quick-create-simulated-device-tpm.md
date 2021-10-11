---
title: 快速入门 - 将模拟的 TPM 设备预配到 Microsoft Azure IoT 中心
description: 快速入门 - 了解如何将 TPM 模拟设备预配到 Azure IoT 中心设备预配服务
author: anastasia-ms
ms.author: v-stharr
ms.date: 9/22/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
zone_pivot_groups: iot-dps-set1
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: 4a1430174e11f1223c4c84af19561609d288eb51
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293669"
---
# <a name="quickstart-provision-a-simulated-tpm-device"></a>快速入门：预配模拟的 TPM 设备

在此快速入门中，你将在 Windows 计算机上创建一台 TPM 模拟设备。 配置设备后，你将使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。 然后将使用示例代码在设备预配服务实例中注册设备

如果不熟悉预配过程，请查看[预配](about-iot-dps.md#provisioning-process)概述。  另外，在继续操作之前，请确保已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

Azure IoT 设备预配服务支持两类注册：

* [注册组](concepts-service.md#enrollment-group)用于注册多台相关设备。

* [单个注册](concepts-service.md#individual-enrollment)用于注册一台设备。

本文演示单个注册。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

以下先决条件适用于 Windows 开发环境。 对于 Linux 或 macOS，请参阅 SDK 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。

::: zone pivot="programming-language-ansi-c"

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019，已启用[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载。 Visual Studio 2015 和 Visual Studio 2017 也受支持。

::: zone-end

::: zone pivot="programming-language-csharp"

* 在基于 Windows 的计算机上安装 [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download) 或更高版本。 可使用以下命令来检查你的版本。

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* 安装 [Node.js v4.0+](https://nodejs.org)。

::: zone-end

::: zone pivot="programming-language-python"

* 安装 [Python 2.x 或 3.x](https://www.python.org/downloads/)。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。

* 如果使用 Windows OS，则安装 [Visual C++ 可再发行组件包](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)，以便使用 Python 中的原生 DLL。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019，已启用[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载。 Visual Studio 2015 和 Visual Studio 2017 也受支持。

* [CMake 生成系统](https://cmake.org/download/)。

::: zone-end

::: zone pivot="programming-language-java"

* 在计算机上安装 [Java SE 开发工具包 8](/azure/developer/java/fundamentals/java-support-on-azure) 或更高版本。

* 下载并安装 [Maven](https://maven.apache.org/install.html)。

::: zone-end

* [安装最新版本的 Git](https://git-scm.com/download/)。 确保将 Git 添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。

<a id="setupdevbox"></a>

## <a name="prepare-your-development-environment"></a>准备开发环境

::: zone pivot="programming-language-ansi-c"

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 和 [TPM](/windows/device-security/tpm/trusted-platform-module-overview) 设备模拟器示例的开发环境。

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

2. 使用以下命令克隆 [azure-utpm-c](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. 打开 Git CMD 或 Git Bash 命令行环境。
  
2. 使用以下命令克隆 [Python](https://github.com/Azure/azure-iot-sdk-python) GitHub 存储库：

    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

3. 在该 GitHub 存储库的本地副本中创建一个用于 CMake 生成过程的文件夹。 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

4. 按照[这些说明](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md)生成 Python 包。

   > [!NOTE]
   > 如果运行 `build_client.cmd`，请确保使用 `--use-tpm-simulator` 标志。
   > 
   > [!NOTE]
   > 如果使用 `pip`，请确保也安装 `azure-iot-provisioning-device-client` 包。 请注意，发布的 PIP 包使用真实的 TPM 而非模拟器。 若要使用模拟器，需使用 `--use-tpm-simulator` 标志从源进行编译。

::: zone-end

::: zone pivot="programming-language-java"

1. 打开 Git CMD 或 Git Bash 命令行环境。
  
2. 使用以下命令克隆 [Java](https://github.com/Azure/azure-iot-sdk-java.git) GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

::: zone-end

## <a name="build-and-run-the-tpm-device-simulator"></a>生成并运行 TPM 设备模拟器

在本部分中，你将生成并运行 TPM 模拟器。 此模拟器通过端口 2321 和 2322 上的套接字进行侦听。 请不要关闭命令窗口。 本快速入门自始至终都需让该模拟器保持运行状态。

::: zone pivot="programming-language-ansi-c"

1. 运行以下命令以生成包含 TPM 设备模拟器示例代码的 Azure IoT C SDK。 在 `cmake` 目录中生成模拟设备的 Visual Studio 解决方案。 此示例通过共享访问签名 (SAS) 令牌身份验证提供 TPM [证明机制](concepts-service.md#attestation-mechanism)。

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    >[!TIP]
    >如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。

2. 生成成功后，最后的几个输出行如下所示：

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

3. 转到克隆 Git 存储库的根文件夹。

4. 使用下面所示的路径运行 [TPM](/windows/device-security/tpm/trusted-platform-module-overview) 模拟器。

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    模拟器不会显示任何输出。 请让它继续模拟 TPM 设备。

::: zone-end

::: zone pivot="programming-language-csharp"

1. 在设备预配服务的主菜单中，选择“概述”。

2. 复制“ID 范围”值。

    ![从门户边栏选项卡复制预配服务范围 ID](./media/quick-create-simulated-device-tpm/extract-dps-endpoints-csharp.png) 

3. 在命令提示符处将目录更改为 TPM 设备预配示例的项目目录。

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

4. 键入以下命令，生成并运行 TPM 设备预配示例（将 `<IDScope>` 替换为预配服务的 ID 范围）。

    ```cmd
    dotnet run <IDScope>
    ```

    >[!NOTE]
    >此命令将在单独的命令提示中启动 TPM 芯片模拟器。 在 Windows 上，你可能会遇到 Windows 安全警报，该警报询问你是否要允许 `Simulator.exe` 在公用网络上通信。 对于本示例，可以取消该请求。

5. 原始命令窗口显示进行设备注册所需的“认可密钥”、“注册 ID”以及建议的“设备 ID”。    记下这些值。 这些值将用于在设备预配服务实例中创建个人注册。

   > [!NOTE]
   > 请勿混淆包含命令输出的窗口与包含 TPM 模拟器输出的窗口。 可能需要选择原始命令窗口，将其置于前台。

::: zone-end

::: zone pivot="programming-language-nodejs"

1. 转到 GitHub 根文件夹。

2. 运行 [TPM](/windows/device-security/tpm/trusted-platform-module-overview) 模拟器作为模拟设备的 [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)。

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

3. 新建名为 **registerdevice** 的空文件夹。 在 registerdevice 文件夹的命令提示符处，使用以下命令创建 package.json 文件（确保回答 `npm` 提问的所有问题，如果默认值适合，则接受默认值）：

    ```cmd/sh
    npm init
    ```

4. 安装以下前提包：

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > 安装上述包时存在一些已知问题。 若要解决这些问题，请使用命令提示符在“以管理员身份运行”模式下运行 `npm install --global --production windows-build-tools`，在将路径替换为已安装版本后运行 `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140`，然后重新运行上述安装命令。
    >

5. 在 registerdevice 文件夹中的命令提示符处运行以下命令来安装所需的包：

    ```cmd/sh
    npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
    ```

    该命令安装以下包：

   * 适用于 TPM 的安全客户端：`azure-iot-security-tpm`
   * 设备的传输，用于连接到设备预配服务：`azure-iot-provisioning-device-http` 或 `azure-iot-provisioning-device-amqp`
   * 将要使用传输的客户端和安全客户端：`azure-iot-provisioning-device`
   * 设备客户端：`azure-iot-device`
   * 一个传输：`azure-iot-device-amqp`、`azure-iot-device-mqtt`、`azure-iot-device-http` 中的任一项
   * 已安装的安全客户端：`azure-iot-security-tpm`

     > [!NOTE]
     > 此快速入门中的示例使用 `azure-iot-provisioning-device-http` 和 `azure-iot-device-mqtt` 传输。
     >

6. 打开所选的文本编辑器。

7. 在 registerdevice 文件夹中，创建新的 ExtractDevice.js 文件。

8. 在 **ExtractDevice.js** 文件的开头添加以下 `require` 语句：

    ```Javascript

    'use strict';
    
    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");
    
    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

9. 添加以下函数以实现该方法：

    ```Javascript

    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

10. 保存并关闭 _ExtractDevice.js_ 文件。

    ```cmd/sh
    node ExtractDevice.js
    ```

11. 运行该示例。

12. 输出窗口会显示进行设备注册所需的“认可密钥”和“注册 ID” 。 复制这些值。

::: zone-end

::: zone pivot="programming-language-python"

1. 运行以下命令，启用 SAS 令牌身份验证（该命令还会为模拟设备生成 Visual Studio 解决方案）：

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

2. 打开第二个命令提示符。

3. 在第二个命令提示符下，导航到 TPM 模拟器文件夹。

4. 运行 [TPM](/windows/device-security/tpm/trusted-platform-module-overview) 模拟器作为模拟设备的 [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)。

5. 选择“允许访问”。 此模拟器通过端口 2321 和 2322 上的套接字进行侦听。 请勿关闭此命令窗口；本快速入门指南自始至终都需让该模拟器保持运行状态。

  ```cmd/sh
  .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
  ```

  ![TPM 模拟器](./media/quick-create-simulated-device-tpm/simulator.png)

::: zone-end

::: zone pivot="programming-language-java"

1. 运行 [TPM](/windows/device-security/tpm/trusted-platform-module-overview) 模拟器作为模拟设备的 [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)。

2. 选择“允许访问”。 此模拟器通过端口 2321 和 2322 上的套接字进行侦听。 请勿关闭此命令窗口；本快速入门指南自始至终都需让该模拟器保持运行状态。

    ```cmd/sh
    .\azure-iot-sdk-java\provisioning\provisioning-tools\tpm-simulator\Simulator.exe
    ```

    ![TPM 模拟器](./media/quick-create-simulated-device-tpm/simulator.png)

3. 打开第二个命令提示符。

4. 在第二个命令提示符下导航到根文件夹，然后生成示例依赖项。

    ```cmd/sh
    cd azure-iot-sdk-java
    mvn install -DskipTests=true
    ```

5. 导航到示例文件夹。

    ```cmd/sh
    cd provisioning/provisioning-samples/provisioning-tpm-sample
    ```

::: zone-end

::: zone pivot="programming-language-ansi-c, programming-language-python,programming-language-java"

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>从 TPM 设备读取加密密钥

在本部分，你将生成并执行一个示例，以便从保持运行的、仍通过端口 2321 和 2322 进行侦听的 TPM 模拟器中读取认可密钥和注册 ID。 这些值用于将设备注册到设备预配服务实例。

::: zone-end

::: zone pivot="programming-language-ansi-c"

1. 启动 Visual Studio。

2. 打开在 cmake 文件夹中生成的名为 `azure_iot_sdks.sln` 的解决方案。

3. 在 Visual Studio 菜单中，选择“生成” > “生成解决方案”以生成解决方案中的所有项目。  

4. 在 Visual Studio 的“解决方案资源管理器”窗口中，导航到 **Provision\_Tools** 文件夹。 右键单击“tpm_device_provision”项目，然后选择“设为启动项目”。 

5. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。  应用将读取并显示“注册 ID”和“认可密钥”。 记录或复制这些值。 在下一部分，这些值将用于设备注册。

::: zone-end

::: zone pivot="programming-language-python"

1. 启动 Visual Studio。

2. 打开在 cmake 文件夹中生成的名为 `azure_iot_sdks.sln` 的解决方案。

3. 在 Visual Studio 菜单中，选择“生成” > “生成解决方案”以生成解决方案中的所有项目。  

4. 右键单击“tpm_device_provision”项目，  然后选择“设为启动项目”。 

5. 运行解决方案。 输出窗口会显示进行设备注册所需的“认可密钥”和“注册 ID”   。 复制这些值。

::: zone-end

::: zone pivot="programming-language-java"

1. 登录到 Azure 门户，选择左侧菜单上的“所有资源”按钮，然后打开设备预配服务。 记下“ID 范围”和“预配服务全局终结点”。  

    ![设备预配服务信息](./media/quick-create-simulated-device-tpm/extract-dps-endpoints-java.png)

2. 编辑 `src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningTpmSample.java`，使之包括“ID 范围”和“预配服务全局终结点”，如前所述。    

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    ```

3. 保存文件。

4. 使用以下命令生成项目，导航到目标文件夹，然后执行创建的 .jar 文件（将 `{version}` 替换为 Java 版本）：

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-tpm-sample-{version}-with-deps.jar
    ```

5. 程序开始运行后，将显示“认可密钥”和“注册 ID” 。  复制这些值，供下一部分使用。 请确保让程序保持运行。
  
::: zone-end


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry"></a>创建设备注册项

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择设备预配服务。

4. 在“设置”菜单中，选择“管理注册” 。

5. 在页面顶部，选择“+ 添加单个注册”。

6. 在“添加注册”面板中，输入以下信息：

   * 选择“TPM”  作为标识证明 *机制*。
   * 使用你之前记下的值输入 TPM 设备的“注册 ID”和“认可密钥”   。
   * 选择与预配服务链接的 IoT 中心。
   * （可选）可以提供以下信息：
       * 输入唯一“设备 ID”（可以使用建议的 test-docs-device，也可以提供自己的设备 ID）。 为设备命名时，请确保避免使用敏感数据。 如果选择不提供此项，则系统将改用注册 ID 来标识设备。
       * 使用设备所需的初始配置更新“初始设备孪生状态”  。
   * 完成后，按“保存”按钮。

      ![在门户中输入设备注册信息](./media/quick-create-simulated-device-tpm/enter-device-enrollment.png)  

7. 选择“保存”。

## <a name="register-the-device"></a>注册设备

在本部分，将示例代码配置为使用[高级消息队列协议 (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) 向设备预配服务实例发送设备的启动序列。 此启动序列使得设备可注册到与设备预配服务实例链接的 IoT 中心。

::: zone pivot="programming-language-ansi-c"

<a id="firstbootsequence"></a>

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡。

2. 复制“ID 范围”值。

    ![从门户中提取设备预配服务终结点信息](./media/quick-create-simulated-device-tpm/extract-dps-endpoints-ansi-c.png)
3. 在 Visual Studio 的“解决方案资源管理器”窗口中，导航到 **Provision\_Samples** 文件夹。 展开名为 **prov\_dev\_client\_sample** 的示例项目。 展开“源文件”，打开 **prov\_dev\_client\_sample.c**。

4. 在该文件的顶部附近，找到每个设备协议的 `#define` 语句，如下所示。 确保仅取消注释 `SAMPLE_AMQP`。

    目前，[TPM 个人注册不支持 MQTT 协议](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk)。

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

5. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

6. 在同一文件中找到 `main()` 函数的定义。 确保 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_X509` 而不是 `SECURE_DEVICE_TYPE_TPM`，如下所示。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

7. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。

8. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。  出现重新生成项目的提示时，请选择“是”，以便在运行项目之前重新生成项目。

    以下输出示例显示预配设备客户端示例成功启动，然后连接到设备预配服务实例来获取 IoT 中心信息并注册：

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

::: zone pivot="programming-language-nodejs"

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡。

2. 复制“ID 范围”值。

     ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-tpm/extract-dps-endpoints-nodejs.png) 

3. 打开选择的文本编辑器。

4. 在 registerdevice 文件夹中，创建新的 RegisterDevice.js 文件。

5. 在 RegisterDevice.js 文件的开头添加以下 `require` 语句：

    ```Javascript

    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **用于 Node.js 的 Azure IoT SDK** 支持其他协议，例如 _AMQP_、_AMQP WS_、_MQTT WS_。  有关更多示例，请参阅 [Device Provisioning Service SDK for Node.js samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples)（用于 Node.js 的设备预配服务 SDK 示例）。
    > 

6. 添加 **globalDeviceEndpoint** 和 **idScope** 变量，使用它们创建 **ProvisioningDeviceClient** 实例。 将 **{globalDeviceEndpoint}** 和 **{idScope}** 替换为  **步骤 1** 中的“全局设备终结点”和“ID 范围”的值：

    ```Javascript

    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

7. 添加以下函数，实现设备上的方法：

    ```JavaScript

    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

8. 保存并关闭 _RegisterDevice.js_ 文件。 

9. 运行以下命令：

    ```cmd/sh
    node RegisterDevice.js
    ```

10. 请注意相关消息，这些消息模拟设备启动后连接到设备预配服务以获取 IoT 中心信息的情况。

::: zone-end

::: zone pivot="programming-language-python"

1. 导航到 Git 存储库的示例文件夹。

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

2. 使用 Python IDE，编辑名为 provisioning\_device\_client\_sample.py 的 Python 脚本（将 `{globalServiceEndpoint}` 和 `{idScope}` 替换为前面复制的值）。 此外，请确保 SECURITY\_DEVICE\_TYPE 设置为 `ProvisioningSecurityDeviceType.TPM`。

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![服务信息](./media/quick-create-simulated-device-tpm/extract-dps-endpoints-python.png)

3. 运行该示例。

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

4. 请注意相关消息，这些消息模拟设备启动后连接到设备预配服务以获取 IoT 中心信息的情况。

::: zone-end

::: zone pivot="programming-language-java"

1. 在计算机上运行 Java 示例代码的命令窗口中按 Enter，继续运行应用程序。 请注意相关消息，这些消息模拟设备启动后连接到设备预配服务以获取 IoT 中心信息的情况。  

    ![最终的 Java TPM 设备程序](./media/quick-create-simulated-device-tpm/program-final-java.png)

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>确认设备预配注册

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择设备分配到的 IoT 中心。

4. 在“资源管理器”菜单中选择“IoT 设备” 。

5. 如果设备预配成功，设备 ID 应显示在列表中，其中状态设为“已启用”。 如果看不到设备，请选择页面顶部的“刷新”。

    :::zone pivot="programming-language-ansi-c"

    ![设备注册到 IoT 中心，适用于 C](./media/quick-create-simulated-device-tpm/hub-registration-ansi-c.png) 

    :::zone-end

    :::zone pivot="programming-language-csharp"

      ![设备注册到 IoT 中心，适用于 CSharp](./media/quick-create-simulated-device-tpm/hub-registration-csharp.png)

    ::: zone-end

    ::: zone pivot="programming-language-nodejs"

    ![设备注册到 IoT 中心，适用于 Node.js](./media/quick-create-simulated-device-tpm/hub-registration-nodejs.png)

    ::: zone-end

    ::: zone pivot="programming-language-python"

    ![设备注册到 IoT 中心](./media/quick-create-simulated-device-tpm/hub-registration-python.png) 

    ::: zone-end

    ::: zone pivot="programming-language-java"

    ![设备注册到 IoT 中心](./media/quick-create-simulated-device-tpm/hub-registration-java.png) 

    ::: zone-end


> [!NOTE]
> 如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。
>

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

在本快速入门中，你已在计算机上创建 TPM 模拟设备，并已使用 IoT 中心设备预配服务将其预配到 IoT 中心。 若要了解如何以编程方式注册 TPM 设备，请继续阅读快速入门中关于 TPM 设备的编程注册内容。

> [!div class="nextstepaction"]
> [快速入门 - 将 TPM 设备注册到 Azure IoT 中心设备预配服务](quick-enroll-device-tpm.md)