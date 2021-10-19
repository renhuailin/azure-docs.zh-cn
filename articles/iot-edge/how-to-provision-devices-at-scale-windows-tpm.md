---
title: 在 Windows 上使用虚拟 TPM 预配设备 - Azure IoT Edge | Microsoft Docs
description: 在 Windows 设备上使用模拟 TPM 来测试 Azure IoT Edge 的 Azure 设备预配服务
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f130adffa4f5d542082b3ab6271952a867c30cdb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661556"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>在 Windows 上使用 TPM 大规模创建和预配 IoT Edge 设备

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

本文提供有关使用受信任的平台模块 (TPM) 自动预配 Windows IoT Edge 设备的端到端说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

本文概述了两种方法。 根据解决方案的体系结构选择你的首选方法：

1. 使用物理 TPM 硬件自动预配 Windows 设备。
1. 自动预配运行模拟 TPM 的 Windows 设备。 建议仅将此方法作为测试方案，因为模拟 TPM 无法提供与物理 TPM 相同的安全性。

具体说明因你的方法而异，因此请确保接下来按照正确选项卡上的说明操作。

任务如下：

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

* 检索设备的预配信息。
* 为设备创建个人注册。
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

# <a name="simulated-tpm"></a>[模拟 TPM](#tab/simulated-tpm)

* 设置模拟 TPM 并检索其预配信息。
* 为设备创建个人注册。
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

---

## <a name="prerequisites"></a>先决条件

物理 TPM 和虚拟 TPM 解决方案的先决条件相同。

* 一台 Windows 开发计算机。 本文使用 Windows 10。
* 活动的 IoT 中心。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果没有设备预配服务实例，则可按照 IoT 中心设备预配服务快速入门的[创建新的 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)和[将 IoT 中心和设备预配服务相链接](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)部分的说明进行操作。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

> [!NOTE]
> 将 TPM 证明与 DPS 结合使用时，需要 TPM 2.0。
>
> 使用 TPM 时，只能创建单个 DPS 注册，不能创建组 DPS 注册。

## <a name="set-up-your-tpm"></a>设置 TPM

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

在本部分中，你将生成一个可用于检索 TPM 的注册 ID 和认可密钥的工具。

1. 遵循[设置 Windows 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)中的步骤安装和生成适用于 C 的 Azure IoT 设备 SDK。

1. 在提升的 PowerShell 会话中运行以下命令，以生成 SDK 工具来检索 TPM 的设备预配信息。

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. 输出窗口会显示设备的“注册 ID”和“认可密钥” 。 请复制这些值，以便稍后在 DPS 中为设备创建单个注册时使用。

> [!TIP]
> 如果不想使用 SDK 工具检索信息，则需要寻找其他方法来获取预配信息。 认可密钥对于每个 TPM 芯片都是唯一的，需从与之关联的 TPM 芯片制造商处获取。 例如，可以通过创建认可密钥的 SHA-256 哈希来为 TPM 设备派生唯一的 **注册 ID**。

获得注册 ID 和认可密钥后，即可继续。

# <a name="simulated-tpm"></a>[模拟 TPM](#tab/simulated-tpm)

如果没有可用的物理 TPM，但想要测试此预配方法，则可在设备上模拟 TPM。

IoT 中心设备预配服务为你提供了模拟 TPM 并返回认可密钥和注册 ID 的示例 。

1. 根据你的首选语言，从以下列表中选择一个示例。
1. 运行模拟 TPM，并且收集认可密钥和注册 ID 后，请不要再按照 DPS 示例步骤操作 。 不要按 Enter 来在示例应用程序中运行注册。
1. 让托管模拟 TPM 的窗口保持运行状态，直到测试完此方案。
1. 返回到本文，创建一个 DPS 注册并配置你的设备。

模拟的 TPM 示例：

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

使用 TPM 的预配信息在设备预配服务中创建单个注册。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

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

在本部分，你将准备 IoT Edge 的 Windows 虚拟机或物理设备。 然后将安装 IoT Edge。

在准备好安装 IoT Edge 运行时之前，需要在设备上完成一个步骤。 需要在设备上安装容器引擎。

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

1. 了解在先前部分中收集的 DPS ID 范围和设备注册 ID 。

1. 在管理员模式下打开 PowerShell 窗口。 在安装 IoT Edge 而不是 PowerShell (x86) 时，请确保使用 PowerShell 的 AMD64 会话。

1. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。 通过 `-Dps` 标志使用设备预配服务，而不是手动预配。

   请将 `{scope_id}` 和 `{registration_id}` 的占位符值替换为前面收集的数据。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。 在设备上使用以下命令验证是否已成功安装并启动运行时。  

检查 IoT Edge 服务的状态。

```powershell
Get-Service iotedge
```

检查过去 5 分钟的服务日志。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出正在运行的模块。

```powershell
iotedge list
```

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作
