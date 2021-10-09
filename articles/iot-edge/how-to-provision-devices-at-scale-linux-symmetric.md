---
title: 在 Linux 上使用对称密钥创建和预配 IoT Edge 设备 - Azure IoT Edge | Microsoft Docs
description: 使用对称密钥证明通过设备预配服务大规模测试 Azure IoT Edge 的 Linux 设备预配
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c2bae93e325a36b6db28d1a9c1e6e47ee947db39
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699372"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>使用对称密钥在 Linux 上大规模创建和预配 IoT Edge 设备

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

本文提供有关使用对称密钥自动预配一个或多个 Linux IoT Edge 设备的详尽说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

任务如下：

1. 为单个设备创建单个注册，或为一组设备创建组注册。
1. 安装 IoT Edge 运行时并连接到 IoT 中心。

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>为了简单起见，请尝试使用 [Azure IoT Edge 配置工具](https://github.com/azure/iot-edge-config)。 此命令行工具目前以公共预览版提供，可在设备上安装 IoT Edge，并使用 DPS 和对称密钥证明来预配它。
:::moniker-end

对称密钥证明是一种通过设备预配服务实例对设备进行身份验证的简单方法。 此证明方法表示不熟悉设备预配或不具备严格安全要求的开发人员的“Hello world”体验。 使用 [TPM](../iot-dps/concepts-tpm-attestation.md) 或 [X.509 证书](../iot-dps/concepts-x509-attestation.md)的设备证明更加安全，且应该用于更严格的安全要求。

## <a name="prerequisites"></a>先决条件

* 活动的 IoT 中心。
* 一个充当 IoT Edge 设备的物理设备或虚拟 Linux 设备。
  * 您需要定义一个唯一的注册 ID 来标识每个设备。 可以使用 MAC 地址、序列号或设备中的任何唯一信息。 例如，可以使用 MAC 地址和序列号的组合，构成以下注册 ID 字符串：`sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`。 有效字符为小写字母数字和短划线 (`-`)。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果没有设备预配服务实例，则可按照 IoT 中心设备预配服务快速入门的[创建新的 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)和[将 IoT 中心和设备预配服务相链接](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)部分的说明进行操作。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

创建注册以通过 DPS 预配一个或多个设备。

如果要预配单个 IoT Edge 设备，请创建单个注册。 如果需要预配多个设备，请按照创建 DPS 组注册的步骤进行操作。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

有关设备预配服务中的注册的详细信息，请参阅[如何管理设备注册](../iot-dps/how-to-manage-enrollments.md)。

# <a name="individual-enrollment"></a>[单独注册](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>创建 DPS 单独注册

> [!TIP]
> 本文中的步骤适用于 Azure 门户，但你也可使用 Azure CLI 创建单个注册。 有关详细信息，请参阅 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)。 作为 CLI 命令的一部分，使用 edge-enabled 标志指定注册适用于单个 IoT Edge设备。

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

   1. 选择“保存” 。

1. 复制个人注册的“主密钥”值，以便在安装 IoT Edge 运行时的情况下使用。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。

# <a name="group-enrollment"></a>[组注册](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>创建 DPS 组注册

> [!TIP]
> 本文中的步骤适用于 Azure 门户，但你也可使用 Azure CLI 创建组注册。 有关详细信息，请参阅 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)。 作为 CLI 命令的一部分，使用 edge-enabled 标志指定注册适用于一组 IoT Edge设备。 对于组注册，所有设备必须是 IoT Edge 设备，或者都不是 IoT Edge 设备。

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

   1. 选择“保存” 。

1. 复制注册组的“主密钥”值，以便在创建用于组注册的设备密钥时使用。

现在注册组已经存在，IoT Edge 运行时在安装期间可以自动预配设备。

#### <a name="derive-a-device-key"></a>派生一个设备密钥

在组注册过程中预配的每个设备都需要一个派生的设备密钥，用于在预配期间向注册执行对称密钥证明。

若要生成设备密钥，请使用从 DPS 注册组复制的密钥来计算设备的唯一注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)，并将结果转换为 Base64 格式。

> [!IMPORTANT]
> 不要在设备代码中包含注册的主密钥或辅助密钥。

在 Linux 上，可以使用 openssl 生成派生的设备密钥，如以下示例中所示。

将“键”  值替换为前面记录的“主键”  。

请将 **REG_ID** 值替换为设备的注册 ID。

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

下面是派生设备密钥的输出示例：

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

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

本部分中的步骤表示在具有 Internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本（如预发行版）或需要在脱机状态下安装，请按照[脱机或特定版本安装步骤](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)进行操作。

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

本部分中的步骤表示在具有 Internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本（如预发行版）或需要在脱机状态下安装，请按照[脱机或特定版本安装步骤](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)进行操作。

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

* DPS 的“ID 范围”值 
* 为设备创建的“注册 ID” 
* 个人注册的[主密钥](#derive-a-device-key)，或者使用组注册时设备的派生密钥。

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

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。

# <a name="individual-enrollment"></a>[单独注册](#tab/individual-enrollment)

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

# <a name="group-enrollment"></a>[组注册](#tab/group-enrollment)

可以验证是否使用了你在设备预配服务中创建的组注册。 在 Azure 门户中导航到设备预配服务实例。 打开你创建的组注册的注册详细信息。 转到“注册记录”选项卡，查看该组中注册的所有设备。

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
