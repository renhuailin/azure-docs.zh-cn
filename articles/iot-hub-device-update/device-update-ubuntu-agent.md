---
title: 使用 Ubuntu Server 18.04 x64 包代理的 Device Update for Azure IoT Hub 教程 | Microsoft Docs
description: 通过 Ubuntu Server 18.04 x64 包代理开始使用 Device Update for Azure IoT Hub。
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658722"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>使用 Ubuntu Server 18.04 x64 包代理的 Device Update for Azure IoT Hub 教程

Device Update for IoT Hub 支持两种形式的更新：基于映像的更新和基于包的更新。

基于包的更新是有针对性的更新，只改变设备上的特定组件或应用程序。 这将降低带宽的消耗，并有助于减少下载和安装更新的时间。 包更新通常可以在应用更新时减少设备的停机时间，并避免创建图像的开销。

本教程将逐步指导你使用 Device Update for IoT Hub 完成基于包的端到端更新。 在本教程中，我们将使用 Ubuntu Server 18.04 x64 的示例包代理。 即使你计划使用其他 OS 平台配置，本教程仍有助于了解 Device Update for IoT Hub 的工具和概念。 了解对端到端更新过程的介绍，然后选择你喜欢的更新形式和 OS 平台来深入了解详情。 根据此教程，你可以使用 Device Update for IoT Hub 来更新 Azure IoT 或 Azure IoT Edge 设备。 

在本教程中，将了解如何：
> [!div class="checklist"]
> * 配置设备更新包存储库
> * 下载并安装设备更新代理及其依赖项
> * 将标记添加到 IoT 设备
> * 导入更新
> * 创建设备组
> * 部署包更新
> * 监视更新部署

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
* 对 IoT 中心的访问权限。 建议使用 S1（标准）层或更高层级。
* 运行 Ubuntu Server 18.04 x64 并已连接到 IoT 中心的 Azure IoT 或 Azure IoT Edge 设备。
   * 如果你使用的是 Azure IoT Edge 设备，请确保它的 Edge 运行时为 v1.2.0 或更高版本 
* 如果你使用的不是 Azure IoT Edge 设备，请 [在 IoT 设备上安装最新的 `aziot-identity-service` 包（预览）](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Device Update 帐户和实例已链接到上述同一 IoT 中心。](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>配置设备更新包存储库

1. 安装与设备操作系统匹配的存储库配置。 在本教程中为 Ubuntu Server 18.04。 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. 将生成的列表复制到 sources.list.d 目录。

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. 安装 Microsoft GPG 公钥。

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>安装 Device Update .deb 代理包

1. 更新设备上的包列表

   ```shell
      sudo apt-get update
   ```

2. 安装 deviceupdate-agent 包及其依赖项

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Device Update for Azure IoT Hub 软件包受以下许可条款的约束：
   * [Device update for IoT Hub 许可证](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [交付优化客户端许可证](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
请在使用包之前阅读许可条款。 安装和使用包即表示你接受这些条款。 如果不同意许可条款，请勿使用该包。

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>使用 Azure IoT 标识服务配置 Device Update 代理（预览版）

安装所需的包后，需要使用设备的云标识和身份验证信息来预配设备。

1. 打开配置文件

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. 找到文件的预配配置部分。 取消注释“使用连接字符串手动预配”部分。 使用 IoT (Edge) 设备的连接字符串更新 connection_string 的值。 确保所有其他预配部分都已被注释禁止。


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. 使用 Ctrl + X、Y 保存并关闭文件

4. 应用配置。 

   如果你使用的是 IoT Edge 设备，请使用以下命令。 
   
   ```shell
      sudo iotedge config apply
   ```
   
   如果你使用的是 IoT 设备，并且安装了 `aziot-identity-service` 包，请使用以下命令。 
      
   ```shell
      sudo aziotctl config apply
   ```

5. 你还可以选择验证服务是否正在运行

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    输出应读取为：

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>将标记添加到设备

1. 登录到 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。

2. 从左侧导航窗格上的“IoT 设备”或“IoT Edge”中，找到 IoT 设备并导航到“设备孪生”。

3. 在“设备孪生”中，通过将任何现有的 Device Update 标记值设置为 null 来将其删除。

4. 按如下所示添加新的 Device Update 标记值。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>导入更新

1. 下载下面的 [apt 清单文件](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json)和[导入清单文件](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json)。 此 apt 清单将在你的 IoT 设备上安装 `libcurl4-doc package` 的最新可用版本。 

   或者，可以下载 [apt 清单文件](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json)和[导入清单文件](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json)。 这会将 `libcurl4-doc package` 的特定版本 v7.58.0 安装到 IoT 设备上。 

2. 在 Azure 门户中，从 IoT 中心的左侧导航栏中选择“自动设备管理”下的“设备更新”选项。

3. 选择“更新”选项卡。

4. 选择“+ 导入新更新”。

5. 在“选择导入清单文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择之前下载的导入清单。 接下来，在“选择一个或多个更新文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择之前下载的 apt 清单更新文件。
   
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

现在，你已经在 Ubuntu Server 18.04 x64 设备上使用 Device Update for IoT Hub 成功完成了端到端包更新。 

## <a name="bonus-steps"></a>额外步骤

1. 下载下面的 [apt 清单文件](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json)和[导入清单文件](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json)。 此 apt 清单将删除 IoT 设备上已安装的 `libcurl4-doc package`。 

2. 重复执行“导入更新”和“部署更新”部分

## <a name="clean-up-resources"></a>清理资源

如果不再需要设备更新帐户、实例、IoT 中心和 IoT 设备，请进行清理。 为此，可以转到每个资源，然后选择“删除”。 请注意，在清理设备更新帐户之前，需要先清理设备更新实例。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Raspberry Pi 3 B+ 上的图像更新教程](device-update-raspberry-pi.md)

