---
title: 快速入门 - 通过 C 使用对称密钥将设备预配到 Azure IoT 中心
description: 本快速入门将使用 C 设备 SDK 预配设备，使其将对称密钥与 Azure IoT 中心设备预配服务 (DPS) 结合使用
author: wesmc7777
ms.author: wesmc
ms.date: 01/14/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f1de50e1e069305c13a521a27462ea786c6a3e49
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748391"
---
# <a name="quickstart-provision-a-device-with-symmetric-keys"></a>快速入门：使用对称密钥预配设备

在本快速入门中，将了解如何在 Windows 开发计算机上运行设备预配代码，将其作为 IoT 设备连接到 IoT 中心。 你将配置此设备，以使用对称密钥对设备预配服务实例进行身份验证，并将此设备分配到 IoT 中心。 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的示例代码将用于预配设备。 将根据预配服务实例的单个注册来识别该设备，然后将其分配到 IoT 中心。

虽然本文演示了使用单个注册进行预配，但你也可以使用注册组。 使用注册组时有一些不同之处。 例如，必须将派生的设备密钥与设备的唯一注册 ID 一起使用。 虽然对称密钥注册组不限于旧设备，但[如何使用对称密钥证明预配旧设备](how-to-legacy-device-symm-key.md)提供了注册组示例。 有关详细信息，请参阅[对称密钥证明的组注册](concepts-symmetric-key-attestation.md#group-enrollments)。

如果不熟悉自动预配过程，请查看[预配](about-iot-dps.md#provisioning-process)概述。 

另外，在继续学习本快速入门之前，请确保已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。 本快速入门需要你已创建设备预配服务实例。

本文面向基于 Windows 的工作站。 但是，你也可以在 Linux 上执行过程。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

以下先决条件适用于 Windows 开发环境。 对于 Linux 或 macOS，请参阅 SDK 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019，已启用[“使用 C++ 的桌面开发”](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负载。 Visual Studio 2015 和 Visual Studio 2017 也受支持。

* 已安装最新版本的 [Git](https://git-scm.com/download/)。

<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 

SDK 包含设备的预配示例代码。 此代码将尝试在设备启动顺序期间进行预配。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装 **之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

    旧版本的 CMake 生成系统无法生成本文中使用的解决方案文件。 请确保使用较新版本的 CMake。

2. 单击“标记”  ，然后在 [Azure IoT C SDK 的版本页](https://github.com/Azure/azure-iot-sdk-c/releases/latest)上查找最新版本的标记名称。

3. 打开命令提示符或 Git Bash shell。 运行以下命令以克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 使用在上一步找到的标记作为 `-b` 参数的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

4. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 从 `azure-iot-sdk-c` 目录运行以下命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 运行以下命令，生成特定于你的开发客户端平台的 SDK 版本。 将在 `cmake` 目录中为设备预配代码生成 Visual Studio 解决方案。 

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

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>在门户中创建设备注册项

1. 登录到 [Azure 门户](https://portal.azure.com)，选择左侧菜单上的“所有资源”  按钮，然后打开“设备预配”服务。

2. 选择“管理注册”选项卡，然后选择顶部的“添加个人注册”按钮   。 

3. 在“添加注册”面板中输入以下信息，然后按“保存”按钮   。

   - **机制**：选择“对称密钥”作为标识证明“机制”   。

   - **自动生成密钥**：选中此框。

   - **注册 ID**：输入注册 ID 以标识注册。 仅使用小写字母数字和短划线（“-”）字符。 例如，symm-key-device-007  。

   - **IoT 中心设备 ID：** 输入设备标识符。 例如：device-007  。

     ![在门户中为对称密钥证明添加单个注册](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. 保存注册后，将生成“主要密钥”和“辅助密钥”，并将其添加到注册条目   。 对称密钥设备注册会在“单独注册”  选项卡的“注册 ID”  列下显示为 symm-key-device-007  。 

    打开注册并复制生成的“主要密钥”  的值。



<a id="firstbootsequence"></a>

## <a name="run-the-provisioning-code-for-the-device"></a>运行设备的预配代码

在本部分更新向设备预配服务实例发送设备启动序列的示例代码。 此启动序列使得设备可被识别并分配到与设备预配服务实例链接的 IoT 中心。



1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值   。

    ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 中，打开通过运行 CMake 生成的 azure_iot_sdks.sln  解决方案文件。 解决方案文件应位于以下位置：

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

    如果该文件不是在 cmake 目录中生成的，请确保使用的是最新版本的 CMake 生成系统。

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

6. 在 prov\_dev\_client\_sample.c 中，找到已注释掉的对 `prov_dev_set_symmetric_key_info()` 的调用。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消对函数调用的注释，并将占位符值（包括尖括号）替换为注册 ID 和主键值。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    保存文件。

7. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。 

8. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。   在重新生成项目提示中，选择“是”，以便在运行项目之前重新生成项目。

    以下输出是设备成功连接到要分配到 IoT 中心的预配服务实例的示例：

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. 在门户中，导航到设备分配到的 IoT 中心，然后选择“IoT 设备”选项卡。将设备成功预配到中心以后，设备 ID 会显示在“IoT 设备”边栏选项卡上，“状态”为“已启用”。 你可能需要按顶部的“刷新”按钮  。 

    ![设备注册到 IoT 中心](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择设备预配服务。 打开服务的“管理注册”，然后选择“个人注册”选项卡   。选中在本快速入门中注册的设备的“注册 ID”旁边的复选框，然后按窗格顶部的“删除”按钮   。 
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择 IoT 中心  。 打开中心的“IoT 设备”，选中在本快速入门中注册的设备的“设备 ID”旁边的复选框，然后按窗格顶部的“删除”按钮    。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在 Windows 计算机上运行了设备预配代码。  已使用对称密钥对设备进行身份验证并将其预配到 IoT 中心。 若要了解如何预配 X.509 证书设备，请继续阅读 X.509 设备快速入门。 

> [!div class="nextstepaction"]
> [Azure 快速入门 - 使用 Azure IoT C SDK 预配 X.509 设备](quick-create-simulated-device-x509.md)