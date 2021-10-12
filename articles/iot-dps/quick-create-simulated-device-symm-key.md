---
title: 快速入门 - 将模拟对称密钥设备预配到 Microsoft Azure IoT 中心
description: 了解如何在 Azure IoT 中心设备预配服务 (DPS) 中预配使用对称密钥进行身份验证的设备
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: 43c6dc8e9d8a6438468c44fd2b8cc31d04a92c2f
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276799"
---
# <a name="quickstart-provision-a-simulated-symmetric-key-device"></a>快速入门：预配模拟对称密钥设备

在本快速入门中，你将在 Windows 计算机上创建一个模拟设备。 模拟设备将配置为使用[对称密钥证明](concepts-symmetric-key-attestation.md)机制进行身份验证。 配置设备后，你将使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。

如果你不熟悉预配过程，请查看[预配](about-iot-dps.md#provisioning-process)概述。

本快速入门将演示适用于基于 Windows 工作站的解决方案。 但是，也可以在 Linux 上执行这些过程。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。
::: zone pivot="programming-language-ansi-c"

* 如果使用 Windows 开发环境，请安装启用了[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载的 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019。 Visual Studio 2015 和 Visual Studio 2017 也受支持。 对于 Linux 或 macOS，请参阅 SDK 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。

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

* 在基于 Windows 的计算机上安装 [Python 3.7](https://www.python.org/downloads/) 或更高版本。 可以通过运行 `python --version` 来检查 Python 版本。

::: zone-end

::: zone pivot="programming-language-java"

* 在计算机上安装 [Java SE 开发工具包 8](/azure/developer/java/fundamentals/java-support-on-azure) 或更高版本。

* 下载并安装 [Maven](https://maven.apache.org/install.html)。

::: zone-end

* [安装最新版本的 Git](https://git-scm.com/download/)。 确保将 Git 添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。


<a id="setupdevbox"></a>

## <a name="prepare-your-development-environment"></a>准备开发环境

::: zone pivot="programming-language-ansi-c"

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 示例代码尝试在执行设备的启动序列期间预配设备。

1. 下载最新的 [CMake 生成系统](https://cmake.org/download/)。

    >[!IMPORTANT]
    >在开始 `CMake` 安装之前，请确认在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负载）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。 另请注意旧版本的 CMake 生成系统无法生成本文中使用的解决方案文件。 请确保使用最新版本的 CMake。

2. 打开 Web 浏览器，转到 [Azure IoT C SDK 发布页](https://github.com/Azure/azure-iot-sdk-c/releases/latest)。

3. 选择页面顶部的“标记”选项卡。

4. 复制最新版 Azure IoT C SDK 的标记名称。

5. 打开命令提示符或 Git Bash shell。 运行以下命令（请将其中的 `<release-tag>` 替换为在上一步骤中复制的标记），克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    此操作可能需要几分钟才能完成。

6. 操作完成后，从 `azure-iot-sdk-c` 目录中运行以下命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. 代码示例使用对称密钥来提供证明。 运行以下命令，生成特定于你的开发客户端平台（包括设备预配客户端）的 SDK 版本：

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    >[!TIP]
    >如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。

8. 生成成功完成后，最后的几个输出行类似于以下输出：

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 16 2019
    -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19042.
    -- The C compiler identification is MSVC 19.29.30040.0
    -- The CXX compiler identification is MSVC 19.29.30040.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
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

2. 使用以下命令克隆[适用于 Node.js 的 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node.git) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. 打开 Git CMD 或 Git Bash 命令行环境。

2. 使用以下命令克隆[适用于 Python 的 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python.git) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

3. 转到 provision_symmetric_key.py 示例文件所在的 `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` 目录。

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. 通过运行以下命令来安装 azure-iot-device 库。

    ```console
    pip install azure-iot-device
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. 打开 Git CMD 或 Git Bash 命令行环境。

2. 使用以下命令克隆[适用于 Java 的 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java.git) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. 转到 `azure-iot-sdk-java` 根目录，并生成项目以下载全部所需的包。

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. 通过运行以下命令来安装 azure-iot-device 库。

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

::: zone-end

## <a name="create-a-device-enrollment"></a>创建设备注册

Azure IoT 设备预配服务支持两类注册：

* [注册组](concepts-service.md#enrollment-group)：用于注册多个相关设备。
* [单个注册](concepts-service.md#individual-enrollment)：用于注册单个设备。

本文演示要使用 IoT 中心预配的单个设备的单个注册过程。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择你的设备预配服务。

4. 在“设置”菜单中，选择“管理注册” 。

5. 在页面顶部，选择“+ 添加单个注册”。

6. 在“添加注册”页中，输入以下信息。

   * 机制：选择“对称密钥”作为标识证明机制。

   * **自动生成密钥**：选中此框。

   * **注册 ID**：输入注册 ID 以标识注册。 仅使用小写字母数字和短划线（“-”）字符。 例如，symm-key-device-007  。

   * **IoT 中心设备 ID：** 输入设备标识符。

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png" alt-text="输入设备注册信息。":::

    ::: zone-end

    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-csharp.png" alt-text="输入 C# 设备注册信息。":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-nodejs.png" alt-text="输入 Node.js 设备注册信息。":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-python.png" alt-text="输入 Python 设备注册信息。":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-java.png" alt-text="输入 Java 设备注册信息。":::

    ::: zone-end

7. 选择“保存”  。 随即会生成主密钥和辅助密钥并将其添加到注册项，你将返回到“管理注册”页  。

8. 若要查看模拟对称密钥设备注册，请选择“单个注册”选项卡。

9. 选择你的设备 (symm-key-device-007)。

10. 复制生成的“主密钥”的值。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/copy-device-enrollment-primary-key.png" alt-text="复制设备注册的主密钥":::

<a id="firstbootsequence"></a>

## <a name="prepare-and-run-the-device-provisioning-code"></a>准备和运行设备预配代码

::: zone pivot="programming-language-ansi-c"

在本部分，你将更新向设备预配服务实例发送设备启动序列的设备示例代码。 此启动序列使得设备可被识别、完成身份验证并分配到与设备预配服务实例链接的 IoT 中心。

示例预配代码按顺序完成以下任务：

1. 使用以下三个参数通过设备预配资源对你的设备进行身份验证：

    * 设备预配服务的 ID 范围
    * 设备注册的注册 ID。
    * 设备注册的主对称密钥。

2. 将设备分配到已与设备预配服务实例链接的 IoT 中心。

若要使用设备信息更新并运行预配示例，请执行以下操作：

1. 在设备预配服务的主菜单中，选择“概述”。

2. 复制“ID 范围”值。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="提取设备预配服务终结点信息":::

3. 在 Visual Studio 中，打开通过运行 CMake 生成的 azure_iot_sdks.sln  解决方案文件。 解决方案文件应位于以下位置：

    ```output

    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln

    ```

    >[!TIP]
    >如果该文件不是在 cmake 目录中生成的，请确保使用的是最新版本的 CMake 生成系统。

4. 在 Visual Studio 的“解决方案资源管理器”窗口中，转到“Provision\_Samples”文件夹。 展开名为 **prov\_dev\_client\_sample** 的示例项目。 展开“源文件”，打开 **prov\_dev\_client\_sample.c**。 

5. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。

    ```c
    static const char* id_scope = "0ne00002193";
    ```

6. 在同一文件中找到 `main()` 函数的定义。 确保 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`，如下所示：

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

7. 在 prov\_dev\_client\_sample.c 中，找到已注释掉的对 `prov_dev_set_symmetric_key_info()` 的调用。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    将函数调用取消注释，并将占位符值（包括尖括号）替换为前面复制的注册 ID 和主密钥值。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```

8. 保存文件。

9. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。

10. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。   在重新生成项目提示中，选择“是”，以便在运行项目之前重新生成项目。

    以下输出是设备成功连接到要分配到 IoT 中心的预配服务实例的示例：

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

示例预配代码完成以下任务：

1. 使用以下三个参数通过设备预配资源对你的设备进行身份验证：

    * 设备预配服务的 ID 范围
    * 设备注册的注册 ID。
    * 设备注册的主对称密钥。

2. 将设备分配到已与设备预配服务实例链接的 IoT 中心。

3. 将测试遥测消息发送到 IoT 中心。

若要使用设备信息更新并运行预配示例，请执行以下操作：

1. 在设备预配服务的主菜单中，选择“概述”。

2. 复制“ID 范围”值。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="提取设备预配服务终结点信息":::

3. 打开命令提示符，转到克隆的示例存储库中的“SymmetricKeySample”：

    ```cmd
    cd azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample
    ```

4. 在 SymmetricKeySample 文件夹中，在文本编辑器中打开 Parameters.cs 。 此文件显示该示例支持的参数。 运行示例时，本文中将仅使用前三个必需的参数。 查看此文件中的代码。 无需任何更改。

    | 参数                         | 必需 | 说明     |
    | :-------------------------------- | :------- | :-------------- |
    | `--s` 或 `--IdScope`              | 正确     | DPS 实例的 ID 范围 |
    | `--i` 或 `--Id`                   | 正确     | 使用个人注册时的注册 ID，或者使用组注册时所需的设备 ID。 |
    | `--p` 或 `--PrimaryKey`           | 正确     | 个人注册或组注册的主密钥。 |
    | `--e` 或 `--EnrollmentType`       | 错误    | 注册类型：`Individual` 或 `Group`。 默认为 `Individual` |
    | `--g` 或 `--GlobalDeviceEndpoint` | 错误    | 设备要连接到的全局终结点。 默认为 `global.azure-devices-provisioning.net` |
    | `--t` 或 `--TransportType`        | 错误    | 用于与设备预配实例通信的传输。 默认为 `Mqtt`。 可能的值包括 `Mqtt`、`Mqtt_WebSocket_Only`、`Mqtt_Tcp_Only`、`Amqp`、`Amqp_WebSocket_Only`、`Amqp_Tcp_only` 和 `Http1`。|

5. 在 SymmetricKeySample 文件夹中，在文本编辑器中打开 ProvisioningDeviceClientSample.cs 。 此文件显示如何将 [SecurityProviderSymmetricKey](/dotnet/api/microsoft.azure.devices.shared.securityprovidersymmetrickey?view=azure-dotnet&preserve-view=true) 类和 [ProvisioningDeviceClient](/dotnet/api/microsoft.azure.devices.provisioning.client.provisioningdeviceclient?view=azure-dotnet&preserve-view=true) 类结合使用来预配模拟对称密钥设备。 查看此文件中的代码。  无需任何更改。

6. 在替换三个示例参数后（将 `<id-scope>` 替换为设备预配服务 ID 范围，将 `<registration-id>` 替换为设备的注册 ID，将 `<primarykey>` 替换为设备的主密钥），使用以下命令生成并运行示例代码。

    ```console
    dotnet run --s <id-scope> --i <registration-id> --p <primarykey>
    ```

7. 现在应会看到类似于以下输出的内容。 “TestMessage”字符串已作为测试消息发送到中心。

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

示例预配代码按顺序完成以下任务：

1. 使用以下四个参数通过设备预配资源对你的设备进行身份验证：
    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. 将设备分配到已与设备预配服务实例链接的 IoT 中心。

3. 将测试遥测消息发送到 IoT 中心。

若要使用设备信息更新并运行预配示例，请执行以下操作：

1. 在设备预配服务的主菜单中，选择“概述”。

2. 复制“ID 范围”和“服务终结点”值 。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="提取设备预配服务终结点信息":::

3. 打开命令提示符以执行 Node.js 命令，然后转到以下目录：

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

4. 在“provisioning/device/samples”文件夹中打开“register_symkey.js”并查看代码 。 请注意，示例代码设置了一个自定义有效负载：

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    可以注释掉此代码，因为本快速入门不需要此代码。 如果要使用自定义分配函数将设备分配到 IoT 中心，则需要自定义有效负载。 有关详细信息，请参阅[教程：使用自定义分配策略](tutorial-custom-allocation-policies.md)。

     使用 `provisioningClient.register()` 方法尝试注册设备。

    不需要进行其他更改。

5. 现在，在命令提示符下设置以下环境变量（请将 `<id-scope>` 替换为设备预配服务 ID 范围，将 `<registration-id>` 替换为设备的注册 ID，将 `<primarykey>` 替换为设备的主密钥，将 `<provisioning-host>` 替换为设备预配服务的服务终结点 URL）：

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

6. 使用以下命令生成并运行示例代码：

   ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

7. 现在应会看到类似于以下输出的内容。 “Hello World”字符串已作为测试消息发送到此中心。

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-python"

示例预配代码按顺序完成以下任务：

1. 使用以下四个参数通过设备预配资源对你的设备进行身份验证：

    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. 将设备分配到已与设备预配服务实例链接的 IoT 中心。

3. 将测试遥测消息发送到 IoT 中心。

若要使用设备信息更新并运行预配示例，请执行以下操作：

1. 在设备预配服务的主菜单中，选择“概述”。

2. 复制“ID 范围”和“服务终结点”值 。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="提取设备预配服务终结点信息":::

3. 现在，在命令提示符下设置以下环境变量（请将 `<id-scope>` 替换为设备预配服务 ID 范围，将 `<registration-id>` 替换为设备的注册 ID，将 `<primarykey>` 替换为设备的主密钥，将 `<provisioning-host>` 替换为设备预配服务的服务终结点 URL）：

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

4. 在命令提示符窗口中，转到以下目录：

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

5. 运行 provision_symmetric_key.py 中的 Python 示例代码。

    ```console
    python provision_symmetric_key.py
    ```

6. 现在应会看到类似于以下输出的内容。 一些示例风速遥测消息也已作为测试发送到中心。

     ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```

::: zone-end

::: zone pivot="programming-language-java"

示例预配代码按顺序完成以下任务：

1. 使用以下四个参数通过设备预配资源对你的设备进行身份验证：

    * `GLOBAL_ENDPOINT`
    * `SCOPE_ID`
    * `REGISTRATION_ID`
    * `SYMMETRIC_KEY`

2. 将设备分配到已与设备预配服务实例链接的 IoT 中心。

3. 将测试遥测消息发送到 IoT 中心。

若要使用设备信息更新并运行预配示例，请执行以下操作：

1. 在设备预配服务的主菜单中，选择“概述”。

2. 复制“ID 范围”和“服务终结点”值 。 这两项分别是你的 `SCOPE_ID` 和 `GLOBAL_ENDPOINT`。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="提取设备预配服务终结点信息":::

3. 打开 Java 设备示例代码进行编辑。 设备示例代码的完整路径为：

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

4. 替换 DPS 和设备注册的以下变量的值（请将 `<id-scope>` 替换为设备预配服务 ID 范围，将 `<registration-id>` 替换为设备的注册 ID，将 `<primarykey>` 替换为设备的主密钥，将 `<provisioning-host>` 替换为设备预配服务的服务终结点 URL）：

    ```java
    private static final String SCOPE_ID = "<id-scope>";
    private static final String GLOBAL_ENDPOINT = "<provisioning-host>";
    private static final String SYMMETRIC_KEY = "<primarykey>";
    private static final String REGISTRATION_ID = "<registration-id>";
    ```

5. 打开命令提示符进行生成。 转到 Java SDK 存储库的预配示例项目文件夹。

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

6. 生成示例，然后导航到 `target` 文件夹执行所创建的 `.jar` 文件。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

7. 现在应会看到类似于以下输出的内容。

     ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>确认设备预配注册

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择设备分配到的 IoT 中心。

4. 在“资源管理器”菜单中选择“IoT 设备” 。

5. 如果设备预配成功，设备 ID 应显示在列表中，其状态设为“已启用”。 如果看不到设备，请选择页面顶部的“刷新”。

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration.png" alt-text="设备注册到 IoT 中心":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-csharp.png" alt-text="CSharp 设备已注册到 IoT 中心":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-nodejs.png" alt-text="Node.js 设备已注册到 IoT 中心":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-python.png" alt-text="Python 设备已注册到 IoT 中心":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-java.png" alt-text="Java 设备已注册到 IoT 中心":::

    ::: zone-end

> [!NOTE]
> 如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。
>

## <a name="clean-up-resources"></a>清理资源

如果你打算继续使用和探索设备客户端示例，请不要清理在本快速入门中创建的资源。 如果你不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

### <a name="delete-your-device-enrollment"></a>删除设备注册

1. 关闭计算机上的设备客户端示例输出窗口。

2. 在 Azure 门户的左侧菜单中，选择“所有资源”。

3. 选择你的设备预配服务。

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

预配 x.509 证书设备：

> [!div class="nextstepaction"]
> [快速入门 - 使用 Azure IoT C SDK 预配 X.509 设备](quick-create-simulated-device-x509.md)