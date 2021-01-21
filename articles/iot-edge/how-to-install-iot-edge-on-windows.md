---
title: 在 Windows 上安装适用于 Linux 的 Azure IoT Edge | Microsoft Docs
description: Windows 设备上的 Azure IoT Edge 安装说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 68fdc29edfd41799e2628abf40efa6a9453a90b9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633800"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>在 Windows 设备上安装和设置适用于 Linux 的 Azure IoT Edge (预览版) 

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 可以将运行时从 PC 类部署到工业服务器。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

Windows 上适用于 Linux 的 Azure IoT Edge 允许使用 Linux 虚拟机在 Windows 设备上使用 Azure IoT Edge。 Linux 版本的 Azure IoT Edge 以及随其一起部署的任何 Linux 模块在虚拟机上运行。 从这里，Windows 应用程序和代码以及 IoT Edge 运行时和模块可以自由地彼此交互。

本文列出了在 Windows 设备上设置 IoT Edge 的步骤。 这些步骤将部署包含要在 Windows 设备上运行的 IoT Edge 运行时的 Linux 虚拟机，然后使用其 IoT 中心设备标识设置设备。

>[!NOTE]
>Windows 上适用于 Linux 的 IoT Edge 提供 [公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备条件

* 含有效订阅的 Azure 帐户。 如果还没有 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

* 具有以下最低系统要求的 Windows 设备：

  * Windows 10 版本1809或更高版本;版本17763或更高版本
  * Professional、Enterprise 或 Server 版本
  * 最小 RAM： 4 GB (建议 8 GB) 
  * 最小存储： 10 GB

* 使用适用于 Windows 管理中心的 Azure IoT Edge 扩展来访问 Windows 管理中心的预览体验版本：  <!-- The link below needs the language localization to work; otherwise broken -->
   1. 请访问 [Windows 预览体验预览](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewserver)。

   1. 在 "预览" 下拉列表中，选择 " **Windows 管理中心预览-生成 2012**"，然后选择 " **确认**"。

      ![从 "可用预览" 下拉菜单中选择 "Windows 管理中心预览-生成 2012"。](./media/how-to-install-iot-edge-on-windows/select-windows-admin-center-preview-build.png)

   1. 在 " **选择语言** " 下拉列表中，选择 " **英语**"，然后选择 " **确认**"。

   1. 选择 " **立即下载** "，下载 *WindowsAdminCenterPreview2012.msi*。

   1. 运行 *WindowsAdminCenterPreview2012.msi* ，然后按照安装向导提示安装 Windows 管理中心。 安装完成后，打开 Windows 管理中心。

   1. 第一次使用 Windows 管理中心时，系统将提示您选择要使用的证书。 选择 " **Windows 管理中心客户端** " 作为证书。

   1. 可以安装 Azure IoT Edge 扩展。 选择 Windows 管理中心仪表板右上方的齿轮图标。

      ![选择仪表板右上方的齿轮图标以访问设置。](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. 在 " **设置** " 菜单的 " **网关**" 下，选择 " **扩展**"。

   1. 在 " **可用扩展** " 选项卡上，在扩展列表中查找 **Azure IoT Edge** 。 选择它，然后选择扩展列表上方的 **安装** 提示。

   1. 安装完成后，应会在 " **已安装的扩展** " 选项卡上的 "已安装的扩展" 列表中看到 Azure IoT Edge。

## <a name="choose-your-provisioning-method"></a>选择设置方法

Windows 上适用于 Linux 的 Azure IoT Edge 支持以下设置方法：

* 使用 IoT Edge 设备的连接字符串进行手动设置。 若要使用此方法，请使用在 [IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤注册设备并检索连接字符串。
  * 选择 "对称密钥" 身份验证选项，因为 x.509 自签名证书目前不受适用于 Windows 上的 Linux IoT Edge。
* 使用设备预配服务自动预配 (DPS) 和对称密钥。 了解有关 [使用 DPS 和对称密钥创建和预配 IoT Edge 设备](how-to-auto-provision-symmetric-keys.md)的详细信息。
* 使用 DPS 和 x.509 证书自动预配。 了解有关 [使用 DPS 和 x.509 证书创建和预配 IoT Edge 设备](how-to-auto-provision-x509-certs.md)的详细信息。

手动预配更易于开始使用几个设备。 设备预配服务有助于预配多台设备。

如果你计划使用其中一个 DPS 方法来预配你的设备或设备，请按照上面链接的适当文章中的步骤创建一个 DPS 实例，将你的 DPS 实例链接到 IoT 中心，然后创建一个 DPS 注册。 可以为一组设备创建单个设备或 *组注册* 的 *单个注册*。 有关注册类型的详细信息，请访问 [Azure IoT 中心设备预配服务概念](https://docs.microsoft.com/azure/iot-dps/concepts-service#enrollment)。

## <a name="create-a-new-deployment"></a>创建新部署

在目标设备上的 Windows 上创建适用于 Linux 的 Azure IoT Edge 部署。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

在 Windows 管理中心的 "开始" 页上的 "连接" 列表中，你将看到一个本地主机连接，该连接表示运行 Windows 管理中心的 PC。 你管理的任何其他服务器、电脑或群集也会显示在此处。

可以使用 Windows 管理中心在本地设备或远程托管设备上的 Windows 上安装和管理适用于 Linux 的 Azure IoT Edge。 在本指南中，本地主机连接将充当用于在 Windows 上部署 Linux Azure IoT Edge 的目标设备。

如果要部署到远程目标设备而不是本地设备，但在列表中看不到所需的目标设备，请按照[说明添加设备。](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![列出了目标设备的初始 Windows 管理中心面板](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. 选择 **添加** 。

1. 在 " **添加或创建资源** " 窗格中，找到 " **Azure IoT Edge** " 磁贴。 选择 " **新建** " 以在设备上的 Windows 上安装适用于 Linux 的 Azure IoT Edge 的新实例。

   如果在设备上运行的 Windows 上已有适用于 Linux 的 IoT Edge，则可以选择 " **添加** " 以连接到现有 IoT Edge 设备，并使用 Windows 管理中心对其进行管理。

   ![在 Windows 管理中心的 Azure IoT Edge 磁贴上选择 "新建"](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. 将打开 " **在 Windows 上创建适用于 Linux 的 Azure IoT Edge 部署** " 窗格。 **1。入门**"选项卡上，确认目标设备满足最低要求，然后选择"**下一步**"。

1. 查看许可条款，选中 " **我接受**"，然后选择 " **下一步**"。

1. 您可以根据自己的偏好，打开或关闭 **可选诊断数据** 。

1. 选择 **下一步：部署**。

   ![选择下一步： "部署" 按钮，然后将可选诊断数据切换到首选项](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. 位于 **2。部署** "选项卡上的" **选择目标设备**"下，单击列出的设备以验证它是否符合最低要求。 一旦其状态被确认为 "支持"，请选择 " **下一步**"。

   ![选择你的设备以验证它是否受支持](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. 接受 " **2.2 设置** " 选项卡上的默认设置。

1. 在 " **2.3 部署** " 选项卡上，你可以监视部署的进度。 完整的过程包括下载适用于 Windows 包的 Linux Azure IoT Edge、安装包、配置主机设备以及设置 Linux 虚拟机。 此过程可能需要几分钟才能完成。 下面介绍了成功的部署。

   ![成功的部署将使用绿色复选标记和 "完成" 标签显示每个步骤](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

部署完成后，便可以预配设备了。 选择 " **下一步"：连接** 转到 **3。"连接** " 选项卡，用于处理 Azure IoT Edge 设备设置。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

如果尚未安装，请在 Windows 上将适用于 Linux 的 IoT Edge 安装到目标设备上。

> [!NOTE]
> 以下 PowerShell 过程概述了如何在 Windows 上创建适用于 Linux 的 Azure IoT Edge 的本地主机部署。 若要使用 PowerShell 创建远程目标设备的部署，可以使用 [远程 PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_remote) 建立与远程设备的连接，并在该设备上远程运行这些命令。

1. 在已提升权限的 PowerShell 会话中，运行以下每个命令，在 Windows 上下载适用于 Linux 的 IoT Edge。

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzureEdgeForLinuxOnWindowsMSI" -OutFile $msiPath
   ```

1. 在设备上的 Windows 上安装适用于 Linux 的 IoT Edge。

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > 可以通过将 INSTALLDIR = "<FULLY_QUALIFIED_PATH>" 和 VHDXDIR = "<FULLY_QUALIFIED_PATH>" 参数添加到上述安装命令，在 Windows 安装和 VHDX 目录上指定适用于 Linux 的自定义 IoT Edge。

1. 若要成功运行部署，需要在目标设备上将执行策略设置为（ `AllSigned` 如果尚未这样做）。 可以使用以下命令，在提升的 PowerShell 提示符中检查当前执行策略：

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   如果的执行策略 `local machine` 不是 `AllSigned` ，则可以使用设置执行策略：

   ```azurepowershell-interactive
   Set-ExecutionPolicy - ExecutionPolicy AllSigned -Force
   ```

1. 在 Windows 部署上创建适用于 Linux 的 IoT Edge。

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   <!-- Most likely temporary until cmdlet is fully documented -->
   > [!NOTE]
   > 可以不带参数运行此命令，也可以选择自定义带参数的部署。 检查 PowerShell 模块 AzureEFLOW，以查看参数及其含义 (参阅 C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW) 下的。

1. 输入 "Y" 接受许可条款。

1. 输入 "O" 或 "R" 可根据自己的偏好切换 **可选诊断数据** 。 下面介绍了成功的部署。

   ![成功的部署将在消息末尾显示 "部署成功"。](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

部署完成后，便可以预配设备了。

---

若要预配你的设备，你可以访问下面的链接以跳转到所选预配方法的部分：

* [选项1：使用 IoT Edge 设备的连接字符串进行手动设置](#option-1-provisioning-manually-using-the-connection-string)
* [选项2：使用设备预配服务自动预配 (DPS) 和对称密钥](#option-2-provisioning-via-dps-using-symmetric-keys)
* [选项3：使用 DPS 和 x.509 证书自动预配](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>预配设备

选择一种方法来预配设备，并按照相应部分中的说明进行操作。 你可以使用 Windows 管理中心或提升的 PowerShell 会话来预配你的设备。

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>选项1：使用连接字符串手动设置

本部分介绍如何使用 Azure IoT Edge 设备的连接字符串手动设置设备。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在 " **Azure IoT Edge 设备设置** " 窗格中，从 "预配方法" 下拉列表中选择 " **手动) 连接字符串 (** 。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心的 " **IoT Edge** " 选项卡。

1. 单击设备的设备 ID。 复制 " **主连接字符串** " 字段。

1. 将其粘贴到 Windows 管理中心中的 "设备连接字符串" 字段。 然后，选择 **"设置" 并选择 "设置"**。

   ![粘贴设备的连接字符串后，选择 "用所选方法进行预配"](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. 预配完成后，选择 " **完成**"。 您将返回到主仪表板。 现在，应会看到列出的新设备，其类型为 `IoT Edge Devices` 。 您可以选择 IoT Edge 设备连接到该设备。 在其 " **概述** " 页上，可以查看设备的 **IoT Edge 模块列表** 和 **IoT Edge 状态** 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心的 " **IoT Edge** " 选项卡。

1. 单击设备的设备 ID。 复制 " **主连接字符串** " 字段。

1. 在以下命令中粘贴占位符文本，并在目标设备上的已提升权限的 PowerShell 会话中运行该文本。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>选项2：使用对称密钥通过 DPS 进行预配

本部分介绍如何使用 DPS 和对称密钥自动设置设备。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在 " **Azure IoT Edge 设备设置** " 窗格中，从 "预配方法" 下拉列表中选择 " **对称密钥 (DPS)** "。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在 " **概述** " 选项卡上，复制 " **ID 范围** " 值。 将其粘贴到 Windows 管理中心的 "作用域 ID" 字段。

1. 在 Azure 门户中的 " **管理注册** " 选项卡上，选择你创建的注册。 复制 "注册详细信息" 中的 " **主密钥** " 值。 将其粘贴到 Windows 管理中心的 "对称密钥" 字段。

1. 在 Windows 管理中心的 "注册 ID" 字段中提供设备的注册 ID。

1. 选择 **"预配"，并选择 "方法"**。

   ![填写对称密钥预配的必填字段后，请选择 "采用所选方法进行预配"](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. 预配完成后，选择 " **完成**"。 您将返回到主仪表板。 现在，应会看到列出的新设备，其类型为 `IoT Edge Devices` 。 您可以选择 IoT Edge 设备连接到该设备。 在其 " **概述** " 页上，可以查看设备的 **IoT Edge 模块列表** 和 **IoT Edge 状态** 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 将以下命令复制到文本编辑器中。 将占位符文本替换为详细信息。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在 " **概述** " 选项卡上，复制 " **ID 范围** " 值。 将其粘贴到命令中的适当占位符文本上。

1. 在 Azure 门户中的 " **管理注册** " 选项卡上，选择你创建的注册。 复制 "注册详细信息" 中的 " **主密钥** " 值。 将其粘贴到命令中的适当占位符文本上。

1. 提供设备的注册 ID 以替换命令中相应的占位符文本。

1. 在目标设备上的已提升权限的 PowerShell 会话中运行命令。

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>选项3：使用 x.509 证书通过 DPS 进行预配

本部分介绍如何使用 DPS 和 x.509 证书自动设置设备。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在 " **Azure IoT Edge 设备设置** " 窗格中，从 "预配方法" 下拉列表中选择 " **x.509 证书" ("DPS)** "。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在 " **概述** " 选项卡上，复制 " **ID 范围** " 值。 将其粘贴到 Windows 管理中心的 "作用域 ID" 字段。

1. 在 Windows 管理中心的 "注册 ID" 字段中提供设备的注册 ID。

1. 上传证书和私钥文件。

1. 选择 **"预配"，并选择 "方法"**。

   ![在为 x.509 证书预配填写必填字段后，选择 "采用所选方法进行预配"](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. 预配完成后，选择 " **完成**"。 您将返回到主仪表板。 现在，应会看到列出的新设备，其类型为 `IoT Edge Devices` 。 您可以选择 IoT Edge 设备连接到该设备。 在其 " **概述** " 页上，可以查看设备的 **IoT Edge 模块列表** 和 **IoT Edge 状态** 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 将以下命令复制到文本编辑器中。 将占位符文本替换为详细信息。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocWin <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在 " **概述** " 选项卡上，复制 " **ID 范围** " 值。 将其粘贴到命令中的适当占位符文本上。

1. 提供设备的注册 ID 以替换命令中相应的占位符文本。

1. 将相应的占位符文本替换为证书文件的绝对源路径。

1. 将相应的占位符文本替换为你的私钥文件的绝对源路径。

1. 在目标设备上的已提升权限的 PowerShell 会话中运行命令。

---

## <a name="verify-successful-configuration"></a>验证配置是否成功

验证是否已在 IoT Edge 设备上成功安装和配置适用于 Windows 的 Linux IoT Edge。

1. 从 Windows 管理中心中连接的设备列表中选择 IoT Edge 设备，以连接到该设备。
1. "设备概述" 页显示有关设备的一些信息：

    1. **IoT Edge Module List** 部分显示设备上正在运行的模块。 第一次启动 IoT Edge 服务时，只应看到 **edgeAgent** 模块正在运行。 edgeAgent 模块会默认运行，用于安装并启动部署到设备的任何其他模块。
    1. " **IoT Edge 状态** " 部分显示服务状态，并且应报告 **正在运行)  (活动**。

1. 如果需要对 IoT Edge 服务进行故障排除，请使用 "设备" 页上的 " **命令行界面** " 工具通过 ssh (安全 Shell) 到虚拟机并运行 Linux 命令。

    1. 若需排查服务问题，请检索服务日志。

       ```bash
       journalctl -u iotedge
       ```

    2. 使用 `check` 工具验证设备的配置和连接状态。

       ```bash
       sudo iotedge check
       ```

## <a name="next-steps"></a>后续步骤

转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
