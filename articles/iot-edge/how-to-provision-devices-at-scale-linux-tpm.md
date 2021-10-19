---
title: 在 Linux 上使用虚拟 TPM 预配设备 - Azure IoT Edge
description: 在 Linux 设备上使用模拟 TPM 来测试 Azure IoT Edge 的 Azure 设备预配服务
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 81c448b39dcbed56d0208f3e8931eacabd5331f0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661557"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>在 Linux 上使用 TPM 大规模创建和预配 IoT Edge 设备

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

本文提供有关使用受信任的平台模块 (TPM) 自动预配 Linux IoT Edge 设备的端到端说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

本文概述了两种方法。 根据解决方案的体系结构选择你的首选方法：

1. 使用物理 TPM 硬件自动预配 Linux 设备。 例如，[Infineon OPTIGA&trade; TPM SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e)。
1. 使用模拟 TPM（在启用了 Hyper-V 的 Windows 开发计算机上运行）自动预配 Linux 虚拟机 (VM)。 建议仅将此方法作为测试方案，因为模拟 TPM 无法提供与物理 TPM 相同的安全性。

具体说明因你的方法而异，因此请确保接下来按照正确选项卡上的说明操作。

# <a name="physical-device"></a>[物理设备](#tab/physical-device)

任务如下：

1. 检索 TPM 的预配信息。
1. 在 IoT 中心设备预配服务的实例中为设备创建单个注册。
1. 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

# <a name="virtual-machine"></a>[虚拟机](#tab/virtual-machine)

任务如下：

1. 使用模拟 TPM 在 Hyper-V 中创建 Linux 虚拟机，以确保硬件安全性。
1. 检索 TPM 的预配信息。
1. 在 IoT 中心设备预配服务的实例中为设备创建单个注册。
1. 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

---

## <a name="prerequisites"></a>先决条件

# <a name="physical-device"></a>[物理设备](#tab/physical-device)

* 一个活动的 IoT 中心。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果没有设备预配服务实例，则可按照 IoT 中心设备预配服务快速入门的[创建新的 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)和[将 IoT 中心和设备预配服务相链接](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)部分的说明进行操作。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

# <a name="virtual-machine"></a>[虚拟机](#tab/virtual-machine)

* 活动的 IoT 中心。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果没有设备预配服务实例，则可按照 IoT 中心设备预配服务快速入门的[创建新的 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)和[将 IoT 中心和设备预配服务相链接](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)部分的说明进行操作。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。
* [已启用 Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 开发计算机。 本文使用运行 Ubuntu Server VM 的 Windows 10。

---

> [!NOTE]
> 将 TPM 证明与 DPS 结合使用时，需要 TPM 2.0。
>
> 使用 TPM 时，只能创建单个 DPS 注册，不能创建组 DPS 注册。

## <a name="set-up-your-device"></a>设置设备

# <a name="physical-device"></a>[物理设备](#tab/physical-device)

如果将物理 Linux 设备与 TPM 结合使用，则无需执行额外的步骤来设置设备。

你已准备就绪，可以继续操作。

# <a name="virtual-machine"></a>[虚拟机](#tab/virtual-machine)

在本部分，我们将在 Hyper-V 上创建新的 Linux 虚拟机。 为此虚拟机配置模拟 TPM，以便测试自动预配与 IoT Edge 配合使用的效果。

> [!TIP]
> 如果使用虚拟机，则会在本文中多次在虚拟机上进行复制和粘贴。 通过 Hyper-V 管理器连接应用程序进行复制和粘贴并不容易。 可能需要通过 Hyper-V 管理器连接到虚拟机一次以检索其 IP 地址。 先运行 `sudo apt install net-tools`，然后运行 `hostname -I`。 然后，可以使用该 IP 地址通过 SSH 进行连接：`ssh <username>@<ipaddress>`。

### <a name="create-a-virtual-switch"></a>创建虚拟交换机

使用虚拟交换机可将虚拟机连接到物理网络。

1. 在 Windows 计算机上打开 Hyper-V 管理器。

1. 在“操作”菜单中，选择“虚拟交换机管理器”。 

1. 选择一个“外部”虚拟交换机，然后选择“创建虚拟交换机”。 

1. 为新的虚拟交换机命名。 例如 EdgeSwitch。 确保将连接类型设置为“外部网络”，然后选择“确定”。 

1. 此时会弹出一条警告，指出网络连接可能会中断。 选择“是”继续。

> [!TIP]
> 如果创建新虚拟交换机时出现错误，请确保没有其他任何交换机正在使用以太网适配器，并且没有其他任何交换机使用相同的名称。

### <a name="create-the-virtual-machine"></a>创建虚拟机

通过可启动映像文件创建新的 VM。

1. 下载虚拟机使用的磁盘映像文件，并将其保存在本地。 例如，[Ubuntu 服务器 18.04](http://releases.ubuntu.com/18.04/)。 若要了解 IoT Edge 设备支持的操作系统，请参阅 [Azure IoT Edge 支持的系统](/support.md)。

1. 在 Hyper-V 管理器的“操作”菜单中，选择“操作” > “新建” > “虚拟机” 。

1. 使用以下特定配置完成“新建虚拟机向导”：

   1. **指定代系**：选择“第 2 代”。 第 2 代虚拟机已启用嵌套虚拟化，在虚拟机上运行 IoT Edge 必须启用此功能。
   1. **配置网络**：设置“连接”的值设置为在上一部分创建的虚拟交换机。
   1. **安装选项**：选择“从可启动映像文件安装操作系统”，并浏览到本地保存的磁盘映像文件。

1. 在向导中选择“完成”以创建虚拟机。

创建新的 VM 可能需要几分钟。

### <a name="enable-virtual-tpm"></a>启用虚拟 TPM

创建 VM 后，打开其设置以启用允许你自动预配设备的虚拟 TPM。

1. 在 Hyper-v 管理器中，右键单击该 VM 并选择“设置”。

1. 导航到“安全性”。

1. 取消选中“启用安全启动”。

1. 选中“启用受信任的平台模块”。

1. 单击 **“确定”** 。

### <a name="start-the-virtual-machine"></a>启动虚拟机

启动虚拟机以完成安装过程。

1. 在 Hyper-v 管理器中，启动你的 VM 并连接到它。

1. 遵照虚拟机中的提示完成安装过程，然后重新启动虚拟机。

完成安装并重新登录到 VM 后，就可以继续操作了。

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>检索 TPM 的预配信息

在本部分中，你将生成一个可用于检索 TPM 的注册 ID 和认可密钥的工具。

1. 登录设备，然后遵循[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步骤安装和生成适用于 C 的 Azure IoT 设备 SDK。

1. 运行以下命令，以生成 SDK 工具来检索 TPM 的设备预配信息。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 输出窗口会显示设备的“注册 ID”和“认可密钥” 。 请复制这些值，以便稍后在 DPS 中为设备创建单个注册时使用。

> [!TIP]
> 如果不想使用 SDK 工具检索信息，则需要寻找其他方法来获取预配信息。 认可密钥对于每个 TPM 芯片都是唯一的，需从与之关联的 TPM 芯片制造商处获取。 例如，可以通过创建认可密钥的 SHA-256 哈希来为 TPM 设备派生唯一的 **注册 ID**。

获得注册 ID 和认可密钥后，即可继续。

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

从 TPM 中检索预配信息，并使用该信息在设备预配服务中创建单个注册。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所用的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

> [!TIP]
> 本文中的步骤适用于 Azure 门户，但你也可使用 Azure CLI 创建单个注册。 有关详细信息，请参阅 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)。 作为 CLI 命令的一部分，使用 edge-enabled 标志指定注册适用于单个 IoT Edge设备。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务实例。

1. 在“设置”下，选择“管理注册”。  

1. 选择“添加单个注册”，然后完成以下步骤以配置注册：

   1. 对于“机制”，请选择“TPM”。 

   1. 提供从虚拟机或物理设备中复制的认可密钥和注册 ID 。

   1. 根据需要，为设备提供一个 ID。 如果未提供设备 ID，则会使用注册 ID。

   1. 选择“True”，以声明虚拟机或物理设备是 IoT Edge 设备。

   1. 选择要将设备连接到的已链接 IoT 中心，或者选择“链接到新的 IoT 中心”。 可以选择多个中心，设备将会根据所选分配策略被分配到其中一个中心。

   1. 根据需要，将标记值添加到“初始设备孪生状态”。 可以使用标记将设备组指定为模块部署的目标。 有关详细信息，请参阅[大规模部署 IoT Edge 模块](how-to-deploy-at-scale.md)。

   1. 选择“保存” 。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。

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

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 了解之前收集的 DPS“ID 范围”和设备“注册 ID”。

1. 在 IoT Edge 设备上打开配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 找到文件的预配配置部分。 取消评论 TPM 预配的行，并确保注释禁止任何其他预配行。

   `provisioning:` 行前面应无空格，并且嵌套项应该缩进两个空格。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. 将 `scope_id` 和 `registration_id` 的值更新为你的 DPS 和设备信息。 `scope_id` 是 DPS 实例概述页中的“ID 范围”

1. （可选）使用 `always_reprovision_on_startup` 或 `dynamic_reprovisioning` 行来配置设备的重新预配行为。 如果设备设置为在启动时重新预配，它将始终尝试先使用 DPS 进行预配，如果失败，则回退到预配备份。 如果设备设置为动态预配自身，IoT Edge 将重启，并在检测到重新预配事件时重新预配。 有关详细信息，请参阅 [IoT 中心设备重新预配概念](../iot-dps/concepts-device-reprovision.md)。

1. 保存并关闭该文件。

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 了解之前收集的 DPS“ID 范围”和设备“注册 ID”。

1. 基于在安装 IoT Edge 的过程中提供的模板文件为你的设备创建配置文件。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. 在 IoT Edge 设备上打开配置文件。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. 找到该文件的预配配置部分。 取消评论 TPM 预配的行，并确保注释禁止任何其他预配行。

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. 将 `id_scope` 和 `registration_id` 的值更新为你的 DPS 和设备信息。 `scope_id` 是 DPS 实例概述页中的“ID 范围”

1. （可选）找到文件的自动重新预配模式部分。 使用 `auto_reprovisioning_mode` 参数将设备的重新预配行为配置为 `Dynamic`、`AlwaysOnStartup` 或 `OnErrorOnly`。 有关详细信息，请参阅 [IoT 中心设备重新预配概念](../iot-dps/concepts-device-reprovision.md)。

1. 保存并关闭该文件。

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>向 IoT Edge 授予 TPM 的访问权限

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge 运行时需要访问 TPM 以自动预配设备。

通过覆盖系统设置可以授予 IoT Edge 运行时对 TPM 的访问权限，以便 `iotedge` 服务获得根特权。 如果不想提升服务权限，也可以使用以下步骤手动提供 TPM 访问权限。

1. 创建一个新规则，用于向 IoT Edge 运行时授予对 tpm0 和 tpmrm0 的访问权限。

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 打开 rules 文件。

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 将以下访问信息复制到 rules 文件。 在使用的内核低于 4.12 的设备上，`tpmrm0` 可能不存在。 没有 tpmrm0 的设备可以安全地忽略该规则。

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. 保存并退出该文件。

1. 触发 udev 系统来评估新规则。

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. 验证是否已成功应用该规则。

   ```bash
   ls -l /dev/tpm*
   ```

   成功的输出如下所示：

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   如果未看到应用了正确的权限，请尝试重新启动计算机来刷新 udev。

1. 重启 IoT Edge 运行时，使之拾取你在设备上所做的所有配置更改。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 运行时依赖于 TPM 服务，该服务是对设备 TPM 的访问的中转站。 该服务需要访问 TPM 以自动预配设备。

通过覆盖系统设置可以授予对 TPM 的访问权限，以便 `aziottpm` 服务获得根特权。 如果不想提升服务权限，也可以使用以下步骤手动提供 TPM 访问权限。

1. 创建一个新规则，用于向 IoT Edge 运行时授予对 tpm0 和 tpmrm0 的访问权限。

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 打开 rules 文件。

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 将以下访问信息复制到 rules 文件。 在使用的内核低于 4.12 的设备上，`tpmrm0` 可能不存在。 没有 tpmrm0 的设备可以安全地忽略该规则。

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. 保存并退出该文件。

1. 触发 udev 系统来评估新规则。

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. 验证是否已成功应用该规则。

   ```bash
   ls -l /dev/tpm*
   ```

   成功的输出如下所示：

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   如果未看到应用了正确的权限，请尝试重新启动计算机来刷新 udev。

1. 应用在设备上所做的配置更改。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>重启 IoT Edge 并验证安装是否成功

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
重启 IoT Edge 运行时（如果尚未重启），使之拾取你在设备上所做的所有配置更改。

   ```bash
   sudo systemctl restart iotedge
   ```

检查 IoT Edge 运行时是否正在运行。

   ```bash
   sudo systemctl status iotedge
   ```

检查守护程序日志。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

如果出现预配错误，可能表示配置更改尚未生效。 请尝试再次重启 IoT Edge 守护程序。

   ```bash
   sudo systemctl daemon-reload
   ```

或者，请尝试重启虚拟机，以确定重新启动后更改是否生效。
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
应用你在设备上所做的配置更改（如果尚未应用）。

   ```bash
   sudo iotedge config apply
   ```

检查 IoT Edge 运行时是否正在运行。

   ```bash
   sudo iotedge system status
   ```

检查守护程序日志。

   ```cmd/sh
   sudo iotedge system logs
   ```

如果出现预配错误，可能表示配置更改尚未生效。 请尝试重启 IoT Edge 守护程序。

   ```bash
   sudo systemctl daemon-reload
   ```

或者，请尝试重启虚拟机，以确定重新启动后更改是否生效。
:::moniker-end
<!-- end 1.2 -->

如果运行时成功启动，则可以转到 IoT 中心，查看新设备是否自动预配。 现在，设备已准备好运行 IoT Edge 模块。

列出正在运行的模块。

```cmd/sh
iotedge list
```

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

## <a name="next-steps"></a>后续步骤

利用 DPS 注册过程，可以在预配新设备的同时设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。
