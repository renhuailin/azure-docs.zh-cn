---
title: 在 Windows 上使用 X.509 证书大规模创建和预配 IoT Edge 设备 - Azure IoT Edge | Microsoft Docs
description: 使用 X.509 证书通过设备预配服务大规模测试 Azure IoT Edge 的设备预配
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d0be2b1752460744d9dad8686f69b27a9350ffb3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699093"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-x509-certificates"></a>使用 X.509 证书在 Windows 上大规模创建和预配 IoT Edge 设备

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

本文提供有关使用 X.509 证书自动预配一个或多个 Windows IoT Edge 设备的端到端说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

任务如下：

1. 生成证书和密钥。
1. 为单个设备创建单个注册，或为一组设备创建组注册。
1. 安装 IoT Edge 运行时并将设备注册到 IoT 中心。

将 X.509 证书用作一种证明机制是扩大生产规模和简化设备设置的极佳途径。 X.509 证书通常排列在证书信任链中。 从自签名证书或受信任的根证书开始，证书链中的每个证书为下一级证书签名。 此模式创建了从设备上安装的根证书到每个中间证书，再到最终“叶”证书的委托信任链。

## <a name="prerequisites"></a>先决条件

* 活动的 IoT 中心。
* 一个充当 IoT Edge 设备的物理设备或虚拟 Windows 设备。
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

> [!NOTE]
> 目前存在一个 libiothsm 限制，会阻止使用在 2038 年 1 月 1 日或之后到期的证书。

### <a name="use-test-certificates-optional"></a>使用测试证书（可选）

如果你没有可用于创建新标识证书的证书颁发机构，但想要尝试此方案，可以使用 Azure IoT Edge Git 存储库中包含的脚本来生成测试证书。 这些证书仅用于开发测试，不得在生产环境中使用。

若要创建测试证书，请遵循[创建演示证书用于测试 IoT Edge 设备功能](how-to-create-test-certificates.md)中的步骤。 请完成其中的两个必要部分来设置证书生成脚本，并创建根 CA 证书。 然后，遵循相应的步骤创建设备标识证书。 完成后，应会获得以下证书链和密钥对：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

需要在 IoT Edge 设备上使用这两个证书。 若要在 DPS 中使用单独的注册，需要上传 .cert.pem 文件。 若要在 DPS 中使用组注册，则还需要上传同一证书信任链中的某个中间证书或根 CA 证书。 如果使用演示证书，请将 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 证书用于组注册。

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

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

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

组注册使用证书信任链中的某个中间证书或根 CA 证书，该证书用于生成单个设备标识证书。

#### <a name="verify-your-root-certificate"></a>验证根证书

创建注册组时，可以选择使用已验证的证书。 可以在 DPS 通过证明你拥有根证书来验证某个证书。 有关详细信息，请参阅[如何对 X.509 CA 证书执行所有权证明](../iot-dps/how-to-verify-certificates.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在左侧菜单中选择“证书”。 

1. 选择“添加”以添加新证书。 

1. 输入证书的易记名称，然后浏览到表示 X.509 证书公共部分的 .cer 或 .pem 文件。

   如果使用演示证书，请上传 `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem` 证书。

1. 选择“保存”  。

1. 该证书现在应会列在“证书”页上。  选择该证书打开其详细信息。

1. 选择“生成验证码”，然后复制生成的代码。 

1. 无论你是使用自己的 CA 证书还是演示证书，都可以使用 IoT Edge 存储库中提供的验证工具来执行所有权证明。 验证工具将使用你的 CA 证书，来为使用提供的验证码作为使用者名称的新证书签名。

   ```powershell
   New-CACertsVerificationCert "<verification code>"
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

你可以继续完成下一部分。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

在本部分，你将准备 IoT Edge 的 Windows 虚拟机或物理设备。 然后将安装 IoT Edge。

在准备好安装 IoT Edge 运行时之前，需要在设备上完成一个步骤。 需要在设备上安装容器引擎。

### <a name="install-a-container-engine"></a>安装容器引擎

Azure IoT Edge 依赖于与 OCI 兼容的容器运行时（例如 [Moby](https://github.com/moby/moby)），该运行时是安装脚本中包含的基于 Moby 的引擎。 无需额外步骤来安装引擎。

### <a name="install-iot-edge"></a>安装 IoT Edge

IoT Edge 安全守护程序提供和维护 IoT Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

本部分中的步骤表示在建立了 Internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本（如预发行版）或需要在脱机状态下安装，请按照脱机或特定版本安装步骤进行操作。

1. 以管理员身份运行 PowerShell。

   使用 PowerShell 的 AMD64 会话，不要使用 PowerShell(x86)。 如果不确定你使用的是哪种会话类型，请运行以下命令：

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 运行 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 命令，它将执行以下任务：

   * 检查 Windows 计算机使用的是否是受支持的版本。
   * 启用容器功能。
   * 下载 Moby 引擎和 IoT Edge 运行时。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. 如果出现提示，请重启设备。

在设备上安装 IoT Edge 时，可使用附加参数来修改过程，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向本地目录以进行脱机安装。

有关这些附加参数的详细信息，请参阅[用于带有 Windows 容器的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

在设备上安装运行时后，请借助它用于连接到设备预配服务和 IoT 中心的信息来配置设备。

准备好以下信息：

* DPS 的“ID 范围”值。  可以从 Azure 门户中 DPS 实例的概述页检索此值。
* 设备上的设备标识证书链文件。
* 设备上的设备标识密钥文件。

1. 在管理员模式下打开 PowerShell 窗口。 在安装 IoT Edge 而不是 PowerShell (x86) 时，请确保使用 PowerShell 的 AMD64 会话。

1. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配，因此使用 `-DpsX509` 标志借助 X.509 证书身份验证来使用自动预配。

   请将 `{scope_id}`、`{identity cert chain path}` 和 `{identity key path}` 的占位符值替换为 DPS 实例中的相应值，以及设备上的文件路径。

   如果你要将设备 ID 设置为除标识证书 CN 名称以外的其他值，请添加 `-RegistrationId {registration_id}` 参数。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >配置文件以文件 URI 的形式存储证书和密钥信息。 但是，Initialize-IoTEdge 命令将为你处理此格式设置步骤，因此，可以提供证书和密钥文件在设备上的绝对路径。

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。

# <a name="individual-enrollment"></a>[单独注册](#tab/individual-enrollment)

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

# <a name="group-enrollment"></a>[组注册](#tab/group-enrollment)

可以验证是否使用了你在设备预配服务中创建的组注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的组注册的注册详细信息。 转到“注册记录”选项卡，查看该组中注册的所有设备。

---

在设备上，使用以下命令验证是否已成功安装并启动 IoT Edge。

检查 IoT Edge 服务的状态。

```powershell
Get-Service iotedge
```

检查服务日志。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出正在运行的模块。

```powershell
iotedge list
```

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。
