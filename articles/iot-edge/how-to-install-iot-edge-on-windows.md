---
title: 在 Windows 上安装适用于 Linux 的 Azure IoT Edge | Microsoft Docs
description: 有关在 Windows 设备上安装 Azure IoT Edge 的说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2799e25dbd84ff07b375c6fa1b103789aae82b49
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538415"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>在 Windows 设备上安装和预配 Azure IoT Edge for Linux（预览版）

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 此运行时可以部署在小到电脑大到工业服务器的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

Azure IoT Edge for Linux on Windows 允许你通过 Linux 虚拟机在 Windows 设备上使用 Azure IoT Edge。 Linux 版本的 Azure IoT Edge 和随它部署的任何 Linux 模块都在虚拟机上运行。 在虚拟机中，Windows 应用程序和代码以及 IoT Edge 运行时和模块可以自由地彼此交互。

本文列出了在 Windows 设备上设置 IoT Edge 的步骤。 这些步骤将部署一台 Linux 虚拟机（其中包含要在 Windows 设备上运行的 IoT Edge 运行时），然后为设备预配其 IoT 中心设备标识。

>[!NOTE]
>IoT Edge for Linux on Windows 现提供[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
>虽然在 Windows 环境中使用 Azure IoT Edge 时建议使用 IoT Edge for Linux on Windows，但仍可使用 Windows 容器。 如果更喜欢使用 Windows 容器，请参阅有关[安装和管理适用于 Windows 的 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md) 的操作指南。

## <a name="prerequisites"></a>先决条件

* 含有效订阅的 Azure 帐户。 如果还没有 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

* 一台满足以下最低系统要求的 Windows 设备：

  * Windows 10 版本 1809 或更高版本；内部版本 17763 或更高版本
  * Professional、Enterprise 或 Server 版
  * 最小可用内存：1 GB
  * 最小可用磁盘空间：10 GB
  * 如果要使用 Windows 10 创建新的部署，请确保启用 Hyper-V。 有关详细信息，请参阅：如何[在 Windows 10 上安装 Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)。
  * 如果要使用 Windows Server 创建新的部署，请确保安装 Hyper-V 角色。 有关详细信息，请参阅：如何[在 Windows Server 上安装 Hyper-V 角色](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)。
  * 如果要使用 VM 创建新的部署，请确保正确配置嵌套虚拟化。 有关详细信息，请参阅[嵌套虚拟化](nested-virtualization.md)指南。

* 能够访问安装了适用于 Windows Admin Center 的 Azure IoT Edge 扩展的 Windows Admin Center：

   1. 下载 [Windows Admin Center 安装程序](https://aka.ms/wacdownload)。

   1. 运行下载的安装程序，并按照安装向导的提示安装 Windows Admin Center。 

   1. 安装完成后，使用受支持的浏览器打开 Windows Admin Center。 受支持的浏览器包括 Microsoft Edge（Windows 10 版本 1709 或更高版本）、Google Chrome 和 Microsoft Edge Insider。

   1. 当你第一次使用 Windows Admin Center 时，系统会提示你选择要使用的证书。 请选择“Windows Admin Center 客户端”作为你的证书。

   1. 接下来应该安装 Azure IoT Edge 扩展。 选择 Windows Admin Center 仪表板右上方的齿轮图标。

      ![选择仪表板右上方的齿轮图标以访问设置。](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. 在“设置”菜单上的“网关”下，选择“扩展”。  

   1. 在“可用扩展”选项卡上的扩展列表中找到“Azure IoT Edge”。 选择它，然后选择扩展列表上方的“安装”提示。

   1. 安装完成后，你应该会在“已安装的扩展”选项卡上的已安装扩展列表中看到 Azure IoT Edge。

## <a name="choose-your-provisioning-method"></a>选择预配方法

Azure IoT Edge for Linux on Windows 支持以下预配方法：

* 使用 IoT Edge 设备的连接字符串进行手动预配。 若要使用此方法，请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤注册设备并检索连接字符串。
  * 请选择对称密钥身份验证选项，因为 IoT Edge for Linux on Windows 当前不支持 X.509 自签名证书。
* 使用设备预配服务 (DPS) 和对称密钥进行自动预配。 详细了解[如何使用 DPS 和对称密钥创建和预配 IoT Edge 设备](how-to-auto-provision-symmetric-keys.md)。
* 使用 DPS 和 X.509 证书进行自动预配。 详细了解[如何使用 DPS 和 X.509 证书创建和预配 IoT Edge 设备](how-to-auto-provision-x509-certs.md)。

如果一开始只使用少量设备，则手动预配更为容易。 如果要预配许多设备，则可使用设备预配服务。

如果你计划使用 DPS 方法之一来预配你的设备，请按照上面链接的合适文章中的步骤创建一个 DPS 实例，将你的 DPS 实例链接到 IoT 中心，然后创建一个 DPS 注册。 你可以为单个设备创建单个注册，也可以为一组设备创建组注册。 有关注册类型的详细信息，请访问 [Azure IoT 中心设备预配服务概念](../iot-dps/concepts-service.md#enrollment)。

## <a name="create-a-new-deployment"></a>创建新部署

在目标设备上创建你的 Azure IoT Edge for Linux on Windows 部署。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

在 Windows Admin Center 起始页的连接列表下，你会看到一个本地主机连接，该连接表示运行 Windows Admin Center 的电脑。 你管理的任何其他服务器、电脑或群集也会显示在此处。

你可以使用 Windows Admin Center 在本地设备或远程的受管理设备上安装和管理 Azure IoT Edge for Linux on Windows。 在本指南中，本地主机连接将充当用于部署 Azure IoT Edge for Linux on Windows 的目标设备。

如果你要部署到远程目标设备而非本地设备，但在列表中看不到所需的目标设备，请按照[设备添加说明](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)进行操作。

   ![列出了目标设备的初始 Windows Admin Center 仪表板](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. 选择 **添加** 。

1. 在“添加或创建资源”窗格中，找到“Azure IoT Edge”磁贴。 选择“新建”，以在设备上安装 Azure IoT Edge for Linux on Windows 的新实例。

   如果你有已在设备上运行的 IoT Edge for Linux on Windows，则可选择“添加”以连接到该现有的 IoT Edge 设备，并使用 Windows Admin Center 对其进行管理。

   ![在 Windows Admin Center 中的 Azure IoT Edge 磁贴上选择“新建”](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. “创建 Azure IoT Edge for Linux on Windows 部署”窗格将会打开。 在“1.开始使用”选项卡上，验证你的目标设备是否满足最低要求，然后选择“下一步”。

1. 查看许可条款，选中“我接受”，然后选择“下一步”。

1. 你可以根据自己的偏好，打开或关闭“可选诊断数据”。

1. 在完成时选择“下一步:**部署**。

   ![在根据你的偏好设置可选诊断数据选项后，选择“下一步:部署”按钮。](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. 在“2.部署”选项卡上的“选择目标设备”下，单击你的已列出设备以验证它是否满足最低要求。 在确认其状态为“受支持”后，选择“下一步”。

   ![选择你的设备以验证它是否受支持](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. 在“2.2 设置”选项卡上，查看你的部署的配置设置。 对设置满意后，选择“下一步”。

   ![检查你的部署的配置设置](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >如果你使用的是 Windows 虚拟机，建议使用默认交换机而不是外部交换机，以确保在部署中创建的 Linux 虚拟机能够获取 IP 地址。
   >
   >使用默认交换机将为 Linux 虚拟机分配内部 IP 地址。 无法从 Windows 虚拟机外部访问此内部 IP 地址，但在登录到 Windows 虚拟机时可以在本地连接到此地址。
   >
   >如果你使用的是 Windows Server，请注意，Azure IoT Edge for Linux on Windows 不会自动支持默认交换机。 对于本地 Windows Server 虚拟机，请确保 Linux 虚拟机可以通过外部交换机获得 IP 地址。 对于 Azure 中的 Windows Server 虚拟机，在部署 IoT Edge for Linux on Windows 之前，请设置一台内部交换机。

1. 在“2.3 部署”选项卡上，你可以观察部署进度。 完整的过程包括下载 Azure IoT Edge for Linux on Windows 程序包，安装该程序包，配置主机设备以及设置 Linux 虚拟机。 此过程可能需要几分钟才能完成。 下面描绘了一个成功的部署。

   ![成功的部署所显示的每个步骤都会带有绿色的复选标记和“完成”标签](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

部署完成后，便可预配设备了。 选择“下一步:连接”以转到“3.连接”选项卡，可以在这里处理 Azure IoT Edge 设备预配。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在目标设备上安装 IoT Edge for Linux on Windows（如果尚未安装）。

> [!NOTE]
> 以下 PowerShell 过程概述了如何创建 Azure IoT Edge for Linux on Windows 的本地主机部署。 若要使用 PowerShell 创建到远程目标设备的部署，可以使用[远程 PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) 来建立与远程设备的连接，并在该设备上远程运行这些命令。

1. 在已提升权限的 PowerShell 会话中，运行以下每个命令来下载 IoT Edge for Linux on Windows。

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. 在设备上安装 IoT Edge for Linux on Windows。

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > 你可以通过将 INSTALLDIR="<FULLY_QUALIFIED_PATH>" 和 VHDXDIR="<FULLY_QUALIFIED_PATH>" 参数添加到上方的安装命令来指定自定义 IoT Edge for Linux on Windows 安装和 VHDX 目录。

1. 若要成功运行部署，需要将目标设备上的执行策略设置为 `AllSigned`（如果尚未设置）。 可以在已提升权限的 PowerShell 提示符中使用以下命令检查当前执行策略：

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   如果 `local machine` 的执行策略不是 `AllSigned`，则可以使用以下命令设置执行策略：

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. 创建 IoT Edge for Linux on Windows 部署。

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > 你可以在不使用参数的情况下运行此命令，也可以选择使用参数来自定义部署。 可以查看 [IoT Edge for Linux on Windows PowerShell 脚本参考](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow)来了解参数含义和默认值。

1. 输入“Y”接受许可条款。

1. 根据自己的偏好，输入“O”或“R”以打开或关闭“可选诊断数据”。 下面描绘了一个成功的部署。

   ![成功的部署会在消息末尾显示“部署成功”](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

部署完成后，便可预配设备了。

---

若要预配你的设备，可以访问下面的链接以跳转到你选择的预配方法对应的部分：

* [选项 1：使用 IoT Edge 设备的连接字符串进行手动预配](#option-1-provisioning-manually-using-the-connection-string)
* [选项 2：使用设备预配服务 (DPS) 和对称密钥进行自动预配](#option-2-provisioning-via-dps-using-symmetric-keys)
* [选项 3：使用 DPS 和 X.509 证书进行自动预配](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>预配你的设备

选择一种用于预配你的设备的方法，并按照相应部分中的说明进行操作。 你可以使用 Windows Admin Center 或已提升权限的 PowerShell 会话来预配你的设备。

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>选项 1：使用连接字符串进行手动预配

本部分介绍了如何使用 Azure IoT Edge 设备的连接字符串手动预配你的设备。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在“Azure IoT Edge 设备预配”窗格上，从“预配方法”下拉列表中选择“连接字符串(手动)”。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心的“IoT Edge”选项卡。

1. 单击设备的设备 ID。 复制“主连接字符串”字段。

1. 将其粘贴到 Windows Admin Center 中的“设备连接字符串”字段。 然后，选择“使用所选方法进行预配”。

   ![粘贴设备的连接字符串后，选择“使用所选方法进行预配”](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. 预配完成后，选择“完成”。 将返回到主仪表板。 现在，你应该会看到一个列出的新设备，其类型为“`IoT Edge Devices`”。 你可以选择该 IoT Edge 设备以连接到它。 在“概述”页上以后，即可查看设备的“IoT Edge 模块列表”和“IoT Edge 状态”。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心的“IoT Edge”选项卡。

1. 单击设备的设备 ID。 复制“主连接字符串”字段。

1. 将其粘贴到以下命令中以覆盖占位符文本，并在目标设备上的已提升权限的 PowerShell 会话中运行该命令。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>选项 2：通过 DPS 使用对称密钥进行预配

本部分介绍了如何使用 DPS 和对称密钥自动预配你的设备。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在“Azure IoT Edge 设备预配”窗格上，从“预配方法”下拉列表中选择“对称密钥(DPS)”。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在“概述”选项卡上，复制“ID 作用域”值。 将其粘贴到 Windows Admin Center 中的“作用域 ID”字段。

1. 在 Azure 门户中的“管理注册”选项卡上，选择你创建的注册。 复制注册详细信息中的“主密钥”值。 将其粘贴到 Windows Admin Center 的“对称密钥”字段中。

1. 在 Windows Admin Center 的“注册 ID”字段中提供设备的注册 ID。

1. 选择“使用所选方法进行预配”。

   ![填写对称密钥预配的必填字段后，选择“使用所选方法进行预配”。](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. 预配完成后，选择“完成”。 将返回到主仪表板。 现在，你应该会看到一个列出的新设备，其类型为“`IoT Edge Devices`”。 你可以选择该 IoT Edge 设备以连接到它。 在“概述”页上以后，即可查看设备的“IoT Edge 模块列表”和“IoT Edge 状态”。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 将以下命令复制到文本编辑器中。 根据详述内容将占位符文本替换为你的信息。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在“概述”选项卡上，复制“ID 作用域”值。 将其粘贴到命令中以覆盖相应的占位符文本。

1. 在 Azure 门户中的“管理注册”选项卡上，选择你创建的注册。 复制注册详细信息中的“主密钥”值。 将其粘贴到命令中以覆盖相应的占位符文本。

1. 提供设备的注册 ID 以替换命令中相应的占位符文本。

1. 在目标设备上已提升权限的 PowerShell 会话中运行该命令。

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>选项 3：通过 DPS 使用 X.509 证书进行预配

本部分介绍了如何使用 DPS 和 X.509 证书自动预配你的设备。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在“Azure IoT Edge 设备预配”窗格上，从“预配方法”下拉列表中选择“X.509 证书(DPS)”。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在“概述”选项卡上，复制“ID 作用域”值。 将其粘贴到 Windows Admin Center 中的“作用域 ID”字段。

1. 在 Windows Admin Center 的“注册 ID”字段中提供设备的注册 ID。

1. 上传你的证书和私钥文件。

1. 选择“使用所选方法进行预配”。

   ![填写 X.509 证书预配的必填字段后，选择“使用所选方法进行预配”。](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. 预配完成后，选择“完成”。 将返回到主仪表板。 现在，你应该会看到一个列出的新设备，其类型为“`IoT Edge Devices`”。 你可以选择该 IoT Edge 设备以连接到它。 在“概述”页上以后，即可查看设备的“IoT Edge 模块列表”和“IoT Edge 状态”。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 将以下命令复制到文本编辑器中。 根据详述内容将占位符文本替换为你的信息。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到你的 DPS 实例。

1. 在“概述”选项卡上，复制“ID 作用域”值。 将其粘贴到命令中以覆盖相应的占位符文本。

1. 提供设备的注册 ID 以替换命令中相应的占位符文本。

1. 将相应的占位符文本替换为你的证书文件的绝对源路径。

1. 将相应的占位符文本替换为你的私钥文件的绝对源路径。

1. 在目标设备上已提升权限的 PowerShell 会话中运行该命令。

---

## <a name="verify-successful-configuration"></a>验证成功的配置

验证是否已在 IoT Edge 设备上成功安装并配置了 IoT Edge for Linux on Windows。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 从 Windows Admin Center 中所连接设备的列表中选择你的 IoT Edge 设备，以连接到该设备。

1. 设备概述页会显示有关设备的一些信息：

    1. “IoT Edge 模块列表”部分会显示设备上正在运行的模块。 当 IoT Edge 服务首次启动时，你应当只会看到 edgeAgent 模块正在运行。 edgeAgent 模块会默认运行，用于安装并启动部署到设备的任何其他模块。
    1. “IoT Edge 状态”部分会显示服务状态，并且应当报告“活动(正在运行)”。

1. 如果需要对 IoT Edge 服务进行故障排除，请使用设备页上的“命令外壳”工具通过 ssh（安全外壳）连接到虚拟机，并运行 Linux 命令。

    1. 若需排查服务问题，请检索服务日志。

       ```bash
       journalctl -u iotedge
       ```

    2. 使用 `check` 工具验证设备的配置和连接状态。

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 在 PowerShell 会话中使用以下命令，登录到 Windows 虚拟机上的 IoT Edge for Linux：

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >允许通过 SSH 登录到虚拟机的唯一帐户是创建了该虚拟机的用户。

1. 登录后，可以使用以下 Linux 命令查看正在运行的 IoT Edge 模块的列表：

   ```bash
   iotedge list
   ```

1. 如果需要对 IoT Edge 服务进行故障排除，请使用以下 Linux 命令。

    1. 若需排查服务问题，请检索服务日志。

       ```bash
       journalctl -u iotedge
       ```

    2. 使用 `check` 工具验证设备的配置和连接状态。

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>后续步骤

* 转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
* 了解如何[在 IoT Edge for Linux on Windows 虚拟机上管理证书](how-to-manage-device-certificates.md)，以及如何将文件从主机 OS 传输到 Linux 虚拟机。
* 了解如何[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。
