---
title: 使用适用于 VS Code 的 Azure IoT 工具创建 Azure IoT 中心| Microsoft Docs
description: 了解如何使用适用于 Visual Studio Code 的 Azure IoT 工具在资源组中创建 Azure IoT 中心。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: e8d6bebffc46b0b158aeb975ae1e32a863b956d2
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109655790"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>使用适用于 Visual Studio Code 的 Azure IoT 工具创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文介绍了如何使用[适用于 Visual Studio Code 的 Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)创建 Azure IoT 中心。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要完成本文，需要以下各项：

- Azure 订阅。 如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [Visual Studio Code](https://code.visualstudio.com/)

- 为 Visual Studio Code 安装的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。


## <a name="create-an-iot-hub-and-device-in-an-iot-project"></a>在 IoT 项目中创建 IoT 中心和设备

以下步骤介绍如何在 Visual Studio Code 的 IoT 项目中创建 IoT 中心并将设备注册到该中心。

而不是从 Azure 门户预配 Azure IoT 中心和设备。 可以在 VS Code 中进行，无需离开开发环境。 本部分的步骤说明如何实现。

1. 在打开的新项目窗口中，单击 `F1` 以打开命令面板，键入并选择“Azure IoT Device Workbench: 预配 Azure 服务…”。遵照分步指南完成预配 Azure IoT 中心和创建 IoT 中心设备的操作。

    ![预配命令](media/iot-hub-create-use-iot-toolkit/provision.png)

    > [!NOTE]
    > 如果尚未登录 Azure， 请遵循弹出的通知登录。

1. 选择要使用的订阅。

    ![选择子项](media/iot-hub-create-use-iot-toolkit/select-subscription.png)

1. 然后，选择现有资源组或创建新[资源组](../azure-resource-manager/management/overview.md#terminology)。

    ![选择资源组](media/iot-hub-create-use-iot-toolkit/select-resource-group.png)

1. 在指定的资源组中，按照提示选择现有的 IoT 中心或创建新的 Azure IoT 中心。

    ![选择“IoT 中心”步骤](media/iot-hub-create-use-iot-toolkit/iot-hub-provision.png)

    ![选择“IoT 中心”](media/iot-hub-create-use-iot-toolkit/select-iot-hub.png)

    ![已选择“IoT 中心”](media/iot-hub-create-use-iot-toolkit/iot-hub-selected.png)

1. 在“输出”窗口中，你会看到预配的 Azure IoT 中心。

    ![已预配 IoT 中心](media/iot-hub-create-use-iot-toolkit/iot-hub-provisioned.png)

1. 在预配的 Azure IoT 中心内选择或新建 IoT 中心设备。

    ![选择 IoT 设备步骤](media/iot-hub-create-use-iot-toolkit/iot-device-provision.png)

    ![选择已预配的 IoT 设备](media/iot-hub-create-use-iot-toolkit/select-iot-device.png)

1. 现已预配 Azure IoT 中心并在其中创建了设备。 此外，设备连接字符串会保存在 VS Code 中。

    ![预配完成](media/iot-hub-create-use-iot-toolkit/provision-done.png)



## <a name="create-an-iot-hub-without-an-iot-project"></a>在不使用 IoT 项目的情况下创建 IoT 中心

以下步骤介绍如何在不使用 Visual Studio Code 中的 IoT 项目的情况下创建 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图。

2. 在资源管理器底部，展开“Azure IoT 中心”部分  。 

   ![展开 Azure IoT 中心设备](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. 单击“Azure IoT 中心”部分标题中的“...” 。 如果没有看到省略号，请将鼠标悬停在标题处。 

4. 选择“创建 IoT 中心”  。

5. 弹出窗口将显示在右下角，以便让你首次登录 Azure。

6. 选择 Azure 订阅。 

7. 选择资源组。

8. 选择位置。

9. 选择定价层。

10. 为 IoT 中心输入一个全局唯一名称。

11. 稍等几分钟，待 IoT 中心创建完毕。

## <a name="next-steps"></a>后续步骤

现在，你已使用适用于 Visual Studio Code 的 Azure IoT 工具部署了 IoT 中心。 若要进一步探索，请查看以下文章：

* [使用适用于 Visual Studio Code 的 Azure IoT 工具在设备和 IoT 中心之间发送和接收消息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

* [使用适用于 Visual Studio Code 的 Azure IoT 工具进行 Azure IoT 中心设备管理](iot-hub-device-management-iot-toolkit.md)

* [请参阅 Azure IoT Hub for VS Code wiki 页](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
