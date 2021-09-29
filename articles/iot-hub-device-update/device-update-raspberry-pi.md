---
title: 使用 Raspberry Pi 3 B + Reference Yocto Image 的 Device Update for Azure IoT Hub 教程 | Microsoft Docs
description: 通过 Raspberry Pi 3 B+ Reference Yocto Image 开始使用 Device Update for Azure IoT Hub。
author: ValOlson
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: f1b5196d422d2bc4a961c2b45ed814ee5555f2c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643833"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>使用 Raspberry Pi 3 B + Reference Image 的 Device Update for Azure IoT Hub 教程

Device Update for IoT Hub 支持两种形式的更新：基于映像的更新和基于包的更新。

映像更新在设备的最终状态中提供更高级别的置信度。 通常可以更轻松地在预生产环境和生产环境之间复制映像更新的结果，因为它不会带来与包及其依赖项相同的挑战。 由于其原子性质，还可以轻松采用 A/B 故障转移模型。

本教程将逐步指导你使用 Raspberry Pi 3 B+ 板上的 Device Update for IoT Hub 完成基于映像的端到端更新。 

在本教程中，将了解如何：
> [!div class="checklist"]
> * 下载映像
> * 将标记添加到 IoT 设备
> * 导入更新
> * 创建设备组
> * 部署映像更新
> * 监视更新部署

## <a name="prerequisites"></a>先决条件
* 创建[设备更新帐户和实例](create-device-update-account.md)，包括配置 IoT 中心（如果尚未这样做）。

## <a name="download-image"></a>下载映像

我们在[设备更新 GitHub 发布页面](https://github.com/Azure/iot-hub-device-update/releases)上的“资产”中提供了示例图像。 .gz 文件是可刷写到 Raspberry Pi B3+ 板上的基础映像，swUpdate 文件是要通过 Device Update for IoT Hub 导入的更新。 

## <a name="flash-sd-card-with-image"></a>使用映像刷写 SD 卡

使用最喜欢的 OS 刷写工具在将在 Raspberry Pi 3 B+ 设备中使用的 SD 卡上安装 Device Update 基础映像 (adu-base-image)。

### <a name="using-bmaptool-to-flash-sd-card"></a>使用 bmaptool 刷写 SD 卡

1. 如果尚未安装，请安装 `bmaptool` 实用工具。

   ```shell
   sudo apt-get install bmap-tools
   ```

2. 在 `/dev` 中找到 SD 卡的路径。 路径应类似于 `/dev/sd*` 或 `/dev/mmcblk*`。 可以使用 `dmesg` 实用工具来帮助找到正确的路径。

3. 需要先卸载所有已装载的分区，然后再进行刷写。

   ```shell
   sudo umount /dev/<device>
   ```

4. 请确保你对该设备具有写入权限。

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. 可选。 为了更快地刷写，请将 bimap 文件与映像文件一起下载，并将其放在同一目录中。

6. 刷写 SD 卡。

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Device Update for Azure IoT Hub 软件受以下许可条款的约束：
   * [Device update for IoT Hub 许可证](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [交付优化客户端许可证](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
使用代理之前，请阅读许可条款。 安装和使用即表示你接受这些条款。 如果不同意许可条款，请不要使用 Device Update for IoT Hub 代理。

## <a name="create-device-or-module-in-iot-hub-and-get-connection-string"></a>在 IoT 中心创建设备或模块并获取连接字符串

现在，需要将设备添加到 Azure IoT 中心。  在 Azure IoT 中心内，将为设备生成一个连接字符串。

1. 在 Azure 门户中，启动 Azure IoT 中心。
2. 创建新设备。
3. 在页面左侧，导航到“IoT 设备”，然后选择“新建”。
4. 在“设备 ID”下提供设备的名称 - 确保选中“自动生成密钥”复选框。
5. 选择“保存”。
6. 现在，你将返回到“设备”页，你创建的设备应在列表中。 
7. 获取设备连接字符串：
    - 选项 1：将设备更新代理用于模块标识：从相同的“设备”页上，单击顶部的“+ 添加模块标识”。 创建名为“IoTHubDeviceUpdate”的新设备更新模块，选择适用于你的用例的其他选项，然后单击“保存”。 单击新创建的“模块”，然后在模块视图中选择“主连接字符串”旁边的“复制”图标。
    - 选项 2：将设备更新代理用于设备标识：在设备视图中，选择“主连接字符串”旁边的“复制”图标。
8. 将复制的字符粘贴到某个位置，以便以后在下面的步骤中使用。
   此复制的字符串为设备连接字符串。

## <a name="provision-connection-string-on-sd-card"></a>在 SD 卡上预配连接字符串

1. 请确保 Raspberry Pi3 已连接到网络。
2. 在 PowerShell 中，使用以下命令通过 ssh 连接到设备
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. 输入“root”作为登录名，并将密码保留为空。
5. 成功通过 ssh 连接到设备后，运行以下命令
 
使用连接字符串替换 `<device connection string>`
 ```markdown
    echo "connection_string=<device connection string>" > /adu/adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> /adu/adu-conf.txt
    echo "aduc_model=RefDevice" >> /adu/adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>在 Device Update IoT 中心中连接设备

1. 在页面左侧，选择“IoT 设备”。
2. 选择包含设备名的链接。
3. 如果使用 IoT 设备标识直接连接到设备更新，请在页面顶部选择“设备孪生”。 否则，选择上面创建的模块，并单击其“模块孪生”。
4. 在设备孪生属性的“已报告”部分下，查找 Linux 内核版本。
对于未从 Device Update 收到更新的新设备，[DeviceManagement:DeviceInformation:1.swVersion](device-update-plug-and-play.md) 值将表示设备上正在运行的固件版本。  将更新应用到设备后，Device Update 将使用 [AzureDeviceUpdateCore:ClientMetadata:4.installedUpdateId](device-update-plug-and-play.md) 属性值来表示设备上正在运行的固件版本。
5. 基础映像文件和更新映像文件的文件名中包含版本号。

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

请在下面的“导入更新”步骤中使用该版本号。

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

1. 下载[示例导入清单](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0/TutorialImportManifest_Pi.json)和[示例映像更新](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0-rc1/adu-update-image-raspberrypi3-0.6.5073.1.swu)。
2. 登录 [Azure 门户](https://portal.azure.com/)并导航到具有设备更新的 IoT 中心。 然后，从左侧导航栏中选择“自动设备管理”下的“设备更新”选项。
3. 选择“更新”选项卡。
4. 选择“+ 导入新更新”。
5. 在“选择导入清单文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择你在上面的步骤 1 中下载的示例导入清单。  接下来，在“选择一个或多个更新文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择你在上面的步骤 1 中下载的示例更新文件。
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="显示更新文件选择的屏幕截图。" lightbox="media/import-update/select-update-files.png":::

5. 在“选择存储容器”下选择文件夹图标或文本框。 然后选择适当的存储帐户。

6. 如果已创建容器，可以重复使用它。 （否则，请选择“+ 容器”，为更新创建新的存储容器。）  选择要使用的容器，然后单击“选择”。
  
  :::image type="content" source="media/import-update/container.png" alt-text="显示容器选择的屏幕截图。" lightbox="media/import-update/container.png":::

7. 选择“提交”以开始导入过程。

8. 导入过程开始，屏幕更改为“导入历史记录”部分。 选择“刷新”以查看进度，直到完成导入过程。 这可能会在几分钟内完成，但可能需要更长时间，具体取决于更新的大小。
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="显示更新导入序列的屏幕截图。" lightbox="media/import-update/update-publishing-sequence-2.png":::

9. 当“状态”列指示导入已成功时，请选择“部署准备就绪”标头。 现在应会在列表中看到导入的更新。

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

现在，你已经在 Raspberry Pi 3 B+ 设备上使用 Device Update for IoT Hub 成功完成了端到端映像更新。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要设备更新帐户、实例、IoT 中心和 IoT 设备，请进行清理。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [模拟器引用代理](device-update-simulator.md)
