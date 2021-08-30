---
title: 安装和使用 Azure IoT 资源管理器 | Microsoft Docs
description: 安装 Azure IoT 资源管理器工具，并使用它与连接到 IoT 中心的 IoT 即插即用设备交互。 尽管本文重点介绍如何使用 IoT 即插即用设备，但你可以将此工具与连接到中心的任何设备一起使用。
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0b4745a2f279dae515f6b9b085679562b0fcec33
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406397"
---
# <a name="install-and-use-azure-iot-explorer"></a>安装和使用 Azure IoT 资源管理器

Azure IoT 资源管理器是一种图形工具，用于与连接到 IoT 中心的设备进行交互。 本文重点介绍如何使用工具测试 IoT 即插即用设备。 在本地计算机上安装该工具后，可以使用该工具来连接到中心。 可以使用该工具查看设备正在发送的遥测数据，使用设备属性以及调用命令。

本文介绍如何：

- 安装和配置 Azure IoT 资源管理器工具。
- 使用该工具与 IoT 即插即用设备进行交互并对其进行测试。

有关使用该工具的更多常规信息，请参阅 GitHub [自述文件](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)。

要使用 Azure IoT 资源管理器工具，需要：

- Azure IoT 中心。 可以通过多种方式将 IoT 中心添加到 Azure 订阅，例如[使用 Azure CLI 创建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 你需要 IoT 中心连接字符串来运行 Azure IoT 资源管理器工具。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在 IoT 中心注册的设备。 可以使用 IoT 资源管理器在 IoT 中心内创建和管理设备注册。

## <a name="install-azure-iot-explorer"></a>安装 Azure IoT 资源管理器

请转到 [Azure IoT 资源管理器版本](https://github.com/Azure/azure-iot-explorer/releases)，展开最新版本的资产列表。 下载并安装最新版应用程序。

>[!Important]
> 更新到版本 0.13.x，以基于 [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) 解析任何存储库中的模型

## <a name="use-azure-iot-explorer"></a>使用 Azure IoT 资源管理器

对于设备，你可以连接你自己的设备，也可以使用其中一个示例模拟设备。 有关使用不同语言编写的一些模拟设备示例，请参阅[将示例 IoT 即插即用设备应用程序连接到 IoT 中心](../iot-develop/tutorial-connect-device.md)教程。

### <a name="connect-to-your-hub"></a>连接到中心

首次运行 Azure IoT 资源管理器时，系统将提示你提供 IoT 中心的连接字符串。 添加连接字符串后，选择“连接”。 通过更新连接字符串，可以使用该工具的设置来切换到另一个 IoT 中心。

IoT 即插即用设备的模型定义存储在公共存储库、连接的设备或本地文件夹中。 默认情况下，该工具会在公共存储库和连接的设备中查找模型定义。 可以在“设置”中添加和删除源，或配置源的优先级：

要添加源，请执行以下操作：

1. 转到“主页/IoT 即插即用设置”
2. 选择“添加”，然后从存储库或本地文件夹中选择源。

要删除源，请执行以下操作：

1. 转到“主页/IoT 即插即用设置”
2. 查找要删除的源。
3. 选择 X 将其删除。

要更改源优先级，请执行以下操作：

可以将模型定义源之一拖放到列表中的其他排名。

### <a name="view-devices"></a>查看设备

该工具连接到 IoT 中心后，将显示“设备”列表页，其中列出了注册到 IoT 中心的设备标识。 可以在列表中选择任何条目以查看详细信息。

在“设备”列表页上，可以执行以下操作：

- 选择“新建”以向中心注册新设备。 然后输入设备 ID。 使用默认设置自动生成身份验证密钥，并启用与中心的连接。
- 选择一个设备，然后选择“删除”以删除设备标识。 完成此操作之前请查看设备详细信息，以确保在删除正确的设备标识。

## <a name="interact-with-a-device"></a>与设备交互

在“设备”列表页上，选择“设备 ID”列中的值，查看已注册设备的详细信息页。  对于每个设备，有两个部分：“设备”和“数字”。 

### <a name="device"></a>设备

本部分包括“设备标识”、“设备克隆”、“遥测数据”、“直接方法”、“云到设备消息”、“模块标识”选项卡。     

- 可以在“设备标识”选项卡上查看和更新[设备标识](../iot-hub/iot-hub-devguide-identity-registry.md)信息。
- 可以在“设备孪生”选项卡上访问[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)信息。
- 如果设备已连接并主动发送数据，则可以在“遥测”选项卡上查看[遥测](../iot-hub/iot-hub-devguide-messages-read-builtin.md)数据。
- 可以在“直接方法”选项卡上对设备调[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
- 可以在“云到设备的消息”选项卡上发送[云到设备的消息](../iot-hub/iot-hub-devguide-messages-c2d.md)。
- 可以访问[模块孪生](../iot-hub/iot-hub-devguide-module-twins.md)信息。

### <a name="iot-plug-and-play-components"></a>IoT 即插即用组件

如果设备使用模型 ID连接到中心，则该工具将显示“IoT 即插即用组件”选项卡，可以在其中查看 **模型 ID**。

如果 **模型 ID** 在某个已配置的源（公共存储库或本地文件夹）中可用，则会显示组件列表。 选择一个组件后会显示可用的属性、命令和遥测数据。

在“组件”页上，可以查看只读属性、更新可写属性、调用命令，以及查看此组件生成的遥测数据消息。

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="在 Azure IoT 资源管理器中查看组件":::

#### <a name="properties"></a>属性

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="在 Azure IoT 资源管理器中查看属性":::

你可以在“属性（只读）”选项卡上查看在接口中定义的只读属性。可以在“属性（可写）”选项卡上更新在接口中定义的可写属性 ：

1. 转到“属性（可写）”选项卡。
1. 单击要更新的属性。
1. 为属性输入新值。
1. 预览要发送到设备的有效负载。
1. 提交更改。

提交更改后，可以跟踪更新状态：“正在同步”、“成功”或“错误”。   完成同步后，会在“报告的属性”列中看到属性的新值。 如果在完成同步之前导航到其他页，则该工具在更新完成后仍会通知你。 你还可以使用该工具的通知中心来查看通知历史记录。

#### <a name="commands"></a>命令

要将命令发送到设备，请转到“命令”选项卡：

1. 在命令列表中，展开要触发的命令。
1. 输入命令所需的任何值。
1. 预览要发送到设备的有效负载。
1. 提交命令。

#### <a name="telemetry"></a>遥测

要查看所选接口的遥测数据，请转到其“遥测数据”选项卡。

#### <a name="known-issues"></a>已知问题

有关该工具的最新版本支持的 IoT 功能的列表，请参阅[功能列表](https://github.com/Azure/azure-iot-explorer/wiki)。

## <a name="next-steps"></a>后续步骤

本操作说明文章介绍如何安装和使用 Azure IoT 资源管理器，以便与 IoT 即插即用设备交互。 建议下一步了解如何[管理 IoT 即插即用数字孪生体](../iot-develop/howto-manage-digital-twin.md)。
