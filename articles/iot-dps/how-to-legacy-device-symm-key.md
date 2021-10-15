---
title: 使用对称密钥预配设备 - Azure IoT 中心设备预配服务
description: 如何使用对称密钥通过设备预配服务 (DPS) 实例预配设备
author: wesmc7777
ms.author: wesmc
ms.date: 04/23/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: c2330203ce2617bf7cc42b8c7549d11a73185f12
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458238"
---
# <a name="how-to-provision-devices-using-symmetric-key-enrollment-groups"></a>如何使用对称密钥注册组预配设备

本文演示了如何使用注册组将多个模拟对称密钥设备安全地预配到一个 IoT 中心。

有些设备可能没有证书、TPM 或可用于安全地识别设备的任何其他安全功能。 设备预配服务包括[对称密钥证明](concepts-symmetric-key-attestation.md)。 对称密钥证明可以用于根据 MAC 地址或序列号等唯一信息来标识设备。

如果安装[硬件安全模块 (HSM)](concepts-service.md#hardware-security-module) 和证书比较轻松，这可能是标识和预配设备更好的方式。 使用 HSM，你可以避免更新部署到所有设备上的代码，并且不必在设备映像中嵌入密钥。 本文假定：HSM 或证书都是不可行的选择。 但你有一些更新设备代码的方法，可供使用设备预配服务来预配设备。 

本文还假定，在安全的环境中进行设备更新，以防发生对组主密钥或派生的设备密钥未经授权的访问。

本文面向基于 Windows 的工作站。 但是，你也可以在 Linux 上执行过程。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)。

> [!NOTE]
> 本文中使用的示例是用 C 编写的。还有一个可用的 [C# 设备预配对称密钥示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/provisioning/Samples/device/SymmetricKeySample)。 若要使用此示例，请下载或克隆 [azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) 存储库，并按照示例代码中的行说明进行操作。 可以按照本文中的说明使用门户创建对称密钥注册组，并查找运行示例所需的 ID 范围和注册组主密钥和辅助密钥。 还可以使用示例创建单个注册。

## <a name="prerequisites"></a>先决条件

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)快速入门。

以下先决条件适用于 Windows 开发环境。 对于 Linux 或 macOS，请参阅 SDK 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019，已启用[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载。 Visual Studio 2015 和 Visual Studio 2017 也受支持。

* 已安装最新版本的 [Git](https://git-scm.com/download/)。

## <a name="overview"></a>概述

将基于标识每个设备的信息为该设备定义唯一注册 ID。 例如，MAC 地址或序列号。

将通过设备预配服务创建使用[对称密钥证明](concepts-symmetric-key-attestation.md)的注册组。 注册组包括组的主密钥。 将使用该主密钥对每个唯一注册 ID 进行哈希处理，为各设备生成一个唯一设备密钥。 设备将该派生设备密钥用于其唯一注册 ID，在设备预配服务中用作证明并且该设备被分配给 IoT 中心。

本文所示的设备代码采用[快速入门：预配模拟对称密钥设备](quick-create-simulated-device-symm-key.md)中所用的模式。 该代码使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的示例来模拟设备。 如快速入门文章中所示，模拟设备将使用注册组（而不是各个注册）进行证明。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 

SDK 包含模拟设备的示例代码。 该模拟设备将尝试在设备启动顺序期间进行预配。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装 **之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

2. 找到[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK 的标记名称。

3. 打开命令提示符或 Git Bash shell。 运行以下命令以克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 使用在上一步找到的标记作为 `-b` 参数的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

4. 在 Git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 从 `azure-iot-sdk-c` 目录运行以下命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 运行以下命令，生成特定于你的开发客户端平台的 SDK 版本。 将在 `cmake` 目录中生成模拟设备的 Visual Studio 解决方案。 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。 

    生成成功后，最后的几个输出行如下所示：

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>创建对称密钥注册组

1. 登录到 [Azure 门户](https://portal.azure.com)，并打开你的设备预配服务实例。

2. 选择“管理注册”选项卡，然后单击页面顶部的“添加注册组”按钮。 

3. 在“添加注册组”中输入以下信息，然后单击“保存”按钮。

   - **组名**：输入 **mylegacydevices**。

   - **证明类型**：选择 **对称密钥**。

   - **自动生成密钥**：选中此复选框。

   - **选择将设备分配到中心的方式**：选择 **静态配置** 以便可以分配到特定的中心。

   - **选择可将该组分配到的 IoT 中心**：选择某一个中心。

     ![为对称密钥证明添加注册组](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. 保存注册后，将生成“主要密钥”和“辅助密钥”，并将其添加到注册条目。 对称密钥注册组在“注册组”选项卡的“组名”栏下显示为“mylegacydevices”。 

    打开注册并复制生成的“主要密钥”  的值。 此密钥是组主密钥。


## <a name="choose-a-unique-registration-id-for-the-device"></a>选择设备的唯一注册 ID

必须定义唯一注册 ID 来标识每个设备。 可以使用 MAC 地址、序列号或设备中的任何唯一信息。 

在此示例中，我们使用 MAC 地址和序列号的组合，构成以下注册 ID 字符串。

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

为每个设备创建唯一的注册 ID。 有效字符为小写字母数字和短划线（“-”）。


## <a name="derive-a-device-key"></a>派生一个设备密钥 

若要生成设备密钥，请使用注册组主密钥计算每个设备的注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)。 然后，为每个设备将结果转换 Base64 格式。

> [!WARNING]
> 每个设备的设备代码应该只包含该设备的相应派生设备密钥。 不要在设备代码中包含你的组主键。 泄露的主密钥可能会危及所有使用该密钥进行身份验证的设备的安全。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 的 IoT 扩展提供了用于生成派生设备密钥的 [`compute-device-key`](/cli/azure/iot/dps?view=azure-cli-latest&preserve-view=true#az_iot_dps_compute_device_key) 命令。 可从基于 Windows 或 Linux 的系统，在 PowerShell 或 Bash shell 中使用此命令。

将 `--key` 参数值替换为注册组中的主密钥。

将 `--registration-id` 参数值替换为注册 ID。

```azurecli
az iot dps compute-device-key --key 8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw== --registration-id sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

示例结果：

```azurecli
"Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc="
```
# <a name="windows"></a>Windows

如果使用的是基于 Windows 的工作站，可以使用 PowerShell 生成派生的设备密钥，如以下示例中所示。

将“键”  值替换为前面记录的“主键”  。

用注册 ID 替换 REG_ID 值。

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="linux"></a>[Linux](#tab/linux)

如果使用的是 Linux 工作站，可以使用 openssl 生成派生的设备密钥，如以下示例中所示。

将“键”  值替换为前面记录的“主键”  。

用注册 ID 替换 REG_ID 值。

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

每个设备将使用其派生的设备密钥和唯一注册 ID，于预配期间在注册组中执行对称密钥证明。



## <a name="create-a-device-image-to-provision"></a>创建用于预配的设备映像

在本部分，你将更新你在较早前设置的位于 Azure IoT C SDK 中的名为 prov\_dev\_client\_sample 的预配示例。 

此示例代码模拟将预配请求发送到你的设备预配服务实例的设备启动序列。 启动序列将会使设备被识别并分配到你在注册组上配置的 IoT 中心。 将针对使用注册组进行预配的每个设备完成此操作。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值 。

    ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-x509/copy-id-scope.png) 

2. 在 Visual Studio 中，打开较早前通过运行 CMake 生成的 azure_iot_sdks.sln  解决方案文件。 解决方案文件应位于以下位置：

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. 在 Visual Studio 的“解决方案资源管理器”窗口中，导航到 **Provision\_Samples** 文件夹。  展开名为 **prov\_dev\_client\_sample** 的示例项目。 展开“源文件”，打开 **prov\_dev\_client\_sample.c**。 

4. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 在同一文件中找到 `main()` 函数的定义。 确保 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`，如下所示：

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. 在 **prov\_dev\_client\_sample.c** 中，找到已注释掉的对 `prov_dev_set_symmetric_key_info()` 的调用。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消注释该函数调用，并将占位符值（包括尖括号）替换为设备的唯一注册 ID 以及生成的派生设备密钥。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    保存文件。

7. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。 

8. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。  在重新生成项目的提示中单击“是”，以便在运行项目之前重新生成项目。

    以下输出是模拟设备成功启动并连接到要分配到 IoT 中心的预配服务实例的示例：

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. 在门户中，导航到模拟设备分配到的 IoT 中心，然后单击“IoT 设备”选项卡。将模拟设备成功预配到中心以后，设备 ID 会显示在“IoT 设备”边栏选项卡上，“状态”为“已启用”。 你可能需要单击顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>安全注意事项

请注意，这会使派生的设备密钥成为每个设备的映像的一部分，这不是推荐的安全最佳做法。 这是通常需要在安全性与易用性之间进行权衡的原因之一。 你必须根据自己的要求全面检查设备的安全性。


## <a name="next-steps"></a>后续步骤

* 要详细了解重新预配，请参阅

> [!div class="nextstepaction"]
> [IoT 中心设备重新预配概念](concepts-device-reprovision.md)

> [!div class="nextstepaction"]
> [快速入门：预配模拟对称密钥设备](quick-create-simulated-device-symm-key.md)

* 要详细了解取消预配，请参阅

> [!div class="nextstepaction"]
> [如何取消预配以前自动预配的设备](how-to-unprovision-devices.md)
