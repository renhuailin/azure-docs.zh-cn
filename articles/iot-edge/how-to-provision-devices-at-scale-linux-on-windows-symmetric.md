---
title: 在 Linux on Windows 上使用对称密钥创建和预配 IoT Edge 设备 - Azure IoT Edge | Microsoft Docs
description: 使用对称密钥证明通过设备预配服务大规模测试 Azure IoT Edge 的 Linux on Windows 设备预配
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d7d33b2f840b88226883bc9e6aff2823d7aa6cd2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699064"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>使用对称密钥大规模创建和预配 IoT Edge for Linux on Windows 设备 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

本文提供用于通过对称密钥自动预配一个或多个 [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) 设备的端到端说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

任务如下：

1. 为单个设备创建单个注册，或为一组设备创建组注册。
1. 部署安装了 IoT Edge 运行时的 Linux 虚拟机，并将其连接到 IoT 中心。

对称密钥证明是一种通过设备预配服务实例对设备进行身份验证的简单方法。 此证明方法表示不熟悉设备预配或不具备严格安全要求的开发人员的“Hello world”体验。 使用 [TPM](../iot-dps/concepts-tpm-attestation.md) 或 [X.509 证书](../iot-dps/concepts-x509-attestation.md)的设备证明更加安全，且应该用于更严格的安全要求。

## <a name="prerequisites"></a>先决条件

云资源：

* 活动的 IoT 中心。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果没有设备预配服务实例，则可按照 IoT 中心设备预配服务快速入门的[创建新的 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)和[将 IoT 中心和设备预配服务相链接](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)部分的说明进行操作。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

开发计算机：

* 一台满足以下最低系统要求的 Windows 设备：
  * Windows 10 版本 1809 或更高版本；内部版本 17763 或更高版本
  * Professional、Enterprise 或 Server 版
  * 最小可用内存：1 GB
  * 最小可用磁盘空间：10 GB
  * 虚拟化支持
    * 在 Windows 10 上，启用 Hyper-V。 有关详细信息，请参阅[在 Windows 10 上安装 Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)。
    * 在 Windows Server 上，安装 Hyper-V 角色并创建默认网络交换机。 有关详细信息，请参阅[用于 Azure IoT Edge for Linux on Windows 的嵌套虚拟化](nested-virtualization.md)。
    * 在虚拟机上配置嵌套虚拟化。 有关详细信息，请参阅[嵌套虚拟化](nested-virtualization.md)。
  * 网络支持
    * Windows Server 未附带默认交换机。 需要创建一个虚拟交换机，然后才能将 EFLOW 部署到 Windows Server 设备。  有关详细信息，请参阅[在 Windows 上创建适用于 Linux 的虚拟交换机](how-to-create-virtual-switch.md)。
    * Windows 桌面版本附带了可用于 EFLOW 安装的默认交换机。 如果需要，可以创建自己的自定义虚拟交换机。

   > [!TIP]
   > 如果要在 Azure IoT Edge for Linux on Windows 部署中使用 GPU 加速的 Linux 模块，则需要考虑多个配置选项。 需要根据 GPU 体系结构安装正确的驱动程序，并且可能需要访问 Windows 预览体验计划内部版本。 若要确定配置需求并满足这些先决条件，请参阅 [适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)。

可以使用 PowerShell 或 Windows Admin Center 来管理 IoT Edge 设备 。 每个实用工具都有自己的先决条件：

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell，请使用以下步骤准备目标设备，以便安装 Azure IoT Edge for Linux on Windows 并部署 Linux 虚拟机：

1. 在目标设备上将执行策略设置为 `AllSigned`。 可以在已提升权限的 PowerShell 提示符下使用以下命令检查当前执行策略：

   ```powershell
   Get-ExecutionPolicy -List
   ```

   如果 `local machine` 的执行策略不是 `AllSigned`，则可以使用以下命令设置执行策略：

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

若要详细了解 Azure IoT Edge for Linux on Windows PowerShell 模块，请参阅 [PowerShell 函数参考](reference-iot-edge-for-linux-on-windows-functions.md)。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

若要使用 Windows Admin Center，请使用以下步骤下载并安装 Windows Admin Center，然后安装 Windows Admin Center Azure IoT Edge 扩展：

   1. 下载并运行 [Windows Admin Center 安装程序](https://aka.ms/wacdownload)。 按照安装向导的提示安装 Windows Admin Center。

   1. 安装完成后，使用受支持的浏览器打开 Windows Admin Center。 受支持的浏览器包括 Microsoft Edge（Windows 10 版本 1709 或更高版本）、Google Chrome 和 Microsoft Edge Insider。

   1. 当你第一次使用 Windows Admin Center 时，系统会提示你选择要使用的证书。 请选择“Windows Admin Center 客户端”作为你的证书。

   1. 安装 Azure IoT Edge 扩展。 选择 Windows Admin Center 仪表板右上方的齿轮图标。

      ![选择仪表板右上方的齿轮图标以访问设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. 在“设置”菜单上的“网关”下，选择“扩展”。  

   1. 在“可用扩展”选项卡上的扩展列表中找到“Azure IoT Edge”。 选择它，然后选择扩展列表上方的“安装”提示。

   1. 安装完成后，你应该会在“已安装的扩展”选项卡上的已安装扩展列表中看到 Azure IoT Edge。

---

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

下面是派生设备密钥的输出示例：

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

## <a name="install-iot-edge"></a>安装 IoT Edge

在目标设备上部署 Azure IoT Edge for Linux on Windows。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在目标设备上安装 IoT Edge for Linux on Windows。

> [!NOTE]
> 以下 PowerShell 过程概述了如何在 Windows 上将适用于 Linux 的 IoT Edge 部署到本地设备。 若要使用 PowerShell 部署到远程目标设备，请使用[远程 PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) 与远程设备建立连接，并在该设备上远程运行这些命令。

1. 在已提升权限的 PowerShell 会话中，运行以下每个命令来下载 IoT Edge for Linux on Windows。

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. 在设备上安装 IoT Edge for Linux on Windows。

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   通过向安装命令中添加 `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` 和 `VHDXDIR="<FULLY_QUALIFIED_PATH>"` 参数，可以指定自定义 IoT Edge for Linux on Windows 安装和 VHDX 目录。

1. 在目标设备上将执行策略设置为 `AllSigned`（如果尚未设置）。 请参阅 PowerShell 先决条件，了解用于检查当前执行策略以及将执行策略设置为 `AllSigned` 的命令。

1. 创建 IoT Edge for Linux on Windows 部署。 部署将为你创建 Linux 虚拟机并安装 IoT Edge 运行时。

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >默认情况下，`Deploy-Eflow` 命令会创建具有 1 GB RAM、1 个 vCPU 核心和 16 GB 磁盘空间的 Linux 虚拟机。 但是，VM 所需的资源高度依赖于部署的工作负荷。 如果 VM 没有足够的内存来支持工作负荷，将无法启动。
   >
   >可以使用 `Deploy-Eflow` 命令的可选参数自定义虚拟机的可用资源。
   >
   >例如，以下命令创建具有 4 个 vCPU 核心、4 GB RAM 和 20 GB 磁盘空间的虚拟机：
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >有关所有可用的可选参数的信息，请参阅适用于 [IoT Edge for Linux on Windows 的 PowerShell 函数](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)。

   可以为部署分配 GPU，以启用 GPU 加速的 Linux 模块。 若要获取这些功能的访问权限，需要安装[适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)中详细说明的必备组件。

   若要使用 GPU 直通，请将 gpuName、gpuPassthroughType 和 gpuCount 参数添加到 `Deploy-Eflow` 命令中  。 有关所有可用的可选参数的信息，请参阅适用于 [IoT Edge for Linux on Windows 的 PowerShell 函数](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)。

   >[!WARNING]
   >启用硬件设备传递可能会增加安全风险。 Microsoft 建议使用 GPU 供应商提供的设备缓解驱动程序（如果适用）。 有关详细信息，请参阅[使用离散设备分配部署图形设备](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)。

1. 输入“Y”接受许可条款。

1. 根据自己的偏好，输入“O”或“R”以打开或关闭“可选诊断数据”。

1. 部署完成后，PowerShell 窗口会报告“部署成功”。

   ![成功的部署会在消息末尾显示“部署成功”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-powershell-deployment.png)

部署完成后，便可预配设备了。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

>[!NOTE]
>适用于 Windows Admin Center 的 Azure IoT Edge 扩展目前处于[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)阶段。 安装和管理过程可能不同于正式版功能的相应过程。

在设备上安装 Azure IoT Edge for Linux on Windows。

1. 下载 [Azure IoT Edge for Linux on Windows 安装程序](https://aka.ms/AzEflowMSI)。

1. 安装 Azure IoT Edge for Linux on Windows 后，打开 Windows Admin Center。

   在 Windows Admin Center 起始页的连接列表下，你会看到一个本地主机连接，该连接表示运行 Windows Admin Center 的电脑。 你管理的任何其他服务器、电脑或群集也会显示在此处。

   可以使用 Windows Admin Center 在本地设备或远程的受管理设备上安装和管理 Azure IoT Edge for Linux on Windows。 在本指南中，本地主机连接将充当用于部署 Azure IoT Edge for Linux on Windows 的目标设备。

1. 确认你的本地设备已在“所有连接”下列出，如下所示。

   ![列出了目标设备的初始 Windows Admin Center 仪表板，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/windows-admin-center-initial-dashboard.png)

   如果你要部署到远程目标设备而非本地设备，但在列表中看不到所需的目标设备，请按照[设备添加说明](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)进行操作。

1. 选择“+ 添加”开始创建部署。 部署将为你创建 Linux 虚拟机并安装 IoT Edge 运行时。

1. 在“添加或创建资源”窗格中，找到“Azure IoT Edge”磁贴。 选择“新建”，以在设备上安装 Azure IoT Edge for Linux on Windows 的新实例。

   如果你有已在设备上运行的 IoT Edge for Linux on Windows，则可选择“添加”以连接到该现有的 IoT Edge 设备，并使用 Windows Admin Center 对其进行管理。

   ![在 Windows Admin Center 中的“Azure IoT Edge”磁贴上选择“新建”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/resource-creation-tiles.png)

1. “创建 Azure IoT Edge for Linux on Windows 部署”窗格将会打开。 在“1. 入门”选项卡上，查看最低要求，然后选择“下一步” 。

1. 查看许可条款，选中“我接受”，然后选择“下一步”。

1. 你可以根据自己的偏好，打开或关闭“可选诊断数据”。

1. 在完成时选择“下一步:**部署**。

   ![在根据你的偏好设置可选诊断数据后，选择“下一步: 部署”按钮，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/select-next-deploy.png)

1. 在“2.部署”选项卡上的“选择目标设备”下，单击你的已列出设备以验证它是否满足最低要求。 在确认其状态为“受支持”后，选择“下一步”。

   ![选择你的设备以验证它是否受支持，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/evaluate-supported-device.png)

1. 在“2.2 设置”选项卡上，查看你的部署的配置设置。

   >[!NOTE]
   >IoT Edge for Linux on Windows 使用默认交换机，该交换机会为 Linux 虚拟机分配内部 IP 地址。 无法从 Windows 计算机外部访问此内部 IP 地址。 登录 Windows 计算机后，可以在本地连接到虚拟机。
   >
   >如果使用的是 Windows Server，请在部署 IoT Edge for Linux on Windows 之前[设置默认交换机](how-to-create-virtual-switch.md)。

   可以为部署分配 GPU，以启用 GPU 加速的 Linux 模块。 若要获取这些功能的访问权限，需要安装[适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)中详细说明的必备组件。 如果只是在部署过程中安装这些必备组件，则需要从头开始。

   有两个可用的 GPU 传递选项：“直接设备分配 (DDA)”和“GPU 半虚拟化 (GPU-PV)”，具体取决于分配给部署的 GPU 适配器 。 下面展示了每种方法的示例。

   对于“直接设备分配”方法，请选择要分配给 Linux 虚拟机的 GPU 处理器的数量。

   ![启用了直接设备分配 GPU 的配置设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-direct-device-assignment.png)

   对于半虚拟化方法，不需要进行其他设置。

   ![启用了半虚拟化 GPU 的配置设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >启用硬件设备传递可能会增加安全风险。 Microsoft 建议使用 GPU 供应商提供的设备缓解驱动程序（如果适用）。 有关详细信息，请参阅[使用离散设备分配部署图形设备](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)。

   对设置满意后，选择“下一步”。

1. 在“2.3 部署”选项卡上，你可以观察部署进度。 完整的过程包括下载 Azure IoT Edge for Linux on Windows 程序包，安装该程序包，配置主机设备以及设置 Linux 虚拟机。 此过程可能需要几分钟才能完成。 下面描绘了一个成功的部署。

   ![成功的部署所显示的每个步骤都会带有绿色的复选标记和“完成”标签，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-deployment.png)

部署完成后，便可预配设备了。 选择“下一步:连接”以转到“3.连接”选项卡，可以在这里处理 Azure IoT Edge 设备预配。

---

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

在设备上安装运行时后，请借助它用于连接到设备预配服务和 IoT 中心的信息来配置设备。

准备好以下信息：

* DPS 的“ID 范围”值 
* 为设备创建的“注册 ID” 
* 个人注册的[主密钥](#derive-a-device-key)，或者使用组注册时设备的派生密钥。

可以使用 PowerShell 或 Windows Admin Center 来预配 IoT Edge 设备。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

对于 PowerShell，请运行以下命令，其中将占位符值更新为你自己的值：

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

对于 Windows Admin Center，请使用以下步骤：

1. 在“Azure IoT Edge 设备预配”窗格上，从“预配方法”下拉列表中选择“对称密钥(DPS)”。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在 Windows Admin Center 字段中提供 DPS 范围 ID、设备注册 ID 和注册主密钥或派生密钥。

1. 选择“使用所选方法进行预配”。

   ![填写用于对称密钥预配的必填字段后，选择“使用所选方法进行预配”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. 预配完成后，选择“完成”。 将返回到主仪表板。 现在，你应该会看到一个列出的新设备，其类型为“`IoT Edge Devices`”。 你可以选择该 IoT Edge 设备以连接到它。 在“概述”页上以后，即可查看设备的“IoT Edge 模块列表”和“IoT Edge 状态”。

---

## <a name="verify-successful-configuration"></a>验证成功的配置

验证是否已在 IoT Edge 设备上成功安装并配置了 IoT Edge for Linux on Windows。

# <a name="individual-enrollment"></a>[单独注册](#tab/individual-enrollment)

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

# <a name="group-enrollment"></a>[组注册](#tab/group-enrollment)

可以验证是否使用了你在设备预配服务中创建的组注册。 在 Azure 门户中导航到设备预配服务实例。 打开你创建的组注册的注册详细信息。 转到“注册记录”选项卡，查看该组中注册的所有设备。

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 在 PowerShell 会话中使用以下命令，登录到 Windows 虚拟机上的 IoT Edge for Linux：

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

    1. 检索服务日志。

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

创建新的 IoT Edge 设备时，它将在 Azure 门户中显示状态代码 `417 -- The device's deployment configuration is not set`。 此状态正常，表示设备已准备好接收模块部署。

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。

也可执行以下操作：

* 转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
* 了解如何[在 IoT Edge for Linux on Windows 虚拟机上管理证书](how-to-manage-device-certificates.md)，以及如何将文件从主机 OS 传输到 Linux 虚拟机。
* 了解如何[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。
