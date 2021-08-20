---
title: 将 ServiceNow 与 Azure Defender for IoT 集成
description: 本教程介绍如何将 ServiceNow 与 Azure Defender for IoT 集成。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 07/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: dd4500940b7a7b009e8cfb8acfb0411f2ade3023
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719943"
---
# <a name="tutorial-integrate-servicenow-with-azure-defender-for-iot"></a>教程：将 ServiceNow 与 Azure Defender for IoT 集成

本教程帮助你了解如何将 ServiceNow 与 Azure Defender for IoT 集成，以及如何使用 ServiceNow。

Defender for IoT 与 ServiceNow 集成为 IoT 和 OT 布局提供了一个新的集中可见性、监视和控制级别。 这些桥接平台实现了对以前无法访问的 ICS 和 IoT 设备的自动设备可见性和威胁管理。

ServiceNow 配置管理数据库 (CMDB) 通过一组由 Defender for IoT 平台推送的丰富的设备属性进行了扩充和补充。 这可确保全面并持续地洞察设备环境。 这样，你便可以在单个视图中进行监视和响应。 

在本教程中，你将了解：

> [!div class="checklist"]
> * 在 ServiceNow 中下载 Defender for IoT 应用程序
> * 设置用于与 ServiceNow 通信的 Defender for IoT
> * 在 ServiceNow 中创建访问令牌
> * 将 Defender for IoT 设备属性发送到 ServiceNow
> * 使用 HTTPS 代理设置集成
> * 查看 ServiceNow 中的 Defender for IoT 检测
> * 查看连接的设备

## <a name="prerequisites"></a>必备条件

### <a name="software-requirements"></a>软件要求

可以访问 ServiceNow 和 Defender for IoT 

- ServiceNow 服务管理版本 3.0.2。

- Defender for IoT 补丁 2.8.11.1 或更高版本。

> [!Note]
> 如果已在使用 Defender for IoT 和 ServiceNow 集成，并使用本地管理控制台进行升级，则应从 ServiceNow 中清除以前从 Defender for IoT 传感器收到的数据。

### <a name="architecture"></a>体系结构

- 本地管理控制台体系结构：设置本地管理控制台，以便与 ServiceNow 的一个实例进行通信。 本地管理控制台使用 REST API 将传感器数据推送到 Defender for IoT 应用程序。

    若要将系统设置为使用本地管理控制台，需要在设置了“ServiceNow 同步”、“转发规则”和“代理”配置的传感器中禁用这些配置。

- 传感器体系结构：如果要将环境设置为包括传感器与 ServiceNow 之间的直接通信，则为每个传感器定义“ServiceNow 同步”、“转发规则”和“代理”配置（如果需要代理）。

## <a name="download-the-defender-for-iot-application-in-servicenow"></a>在 ServiceNow 中下载 Defender for IoT 应用程序

若要在 ServiceNow 中访问 Defender for IoT 应用程序，需要从 ServiceNow 应用商店下载该应用程序。 

若要在 ServiceNow 中访问 Defender for IoT 应用程序，请执行以下操作：

1. 导航到 [ServiceNow 应用商店](https://store.servicenow.com/)。

1. 搜索 `Defender for IoT` 或 `CyberX IoT/ICS Management`。

   :::image type="content" source="media/tutorial-servicenow/search-results.png" alt-text="ServiceNow 中的“搜索”屏幕的屏幕截图。":::

1. 选择应用程序。

   :::image type="content" source="media/tutorial-servicenow/cyberx-app.png" alt-text="“搜索结果”屏幕的屏幕截图。":::

1. 选择“请求应用”。

   :::image type="content" source="media/tutorial-servicenow/sign-in.png" alt-text="使用凭据登录应用程序。":::

1. 登录并下载该应用程序。

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>设置用于与 ServiceNow 通信的 Defender for IoT

配置 Defender for IoT 以将警报信息推送到 ServiceNow 表。 Defender for IoT 警报将在 ServiceNow 中显示为安全事件。 要执行此操作，可以定义一个 Defender for IoT 转发规则，以将警报信息发送到 ServiceNow。

若要将警报信息推送到 ServiceNow 表，请执行以下操作：

1. 登录到本地管理控制台。

1. 在左侧窗格中选择“转发”。

1. 选择 :::image type="icon" source="media/tutorial-servicenow/plus-icon.png" border="false"::: 按钮。

    :::image type="content" source="media/tutorial-servicenow/forwarding-rule.png" alt-text="“创建转发规则”窗口的屏幕截图。":::

1. 添加规则名称。

1. 定义 Defender for IoT 触发转发规则的条件。 使用转发规则条件可帮助确定和管理从 Defender for IoT 发送到 ServiceNow 的信息量。 提供了以下选项：

    - 严重性级别：这是要转发的最小安全级别事件。 例如，如果选择“次要”，将转发次要警报和此严重级别以上的任何警报。 级别由 Defender for IoT 预定义。

    - 协议：如果检测到的流量在特定协议上运行，则仅触发转发规则。 从下拉列表中选择所需的协议，或选择全部协议。

    - 引擎：选择所需的引擎或选择全部引擎。 将发送所选引擎发出的警报。

1. 验证是否选择了“报表警报通知”。

1. 在“操作”部分中，选择“添加”，然后选择“ServiceNow”。

    :::image type="content" source="media/tutorial-servicenow/select-servicenow.png" alt-text="从下拉选项中选择“ServiceNow”。":::

1. 输入 ServiceNow 操作参数：

    :::image type="content" source="media/tutorial-servicenow/parameters.png" alt-text="填写 ServiceNow 操作参数。":::

1. 在“操作”窗格中，设置以下参数：

      | 参数 | 说明 |
      |--|--|
      | 域 | 输入 ServiceNow 服务器 IP 地址。 |
      | 用户名 | 输入 ServiceNow 服务器用户名。 |
      | 密码 | 输入 ServiceNow 服务器密码。 |
      | 客户端 ID | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 接收的客户端 ID。 |
      | 客户端机密 | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 创建的客户端密码字符串。 |
      | 报表类型 | 事件：转发 ServiceNow 中显示的具有事件 ID 和每个警报简短说明的警报的列表。<br /><br />Defender for IoT 应用程序：转发完整警报信息，包括传感器详细信息、引擎、源和目标地址。 此信息将转发到 ServiceNow 应用程序上的 Defender for IoT。 |

1. 选择“保存”。 

现在，Defender for IoT 警报将在 ServiceNow 中显示为事件。

## <a name="create-access-tokens-in-servicenow"></a>在 ServiceNow 中创建访问令牌

要使 ServiceNow 能够与 Defender for IoT 通信，需要提供一个令牌。

需使用创建 Defender for IoT 转发规则时输入的 `Client ID` 和 `Client Secret`。 如果已将 Defender for IoT 配置为向 ServiceNow 表推送设备属性，转发规则会将警报信息转发到 ServiceNow。

## <a name="send-defender-for-iot-device-attributes-to-servicenow"></a>将 Defender for IoT 设备属性发送到 ServiceNow

配置 Defender for IoT 以将各种设备属性推送到 ServiceNow 表。 若要将属性发送到 ServiceNow，必须将本地管理控制台映射到 ServiceNow 实例。 这可以确保 Defender for IoT 平台能够与该实例进行通信和身份验证。

若要添加 ServiceNow 实例，请执行以下操作：

1. 登录到 Defender for IoT 本地管理控制台。

1. 在本地管理控制台的“集成”部分选择“系统设置”，然后选择“ServiceNow” 。

      :::image type="content" source="media/tutorial-servicenow/servicenow.png" alt-text="选择 ServiceNow 按钮的屏幕截图。":::

1. 在“ServiceNow 同步”对话框中输入以下同步参数。

    :::image type="content" source="media/tutorial-servicenow/sync.png" alt-text="“ServiceNow 同步”对话框的屏幕截图。":::

     参数 | 说明 |
    |--|--|
    | 启用同步 | 在定义参数后启用和禁用同步。 |
    | 同步频率（分钟） | 默认情况下，信息每 60 分钟推送到 ServiceNow。 最小值为 5 分钟。 |
    | ServiceNow 实例 | 输入 ServiceNow 实例 URL。 |
    | 客户端 ID | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 接收的客户端 ID。 |
    | 客户端机密 | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 创建的客户端密码字符串。 |
    | 用户名 | 输入此实例的用户名。 |
    | 密码 | 输入此实例的密码。 |

1. 选择“保存”。

通过查看“上次同步”日期，验证本地管理控制台是否已连接到 ServiceNow 实例。

:::image type="content" source="media/tutorial-servicenow/sync-confirmation.png" alt-text="通过查看上次同步日期确认是否发生通信的屏幕截图。":::

## <a name="set-up-the-integrations-using-a-https-proxy"></a>使用 HTTPS 代理设置集成

设置 Defender for IoT 和 ServiceNow 集成时，本地管理控制台和 ServiceNow 服务器使用端口 443 进行通信。 如果 ServiceNow 服务器在代理后面，则不能使用默认端口。

Defender for IoT 通过启用对用于集成的默认端口的更改在 ServiceNow 集成中支持 HTTPS 代理。

若要配置代理，请执行以下操作：

1. 在本地管理控制台上使用以下命令编辑全局属性：

    ```bash
    sudo vim /var/cyberx/properties/global.properties
    ```

2. 添加以下参数：

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. 选择“保存并退出”。

4. 使用以下命令重置本地管理控制台： 

    ```bash
    sudo monit restart all
    ```

设置配置后，将使用配置的代理转发所有 ServiceNow 数据。

## <a name="view-defender-for-iot-detections-in-servicenow"></a>查看 ServiceNow 中的 Defender for IoT 检测

本文介绍 ServiceNow 中提供的设备属性和警报信息。

若要查看设备属性，请执行以下操作：

1. 登录到 ServiceNow。

2. 导航到“CyberX 平台”。

3. 导航到“清单”或“警报” 。

   [:::image type="content" source="media/tutorial-servicenow/alert-list.png" alt-text="“清单”或“警报”的屏幕截图。":::](media/tutorial-servicenow/alert-list.png#lightbox)

## <a name="view-connected-devices"></a>查看连接的设备

查看连接的设备：

1. 选择一个设备，然后选择为该设备列出的“设备”。

    :::image type="content" source="media/tutorial-servicenow/appliance.png" alt-text="列表中所需设备的屏幕截图。":::

1. 在“设备详细信息”对话框中，选择“连接的设备”。

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何开始进行 ServiceNow 集成。 请继续了解 [Cisco 集成](integration-cisco-ise-pxgrid.md)。

> [!div class="nextstepaction"]
> [“后续步骤”按钮](integration-cisco-ise-pxgrid.md)
