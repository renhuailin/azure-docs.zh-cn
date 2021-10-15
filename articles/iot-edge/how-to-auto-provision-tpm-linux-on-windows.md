---
title: 使用 DPS 和 TPM 自动预配 Windows 设备 - Azure IoT Edge | Microsoft Docs
description: 通过设备预配服务和 TPM 证明，对 IoT Edge for Linux on Windows 使用自动设备预配
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 03ed23fbc22c632e3bc9a0e5afeb1163e7f99c80
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278493"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-with-tpm-attestation"></a>使用 TPM 证明创建和预配 IoT Edge for Linux on Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

可使用[设备预配服务](../iot-dps/index.yml)预配 Azure IoT Edge 设备，就像预配未启用边缘的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配](../iot-dps/about-iot-dps.md#provisioning-process)概述。

DPS 支持仅对单个注册（而不是组注册）使用 IoT Edge 设备的受信任的平台模块 (TPM) 证明。

本文介绍如何在运行 IoT Edge for Linux on Windows 的设备上使用自动预配，步骤如下：

* 安装 IoT Edge for Linux on Windows。
* 检索设备中的 TPM 信息。
* 为设备创建个人注册。
* 根据其 TPM 信息预配设备。

## <a name="prerequisites"></a>先决条件

* 一台 Windows 设备。 如需了解支持的 Windows 版本，请参阅[操作系统](support.md#operating-systems)。
* 一个活动的 IoT 中心。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果你没有设备预配服务实例，请按照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明设置一个。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  预配 IoT Edge 设备时，需要使用此值。

> [!NOTE]
> 将 TPM 证明与 DPS 一起使用时，TPM 2.0 是必需的，并且只能用于创建个人（而非组）注册。

## <a name="install-iot-edge-for-linux-on-windows"></a>安装 IoT Edge for Linux on Windows

删减本部分中的安装步骤，重点介绍特定于 TPM 预配方案的步骤。 如需了解更详细的说明，包括先决条件和远程安装步骤，请参阅[在 Windows 设备上安装和预配 Azure IoT Edge for Linux](how-to-install-iot-edge-on-windows.md)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 在 Windows 设备上打开提升的 PowerShell 会话。

1. 下载 IoT Edge for Linux on Windows。

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. 在设备上安装 IoT Edge for Linux on Windows。

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

1. 若要成功运行部署，需要将设备上的执行策略设置为 `AllSigned`（如果还没有这样做）。

   1. 检查当前的执行策略。

      ```powershell
      Get-ExecutionPolicy -List
      ```

   1. 如果 `local machine` 的执行策略不是 `AllSigned`，请更新执行策略。

      ```powershell
      Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
      ```

1. 部署 IoT Edge for Linux on Windows。

   ```powershell
   Deploy-Eflow
   ```

1. 输入 `Y` 以接受许可条款。

1. 根据你的偏好输入 `O` 或 `R`，将“可选诊断数据”切换为开或关。

1. 将 IoT Edge for Linux on Windows 成功部署到设备后，输出将报告“部署成功”。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

>[!NOTE]
>适用于 Windows Admin Center 的 Azure IoT Edge 扩展目前处于[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)阶段。 安装和管理过程可能不同于正式版功能的相应过程。

1. 为 Windows 管理中心配置 Azure IoT Edge 扩展。

1. 在 Windows 管理中心的连接页上，选择“添加”。

1. 在“添加或创建资源”面板上，找到“Azure IoT Edge”磁贴 。 选择“新建”，以在设备上安装 Azure IoT Edge for Linux on Windows 的新实例。

1. 按照部署向导中的步骤，安装和配置 IoT Edge for Linux on Windows。

   1. 在“入门”步骤中，查看先决条件，接受许可条款，并选择是否发送诊断数据。

   1. 在“部署”步骤中，选择你的设备及其配置设置。 然后，观察 IoT Edge 部署到设备的进度。

   1. 选择“下一步”来继续执行“连接”步骤，在其中为你的设备提供预配信息 。

1. 不再遵循“预配”页面上的部署向导，因为需要执行一些步骤来让设备准备好使用 TPM 进行预配，然后才能继续操作。 使“Windows 管理中心”窗口保持打开状态，因为你将在本文末尾回来完成预配步骤。

---

## <a name="enable-tpm-passthrough"></a>启用 TPM 传递

Windows 虚拟机上的 IoT Edge for Linux 具有可启用/禁用的 TPM 功能。 默认禁用此功能。 启用此功能后，虚拟机可访问主机的 TPM。

1. 在提升的会话中打开 PowerShell。

1. 如果还未这样做，请将设备上的执行策略设置为 `AllSigned`，以便可运行 IoT Edge for Linux on Windows PowerShell 函数。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. 启用 TPM 功能。

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>检索设备中的 TPM 信息

若要预配设备，需要从 TPM 芯片收集信息，并将其提供给设备预配服务 (DPS) 实例，以便服务在尝试连接时能够识别你的设备。

首先，需要确定认可密钥，该密钥对于每个 TPM 芯片都是唯一的，并从与之关联的 TPM 芯片制造商处获取。 然后，需要为设备提供一个注册 ID。 例如，可通过创建认可密钥的 SHA-256 哈希来为 TPM 设备派生唯一的注册 ID。

IoT Edge for Linux on Windows 提供一个 PowerShell 函数来帮助从 TPM 中检索此信息。 若要使用脚本，请在设备上执行以下步骤：

1. 在提升的会话中打开 PowerShell。

1. 运行命令。

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

### <a name="simulate-a-tpm-optional"></a>模拟 TPM（可选）

如果没有可用的物理 TPM，但想要测试此预配方法，则可在设备上模拟 TPM。

IoT 中心设备预配服务为你提供了模拟 TPM 并返回认可密钥和注册 ID 的示例。

1. 根据你的首选语言，从以下列表中选择一个示例。
1. 运行模拟的 TPM，并且收集认可密钥和注册 ID 后，请不要再按照 DPS 示例步骤操作。 不要按 Enter 来在示例应用程序中运行注册。
1. 让承载模拟 TPM 的窗口保持在运行状态，直到完全测试此方案。
1. 返回到本文，创建一个 DPS 注册并配置你的设备。

若要了解如何创建模拟 TPM 设备的单个注册，请参阅[预配模拟的 TPM 设备快速入门指南](../iot-dps/quick-create-simulated-device-tpm.md)。


## <a name="create-a-device-enrollment-entry"></a>创建设备注册项

设备预配服务支持两类注册。 注册组用于注册多台相关设备。 单个注册用于注册一台设备。 TPM 证明仅支持单个注册，因为一组设备无法共享 TPM 信息。 因此，本文演示单个注册。

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到设备预配服务实例。

1. 在“设备预配服务”菜单中，选择“管理注册”。 选择顶部的“添加单个注册”  按钮。

1. 在“添加注册”面板中，输入以下信息：

   * 选择“TPM”作为标识证明机制。
   * 使用你之前记下的值输入 TPM 设备的“注册 ID”和“认可密钥”。
   * 选择与预配服务链接的 IoT 中心。
   * （可选）输入唯一的设备 ID。 如果选择不提供此项，则系统将改用注册 ID 来标识设备。
   * 选择 True 来声明该注册用于 IoT Edge 设备。

1. 选择“保存”来保存单个注册。

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 在 Windows 设备上打开提升的 PowerShell 会话。

1. 使用从设备预配服务实例中收集的范围 ID 来预配设备。

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在“连接”步骤中，预配你的设备。

   1. 选择“DpsTpm”预配方法。
   1. 提供从设备预配服务实例中检索的范围 ID。

   ![通过 DPS 和 TPM 证明预配设备。](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. 选择“使用所选方法进行预配”。

1. 在设备上成功安装并预配 IoT Edge 后，选择“完成”以退出部署向导。

---

## <a name="verify-successful-configuration"></a>验证成功的配置

验证是否已在 IoT Edge 设备上成功安装并配置了 IoT Edge for Linux on Windows。

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在设备上使用以下命令验证是否已成功安装并启动 IoT Edge。

1. 在 PowerShell 会话中使用以下命令，连接到 Windows 虚拟机上的 IoT Edge for Linux：

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >唯一被允许通过 SSH 登录到虚拟机的帐户是创建该虚拟机的用户。

1. 登录后，可以使用以下 Linux 命令查看正在运行的 IoT Edge 模块的列表：

   ```bash
   sudo iotedge list
   ```

1. 如果需要对 IoT Edge 服务进行故障排除，请使用以下 Linux 命令。

    1. 若需排查服务问题，请检索服务日志。

       ```bash
       sudo journalctl -u iotedge
       ```

    2. 使用 `check` 工具验证设备的配置和连接状态。

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 从 Windows Admin Center 中所连接设备的列表中选择你的 IoT Edge 设备，以连接到该设备。

1. 设备概述页会显示有关设备的一些信息：

   * “IoT Edge 模块列表”部分会显示设备上正在运行的模块。 当 IoT Edge 服务首次启动时，你应当只会看到 edgeAgent 模块正在运行。 edgeAgent 模块会默认运行，用于安装并启动部署到设备的任何其他模块。

   * “IoT Edge 状态”部分会显示服务状态，并且应当报告“活动(正在运行)”。

---

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作
