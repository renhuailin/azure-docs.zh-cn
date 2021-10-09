---
title: 使用 Ubuntu (18.04 x64) 模拟器引用代理的 Device Update for Azure IoT Hub 教程 | Microsoft Docs
description: 通过 Ubuntu (18.04 x64) 模拟器引用代理开始使用 Device Update for Azure IoT Hub。
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: d5a860455a66dc7db137b8d0eb7aad942271ef90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566305"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>使用 Ubuntu (18.04 x64) 模拟器引用代理的 Device Update for Azure IoT Hub 教程

Device Update for IoT Hub 支持两种形式的更新：基于映像的更新和基于包的更新。

映像更新在设备的最终状态中提供更高级别的置信度。 通常可以更轻松地在预生产环境和生产环境之间复制映像更新的结果，因为它不会带来与包及其依赖项相同的挑战。 由于其原子性质，还可以轻松采用 A/B 故障转移模型。

本教程将逐步指导你使用 Device Update for IoT Hub 完成基于映像的端到端更新。 

在本教程中，将了解如何：
> [!div class="checklist"]
> * 下载并安装映像
> * 将标记添加到 IoT 设备
> * 导入更新
> * 创建设备组
> * 部署映像更新
> * 监视更新部署

## <a name="prerequisites"></a>先决条件
* 创建[设备更新帐户和实例](create-device-update-account.md)，包括配置 IoT 中心（如果尚未这样做）。

### <a name="download-and-install"></a>下载和安装

* 用于 PowerShell 的 Az (Azure CLI) cmdlet：
  * 打开 PowerShell > 安装 Azure CLI（当提示从“不受信任”来源进行安装时，选择“Y”）

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>在 Windows 设备上启用 WSL（适用于 Linux 的 Windows 子系统）

1. 在你的计算机上，以管理员身份打开 PowerShell，并运行以下命令（在每一步之后可能会要求你重启；此时请重启）：

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

（在这一步之后系统可能会提示你重启）

2. 在网页上转到 Microsoft Store 并安装 [Ubuntu 18.04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`)。

3. 启动“Ubuntu 18.04 LTS”并安装。

4. 安装后，系统将要求你设置根名称（用户名）和密码。 请确保使用易记的根名称和密码。

5. 在 PowerShell 中运行以下命令，将 Ubuntu 设置为默认 Linux 分发版：

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. 列出所有 Linux 分发版，确保 Ubuntu 是默认分发版。

```powershell
PS> wsl --list
```

7. 应会看到：Ubuntu-18.04（默认）

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>下载 Device Update Ubuntu (18.04 x64) 模拟器引用代理

可以从发行说明（[此处](https://github.com/Azure/iot-hub-device-update/releases)）中的资产部分下载 Ubuntu 参考代理。

代理有两种版本。 对于本教程，由于你练习的是基于映像的场景，因此请使用 AducIotAgentSim-microsoft-swupdate。 如果打算改为使用基于包的场景，请使用 AducIotAgentSim-microsoft-apt。

## <a name="install-device-update-agent-simulator"></a>安装 Device Update 代理模拟器

1. 启动 Ubuntu WSL 并输入以下命令（请注意，末尾有多余的空格和点）。

  ```shell
  explorer.exe .
  ```

2. 将 AducIotAgentSim-microsoft-swupdate（或 AducIotAgentSim-microsoft-apt）从本地文件夹（在 /mnt 下下载）复制到 WSL 中的主文件夹中。

3. 运行以下命令，使二进制文件成为可执行文件。

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  或

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Device Update for Azure IoT Hub 软件受以下许可条款的约束：
   * [Device update for IoT Hub 许可证](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [交付优化客户端许可证](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
使用代理之前，请阅读许可条款。 安装和使用即表示你接受这些条款。 如果不同意许可条款，请不要使用 Device Update for IoT Hub 代理。

## <a name="add-device-to-azure-iot-hub"></a>将设备添加到 Azure IoT 中心

Device Update 代理在 IoT 设备上运行后，需要将该设备添加到 Azure IoT 中心。  在 Azure IoT 中心内，将为特定设备生成一个连接字符串。

1. 在 Azure 门户中，启动 Device Update IoT 中心。
2. 创建新设备。
3. 在页面左侧，导航到“IoT 设备”，然后选择“新建”。
4. 在“设备 ID”下提供设备的名称 - 确保选中“自动生成密钥”复选框。
5. 选择“保存”。
6. 现在，你将返回到“设备”页，你创建的设备应在列表中。 选择该设备。
7. 在“设备”视图中，选择“主连接字符串”旁边的“复制”图标。
8. 将复制的字符粘贴到某个位置，以便以后在下面的步骤中使用。 此复制的字符串为设备连接字符串。

## <a name="add-connection-string-to-simulator"></a>向模拟器添加连接字符串

在新的软件设备上启动 Device Update 代理。

1. 启动 Ubuntu。
2. 运行 Device Update 代理，并指定上一节中用引号引起来的设备连接字符串：

使用连接字符串替换 `<device connection string>`
```shell
sudo ./AducIotAgentSim-microsoft-swupdate "<device connection string>"
```

或

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. 向上滚动，查找表示设备处于“空闲”状态的字符串。 “空闲”状态表示设备已准备好运行服务命令：

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>将标记添加到设备

1. 登录到 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。

2. 在左侧导航窗格上的“IoT 设备”或“IoT Edge”中，找到 IoT 设备并导航到“设备孪生”或“模块孪生”。

3. 在设备更新代理模块的“模块孪生”中，通过将现有“设备更新”标记值设置为 null 来删除这些标记。 如果在设备更新代理中使用设备标识，请在“设备孪生”中进行这些更改。

4. 按如下所示添加新的 Device Update 标记值。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>导入更新

1. 下载[示例导入清单](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0/TutorialImportManifest_Sim.json)和[示例映像更新](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0-rc1/adu-update-image-raspberrypi3-0.6.5073.1.swu)。 注意：这些是 Raspberry Pi 教程中重新使用的更新文件，由于本教程中的更新将被模拟，因此特定文件内容并不重要。 
2. 登录 [Azure 门户](https://portal.azure.com/)并导航到具有设备更新的 IoT 中心。 然后，从左侧导航栏中选择“自动设备管理”下的“设备更新”选项。

3. 选择“更新”选项卡。

4. 选择“+ 导入新更新”。

5. 在“选择导入清单文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择你在上面的步骤 1 中下载的示例导入清单。  接下来，在“选择一个或多个更新文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择你在上面的步骤 1 中下载的示例映像更新。 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="显示更新文件选择的屏幕截图。" lightbox="media/import-update/select-update-files.png":::

6. 在“选择存储容器”下选择文件夹图标或文本框。 然后选择适当的存储帐户。

7. 如果已创建容器，可以重复使用它。 （否则，请选择“+ 容器”，为更新创建新的存储容器。）  选择要使用的容器，然后单击“选择”。
  
  :::image type="content" source="media/import-update/container.png" alt-text="显示容器选择的屏幕截图。" lightbox="media/import-update/container.png":::

8. 选择“提交”以开始导入过程。

9. 导入过程开始，屏幕更改为“导入历史记录”部分。 选择“刷新”以查看进度，直到完成导入过程。 这可能会在几分钟内完成，但可能需要更长时间，具体取决于更新的大小。
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="显示更新导入序列的屏幕截图。" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 当“状态”列指示导入已成功时，请选择“部署准备就绪”标头。 现在应会在列表中看到导入的更新。

[详细了解](import-update.md)如何导入更新。

## <a name="create-update-group"></a>创建更新组

1. 转到你之前连接到 Device Update 实例的 IoT 中心。

2. 从左侧导航栏中选择“自动设备管理”下的“设备更新”选项。

3. 选择页面顶部的“组”选项卡。 

4. 选择“添加”按钮以创建新组。

5. 从列表中选择在上一步中创建的 IoT 中心标记。 选择“创建更新组”。

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="显示标记选择的屏幕截图。" lightbox="media/create-update-group/select-tag.PNG":::

[详细了解](create-update-group.md)如何添加标记和创建更新组


## <a name="deploy-update"></a>部署更新

1. 创建组后，你应该会看到新的更新可用于你的设备组，并在“挂起的更新”下显示该更新的链接。 可能需要刷新一次。 

2. 单击可用更新。

3. 确认选择了正确的组作为目标组。 计划你的部署，然后选择“部署更新”。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="选择更新" lightbox="media/deploy-update/select-update.png":::

4. 查看符合性图表。 此时会看到更新正在进行。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="正在进行更新" lightbox="media/deploy-update/update-in-progress.png":::

5. 成功更新设备后，应会看到符合性图表和部署详细信息更新，以反映相同的情况。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新成功" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>监视更新部署

1. 选择页面顶部的“部署”选项卡。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="“部署”选项卡" lightbox="media/deploy-update/deployments-tab.png":::

2. 选择你创建的部署以查看部署详细信息。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="部署详细信息" lightbox="media/deploy-update/deployment-details.png":::

3. 选择“刷新”以查看最新状态详细信息。 继续此过程，直到状态更改为“成功”。

现在，你已经通过 Ubuntu (18.04 x64) 模拟器引用代理使用 Device Update for IoT Hub 成功完成了端到端映像更新。

## <a name="clean-up-resources"></a>清理资源

如果不再需要设备更新帐户、实例、IoT 中心和 IoT 设备，请进行清理。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [故障排除](troubleshoot-device-update.md)

