---
title: 在 Windows 上安装适用于 Linux 的 Azure IoT Edge | Microsoft Docs
description: 有关在 Windows 设备上安装 Azure IoT Edge 的说明
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8cc39325f8c993afb6901e9700376fc73ecbefe7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602852"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device"></a>在 Windows 设备上安装和预配适用于 Linux 的 Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 此运行时可以部署在小到电脑大到工业服务器的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

Azure IoT Edge for Linux on Windows 支持在 Windows 设备上运行的 Linux 虚拟机中安装 IoT Edge。 Linux 版本的 Azure IoT Edge 和随它部署的任何 Linux 模块都在虚拟机上运行。 在虚拟机中，Windows 应用程序和代码以及 IoT Edge 运行时和模块可以自由地彼此交互。

本文列出了在 Windows 设备上设置 IoT Edge 的步骤。 这些步骤将部署一台 Linux 虚拟机（其中包含要在 Windows 设备上运行的 IoT Edge 运行时），然后为设备预配其 IoT 中心设备标识。

>[!NOTE]
>在 Windows 环境中使用 Azure IoT Edge 时建议使用 IoT Edge for Linux on Windows。 但仍然可以使用 Windows 容器。 如果希望使用 Windows 容器，请参阅[使用 Windows 容器安装和管理 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md)。

## <a name="prerequisites"></a>先决条件

* 含有效订阅的 Azure 帐户。 如果还没有 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

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

* 如果要使用 Windows Admin Center 安装和管理 IoT Edge 设备，请确保有权访问 Windows Admin Center 并安装了 Azure IoT Edge 扩展：

   1. 下载并运行 [Windows Admin Center 安装程序](https://aka.ms/wacdownload)。 按照安装向导的提示安装 Windows Admin Center。

   1. 安装完成后，使用受支持的浏览器打开 Windows Admin Center。 受支持的浏览器包括 Microsoft Edge（Windows 10 版本 1709 或更高版本）、Google Chrome 和 Microsoft Edge Insider。

   1. 当你第一次使用 Windows Admin Center 时，系统会提示你选择要使用的证书。 请选择“Windows Admin Center 客户端”作为你的证书。

   1. 安装 Azure IoT Edge 扩展。 选择 Windows Admin Center 仪表板右上方的齿轮图标。

      ![选择仪表板右上方的齿轮图标以访问设置。](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. 在“设置”菜单上的“网关”下，选择“扩展”。  

   1. 在“可用扩展”选项卡上的扩展列表中找到“Azure IoT Edge”。 选择它，然后选择扩展列表上方的“安装”提示。

   1. 安装完成后，你应该会在“已安装的扩展”选项卡上的已安装扩展列表中看到 Azure IoT Edge。

* 如果要在 Azure IoT Edge for Linux on Windows 部署中使用 GPU 加速的 Linux 模块，则需要考虑多个配置选项。 需要根据 GPU 体系结构安装正确的驱动程序，并且可能需要访问 Windows 预览体验计划内部版本。 若要确定配置需求并满足这些先决条件，请参阅 [适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)。

## <a name="choose-your-provisioning-method"></a>选择预配方法

Azure IoT Edge for Linux on Windows 支持以下预配方法：

* 为单个设备手动预配。

  * 若要准备手动预配，请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤操作。 选择“对称密钥身份验证”或“x.509 证书身份验证”，然后返回本文以安装并预配 IoT Edge。

* 使用 IoT 中心设备预配服务 (DPS) 为一台或多台设备进行自动预配。

  * 选择要使用的身份验证方法，然后按照相应文章中的步骤设置 DPS 实例，并创建注册以设置设备。 有关注册类型的详细信息，请访问 [Azure IoT 中心设备预配服务概念](../iot-dps/concepts-service.md#enrollment)。

    * [使用 DPS 和对称密钥预配 IoT Edge 设备](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)。
    * [使用 DPS 和 X.509 证书预配 IoT Edge 设备](how-to-provision-devices-at-scale-linux-on-windows-x509.md)。
    * [使用 DPS 和 TPM 证明预配 IoT Edge 设备](how-to-auto-provision-tpm-linux-on-windows.md)。

## <a name="create-a-new-deployment"></a>创建新部署

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

1. 在目标设备上将执行策略设置为 `AllSigned`（如果尚未设置）。 可以在已提升权限的 PowerShell 提示符中使用以下命令检查当前执行策略：

   ```powershell
   Get-ExecutionPolicy -List
   ```

   如果 `local machine` 的执行策略不是 `AllSigned`，则可以使用以下命令设置执行策略：

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

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

   ![成功的部署会在消息末尾显示“部署成功”](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment-2.png)

部署完成后，便可预配设备了。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

>[!NOTE]
>适用于 Windows Admin Center 的 Azure IoT Edge 扩展目前处于[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)阶段。 安装和管理过程可能不同于正式版功能的相应过程。

在 Windows Admin Center 起始页的连接列表下，你会看到一个本地主机连接，该连接表示运行 Windows Admin Center 的电脑。 你管理的任何其他服务器、电脑或群集也会显示在此处。

可以使用 Windows Admin Center 在本地设备或远程的受管理设备上安装和管理 Azure IoT Edge for Linux on Windows。 在本指南中，本地主机连接将充当用于部署 Azure IoT Edge for Linux on Windows 的目标设备。

如果你要部署到远程目标设备而非本地设备，但在列表中看不到所需的目标设备，请按照[设备添加说明](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)进行操作。

   ![列出了目标设备的初始 Windows Admin Center 仪表板](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. 选择 **添加** 。

1. 在“添加或创建资源”窗格中，找到“Azure IoT Edge”磁贴。 选择“新建”，以在设备上安装 Azure IoT Edge for Linux on Windows 的新实例。

   如果你有已在设备上运行的 IoT Edge for Linux on Windows，则可选择“添加”以连接到该现有的 IoT Edge 设备，并使用 Windows Admin Center 对其进行管理。

   ![在 Windows Admin Center 中的 Azure IoT Edge 磁贴上选择“新建”](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. “创建 Azure IoT Edge for Linux on Windows 部署”窗格将会打开。 在“1. 入门”选项卡上，查看最低要求，然后选择“下一步” 。

1. 查看许可条款，选中“我接受”，然后选择“下一步”。

1. 你可以根据自己的偏好，打开或关闭“可选诊断数据”。

1. 在完成时选择“下一步:**部署**。

   ![在根据你的偏好设置可选诊断数据选项后，选择“下一步:部署”按钮。](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. 在“2.部署”选项卡上的“选择目标设备”下，单击你的已列出设备以验证它是否满足最低要求。 在确认其状态为“受支持”后，选择“下一步”。

   ![选择你的设备以验证它是否受支持](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. 在“2.2 设置”选项卡上，查看你的部署的配置设置。

   >[!NOTE]
   >IoT Edge for Linux on Windows 使用默认交换机，该交换机会为 Linux 虚拟机分配内部 IP 地址。 无法从 Windows 计算机外部访问此内部 IP 地址。 登录 Windows 计算机后，可以在本地连接到虚拟机。
   >
   >如果使用的是 Windows Server，请在部署 IoT Edge for Linux on Windows 之前设置默认交换机。

   可以为部署分配 GPU，以启用 GPU 加速的 Linux 模块。 若要获取这些功能的访问权限，需要安装[适用于 Azure IoT Edge for Linux on Windows 的 GPU 加速](gpu-acceleration.md)中详细说明的必备组件。 如果只是在部署过程中安装这些必备组件，则需要从头开始。

   有两个可用的 GPU 传递选项：“直接设备分配 (DDA)”和“GPU 半虚拟化 (GPU-PV)”，具体取决于分配给部署的 GPU 适配器 。 下面展示了每种方法的示例。

   对于“直接设备分配”方法，请选择要分配给 Linux 虚拟机的 GPU 处理器的数量。

   ![启用了直接设备分配 GPU 的配置设置。](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-direct-device-assignment.png)

   对于半虚拟化方法，不需要进行其他设置。

   ![启用了半虚拟化 GPU 的配置设置。](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >启用硬件设备传递可能会增加安全风险。 Microsoft 建议使用 GPU 供应商提供的设备缓解驱动程序（如果适用）。 有关详细信息，请参阅[使用离散设备分配部署图形设备](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)。

   对设置满意后，选择“下一步”。

1. 在“2.3 部署”选项卡上，你可以观察部署进度。 完整的过程包括下载 Azure IoT Edge for Linux on Windows 程序包，安装该程序包，配置主机设备以及设置 Linux 虚拟机。 此过程可能需要几分钟才能完成。 下面描绘了一个成功的部署。

   ![成功的部署所显示的每个步骤都会带有绿色的复选标记和“完成”标签](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

部署完成后，便可预配设备了。 选择“下一步:连接”以转到“3.连接”选项卡，可以在这里处理 Azure IoT Edge 设备预配。

---

## <a name="provision-your-device"></a>预配你的设备

选择一种用于预配你的设备的方法，并按照相应部分中的说明进行操作。 本文提供了通过对称密钥或 x.509 证书手动预配设备的步骤。 如果要将自动预配与 DPS 一起使用，请单击相应的链接来完成预配。

你可以使用 Windows Admin Center 或已提升权限的 PowerShell 会话来预配你的设备。

* 手动预配：

  * [使用 IoT Edge 设备的连接字符串进行手动预配](#manual-provisioning-using-the-connection-string)
  * [使用 x.509 证书进行手动预配](#manual-provisioning-using-x509-certificates)

* 自动预配：

  * [使用设备预配服务 (DPS) 和对称密钥进行自动预配](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md#configure-the-device-with-provisioning-information)
  * [使用 DPS 和 X.509 证书进行自动预配](how-to-provision-devices-at-scale-linux-on-windows-x509.md#configure-the-device-with-provisioning-information)
  * [使用 DPS 和 TPM 证明进行自动预配](how-to-auto-provision-tpm-linux-on-windows.md#configure-the-device-with-provisioning-information)

### <a name="manual-provisioning-using-the-connection-string"></a>使用连接字符串进行手动预配

本部分介绍如何使用 IoT Edge 设备的连接字符串手动预配设备。

请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤注册设备并检索其连接字符串（如果还没有这样做）。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在目标设备上权限提升的 PowerShell 会话中运行以下命令。 将占位符文本替换为你自己的值。

```powershell
Provision-EflowVm -provisioningType ManualConnectionString -devConnString "<CONNECTION_STRING_HERE>"
```

有关 `Provision-EflowVM` 命令的详细信息，请参阅[适用于 IoT Edge for Linux on Windows 的 PowerShell 函数](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm)。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在“Azure IoT Edge 设备预配”窗格上，从“预配方法”下拉列表中选择“连接字符串(手动)”。

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心的“IoT Edge”选项卡。

1. 单击设备的设备 ID。 复制“主连接字符串”字段。

1. 提供注册设备后，从 IoT 中心检索到的设备连接字符串。

1. 选择“使用所选方法进行预配”。

   ![粘贴设备的连接字符串后，选择“使用所选方法进行预配”](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. 预配完成后，选择“完成”。 将返回到主仪表板。 现在，应会看到一个列出的类型为 `IoT Edge Devices` 的新设备。 你可以选择该 IoT Edge 设备以连接到它。 在“概述”页上以后，即可查看设备的“IoT Edge 模块列表”和“IoT Edge 状态”。

---

### <a name="manual-provisioning-using-x509-certificates"></a>使用 x.509 证书进行手动预配

本部分介绍如何在 IoT Edge 设备上使用 X.509 证书手动预配设备。

请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤准备所需的证书并注册设备（如果还没有这样做）。 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

在目标设备上准备好设备标识证书及其匹配的私钥。 了解这两个文件的绝对路径。

在目标设备上权限提升的 PowerShell 会话中运行以下命令。 将占位符文本替换为你自己的值。

```powershell
Provision-EflowVm -provisioningType ManualX509 -iotHubHostname "<HUB HOSTNAME>" -deviceId "<DEVICE ID>" -identityCertPath "<ABSOLUTE PATH TO IDENTITY CERT>" -identityPrivKeyPath "<ABSOLUTE PATH TO PRIVATE KEY>"
```

有关 `Provision-EflowVM` 命令的详细信息，请参阅[适用于 IoT Edge for Linux on Windows 的 PowerShell 函数](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm)。

# <a name="windows-admin-center"></a>[Windows 管理中心](#tab/windowsadmincenter)

1. 在“Azure IoT Edge 设备预配”窗格中，从预配方法下拉列表中选择“ManualX509” 。

   ![选择使用 X.509 证书进行手动预配](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-manual-x509.png)

1. 提供所需的参数：

   * IoT 中心主机名：此设备注册到的 IoT 中心的名称。
   * 设备 ID：注册此设备时所用的名称。
   * 证书文件：上传设备标识证书，该证书将被移动到虚拟机并用于预配设备。
   * 私钥文件：上传匹配的私钥文件，该文件将被移动到虚拟机并用于预配设备。

1. 选择“使用所选方法进行预配”。

1. 预配完成后，选择“完成”。 将返回到主仪表板。 现在，应会看到一个列出的类型为 `IoT Edge Devices` 的新设备。 你可以选择该 IoT Edge 设备以连接到它。 在“概述”页上以后，即可查看设备的“IoT Edge 模块列表”和“IoT Edge 状态”。

---

## <a name="verify-successful-configuration"></a>验证成功的配置

验证是否已在 IoT Edge 设备上成功安装并配置了 IoT Edge for Linux on Windows。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

> [!IMPORTANT]
> 如果使用 IoT Edge for Linux on Windows PowerShell 公共函数，请确保将目标设备上的执行策略设置为 `AllSigned`。 确保满足[适用于 IoT Edge for Linux on Windows 的 PowerShell 函数](reference-iot-edge-for-linux-on-windows-functions.md)的所有先决条件。

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

* 转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
* 了解如何[在 IoT Edge for Linux on Windows 虚拟机上管理证书](how-to-manage-device-certificates.md)，以及如何将文件从主机 OS 传输到 Linux 虚拟机。
* 了解如何[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。
