---
title: 使用对称密钥证明预配设备 - Azure IoT Edge
description: 使用对称密钥证明通过设备预配服务测试 Azure IoT Edge 的自动设备预配
author: kgremban
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/21/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d4aa7f1a02d8ab789810f06b38c95e9cfd76d5fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742253"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>使用对称密钥证明创建和预配 IoT Edge 设备

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备，就像预配未启用 Edge 的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配](../iot-dps/about-iot-dps.md#provisioning-process)概述。

本文介绍如何通过以下步骤，在 IoT Edge 设备上使用对称密钥证明创建设备预配服务的个人或组注册：

* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 创建个人注册或组注册。
* 安装 IoT Edge 运行时并连接到 IoT 中心。

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>为了简单起见，请尝试使用 [Azure IoT Edge 配置工具](https://github.com/azure/iot-edge-config)。 此命令行工具目前提供公共预览版，请在设备上安装 IoT Edge，并使用 DPS 和对称密钥证明来预配它。
:::moniker-end

对称密钥证明是一种通过设备预配服务实例对设备进行身份验证的简单方法。 此证明方法表示不熟悉设备预配或不具备严格安全要求的开发人员的“Hello world”体验。 使用 [TPM](../iot-dps/concepts-tpm-attestation.md) 或 [X.509 证书](../iot-dps/concepts-x509-attestation.md)的设备证明更加安全，且应该用于更严格的安全要求。

## <a name="prerequisites"></a>先决条件

* 一个有效的 IoT 中心
* 一个物理设备或虚拟设备

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

## <a name="choose-a-unique-device-registration-id"></a>选择唯一设备注册 ID

必须定义唯一注册 ID 来标识每个设备。 可以使用 MAC 地址、序列号或设备中的任何唯一信息。 例如，可以使用 MAC 地址和序列号的组合，构成以下注册 ID 字符串：`sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`。 有效字符为小写字母数字和短划线 (`-`)。

## <a name="option-1-create-a-dps-individual-enrollment"></a>选项 1：创建 DPS 个人注册

创建个人注册，通过 DPS 预配单个设备。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

> [!TIP]
> 本文中的步骤适用于Azure 门户，但你也可使用 Azure CLI 创建个人注册。 有关详细信息，请参阅 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)。 作为 CLI 命令的一部分，使用 edge-enabled 标志指定注册适用于单个 IoT Edge设备。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在“设置”下，选择“管理注册”。  

1. 选择“添加个人注册”，然后完成以下步骤以配置注册：   

   1. 对于“机制”，请选择“对称密钥”。  

   1. 为设备提供一个唯一的注册 ID。

   1. （可选）为设备提供一个 IoT 中心设备 ID。 可以使用设备 ID 将单个设备指定为模块部署的目标。 如果未提供设备 ID，则会使用注册 ID。

   1. 选择“True”，声明该注册适用于 IoT Edge 设备。 

   1. （可选）向“初始设备孪生状态”添加一个标记值。 可以使用标记将设备组指定为模块部署的目标。 例如：

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

   1. 选择“保存”  。

1. 复制个人注册的“主密钥”值，以便在安装 IoT Edge 运行时的情况下使用。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。

## <a name="option-2-create-a-dps-enrollment-group"></a>选项 2：创建 DPS 注册组

使用设备的注册 ID 在 DPS 中创建单个注册。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

> [!TIP]
> 本文中的步骤适用于Azure 门户，但你也可使用 Azure CLI 创建个人注册。 有关详细信息，请参阅 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)。 作为 CLI 命令的一部分，使用 edge-enabled 标志指定注册适用于一组 IoT Edge设备。 对于组注册，所有设备必须是 IoT Edge 设备，或者都不是 IoT Edge 设备。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在“设置”下，选择“管理注册”。  

1. 选择“添加个人注册”，然后完成以下步骤以配置注册：   

   1. 提供组名称。

   1. 选择“对称密钥”作为证明类型。

   1. 选择“True”，声明该注册适用于 IoT Edge 设备。  对于组注册，所有设备必须是 IoT Edge 设备，或者都不是 IoT Edge 设备。

   1. （可选）向“初始设备孪生状态”添加一个标记值。 可以使用标记将设备组指定为模块部署的目标。 例如：

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

   1. 选择“保存”  。

1. 复制注册组的“主密钥”值，以便在创建用于组注册的设备密钥时使用。

现在注册组已经存在，IoT Edge 运行时在安装期间可以自动预配设备。

### <a name="derive-a-device-key"></a>派生一个设备密钥

在组注册过程中预配的每个设备都需要一个派生的设备密钥，用于在预配期间向注册执行对称密钥证明。

若要生成设备密钥，请使用从 DPS 注册组复制的密钥来计算设备的唯一注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)，并将结果转换为 Base64 格式。

不要在设备代码中包含注册的主密钥或辅助密钥。

#### <a name="derive-a-key-on-linux"></a>在 Linux 上派生密钥

在 Linux 上，可以使用 openssl 生成派生的设备密钥，如以下示例中所示。

将“键”  值替换为前面记录的“主键”  。

请将 **REG_ID** 值替换为设备的注册 ID。

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

#### <a name="derive-a-key-on-windows"></a>在 Windows 上派生密钥

在 Windows 上，可以使用 PowerShell 生成派生的设备密钥，如以下示例中所示。

将“键”  值替换为前面记录的“主键”  。

请将 **REG_ID** 值替换为设备的注册 ID。

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

根据你的操作系统，按照相应步骤安装 Azure IoT Edge：

* [安装 IoT Edge for Linux](how-to-install-iot-edge.md)
* [安装 IoT Edge for Linux on Windows 设备](how-to-install-iot-edge-on-windows.md)
  * 此方案是运行 IoT Edge on Windows 设备的建议方法。
* [使用 Windows 容器安装 IoT Edge](how-to-install-iot-edge-windows-on-windows.md)

在设备上安装 IoT Edge 后，请返回到本文来预配此设备。

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

按照[安装 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的步骤操作，然后返回到本文来预配设备。

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

在设备上安装运行时后，请借助它用于连接到设备预配服务和 IoT 中心的信息来配置设备。

准备好以下信息：

* DPS 的“ID 范围”值 
* 为设备创建的“注册 ID” 
* 个人注册的[主密钥](#derive-a-device-key)，或者使用组注册时设备的派生密钥。

# <a name="linux"></a>[Linux](#tab/linux)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 在 IoT Edge 设备上打开配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 找到该文件的预配配置部分。 取消注释 DPS 对称密钥预配的行，并确保注释掉任何其他预配行。

   `provisioning:` 行前面应无空格，并且嵌套项应该缩进两个空格。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. 将 `scope_id`、`registration_id` 和 `symmetric_key` 的值更新为你的 DPS 和设备信息。

1. （可选）使用 `always_reprovision_on_startup` 或 `dynamic_reprovisioning` 行来配置设备的重新预配行为。 如果设备设置为在启动时重新预配，它将始终尝试先使用 DPS 进行预配，如果失败，则回退到预配备份。 如果设备设置为动态重新预配自身，则 IoT Edge 将重启，并在检测到重新预配事件时重新预配。 有关详细信息，请参阅 [IoT 中心设备重新预配概念](../iot-dps/concepts-device-reprovision.md)。

1. 重启 IoT Edge 运行时，使之拾取你在设备上所做的所有配置更改。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

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

1. 找到文件的 **Provisioning** 节。 取消注释 DPS 对称密钥预配的行，并确保注释掉任何其他预配行。

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. 将 `id_scope`、`registration_id` 和 `symmetric_key` 的值更新为你的 DPS 和设备信息。

   对称密钥参数可以接受内联密钥、文件 URI 或 PKCS#11 URI 的值。 根据所使用的格式，取消注释一条对称密钥行。

   如果使用任何 PKCS#11 URI，请在配置文件中找到 PKCS#11 节，并提供有关 PKCS#11 配置的信息。

1. 保存并关闭 config.toml 文件。

1. 应用对 IoT Edge 所做的配置更改。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

# <a name="linux-on-windows"></a>[Linux on Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

可以使用 PowerShell 或 Windows Admin Center 来预配 IoT Edge 设备。

### <a name="powershell"></a>PowerShell

对于 PowerShell，请运行以下命令，其中将占位符值更新为你自己的值：

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

### <a name="windows-admin-center"></a>Windows Admin Center

对于 Windows Admin Center，请使用以下步骤：

1. 在“Azure IoT Edge 设备预配”窗格上，从“预配方法”下拉列表中选择“对称密钥(DPS)”。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在 Windows Admin Center 字段中提供 DPS 范围 ID、设备注册 ID 和注册主密钥或派生密钥。

1. 选择“使用所选方法进行预配”。

   ![填写对称密钥预配的必填字段后，选择“使用所选方法进行预配”。](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. 预配完成后，选择“完成”。 将返回到主仪表板。 现在，你应该会看到一个列出的新设备，其类型为“`IoT Edge Devices`”。 你可以选择该 IoT Edge 设备以连接到它。 在“概述”页上以后，即可查看设备的“IoT Edge 模块列表”和“IoT Edge 状态”。

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>目前，不支持在 IoT Edge for Linux on Windows 上运行的 IoT Edge 版本 1.2。

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

1. 在管理员模式下打开 PowerShell 窗口。 在安装 IoT Edge 而不是 PowerShell (x86) 时，请确保使用 PowerShell 的 AMD64 会话。

1. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配，因此使用 `-DpsSymmetricKey` 标志借助对称密钥身份验证来使用自动预配。

   请将 `{scope_id}`、`{registration_id}` 和 `{symmetric_key}` 的占位符值替换为前面收集的数据。

   如果是在 Windows 上使用 Linux 容器，则添加 `-ContainerOs Linux` 参数。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>目前，不支持在 Windows 上运行的 IoT Edge 版本 1.2。

:::moniker-end
<!-- end 1.2 -->

---

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

在设备上，使用以下命令验证是否已成功安装并启动 IoT Edge。

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="linux-on-windows"></a>[Linux on Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

连接到 IoT Edge for Linux on Windows 虚拟机。

```powershell
Connect-EflowVM
```

检查 IoT Edge 服务的状态。

```cmd/sh
sudo systemctl status iotedge
```

检查服务日志。

```cmd/sh
sudo journalctl -u iotedge --no-pager --no-full
```

列出正在运行的模块。

```cmd/sh
sudo iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>目前，不支持在 IoT Edge for Linux on Windows 上运行的 IoT Edge 版本 1.2。

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

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

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>目前，不支持在 Windows 上运行的 IoT Edge 版本 1.2。

:::moniker-end
<!-- end 1.2 -->

---

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。
