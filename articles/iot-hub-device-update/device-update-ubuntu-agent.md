---
title: 使用 Ubuntu Server 18.04 x64 包代理的 Device Update for Azure IoT Hub 教程 | Microsoft Docs
description: 通过 Ubuntu Server 18.04 x64 包代理开始使用 Device Update for Azure IoT Hub。
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 6464ad632251053ac481fbd1f6a3e1197aa470df
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121296"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>使用 Ubuntu Server 18.04 x64 包代理的 Azure IoT 中心的设备更新教程

Device Update for IoT Hub 支持两种形式的更新：基于映像的更新和基于包的更新。

基于包的更新是有针对性的更新，只改变设备上的特定组件或应用程序。 基于包的更新会降低带宽的消耗，并有助于减少下载和安装更新的时间。 包更新通常可以在应用更新时减少设备的停机时间，并避免创建图像的开销。

本端到端教程指导你使用设备更新包代理来更新 Ubuntu Server 18.04 x64 上的 Azure IoT Edge。 虽然本教程演示如何更新 IoT Edge，但你可以使用类似步骤更新其他包，例如 IoT Edge 使用的容器引擎。

即使你计划使用其他 OS 平台配置，本教程中的工具和概念仍然适用。 了解对端到端更新过程的介绍，然后选择你喜欢的更新形式和 OS 平台来深入了解详情。

在本教程中，将了解如何：
> [!div class="checklist"]
> * 下载并安装设备更新代理及其依赖项
> * 将标记添加到设备
> * 导入更新
> * 创建设备组
> * 部署包更新
> * 监视更新部署

## <a name="prerequisites"></a>先决条件

* 创建[设备更新帐户和实例](create-device-update-account.md)，包括配置 IoT 中心（如果尚未这样做）。
* [IoT Edge 设备的连接字符串](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings)。

## <a name="prepare-a-device"></a>准备设备
### <a name="using-the-automated-deploy-to-azure-button"></a>使用“自动部署到 Azure”按钮

为方便起见，本教程使用基于 [cloud-init](../virtual-machines/linux/using-cloud-init.md) 的 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)来帮助你快速设置 Ubuntu 18.04 LTS 虚拟机。 它同时安装 Azure IoT Edge 运行时和设备更新包代理，然后使用你提供的 IoT Edge 设备（先决条件）的设备连接字符串通过预配信息自动配置设备。 Azure 资源管理器模板还避免了启动 SSH 会话来完成设置的需要。

1. 若要开始，请单击下面的按钮：

   [![iotedge-vm-deploy 的“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. 在新启动的窗口中，填写可用的窗体字段：

    > [!div class="mx-imgBorder"]
    > [![显示 iotedge-vm-deploy 模板的屏幕截图](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **订阅**：要将虚拟机部署到的活动 Azure 订阅。

    **资源组**：用于包含该虚拟机及其相关资源的现有或新建的资源组。

    **DNS 标签前缀**：必需的值，用作虚拟机主机名的前缀。

    **管理员用户名**：一个用户名，将为其提供对部署的 root 特权。

    **设备连接字符串**：在所需 [IoT 中心](../iot-hub/about-iot-hub.md)内创建的设备的 [设备连接字符串](../iot-edge/how-to-register-device.md)。

    **VM 大小**：要部署的虚拟机的 [大小](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu OS 版本**：要在基础虚拟机上安装的 Ubuntu OS 版本。 保持默认值不变，因为它将设置为 Ubuntu 18.04-LTS。

    **位置**：要将虚拟机部署到的 [地理区域](https://azure.microsoft.com/global-infrastructure/locations/)，此值默认为所选资源组的位置。

    **身份验证类型**：根据偏好选择“sshPublicKey”或“password”。  

    **管理员密码或密钥**：SSH 公钥的值或密码的值，具体取决于所选的身份验证类型。

    填写所有字段后，选中页面底部的复选框以接受条款，然后选择“购买”开始部署。 

1. 验证部署是否已成功完成。 在部署完成后，留出几分钟用于安装后和配置，以完成安装 IoT Edge 和设备包更新代理。

   虚拟机资源应已部署到所选的资源组中。  请记下计算机名称，此名称应采用 `vm-0000000000000` 格式。 另外，请记下关联的“DNS 名称”，其格式应为 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com。 

    可以从 Azure 门户中新部署的虚拟机的“概述”部分获取“DNS 名称”。  

    > [!div class="mx-imgBorder"]
    > [![显示 IoT Edge VM 的 DNS 名称的屏幕截图](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > 设置后若要通过 SSH 连接到此 VM，请在命令中使用关联的“DNS 名称”：`ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>（可选）手动准备设备
下面是安装和配置设备的手动步骤，这些步骤与 [cloud-init 脚本](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt)自动执行的步骤类似。 这些步骤可用于准备物理设备。

1. 遵循有关[安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true)的说明。
   > [!NOTE]
   > 设备更新包代理不依赖于 IoT Edge。 但是，它确实依赖于安装有 IoT Edge（1.2.0 及更高版本）的 IoT 标识服务守护程序来获取标识并连接到 IoT 中心。
   >
   > 虽然本教程中未介绍，但[基于 Linux 的 IoT 设备上可以独立安装 IoT 标识服务守护程序](https://azure.github.io/iot-identity-service/packaging.html)。 安装的顺序很重要。 设备更新包代理必须在 IoT 标识服务后安装。 否则，包代理将不会注册为获得授权的组件来建立与 IoT 中心的连接。

1. 然后，安装设备更新代理 .deb 包。

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Device Update for Azure IoT Hub 软件包受以下许可条款的约束：
  * [Device update for IoT Hub 许可证](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [交付优化客户端许可证](https://github.com/microsoft/do-client/blob/main/LICENSE)

请在使用包之前阅读许可条款。 安装和使用包即表示你接受这些条款。 如果不同意许可条款，请勿使用该包。

## <a name="add-a-tag-to-your-device"></a>将标记添加到设备

1. 登录到 [Azure 门户](https://portal.azure.com)并导航到 IoT 中心。

2. 在左侧导航窗格上的“IoT Edge”中，找到你的 IoT Edge 设备并导航到“设备孪生”或“模块孪生”。

3. 在设备更新代理模块的“模块孪生”中，通过将现有“设备更新”标记值设置为 null 来删除这些标记。 如果在设备更新代理中使用设备标识，请在“设备孪生”中进行这些更改。

4. 按如下所示添加新的 Device Update 标记值。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>导入更新

1. 在 GitHub 中转到“[设备更新版本](https://github.com/Azure/iot-hub-device-update/releases)”，然后单击“资产”下拉列表。

3. 单击 `Edge.package.update.samples.zip` 进行下载。

5. 提取文件夹的内容以发现更新示例及其相应的导入清单。 

2. 在 Azure 门户中，从 IoT 中心的左侧导航栏中选择“自动设备管理”下的“设备更新”选项。

3. 选择“更新”选项卡。

4. 选择“+ 导入新更新”。

5. 在“选择导入清单文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 从先前下载的文件夹中选择 `sample-1.0.1-aziot-edge-importManifest.json` 导入清单。 接下来，在“选择一个或多个更新文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 从先前下载的文件夹中选择 `sample-1.0.1-aziot-edge-apt-manifest.json` apt 清单更新文件。
此更新操作会将设备上的 `aziot-identity-service` 和 `aziot-edge` 包更新到版本 1.2.0~rc4-1。

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="显示更新文件选择的屏幕截图。" lightbox="media/import-update/select-update-files.png":::

6. 在“选择存储容器”下选择文件夹图标或文本框。 然后选择适当的存储帐户。

7. 如果已创建容器，可以重复使用它。 （否则，请选择“+ 容器”，为更新创建新的存储容器。）  选择要使用的容器，然后单击“选择”。

   :::image type="content" source="media/import-update/container.png" alt-text="显示容器选择的屏幕截图。" lightbox="media/import-update/container.png":::

8. 选择“提交”以开始导入过程。

9. 导入过程开始，屏幕更改为“导入历史记录”部分。 选择“刷新”以查看进度，直到完成导入过程。 导入过程可能会在几分钟内完成，但可能需要更长时间，具体取决于更新的大小。

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="显示更新导入序列的屏幕截图。" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 当“状态”列指示导入已成功时，请选择“部署准备就绪”标头。 现在应会在列表中看到导入的更新。

[详细了解](import-update.md)如何导入更新。

## <a name="create-update-group"></a>创建更新组

1. 转到你之前连接到 Device Update 实例的 IoT 中心。

1. 从左侧导航栏中选择“自动设备管理”下的“设备更新”选项。

1. 选择页面顶部的“组”选项卡。

1. 选择“添加”按钮以创建新组。

1. 从列表中选择在上一步中创建的 IoT 中心标记。 选择“创建更新组”。

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="显示标记选择的屏幕截图。" lightbox="media/create-update-group/select-tag.PNG":::

[详细了解](create-update-group.md)如何添加标记和创建更新组

## <a name="deploy-update"></a>部署更新

1. 创建组后，你应该会看到可用于你的设备组的新更新，并且“可用的更新”列中有该更新的链接。 可能需要刷新一次。

1. 单击可用更新的链接。

1. 确认选择了正确的组作为目标组，并计划部署

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="选择更新" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > 默认情况下，开始日期/时间距当前时间 24 小时。 如果你希望部署更早开始，请务必选择其他日期/时间。

1. 选择“部署更新”。

1. 查看符合性图表。 此时会看到更新正在进行。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="正在进行更新" lightbox="media/deploy-update/update-in-progress.png":::

1. 成功更新设备后，应会看到符合性图表和部署详细信息更新，以反映相同的情况。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新成功" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>监视更新部署

1. 选择页面顶部的“部署”选项卡。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="“部署”选项卡" lightbox="media/deploy-update/deployments-tab.png":::

1. 选择你创建的部署以查看部署详细信息。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="部署详细信息" lightbox="media/deploy-update/deployment-details.png":::

1. 选择“刷新”以查看最新状态详细信息。 继续此过程，直到状态更改为“成功”。

现在，你已经在 Ubuntu Server 18.04 x64 设备上使用 IoT 中心设备更新成功完成了端到端包更新。 

## <a name="clean-up-resources"></a>清理资源

不再需要设备更新帐户、实例、IoT 中心和 IoT Edge 设备（如果你通过“部署到 Azure”按钮创建了 VM）时，请清理这些资源。 为此，可以转到每个资源，然后选择“删除”。 在清理设备更新帐户之前，需要先清理设备更新实例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Raspberry Pi 3 B+ 上的图像更新教程](device-update-raspberry-pi.md)
