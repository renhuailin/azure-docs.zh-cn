---
title: 在 Windows 上使用 X.509 证书创建和预配用于 Linux 的 IoT Edge 设备 - Azure IoT Edge | Microsoft Docs
description: 使用 X.509 证书证明通过设备预配服务大规模测试 Azure IoT Edge 的设备预配
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 309ac9bf4810d245695ad92c316a23d4d8571ac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698968"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>使用 X.509 证书大规模创建和预配 IoT Edge for Linux on Windows 设备

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

本文提供用于通过 X.509 证书自动预配一个或多个 [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) 设备的端到端说明。 可以通过 [Azure IoT 中心设备预配服务](../iot-dps/index.yml) (DPS) 自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配概述](../iot-dps/about-iot-dps.md#provisioning-process)。

任务如下：

1. 生成证书和密钥。
1. 为单个设备创建单个注册，或为一组设备创建组注册。
1. 部署安装了 IoT Edge 运行时的 Linux 虚拟机，并将其连接到 IoT 中心。

将 X.509 证书用作一种证明机制是扩大生产规模和简化设备设置的极佳途径。 X.509 证书通常排列在证书信任链中。 从自签名证书或受信任的根证书开始，证书链中的每个证书为下一级证书签名。 此模式创建了从设备上安装的根证书到每个中间证书，再到最终“叶”证书的委托信任链。

## <a name="prerequisites"></a>先决条件

* 活动的 IoT 中心。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。
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
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果没有设备预配服务实例，则可按照 IoT 中心设备预配服务快速入门的[创建新的 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)和[将 IoT 中心和设备预配服务相链接](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)部分的说明进行操作。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。
* 如果要在 Azure IoT Edge for Linux on Windows 部署中使用 GPU 加速的 Linux 模块，则需要考虑多个配置选项。 需要根据 GPU 体系结构安装正确的驱动程序，并且可能需要访问 Windows 预览体验计划内部版本。 若要确定配置需求并满足这些先决条件，请参阅 [适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)。

可以使用 PowerShell 或 Windows Admin Center 来安装和管理 IoT Edge 设备。 每个实用工具都有自己的先决条件：

# <a name="powershell"></a>[PowerShell](#tab/powershell)

如果要使用 PowerShell，请按照以下步骤下载并安装 Azure IoT Edge for Linux on Windows：

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

   通过向安装命令中添加 `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` 和 `VHDXDIR="<FULLY_QUALIFIED_PATH>"` 参数，可以指定自定义安装和 VHDX 目录。

1. 在目标设备上将执行策略设置为 `AllSigned`。 可以在已提升权限的 PowerShell 提示符中使用以下命令检查当前执行策略：

   ```powershell
   Get-ExecutionPolicy -List
   ```

   如果 `local machine` 的执行策略不是 `AllSigned`，则可以使用以下命令设置执行策略：

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

若要详细了解 Azure IoT Edge for Linux on Windows PowerShell 模块，请参阅 [PowerShell 函数参考](reference-iot-edge-for-linux-on-windows-functions.md)。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

如果要使用 Windows Admin Center，请确保有权访问 Windows Admin Center 并安装了 Azure IoT Edge 扩展：

   1. 下载并运行 [Windows Admin Center 安装程序](https://aka.ms/wacdownload)。 按照安装向导的提示安装 Windows Admin Center。

   1. 安装完成后，使用受支持的浏览器打开 Windows Admin Center。 受支持的浏览器包括 Microsoft Edge（Windows 10 版本 1709 或更高版本）、Google Chrome 和 Microsoft Edge Insider。

   1. 当你第一次使用 Windows Admin Center 时，系统会提示你选择要使用的证书。 请选择“Windows Admin Center 客户端”作为你的证书。

   1. 安装 Azure IoT Edge 扩展。 选择 Windows Admin Center 仪表板右上方的齿轮图标。

      ![选择仪表板右上方的齿轮图标以访问设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. 在“设置”菜单上的“网关”下，选择“扩展”。  

   1. 在“可用扩展”选项卡上的扩展列表中找到“Azure IoT Edge”。 选择它，然后选择扩展列表上方的“安装”提示。

   1. 安装完成后，你应该会在“已安装的扩展”选项卡上的已安装扩展列表中看到 Azure IoT Edge。

---

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

## <a name="create-a-new-iot-edge-for-linux-on-windows-deployment"></a>创建新的 IoT Edge for Linux on Windows 部署

在目标设备上部署 Azure IoT Edge for Linux on Windows。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在目标设备上安装 IoT Edge for Linux on Windows（如果尚未安装）。

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

1. 在目标设备上将执行策略设置为 `AllSigned`（如果尚未设置）。 请参阅 PowerShell 先决条件，了解哪些命令可用来检查当前执行策略并将执行策略设置为 `AllSigned`。

1. 创建 IoT Edge for Linux on Windows 部署。

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

   若要使用 GPU 传递，需要将“gpuName”、“gpuPassthroughType”和“gpuCount”参数添加到 `Deploy-Eflow` 命令中  。 有关所有可用的可选参数的信息，请参阅适用于 [IoT Edge for Linux on Windows 的 PowerShell 函数](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)。

   >[!WARNING]
   >启用硬件设备传递可能会增加安全风险。 Microsoft 建议使用 GPU 供应商提供的设备缓解驱动程序（如果适用）。 有关详细信息，请参阅[使用离散设备分配部署图形设备](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)。

1. 输入“Y”接受许可条款。

1. 根据自己的偏好，输入“O”或“R”以打开或关闭“可选诊断数据”。

1. 部署完成后，PowerShell 窗口会报告“部署成功”。

   ![成功的部署会在消息末尾显示“部署成功”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-powershell-deployment.png)

部署完成后，便可预配设备了。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

>[!NOTE]
>适用于 Windows Admin Center 的 Azure IoT Edge 扩展目前处于[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)阶段。 安装和管理过程可能不同于正式版功能的相应过程。

在 Windows Admin Center 起始页的连接列表下，你会看到一个本地主机连接，该连接表示运行 Windows Admin Center 的电脑。 你管理的任何其他服务器、电脑或群集也会显示在此处。

可以使用 Windows Admin Center 在本地设备或远程的受管理设备上安装和管理 Azure IoT Edge for Linux on Windows。 在本指南中，本地主机连接将充当用于部署 Azure IoT Edge for Linux on Windows 的目标设备。

如果你要部署到远程目标设备而非本地设备，但在列表中看不到所需的目标设备，请按照[设备添加说明](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)进行操作。

   ![列出了目标设备的初始 Windows Admin Center 仪表板，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/windows-admin-center-initial-dashboard.png)

1. 选择 **添加** 。

1. 在“添加或创建资源”窗格中，找到“Azure IoT Edge”磁贴。 选择“新建”，以在设备上安装 Azure IoT Edge for Linux on Windows 的新实例。

   如果你有已在设备上运行的 IoT Edge for Linux on Windows，则可选择“添加”以连接到该现有的 IoT Edge 设备，并使用 Windows Admin Center 对其进行管理。

   ![在 Windows Admin Center 中的 Azure IoT Edge 磁贴上选择“新建”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/resource-creation-tiles.png)

1. “创建 Azure IoT Edge for Linux on Windows 部署”窗格将会打开。 在“1. 入门”选项卡上，查看最低要求，然后选择“下一步” 。

1. 查看许可条款，选中“我接受”，然后选择“下一步”。

1. 你可以根据自己的偏好，打开或关闭“可选诊断数据”。

1. 在完成时选择“下一步:**部署**。

   ![在根据你的偏好设置可选诊断数据后，选择“下一步: 部署”按钮，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-next-deploy.png)

1. 在“2.部署”选项卡上的“选择目标设备”下，单击你的已列出设备以验证它是否满足最低要求。 在确认其状态为“受支持”后，选择“下一步”。

   ![选择你的设备以验证它是否受支持，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/evaluate-supported-device.png)

1. 在“2.2 设置”选项卡上，查看你的部署的配置设置。

   >[!NOTE]
   >IoT Edge for Linux on Windows 使用默认交换机，该交换机会为 Linux 虚拟机分配内部 IP 地址。 无法从 Windows 计算机外部访问此内部 IP 地址。 登录 Windows 计算机后，可以在本地连接到虚拟机。
   >
   >如果使用的是 Windows Server，请在部署 IoT Edge for Linux on Windows 之前设置默认交换机。

   可以为部署分配 GPU，以启用 GPU 加速的 Linux 模块。 若要获取这些功能的访问权限，需要安装[适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)中详细说明的必备组件。 如果只是在部署过程中安装这些必备组件，则需要从头开始。

   有两个可用的 GPU 传递选项：“直接设备分配 (DDA)”和“GPU 半虚拟化 (GPU-PV)”，具体取决于分配给部署的 GPU 适配器 。 下面展示了每种方法的示例。

   对于“直接设备分配”方法，请选择要分配给 Linux 虚拟机的 GPU 处理器的数量。

   ![启用了直接设备分配 GPU 的配置设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-direct-device-assignment.png)

   对于半虚拟化方法，不需要进行其他设置。

   ![启用了半虚拟化 GPU 的配置设置，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >启用硬件设备传递可能会增加安全风险。 Microsoft 建议使用 GPU 供应商提供的设备缓解驱动程序（如果适用）。 有关详细信息，请参阅[使用离散设备分配部署图形设备](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)。

   对设置满意后，选择“下一步”。

1. 在“2.3 部署”选项卡上，你可以观察部署进度。 完整的过程包括下载 Azure IoT Edge for Linux on Windows 程序包，安装该程序包，配置主机设备以及设置 Linux 虚拟机。 此过程可能需要几分钟才能完成。 下面描绘了一个成功的部署。

   ![成功的部署所显示的每个步骤都会带有绿色的复选标记和“完成”标签，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-deployment.png)

部署完成后，便可预配设备了。 选择“下一步:连接”以转到“3.连接”选项卡，可以在这里处理 Azure IoT Edge 设备预配。

---

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

在设备上安装运行时后，请借助它用于连接到设备预配服务和 IoT 中心的信息来配置设备。

准备好以下信息：

* DPS 的“ID 范围”值。  可以从 Azure 门户中 DPS 实例的概述页检索此值。
* 设备上的设备标识证书链文件。
* 设备上的设备标识密钥文件。

可以使用 PowerShell 或 Windows Admin Center 来预配 IoT Edge 设备。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

对于 PowerShell，请运行以下命令，其中将占位符值更新为你自己的值：

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

对于 Windows Admin Center，请使用以下步骤：

1. 在“Azure IoT Edge 设备预配”窗格中，从预配方法下拉列表中选择“X509 证书(DPS)” 。

1. 在 Windows Admin Center 字段中提供 DPS 范围 ID、设备注册 ID 和注册主密钥或派生密钥。

1. 选择“使用所选方法进行预配”。

   ![填写用于对称密钥预配的必填字段后，选择“使用所选方法进行预配”，PNG。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

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

创建新的 IoT Edge 设备时，它将在 Azure 门户中显示状态代码 `417 -- The device's deployment configuration is not set`。 此状态正常，表示设备已准备好接收模块部署。

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。

也可执行以下操作：

* 转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
* 了解如何[在 IoT Edge for Linux on Windows 虚拟机上管理证书](how-to-manage-device-certificates.md)，以及如何将文件从主机 OS 传输到 Linux 虚拟机。
* 了解如何[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。
