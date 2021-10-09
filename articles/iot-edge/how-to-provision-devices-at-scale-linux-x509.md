---
title: 在 Linux 上使用 X.509 证书大规模创建和预配 IoT Edge 设备 - Azure IoT Edge | Microsoft Docs
description: 使用 X.509 证书通过设备预配服务大规模测试 Azure IoT Edge 的设备预配
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2dc45c3b088dc25a39496280b1e612a27c9dd69d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699337"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>使用 X.509 证书在 Linux 上大规模创建和预配 IoT Edge 设备

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

本文提供有关使用 X.509 证书自动预配一个或多个 Linux IoT Edge 设备的端到端说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

任务如下：

1. 生成证书和密钥。
1. 为单个设备创建单个注册，或为一组设备创建组注册。
1. 安装 IoT Edge 运行时并将设备注册到 IoT 中心。

将 X.509 证书用作一种证明机制是扩大生产规模和简化设备设置的极佳途径。 X.509 证书通常排列在证书信任链中。 从自签名证书或受信任的根证书开始，证书链中的每个证书为下一级证书签名。 此模式创建了从设备上安装的根证书到每个中间证书，再到最终“叶”证书的委托信任链。

## <a name="prerequisites"></a>先决条件

* 活动的 IoT 中心。
* 一个充当 IoT Edge 设备的物理或虚拟 Linux 设备。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果没有设备预配服务实例，则可按照 IoT 中心设备预配服务快速入门的[创建新的 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)和[将 IoT 中心和设备预配服务相链接](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)部分的说明进行操作。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

## <a name="generate-device-identity-certificates"></a>生成设备标识证书

设备标识证书是通过证书信任链连接到顶级 X.509 证书颁发机构 (CA) 证书的叶证书。 设备标识证书的公用名 (CN) 必须设置为你希望该设备在 IoT 中心内使用的设备 ID。

设备标识证书仅用于预配 IoT Edge 设备，以及通过 Azure IoT 中心对设备进行身份验证。 设备标识证书不是签名证书，这与 CA 证书不同，后者是 IoT Edge 设备向模块或叶设备提供的用于验证的证书。 有关详细信息，请参阅 [Azure IoT Edge 证书用法详细信息](iot-edge-certs.md)。

创建设备标识证书后，应会获得两个文件：一个包含证书公共部分的 .cer 或 .pem 文件，一个包含证书私钥的 .cer 或 .pem 文件。 如果你打算在 DPS 中使用组注册，则还需要提供同一证书信任链中某个中间证书或根 CA 证书的公共部分。

若要使用 X.509 设置自动预配，需要以下文件：

* 设备标识证书及其私钥证书。 如果创建单个注册，则设备标识证书会上传到 DPS。 私钥会传递到 IoT Edge 运行时。
* 完整链证书，其中应至少包含设备标识和中间证书。 完整链证书会传递到 IoT Edge 运行时。
* 证书信任链中的中间或根 CA 证书。 如果创建组注册，则此证书会上传到 DPS。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> 目前存在一个 libiothsm 限制，会阻止使用在 2038 年 1 月 1 日或之后到期的证书。

:::moniker-end
<!-- end 1.1 -->

### <a name="use-test-certificates-optional"></a>使用测试证书（可选）

如果你没有可用于创建新标识证书的证书颁发机构，但想要尝试此方案，可以使用 Azure IoT Edge Git 存储库中包含的脚本来生成测试证书。 这些证书仅用于开发测试，不得在生产环境中使用。

若要创建测试证书，请遵循[创建演示证书用于测试 IoT Edge 设备功能](how-to-create-test-certificates.md)中的步骤。 请完成其中的两个必要部分来设置证书生成脚本，并创建根 CA 证书。 然后，遵循相应的步骤创建设备标识证书。 完成后，应会获得以下证书链和密钥对：

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

需要在 IoT Edge 设备上使用这两个证书。 若要在 DPS 中使用单独的注册，需要上传 .cert.pem 文件。 若要在 DPS 中使用组注册，则还需要上传同一证书信任链中的某个中间证书或根 CA 证书。 如果使用演示证书，请将 `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem` 证书用于组注册。

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

使用生成的证书和密钥在 DPS 中为一个或多个 IoT Edge 设备创建注册。

如果要预配单个 IoT Edge 设备，请创建单个注册。 如果需要预配多个设备，请按照创建 DPS 组注册的步骤进行操作。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

有关设备预配服务中的注册的详细信息，请参阅[如何管理设备注册](../iot-dps/how-to-manage-enrollments.md)。

# <a name="individual-enrollment"></a>[单独注册](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>创建 DPS 单独注册

单独注册采用设备标识证书的公共部分，并将其与设备上的证书进行匹配。

> [!TIP]
> 本文中的步骤适用于 Azure 门户，但你也可使用 Azure CLI 创建单个注册。 有关详细信息，请参阅 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)。 作为 CLI 命令的一部分，使用 edge-enabled 标志指定注册适用于单个 IoT Edge设备。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在“设置”下，选择“管理注册”。  

1. 选择“添加个人注册”，然后完成以下步骤以配置注册：   

   * **机制**：选择“X.509”。 

   * **主要证书 .pem 或 .cer 文件**：上传设备标识证书中的公共文件。 如果使用脚本生成了测试证书，请选择以下文件：

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT 中心设备 ID**：根据需要，为设备提供一个 ID。 可以使用设备 ID 将单个设备指定为模块部署的目标。 如果未提供设备 ID，将使用 X.509 证书中的公用名 (CN)。

   * **IoT Edge 设备**：选择“True”，声明该注册适用于 IoT Edge 设备。 

   * **选择此设备可分配到的 IoT 中心**：选择要将设备连接到的链接 IoT 中心。 可以选择多个中心，设备将根据所选的分配策略分配到其中的一个中心。

   * **初始设备孪生状态**：如果需要，请添加要添加到设备孪生的标记值。 可以使用标记将设备组指定为自动部署的目标。 例如：

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. 选择“保存” 。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。

# <a name="group-enrollment"></a>[组注册](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>创建 DPS 组注册

组注册使用证书信任链中的某个中间证书或根 CA 证书，该证书用于验证单个设备标识证书。

#### <a name="verify-your-root-certificate"></a>验证根证书

创建注册组时，可以选择使用已验证的证书。 可以在 DPS 通过证明你拥有根证书来验证某个证书。 有关详细信息，请参阅[如何对 X.509 CA 证书执行所有权证明](../iot-dps/how-to-verify-certificates.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在左侧菜单中选择“证书”。 

1. 选择“添加”以添加新证书。 

1. 输入证书的易记名称，然后浏览到表示 X.509 证书公共部分的 .cer 或 .pem 文件。

   如果使用演示证书，请上传 `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 证书。

1. 选择“保存”  。

1. 该证书现在应会列在“证书”页上。  选择该证书打开其详细信息。

1. 选择“生成验证码”，然后复制生成的代码。 

1. 无论你是使用自己的 CA 证书还是演示证书，都可以使用 IoT Edge 存储库中提供的验证工具来执行所有权证明。 验证工具将使用你的 CA 证书，来为使用提供的验证码作为使用者名称的新证书签名。

   ```bash
   ./certGen.sh create_verification_certificate <verification code>
   ```

1. 在 Azure 门户上的相同证书详细信息页中，上传新生成的验证证书。

1. 选择“验证”  。

#### <a name="create-enrollment-group"></a>创建注册组

有关设备预配服务中的注册的详细信息，请参阅[如何管理设备注册](../iot-dps/how-to-manage-enrollments.md)。

> [!TIP]
> 本文中的步骤适用于 Azure 门户，但你也可使用 Azure CLI 创建组注册。 有关详细信息，请参阅 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)。 作为 CLI 命令的一部分，使用 edge-enabled 标志指定注册适用于一组 IoT Edge设备。 对于组注册，所有设备必须是 IoT Edge 设备，或者都不是 IoT Edge 设备。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在“设置”下，选择“管理注册”。  

1. 选择“添加注册组”，然后完成以下步骤以配置注册： 

   * **组名称**：为此组注册提供一个易记的名称。

   * **证明类型**：选择“证书”。 

   * **IoT Edge 设备**：选择“True”。  对于组注册，所有设备必须是 IoT Edge 设备，或者都不是 IoT Edge 设备。

   * 证书类型：选择“CA 证书”。

   * 主证书：从下拉列表中选择证书。

   * **选择此设备可分配到的 IoT 中心**：选择要将设备连接到的链接 IoT 中心。 可以选择多个中心，设备将根据所选的分配策略分配到其中的一个中心。

   * **初始设备孪生状态**：如果需要，请添加要添加到设备孪生的标记值。 可以使用标记将设备组指定为自动部署的目标。 例如：

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. 选择“保存” 。

这些设备存在注册后，IoT Edge 运行时就可以在安装期间自动预配设备。

---

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

在本部分，你将准备 IoT Edge 的 Linux 虚拟机或物理设备。 然后将安装 IoT Edge。

在准备好安装 IoT Edge 运行时之前，需要在设备上完成两个步骤。 设备需要访问 Microsoft 安装包，并且需要安装容器引擎。

### <a name="access-the-microsoft-installation-packages"></a>访问 Microsoft 安装包

设备必须有权访问 Microsoft 安装包。

1. 安装与设备操作系统匹配的存储库配置。

   * Ubuntu Sever 18.04：

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**：

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. 将生成的列表复制到 sources.list.d 目录。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. 安装 Microsoft GPG 公钥。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Azure IoT Edge 软件包受制于每个包（`usr/share/doc/{package-name}` 或 `LICENSE` 目录）中的许可条款。 请在使用包之前阅读许可条款。 安装和使用包即表示你接受这些条款。 如果不同意许可条款，请勿使用该包。

### <a name="install-a-container-engine"></a>安装容器引擎

Azure IoT Edge 依赖于 OCI 兼容的容器运行时。 对于生产方案，建议使用 Moby 引擎。 Moby 引擎是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时兼容。

1. 更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

1. 安装 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > 如果在安装 Moby 容器引擎时出现错误，请验证 Linux 内核的 Moby 兼容性。 有些嵌入式设备制造商寄送的设备映像包含的自定义 Linux 内核没有确保容器引擎兼容所需的功能。 运行以下命令，该命令使用 Moby 提供的 [check-config 脚本](https://github.com/moby/moby/blob/master/contrib/check-config.sh)来检查内核配置。
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > 在该脚本的输出中，检查 `Generally Necessary` 和 `Network Drivers` 下的所有项是否都已启用。 如果缺少某些功能，请启用它们，方法是通过源重新构建内核，然后选择关联的模块，将其包括在相应的内核 .config 中。同样，如果使用内核配置生成器（例如 `defconfig` 或 `menuconfig`），请找到并启用相应的功能，然后以相应方式重新构建内核。 部署新修改的内核以后，请再次运行 check-config 脚本，验证是否已成功启用所有必需功能。

### <a name="install-iot-edge"></a>安装 IoT Edge

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 安全守护程序提供和维护 IoT Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

本部分中的步骤表示在建立了 Internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本（如预发行版）或需要在脱机状态下安装，请按照脱机或特定版本安装步骤进行操作。

1. 更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

1. 安装 IoT Edge 版本 1.1* 以及 libiothsm-std 包。

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge 1.1 版本是 IoT Edge 的长期支持分支。 如果运行的是旧版本，建议安装或更新到最新补丁，因为旧版本不再受支持。

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 服务在 IoT Edge 设备上提供并维护安全标准。 该服务在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

IoT 标识服务在 IoT Edge 1.2 版中引入。 此服务处理 IoT Edge 以及需要与 IoT 中心通信的其他设备组件的标识预配和管理。

本部分中的步骤表示在具有 Internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本（如预发行版）或需要在脱机状态下安装，请按照脱机或特定版本安装步骤进行操作。

更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

查看可用的 IoT Edge 和 IoT 标识服务版本。

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

要安装最新版本的 IoT Edge 和 IoT 标识服务包，请使用以下命令：

   ```bash
   sudo apt-get install aziot-edge
   ```

如果选择安装与最新版本不同的 IoT Edge 版本，请确保为 `aziot-edge` 和 `aziot-identity-service` 服务安装相同版本。

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

在设备上安装运行时后，请借助它用于连接到设备预配服务和 IoT 中心的信息来配置设备。

准备好以下信息：

* DPS 的“ID 范围”值。  可以从 Azure 门户中 DPS 实例的概述页检索此值。
* 设备上的设备标识证书链文件。
* 设备上的设备标识密钥文件。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 在 IoT Edge 设备上打开配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 找到该文件的预配配置部分。 取消注释 DPS X.509 证书预配的行，并确保注释掉任何其他预配行。

   `provisioning:` 行前面应无空格，并且嵌套项应该缩进两个空格。

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. 将 `scope_id`、`identity_cert` 和 `identity_pk` 的值更新为你的 DPS 和设备信息。

   将 X.509 证书和密钥信息添加到 config.yaml 文件时，应以文件 URI 的形式提供路径。 例如：

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. （可选）为设备提供 `registration_id`，它需要与标识证书的公用名称 (CN) 匹配。 如果将该行注释掉，将会自动应用 CN。

1. （可选）使用 `always_reprovision_on_startup` 或 `dynamic_reprovisioning` 行来配置设备的重新预配行为。 如果设备设置为在启动时重新预配，它将始终尝试先使用 DPS 进行预配，如果失败，则回退到预配备份。 如果设备设置为动态重新预配自身，则 IoT Edge 将重启，并在检测到重新预配事件时重新预配。 有关详细信息，请参阅 [IoT 中心设备重新预配概念](../iot-dps/concepts-device-reprovision.md)。

1. 保存并关闭 config.yaml 文件。

1. 重启 IoT Edge 运行时，使之拾取你在设备上所做的所有配置更改。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 基于在安装 IoT Edge 的过程中提供的模板文件为你的设备创建配置文件。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. 在 IoT Edge 设备上打开配置文件。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. 找到文件的 **Provisioning** 节。 取消注释使用 X.509 证书进行 DPS 预配的行，并确保注释掉任何其他预配行。

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "<REGISTRATION ID>"

   identity_cert = "<DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<DEVICE IDENTITY PRIVATE KEY>"
   ```

1. 将 `id_scope` 的值更新为你从 DPS 实例复制的范围 ID。

1. 为设备提供 `registration_id`，它是设备在 IoT 中心具有的 ID。 注册 ID 必须与标识证书的公用名称 (CN) 匹配。

1. 将 `identity_cert` 和 `identity_pk` 的值更新为你的证书和密钥信息。

   标识证书值可以作为文件 URI 提供，也可以通过 EST 或本地证书颁发机构进行动态颁发。 根据你选择使用的格式，仅取消注释一行。

   标识私钥值可以作为文件 URI 或 PKCS#11 URI 提供。 根据你选择使用的格式，仅取消注释一行。

   如果使用任何 PKCS#11 URI，请在配置文件中找到 PKCS#11 节，并提供有关 PKCS#11 配置的信息。

1. 保存并关闭该文件。

1. 应用对 IoT Edge 所做的配置更改。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。

# <a name="individual-enrollment"></a>[单独注册](#tab/individual-enrollment)

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

# <a name="group-enrollment"></a>[组注册](#tab/group-enrollment)

可以验证是否使用了你在设备预配服务中创建的组注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的组注册的注册详细信息。 转到“注册记录”选项卡，查看该组中注册的所有设备。

---

在设备上，使用以下命令验证是否已成功安装并启动 IoT Edge。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

检查 IoT Edge 服务的状态。

```cmd/sh
systemctl status iotedge
```

检查服务日志。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

列出正在运行的模块。

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

检查 IoT Edge 服务的状态。

```cmd/sh
sudo iotedge system status
```

检查服务日志。

```cmd/sh
sudo iotedge system logs
```

列出正在运行的模块。

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。
