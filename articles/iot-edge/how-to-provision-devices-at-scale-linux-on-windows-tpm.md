---
title: 使用 TPM 预配 IoT Edge for Linux on Windows 设备 | Microsoft Docs
description: 在 Linux on Windows 设备上使用模拟 TPM 来测试 Azure IoT Edge 的 Azure 设备预配服务
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d6943fa78960760a665f0abad51f0839da9d63bb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661545"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-using-a-tpm"></a>使用 TPM 大规模创建和预配 IoT Edge for Linux on Windows 设备

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

本文提供有关使用受信任的平台模块 (TPM) 自动预配 IoT Edge for Linux on Windows 设备的端到端说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

本文概述了两种方法。 根据解决方案的体系结构选择你的首选方法：

1. 使用物理 TPM 硬件自动预配 Linux on Windows 设备。
1. 使用模拟 TPM 自动预配 Linux on Windows 设备。 建议仅将此方法作为测试方案，因为模拟 TPM 无法提供与物理 TPM 相同的安全性。

任务如下：

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

* 安装 IoT Edge for Linux on Windows。
* 检索设备中的 TPM 信息。
* 为设备创建个人注册。
* 根据其 TPM 信息预配设备。

# <a name="simulated-tpm"></a>[模拟 TPM](#tab/simulated-tpm)

* 安装 IoT Edge for Linux on Windows。
* 设置模拟 TPM 并检索其预配信息。
* 为设备创建个人注册。
* 根据其 TPM 信息预配设备。

---

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

      ![选择仪表板右上方的齿轮图标以访问设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-gear-icon.png)

   1. 在“设置”菜单上的“网关”下，选择“扩展”。  

   1. 在“可用扩展”选项卡上的扩展列表中找到“Azure IoT Edge”。 选择它，然后选择扩展列表上方的“安装”提示。

   1. 安装完成后，你应该会在“已安装的扩展”选项卡上的已安装扩展列表中看到 Azure IoT Edge。

---

> [!NOTE]
> 将 TPM 证明与 DPS 结合使用时，需要 TPM 2.0。
>
> 使用 TPM 时，只能创建单个 DPS 注册，不能创建组 DPS 注册。

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

   ![成功的部署会在消息末尾显示“部署成功”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-powershell-deployment.png)

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

   ![列出了目标设备的初始 Windows Admin Center 仪表板，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/windows-admin-center-initial-dashboard.png)

   如果你要部署到远程目标设备而非本地设备，但在列表中看不到所需的目标设备，请按照[设备添加说明](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)进行操作。

1. 选择“+ 添加”开始创建部署。 部署将为你创建 Linux 虚拟机并安装 IoT Edge 运行时。

1. 在“添加或创建资源”窗格中，找到“Azure IoT Edge”磁贴。 选择“新建”，以在设备上安装 Azure IoT Edge for Linux on Windows 的新实例。

   如果你有已在设备上运行的 IoT Edge for Linux on Windows，则可选择“添加”以连接到该现有的 IoT Edge 设备，并使用 Windows Admin Center 对其进行管理。

   ![在 Windows Admin Center 中的“Azure IoT Edge”磁贴上选择“新建”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/resource-creation-tiles.png)

1. “创建 Azure IoT Edge for Linux on Windows 部署”窗格将会打开。 在“1. 入门”选项卡上，查看最低要求，然后选择“下一步” 。

1. 查看许可条款，选中“我接受”，然后选择“下一步”。

1. 你可以根据自己的偏好，打开或关闭“可选诊断数据”。

1. 在完成时选择“下一步:**部署**。

   ![在根据你的偏好设置可选诊断数据后，选择“下一步: 部署”按钮，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-next-deploy.png)

1. 在“2.部署”选项卡上的“选择目标设备”下，单击你的已列出设备以验证它是否满足最低要求。 在确认其状态为“受支持”后，选择“下一步”。

   ![选择你的设备以验证它是否受支持，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/evaluate-supported-device.png)

1. 在“2.2 设置”选项卡上，查看你的部署的配置设置。

   >[!NOTE]
   >IoT Edge for Linux on Windows 使用默认交换机，该交换机会为 Linux 虚拟机分配内部 IP 地址。 无法从 Windows 计算机外部访问此内部 IP 地址。 登录 Windows 计算机后，可以在本地连接到虚拟机。
   >
   >如果使用的是 Windows Server，请在部署 IoT Edge for Linux on Windows 之前[设置默认交换机](how-to-create-virtual-switch.md)。

   可以为部署分配 GPU，以启用 GPU 加速的 Linux 模块。 若要获取这些功能的访问权限，需要安装[适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)中详细说明的必备组件。 如果只是在部署过程中安装这些必备组件，则需要从头开始。

   有两个可用的 GPU 传递选项：“直接设备分配 (DDA)”和“GPU 半虚拟化 (GPU-PV)”，具体取决于分配给部署的 GPU 适配器 。 下面展示了每种方法的示例。

   对于“直接设备分配”方法，请选择要分配给 Linux 虚拟机的 GPU 处理器的数量。

   ![启用了直接设备分配 GPU 的配置设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-direct-device-assignment.png)

   对于半虚拟化方法，不需要进行其他设置。

   ![启用了半虚拟化 GPU 的配置设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >启用硬件设备传递可能会增加安全风险。 Microsoft 建议使用 GPU 供应商提供的设备缓解驱动程序（如果适用）。 有关详细信息，请参阅[使用离散设备分配部署图形设备](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)。

   对设置满意后，选择“下一步”。

1. 在“2.3 部署”选项卡上，你可以观察部署进度。 完整的过程包括下载 Azure IoT Edge for Linux on Windows 程序包，安装该程序包，配置主机设备以及设置 Linux 虚拟机。 此过程可能需要几分钟才能完成。 下面描绘了一个成功的部署。

   ![成功的部署所显示的每个步骤都会带有绿色的复选标记和“完成”标签，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-deployment.png)

1. 部署完成后，便可预配设备了。 选择“下一步:连接”以转到“3.连接”选项卡，可以在这里处理 Azure IoT Edge 设备预配。

不再遵循“预配”页面上的部署向导，因为需要执行一些步骤来让设备准备好使用 TPM 进行预配，然后才能继续操作。 使“Windows 管理中心”窗口保持打开状态，因为你将在本文末尾回来完成预配步骤。

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

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

若要预配设备，需要从 TPM 芯片收集信息，并将其提供给设备预配服务 (DPS) 实例，以便服务在尝试连接时能够识别你的设备。

首先，需要确定认可密钥，该密钥对于每个 TPM 芯片都是唯一的，并从与之关联的 TPM 芯片制造商处获取。 然后，需要为设备提供一个注册 ID。 例如，可通过创建认可密钥的 SHA-256 哈希来为 TPM 设备派生唯一的注册 ID。

IoT Edge for Linux on Windows 提供一个 PowerShell 脚本，帮助从 TPM 中检索此信息。 若要使用脚本，请在设备上执行以下步骤：

1. 在提升的会话中打开 PowerShell。

1. 克隆 [iotedge-eflow](https://github.com/Azure/iotedge-eflow) 存储库。

   ```powershell
   git clone https://github.com/Azure/iotedge-eflow.git
   ```

1. 导入下载的模块。

   ```powershell
   Import-Module <path>\iotedge-eflow\samples\scripts\EflowTpmProvisioningInfo.ps1
   ```

1. 运行命令。

   ```powershell
   Get-EflowVmTpmProvisioningInformation
   ```

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

现在，此设备已有注册，IoT Edge 运行时可以预配它。

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