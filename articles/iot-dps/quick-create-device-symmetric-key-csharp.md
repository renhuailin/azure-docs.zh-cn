---
title: 快速入门 - 通过 C# 使用对称密钥将设备预配到 Azure IoT 中心
description: 本快速入门将使用用于设备预配服务 (DPS) 的 C# 设备 SDK 将对称密钥设备预配到 IoT 中心
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: f97840a05115bf5659a6f7579b72786e890051a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92429222"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>快速入门：使用 C# 预配对称密钥设备

本快速入门将介绍如何使用 C# 将 Windows 开发计算机作为设备预配到 IoT 中心。 此设备将使用对称密钥和单个注册对设备预配服务 (DPS) 实例进行身份验证，以便被分配到 IoT 中心。 [C# 的 Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp)中的示例代码将用于预配设备。 

虽然本文演示了使用单个注册进行预配，但你也可以使用注册组。 使用注册组时有一些不同之处。 例如，必须将派生的设备密钥与设备的唯一注册 ID 一起使用。 [使用对称密钥预配设备](how-to-legacy-device-symm-key.md)提供了注册组示例。 有关注册组的详细信息，请参阅[对称密钥证明的组注册](concepts-symmetric-key-attestation.md#group-enrollments)。

如果不熟悉自动预配过程，请查看[预配](about-iot-dps.md#provisioning-process)概述。 

另外，在继续学习本快速入门之前，请确保已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。 本快速入门需要你已创建设备预配服务实例。

本文面向基于 Windows 的工作站。 但是，你也可以在 Linux 上执行过程。 对于 Linux 示例，请参阅[多租户预配](how-to-provision-multitenant.md)。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>必备条件

* 确保在基于 Windows 的计算机上安装了 [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/windows) 或更高版本。

* 已安装最新版本的 [Git](https://git-scm.com/download/)。

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>创建设备注册

1. 登录到 [Azure 门户](https://portal.azure.com)，选择左侧菜单上的“所有资源”按钮，打开设备预配服务 (DPS) 实例。

2. 选择“管理注册”选项卡，然后选择顶部的“添加个人注册”按钮   。 

3. 在“添加注册”面板中输入以下信息，然后按“保存”按钮   。

   - **机制**：选择“对称密钥”作为标识证明“机制”   。

   - **自动生成密钥**：选中此框。

   - **注册 ID**：输入注册 ID 以标识注册。 仅使用小写字母数字和短划线（“-”）字符。 例如 symm-key-csharp-device-01。

   - **IoT 中心设备 ID：** 输入设备标识符。 例如 csharp-device-01。

     ![在门户中为对称密钥证明添加单个注册](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. 保存注册后，将生成“主要密钥”和“辅助密钥”，并将其添加到注册条目   。 对称密钥设备注册会在“单个注册”选项卡的“注册 ID”列下显示为“symm-key-csharp-device-01” 。 

5. 打开注册并复制生成的“主要密钥”和“辅助密钥”的值 。 稍后你将在添加用于设备预配示例代码的环境变量时使用此密钥值和“注册 ID”。



## <a name="prepare-the-c-environment"></a>准备 C# 环境 

1. 打开 Git CMD 或 Git Bash 命令行环境。 使用以下命令克隆 [C# 的 Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>准备设备预配代码

本部分将添加以下四个环境变量，这些变量将作为参数用于设备预配示例代码，以预配对称密钥设备。 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

预配代码将根据这些变量联系 DPS 实例，以便对设备进行身份验证。 然后，会根据单个注册配置将设备分配到已链接到 DPS 实例的 IoT 中心。 预配后，示例代码会将一些测试遥测发送到 IoT 中心。

1. 在 [Azure 门户](https://portal.azure.com)中的“设备预配服务”菜单上，选择“概述”，并复制“服务终结点”和“ID 范围” 。 你会将这些值用于 `PROVISIONING_HOST` 和 `DPS_IDSCOPE` 环境变量。

    ![服务信息](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. 打开命令提示符，导航到克隆的示例存储库中的 SymmetricKeySample：

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. 在“SymmetricKeySample”文件夹中，在文本编辑器中打开“Program.cs”并找到设置 `individualEnrollmentPrimaryKey` 和 `individualEnrollmentSecondaryKey` 字符串的代码行 。 按如下所示更新这些代码行，以使用环境变量，而不是对密钥进行硬编码。
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "";
        //private const string individualEnrollmentSecondaryKey = "";

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable("PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    此外，找到设置 `registrationId` 字符串的代码行，并按如下所示将其更新为也使用环境变量：

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    保存对 Program.cs 所做的更改。

3. 在命令提示符中，为上一部分从单个注册中复制的 ID 范围、注册 ID、主对称密钥和辅助对称密钥添加环境变量。  

    以下命令是展示命令语法的示例。 确保使用正确的值。

    ```console
    set DPS_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-csharp-device-01
    ```

    ```console
    set PRIMARY_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

    ```console
    set SECONDARY_SYMMETRIC_KEY=Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
    ```


4. 使用以下命令生成并运行示例代码。

    ```console
    dotnet run
    ```

5. 预期输出应与以下内容类似，其中显示根据单个注册设置设备被分配到的已链接的 IoT 中心。 示例“TestMessage”字符串将作为测试发送到此中心：

    ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run
    RegistrationID = symm-key-csharp-device-01
    ProvisioningClient RegisterAsync . . . Assigned
    ProvisioningClient AssignedHub: docs-test-iot-hub.azure-devices.net; DeviceID: csharp-device-01
    Creating Symmetric Key DeviceClient authentication
    DeviceClient OpenAsync.
    DeviceClient SendEventAsync.
    DeviceClient CloseAsync.
    Enter any key to exit
    ```
    
6. 在 Azure 门户中，导航到已链接到预配服务的 IoT 中心，并打开“IoT 设备”边栏选项卡。 成功将对称密钥设备预配到中心后，设备 ID 的“状态”会显示为“已启用”。 如果在运行设备示例代码前已打开边栏选项卡，则可能需要按顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> 如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择设备预配服务。 打开服务的“管理注册”，然后选择“个人注册”选项卡   。选中在本快速入门中注册的设备的“注册 ID”旁边的复选框，然后按窗格顶部的“删除”按钮   。 
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择 IoT 中心  。 打开中心的“IoT 设备”，选中在本快速入门中注册的设备的“设备 ID”旁边的复选框，然后按窗格顶部的“删除”按钮    。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你使用 IoT 中心设备预配服务将基于 Windows 的对称密钥设备预配到了 IoT 中心。 若要了解如何使用 C# 预配 X.509 证书设备，请继续阅读下面的 X.509 设备快速入门。 

> [!div class="nextstepaction"]
> [Azure 快速入门 - 使用 DPS 和 C# 预配 X.509 设备](quick-create-simulated-device-x509-csharp.md)
