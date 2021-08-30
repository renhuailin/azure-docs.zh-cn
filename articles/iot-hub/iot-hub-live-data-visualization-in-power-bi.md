---
title: Azure IoT 中心的实时数据可视化 - Power BI
description: 使用 Power BI 可视化从传感器收集的温度和湿度数据并将其发送到 Azure IoT 中心。
author: robinsh
keywords: 实时数据可视化, 即时数据可视化, 传感器数据可视化
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/23/2021
ms.author: robinsh
ms.openlocfilehash: 4bfd0f761538516d771fd1bb00eb9dc625f46ce8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015606"
---
# <a name="tutorial-visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>教程：使用 Power BI 可视化 Azure IoT 中心的实时传感器数据

了解如何使用 Microsoft Power BI 可视化 Azure IoT 中心接收的实时传感器数据。 为此，请配置 Azure 流分析作业，来使用 IoT 中心内的数据并将其路由到 Power BI 中的数据集。 

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png" alt-text="端到端关系图" border="false":::

 [Microsoft Power BI](https://powerbi.microsoft.com/) 是一种数据可视化工具，可用于对大型数据集执行自助服务和企业商业智能 (BI)。 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/#overview)是一项完全托管的实时分析服务，旨在帮助你分析和处理可用于获取见解、生成报表或触发警报和操作的快速移动数据流。 

将在本教程中执行以下任务：

> [!div class="checklist"]
> * 在 IoT 中心创建使用者组。
> * 创建并配置 Azure 流分析作业，以便从使用者组读取温度遥测数据并将其发送到 Power BI。
> * 在 Power BI 中创建温度数据的报表，并将其共享到 Web。

## <a name="prerequisites"></a>先决条件

* 使用你选择的开发语言完成[发送遥测数据](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)快速入门之一。 或者，可使用任何发送温度遥测数据的设备应用，例如 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)或[嵌入式设备](../iot-develop/quickstart-devkit-mxchip-az3166.md)快速入门之一。 这些文章阐述下列要求：
  
  * 一个有效的 Azure 订阅。
  * 订阅中的 Azure IoT 中心。
  * 一个可向 Azure IoT 中心发送消息的客户端应用。

* 一个 Power BI 帐户。 （[免费试用 Power BI](https://powerbi.microsoft.com/)）

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>创建、配置和运行流分析作业

让我们首先创建一个流分析作业。 创建作业后，可以定义输入、输出和用于检索数据的查询。

### <a name="create-a-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”。 在搜索框中键入“流分析作业”，然后从下拉列表中选择它。 在“流分析作业”概述页面中，选择“创建” 

2. 为作业输入以下信息。

   **作业名称**：作业的名称。 该名称必须全局唯一。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：与资源组使用同一位置。

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png" alt-text="在 Azure 中创建流分析作业":::

3. 选择“创建” 。

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

1. 打开流分析作业。

2. 在“作业拓扑”下选择“输入”。

3. 在“输入”窗格中选择“添加流输入”，然后从下拉列表中选择“IoT 中心”  。 在新输入窗格中输入以下信息：

   输入别名：提供唯一的输入别名。

   从订阅选择 IoT 中心：选中此单选按钮。

   **订阅**：选择本教程使用的 Azure 订阅。

   IoT 中心：选择用于本教程的 IoT 中心。

   **终结点**：选择“消息传送”  。

   共享访问策略名称：选择希望流分析作业对你的 IoT 中心使用的共享访问策略的名称。 对于本教程，可以选择“服务”。 “服务”策略是在新 IoT 中心内默认创建的，它授予在 IoT 中心公开的云端终结点上发送和接收数据的权限。 有关详细信息，请参阅[访问控制和权限](iot-hub-dev-guide-sas.md#access-control-and-permissions)。

   共享访问策略密钥：系统会根据你选择的共享访问策略名称自动填充此字段。

   使用者组：选择之前创建的使用者组。

   将所有其他字段保留为默认值。

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png" alt-text="向 Azure 中的流分析作业添加输入":::

4. 选择“保存”。 

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下选择“输出”。

2. 在“输出”窗格中，选择“添加”，然后在下拉列表中选择“Power BI”  。

3. 在“Power BI - 新建输出”窗格中选择“授权”，然后按照提示登录到你的 Power BI 帐户 。

4. 登录到 Power BI 后，输入以下信息：

   输出别名：输出的唯一别名。

   组工作区：选择目标组工作区。

   数据集名称：输入数据集名称。

   表名称：输入表名称。

   身份验证模式：保留默认值。

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png" alt-text="向 Azure 中的流分析作业添加输出":::

5. 选择“保存”。 

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

1. 在“作业拓扑”下选择“查询”。

2. 将 `[YourInputAlias]` 替换为作业的输入别名。

3. 将 `[YourOutputAlias]` 替换为作业的输出别名。

1. 将以下 `WHERE` 子句添加到查询的最后一行。 此行确保仅将具有温度属性的消息转发到 Power BI。

    ```sql
    WHERE temperature IS NOT NULL
    ```
1. 查询应类似于以下屏幕截图。 选择“保存查询”。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png" alt-text="向流分析作业添加查询":::

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中选择“概述”，然后选择“启动” > “现在” > “启动”   。 成功启动作业后，作业状态将从“已停止”  更改为“正在运行”  。

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png" alt-text="在 Azure 中运行流分析作业":::

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>创建并发布实现数据可视化的 Power BI 报表

以下步骤说明如何使用 Power BI 服务创建和发布报表。 如果你想在 Power BI 中使用“新外观”，可以遵循这些步骤并做出一些修改。 若要了解差异以及如何在“新外观”中导航，请参阅 [Power BI 服务的“新外观”](/power-bi/fundamentals/desktop-latest-update)。

1. 确保客户端应用在设备上运行。

2. 登录到 [Power BI](https://powerbi.microsoft.com/) 帐户，选择顶部菜单中的“Power BI 服务”。

3. 在边侧菜单中选择你使用的工作区，即“我的工作区”。

4. 在“全部”选项卡或“数据集 + 数据流”选项卡中，应该会看到在为流分析作业创建输出时指定的数据集 。

5. 将鼠标悬停在创建的数据集上，选择“更多选项”菜单（数据集名称右侧三个点），然后选择“创建报表” 。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png" alt-text="创建 Microsoft Power BI 报表":::

6. 创建折线图，显示某段时间的实时温度。

   1. 在报表创建页的“可视化”窗格中，选择折线图图标以添加折线图。 使用位于图表的侧边和角上的参考线来调整其大小和位置。

   2. 在“字段”窗格中展开一个表，该表是在为流分析作业创建输出时指定的。 

   3. 将 **EventEnqueuedUtcTime** 拖至“可视化效果”窗格中的“轴”。  

   4. 将“温度”拖至“值”。  

      已创建一个折线图。 X 轴显示 UTC 时区的日期和时间。 Y 轴显示来自传感器的温度。

      :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png" alt-text="向 Microsoft Power BI 报表添加温度折线图":::

     > [!NOTE]
     > 字段列表可能略有不同，具体取决于用于发送遥测数据的设备或模拟设备。
     >

8. 选择“保存”以保存报表。 出现提示时，输入报表的名称。 出现关于敏感度标签的提示时，可以选择“公开”，然后选择“保存” 。

10. 依然是在报表窗格中，选择“文件” > “嵌入报表” > “网站或门户”  。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-embed-report.png" alt-text="为 Microsoft Power BI 报表选择嵌入报表网站":::

    > [!NOTE]
    > 如果收到通知，让你联系管理员启用嵌入代码创建，则可能需要联系管理员。 必须先启用嵌入代码创建才能完成此步骤。
    >
    > :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png" alt-text="有关联系管理员的通知":::


11. 系统会提供一个报表链接和一个代码片段，你可将链接共享给任何需要进行报表访问的人员，而代码片段可用于将报表集成到博客或网站中。 复制“安全嵌入代码”窗口中的链接，然后关闭该窗口。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/copy-secure-embed-code.png" alt-text="复制嵌入报表链接":::

12. 打开 Web 浏览器，然后将链接粘贴至地址栏。

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png" alt-text="发布 Microsoft Power BI 报表":::

Microsoft 还提供 [Power BI 移动应用](https://powerbi.microsoft.com/documentation/powerbi-power-bi-apps-for-mobile-devices/)，用于在移动设备上查看 Power BI 仪表板和报表并进行交互。

## <a name="cleanup-resources"></a>清理资源

在本教程中，你已在 Power BI 中创建资源组、IoT 中心、流分析作业和数据集。 

如果你打算完成其他教程，可能需要保留资源组和 IoT 中心，稍后重复使用。 

如果不再需要 IoT 中心或创建的其他资源，可在门户中删除资源组。 为此，请选择资源组，然后选择“删除资源组”。 如果要保留 IoT 中心，可从资源组的“概述”窗格中删除其他资源。 为此，请右键单击资源，从上下文菜单中选择“删除”，然后按照提示操作。 

### <a name="use-the-azure-cli-to-clean-up-azure-resources"></a>使用 Azure CLI 清理 Azure 资源

若要删除资源组及其所有资源，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令。

```azurecli-interactive
az group delete --name {your resource group}
```

### <a name="clean-up-power-bi-resources"></a>清理 Power BI 资源

你在 Power BI 中创建了一个数据集 PowerBiVisualizationDataSet。 要删除该数据集，请登录到你的 [Power BI](https://powerbi.microsoft.com/) 帐户。 在左侧菜单的“工作区”下，选择“我的工作区” 。 在“数据集 + 数据流”选项卡下的数据集列表中，将鼠标悬停在 PowerBiVisualizationDataSet 数据集上 。 选择数据集名称右侧出现的三个垂直点以打开“更多选项”菜单，然后选择“删除”并按照提示操作 。 删除数据集时，报表也随之删除。

## <a name="next-steps"></a>后续步骤

在本教程中，你通过执行以下任务学习了如何使用 Power BI 可视化 Azure IoT 中心中的实时传感器数据：

> [!div class="checklist"]
> * 在 IoT 中心创建使用者组。
> * 创建并配置 Azure 流分析作业，以便从使用者组读取温度遥测数据并将其发送到 Power BI。
> * 在 Power BI 中配置温度数据的报表，并将其共享到 Web。

有关可视化 Azure IoT 中心中的数据的另一种方法，请参阅以下文章。

> [!div class="nextstepaction"]
> [使用 Web 应用可视化 Azure IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-web-apps.md)。