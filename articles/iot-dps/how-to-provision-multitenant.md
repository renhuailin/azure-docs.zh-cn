---
title: 如何在 Azure IoT 中心设备预配服务中预配多租户的设备
description: 如何使用你的设备预配服务 (DPS) 实例来预配多租户的设备
author: anastasia-ms
ms.author: v-stharr
ms.topic: how-to
ms.date: 10/02/2021
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 595bd473bb70f4c23554998b7240e4ef0ed1183d
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670259"
---
# <a name="how-to-provision-for-multitenancy"></a>如何预配多租户 

本操作指南如何使用[分配策略](concepts-service.md#allocation-policy)将多个模拟对称密钥设备安全地预配到一组 IoT 中心。 由预配服务定义的分配策略支持各种不同的分配方案。 两个常见的方案是：

* 地理位置/GeoLatency：当设备在两位置之间移动时，通过将设备预配到距离每个位置最近的 IoT 中心来改善网络延迟。 在此方案中，为注册选择跨越区域的一组 IoT 中心。 为这些注册选择“最低延迟”分配策略。 此策略会使设备预配服务评估设备延迟，并从一组 IoT 中心确定最接近的 IoT 中心。

* 多租户：IoT 解决方案中使用的设备可能需要被分配到一个特定 IoT 中心或一组 IoT 中心。 解决方案可能要求特定租户的所有设备与一组特定的 IoT 中心进行通信。 在某些情况下，租户可能拥有 IoT 中心并要求设备被分配到其 IoT 中心。

通常会将这两种方案结合使用。 例如，多租户 IoT 解决方案通常使用跨不同区域分散的一组 IoT 中心来分配租户设备。 这些租户设备可被分配到基于地理位置具有最低延迟的组中的 IoT 中心。

本文使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的模拟设备示例来演示如何在跨区域的多租户方案中预配设备。 你将在本文中执行以下步骤：

> [!div class="checklist"]
> * 使用 Azure CLI 创建两个区域 IoT 中心（美国西部 2 和美国东部）
> * 创建多租户注册
> * 使用 Azure CLI 创建两个区域 Linux VM，以充当同一区域中的设备（美国西部 2 和美国东部）
> * 在这两个 Linux VM 上为 Azure IoT C SDK 设置开发环境
> * 模拟设备，以确保为最近区域中同一租户预配设备。

>[!IMPORTANT]
> 某些地区可能会不时对虚拟机的创建实施限制。 在编写本指南时，westus2（美国西部 2）和 eastus（美国东部）区域允许创建 VM。  如果无法在其中一个区域创建 VM，可以尝试在其他区域中创建。 若要了解有关在创建 VM 时选择 Azure 地理区域的详细信息，请参阅 [Azure 中虚拟机的区域](../virtual-machines/regions.md)

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>创建两个区域 IoT 中心

在本部分中，你将为租户创建一个 Azure 资源组和两个新的区域性 IoT 中心资源。 一个 IoT 中心用于“美国西部 2”区域，另一个 IoT 中心用于“美国东部”区域。 

>[!IMPORTANT]
>建议对本文中创建的所有资源使用相同的资源组。 这将便于在完成学习后更为轻松地清理创建的资源。

1. 在 Azure Cloud Shell 中，使用以下 [az group create](/cli/azure/group#az_group_create) 命令创建资源组：

    ```azurecli-interactive
    az group create --name contoso-us-resource-group --location eastus
    ```

2. 在 eastus 位置创建一个 IoT 中心，并将其添加到之前使用以下 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 命令创建的资源组（将 `{unique-hub-name}` 替换为自己的唯一名称）：

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    此命令可能需要花费几分钟时间完成。

3. 现在，在 westus2 位置创建一个 IoT 中心，并将其添加到之前使用以下 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 命令创建的资源组（将 `{unique-hub-name}` 替换为自己的唯一名称）：

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location westus2 --sku S1
    ```

    此命令可能需要花费几分钟时间完成。

## <a name="create-the-multitenant-enrollment"></a>创建多租户注册

在这一部分，你将为租户设备创建新的注册组。  

为简单起见，本文将在注册中使用[对称密钥证明](concepts-symmetric-key-attestation.md)。 对于更安全的解决方案，请考虑使用具有信任链的 [X.509 证书证明](concepts-x509-attestation.md)。

1. 在 Azure 门户中，选择你的设备预配服务。

2. 在“设置”菜单中，选择“管理注册” 。

3. 选择“+ 添加注册组”。

4. 在“添加注册组”页中，输入以下信息：

    组名称：输入 contoso-us-devices。

    **证明类型**：选择 *对称密钥*。

    自动生成密钥：此复选框应已处于选中状态。

    选择要如何将设备分配到中心：选择“最低延迟”。

5. 选择“链接新的 IoT 中心”

    :::image type="content" source="./media/how-to-provision-multitenant/create-multitenant-enrollment.png" alt-text="为对称密钥证明添加多租户注册组。":::

6. 在“添加指向 IoT 中心的链接”页中，输入以下信息。

    订阅：如果你有多个订阅，请选择创建区域 IoT 中心的订阅。

    IoT 中心：选择为 eastus 位置创建的 IoT 中心。

    访问策略：选择“iothubowner”。

    :::image type="content" source="./media/how-to-provision-multitenant/link-regional-hubs.png" alt-text="使用预配服务链接区域 IoT 中心。":::

7. 选择“保存”。

8. 针对你为 westgus 位置创建的第二个 IoT 中心，重复步骤 5 到步骤 7。

9. 在“选择此组可分配到的 IoT 中心”下拉菜单中选择你创建的两个 IoT 中心。

    :::image type="content" source="./media/how-to-provision-multitenant/enrollment-regional-hub-group.png" alt-text="选择链接的 IoT 中心。":::

10. 选择“保存”

11. 在注册组列表中选择“contoso-us-devices”。

12. 复制主密钥。 此密钥稍后将会在为这两个模拟设备生成唯一设备密钥时使用。

    :::image type="content" source="./media/how-to-provision-multitenant/copy-primary-key.png" alt-text="复制主密钥。":::

## <a name="create-regional-linux-vms"></a>创建区域 Linux VM

在这一部分，你将创建两个区域 Linux 虚拟机 (VM)。 这些 VM 将从每个区域运行设备模拟示例，以演示这两个区域中针对租户设备的设备预配。

为了更易清理资源，这些 VM 将被添加到包含创建的 IoT 中心的同一资源组 contoso-us-resource-group。 但是，VM 将在不同区域中运行（美国西部 2 和美国东部）。

1. 在 Azure Cloud Shell 中，在命令中更改以下参数后，运行该命令以创建美国东部区域 VM：

    --name：为美国东部区域设备 VM 输入一个唯一名称。

    --admin-username：使用你自己的管理员用户名称。

    --admin-password：使用你自己的管理员密码。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    此命令需要花费几分钟时间完成。 

2. 完成该命令后，请复制美国东部区域 VM 的 publicIpAddress 值。

3. 在 Azure Cloud Shell 中，在命令中更改以下参数后，运行该命令以创建美国西部 2 区域 VM：

    --name：为美国西部 2 区域设备 VM 输入一个唯一名称。 

    --admin-username：使用你自己的管理员用户名称。

    --admin-password：使用你自己的管理员密码。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest2 \
    --location westus2 \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    此命令需要花费几分钟时间完成。

4. 完成该命令后，请复制美国西部区域 2 VM 的 publicIpAddress 值。

5. 打开两个命令行 shell。

6. 使用 SSH 连接到每个 shell 中的其中一个区域 VM。

    将你作为参数复制的管理员用户名和公共 IP 地址传递给 SSH。 在出现提示时输入管理员密码。

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

在这一部分，你将克隆每个 VM 上的 Azure IoT C SDK。 SDK 包含将从每个区域模拟租户的设备预配的示例。

对于每个 VM：

1. ，使用以下命令安装 CMake  、g++  、gcc  和 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)：

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

2. 找到并复制[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK 的标记名称。

3. 在这两个 VM 上克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)。  使用在上一步找到的标记作为 `-b` 参数的值：

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

4. 在存储库内创建一个新的 cmake 文件夹，并更改为该文件夹。

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

5. 运行以下命令，生成特定于你的开发客户端平台的 SDK 版本：

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

6. 生成成功后，最后的几个输出行如下所示：

    ```bash
    -- IoT Client SDK Version = 1.7.0
    -- Provisioning SDK Version = 1.7.0
    -- Looking for include file stdint.h
    -- Looking for include file stdint.h - found
    -- Looking for include file stdbool.h
    -- Looking for include file stdbool.h - found
    -- target architecture: x86_64
    -- Performing Test CXX_FLAG_CXX11
    -- Performing Test CXX_FLAG_CXX11 - Success
    -- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so (found version "7.58.0")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/azure-iot-sdk-c
    ```

## <a name="derive-unique-device-keys"></a>派生唯一设备密钥

在组注册中使用对称密钥证明时，不会直接使用注册组密钥。 而是你自己从每个设备的注册组密钥派生唯一密钥。 有关详细信息，请参阅[采用对称密钥的组注册](concepts-symmetric-key-attestation.md#group-enrollments)。

在本教程的这一部分中，你将从您的组主密钥生成一个设备密钥，以计算设备唯一注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)。 然后，结果将转换为 Base64 格式。

>[!IMPORTANT]
>不要在设备代码中包含你的组主键。

对于 eastus 和 westus 2 设备： 

1. 使用 openssl 生成唯一密钥。 将使用以下 Bash shell 脚本（将 `{primary-key}` 替换为之前复制的注册组的主键，并将 `{contoso-simdevice-east}` 替换为您自己的每个设备的唯一注册 ID。 使用小写字母数字和短划线（“-”）字符定义这两个 ID）。

    ```bash
    KEY={primary-key}
    REG_ID={contoso-simdevice}
    
    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
    ```

2. 该脚本将输出类似于如下密钥：

    ```bash
    p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
    ```

3. 现在，每个租户设备都有自己的派生设备密钥和唯一的注册 ID，以便在预配过程中与注册组执行对称密钥证明。

## <a name="simulate-the-devices-from-each-region"></a>模拟来自每个区域的设备

在这一部分，你将为这两个区域 VM 更新 Azure IoT C SDK 中的预配示例。 

示例代码模拟将预配请求发送到你的设备预配服务实例的设备启动序列。 启动序列将会使设备被识别，并基于延迟被分配到最邻近的 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值 。

    :::image type="content" source="./media/how-to-provision-multitenant/copy-id-scope.png" alt-text="从门户边栏选项卡中提取设备预配服务终结点信息。":::

2. 在两个 VM 上，打开 ~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c 进行编辑。

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

3. 在两个 VM 上，找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. 在两个 VM 上，在同一文件中找到 `main()` 函数的定义。 请确保将 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`（如下所示），以匹配注册组证明方法。 

    将你对文件的更改保存在这两个 VM 上。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. 在两个 VM 上，在 **prov\_dev\_client\_sample.c** 中找到已注释掉的对 `prov_dev_set_symmetric_key_info()` 的调用。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消注释这些函数调用，并将占位符值（包括尖括号）替换为每个设备的唯一注册 ID 以及在上一节中派生的设备的派生设备密钥。 下面显示的密钥仅用作示例。 请使用你之前生成的密钥。

    美国东部：
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    美国西部：
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

6. 在这两个 VM 上，保存文件。

7. 在这两个 VM 上，导航到以下所示的示例文件夹，并生成示例。

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

8. 成功生成后，在这两个 VM 上运行 prov\_dev\_client\_sample.exe，以模拟来自每个区域的租户设备。 请注意，每个设备将被分配到最邻近模拟设备区域的租户 IoT 中心。

    运行模拟：
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    来自美国东部 VM 的示例输出：

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    来自美国西部 VM 的示例输出：
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用本文中创建的资源，则可以保留它们。 否则，请使用以下步骤删除本文创建的所有资源，以避免不必要的费用。

此处的步骤假定你按照名为 contoso-us-resource-group 的同一资源组的指示创建了本文中的所有资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要按名称删除资源组：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“资源组”。

3. 在“按名称筛选...”文本框中，键入包含资源的资源组名称“contoso-us-resource-group”。

4. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。

5. 系统会要求确认是否删除该资源组。 再次键入资源组的名称进行确认，然后选择“删除”  。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

* 要详细了解重新设置，请参阅：

> [!div class="nextstepaction"]
> [IoT 中心设备重新预配概念](concepts-device-reprovision.md)

* 要详细了解取消设置，请参阅
> [!div class="nextstepaction"]
> [如何取消预配以前自动预配的设备](how-to-unprovision-devices.md)