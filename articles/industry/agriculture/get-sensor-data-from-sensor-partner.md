---
title: 从合作伙伴获取传感器数据
description: 本文介绍如何从合作伙伴获取传感器数据。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 474b6f8356724546105d902a15427f483f656317
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182768"
---
# <a name="get-sensor-data-from-sensor-partners"></a>从传感器合作伙伴获取传感器数据

Azure FarmBeats 可帮助你将流数据从 IoT 设备和传感器数据引入数据中心。 当前支持以下传感器设备合作伙伴。

  ![FarmBeats 合作伙伴](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

将设备数据与 Azure FarmBeats 集成可帮助你将地面数据从部署在农场中的 IoT 传感器导入数据中心。 数据在可用后，便可通过 FarmBeats 加速器进行可视化。 使用 FarmBeats 可将数据用于数据合成和机器学习/人工智能 (ML/AI) 模型构建。

若要启动传感器数据流式传输，请确保以下各项：

-  在 Azure 市场中安装了 FarmBeats。
-  确定要在农场中安装的传感器和设备。
-  如果计划使用土壤湿度传感器，请使用 FarmBeats 土壤湿度传感器放置地图来获取有关传感器数量和确切放置位置的建议。 有关详细信息，请参阅[生成地图](generate-maps-in-azure-farmbeats.md)。
- 可以从设备合作伙伴购买设备或传感器并部署在农场中。 请确保可以通过设备合作伙伴的解决方案访问传感器数据。

## <a name="enable-device-integration-with-farmbeats"></a>实现设备与 FarmBeats 的集成

启动传感器数据流式传输后，可以开始将数据导入 FarmBeats 系统的过程。 向设备提供商提供以下信息以实现与 FarmBeats 的集成：

 - API 终结点
 - 租户 ID
 - 客户端 ID
 - 客户端机密
 - EventHub 连接字符串

按照以下步骤生成以上信息：

> [!NOTE]
> 需要在 Azure 上完成这些步骤才能访问部署 FarmBeats 的 Azure 订阅。

1. 登录 https://portal.azure.com/ 。

2. 如果你使用的是 FarmBeats 版本 1.2.7 或更高版本，请跳过步骤 a、b 和 c，然后转到步骤 3。 可以通过选择 FarmBeats UI 右上角的“设置”图标来检查 FarmBeats 版本。

      a.  转到“Azure Active Directory” > “应用注册” 

      b. 选择作为 FarmBeats 部署过程的一部分而创建的“应用注册”。 它具有与 FarmBeats datahub 相同的名称。

      c. 选择“公开 API”>选择“添加客户端应用程序”并输入“04b07795-8ddb-461a-bbee-02f9e1bf7b46”以及检查“授权范围”。    这将授予对 Azure CLII (Cloud Shell) 的访问权限以执行以下步骤：

3. 打开 Cloud Shell。 此选项位于 Azure 门户右上角工具栏中。

    ![Azure 门户工具栏](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 确保环境设置为“PowerShell”。 默认情况下它设置为 Bash。

    ![PowerShell 工具栏设置](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 转到主目录。

    ```azurepowershell-interactive
    cd
    ```

6. 运行以下命令。 这会连接经过身份验证的帐户以用于 Azure AD 请求

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. 运行以下命令。 这会将脚本下载到主目录。

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. 运行以下脚本。 该脚本要求提供租户 ID，该 ID 可从“Azure Active Directory” > “概述”页获取。 

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

> [!NOTE]
> 1. 数据中心 API 终结点名称必须为小写。
> 2. 如果要复制数据中心 API 终结点的 farmbeats 网站名称 url，请确保没有尾随斜杠 (/)。

9. 按照屏幕上的说明来捕获“API 终结点”、“租户 ID”、“客户端 ID”、“客户端密码”和“EventHub 连接字符串”的值。    

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>使用生成的凭据集成设备数据

现在你已从上一部分生成了以下信息。
 - API 终结点
 - EventHub 连接字符串
 - 客户端 ID
 - 客户端机密
 - 租户 ID

需要向设备合作伙伴提供这些信息以便链接 FarmBeats。 请转到设备合作伙伴门户以便执行相同操作。 例如，如果你使用的设备来自 Davis Instruments、Teralytic 或 Pessl Instruments (Metos.at)，请按照下面所述转到相应的页面：

1. [Davis Instruments](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Pessl Instruments](https://ng.fieldclimate.com/user-api-services)

设备提供程序确认集成成功。 确认时，可以在 Azure FarmBeats 上查看所有设备和传感器。

## <a name="view-devices-and-sensors"></a>查看设备和传感器

使用以下部分可查看农场的设备和传感器。

### <a name="view-devices"></a>查看设备

当前，FarmBeats 支持以下设备：

- 节点：连接了一个或多个传感器的设备。
- 网关：连接了一个或多个节点的设备。

按照以下步骤操作：

1. 在主页中，从菜单中选择“设备”。
  “设备”页显示设备类型、型号、状态、它所在的农场以及元数据的上次更新日期。 默认情况下，农场列设置为 NULL。 可以选择将设备分配给农场。 有关详细信息，请参阅[分配设备](#assign-devices)。
2. 选择设备以查看设备属性、遥测数据和连接到设备的子设备。

    ![“设备”页](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>查看传感器

按照以下步骤操作：

1. 在主页中，从菜单中选择“传感器”。
  “传感器”页显示有关传感器类型、它连接到的农场、父设备、端口名称、端口类型和上次更新状态的详细信息。
2. 选择传感器以查看传感器属性、活动警报和来自传感器的遥测数据。

    ![传感器页](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>分配设备

传感器数据流入后，可以将它分配给部署传感器的农场。

1. 在主页中，从菜单中选择“农场”。 此时会显示“农场”列表页。
2. 选择要向其分配设备的农场，然后选择“添加设备”。
3. 此时会显示“添加设备”窗口。 选择要分配给农场的设备。

    ![“添加设备”窗口](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 选择“添加设备”。 或者，转到“设备”菜单，选择要分配给农场的设备，然后选择“关联设备” 。
5. 在“关联设备”窗口中，从下拉列表中选择农场，然后选择“应用于所有项”以将农场关联到所有选定设备 。

    ![“关联设备”窗口](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 若要将每个设备关联到不同农场，请选择“分配给农场”列中的下拉箭头，然后为每个设备行选择农场。

7. 选择“分配”以完成设备分配。

### <a name="visualize-sensor-data"></a>可视化传感器数据

按照以下步骤操作：

1. 在主页上，从菜单中选择“农场”以查看“农场”页 。
2. 选择要查看其传感器数据的“农场”。
3. 在“农场”仪表板上，可以查看遥测数据。 可以查看实时遥测数据，也可以使用“自定义范围”查看特定日期范围。

    ![“农场”仪表板](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>删除传感器

按照以下步骤操作：

1. 在主页上，从菜单中选择“传感器”以查看“传感器”页 。
2. 选择要删除的设备，然后在确认窗口中选择“删除”。

    ![突出显示“传感器删除”页和“删除”按钮的屏幕截图。](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

确认消息显示传感器已成功删除。

## <a name="delete-devices"></a>删除设备

按照以下步骤操作：

1. 在主页上，从菜单中选择“设备”以查看“设备”页 。
2. 选择要删除的设备，然后在确认窗口中选择“删除”。

    ![“删除”按钮](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>后续步骤

现在已让传感器数据流入 Azure FarmBeats 实例。 现在了解如何为农场[生成地图](generate-maps-in-azure-farmbeats.md#generate-maps)。
