---
title: 创建和管理 Azure IoT Central 仪表板 | Microsoft Docs
description: 了解如何在 Azure IoT Central 中创建和管理应用程序仪表板与个人仪表板。
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 723af65800674fcb539c5f3003b9504b02969e6f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696722"
---
# <a name="create-and-manage-dashboards"></a>创建和管理仪表板

默认的应用程序仪表板是首次转至应用程序时加载的页面。 管理员最多可以创建 10 个对所有应用程序用户可见的应用程序仪表板。 只有管理员能够创建、编辑和删除应用程序级别的仪表板。

所有用户可以创建自己的个人仪表板。 用户可以在应用程序仪表板与个人仪表板之间切换。

## <a name="create-a-dashboard"></a>创建仪表板

以下屏幕截图显示从“自定义应用程序”模板创建的应用程序中的仪表板。 可使用个人仪表板替换默认应用程序仪表板。 如果你是管理员，还可以将其替换为另一个应用程序级别的仪表板。 为此，请选择页面左上角的“新建仪表板”：

:::image type="content" source="media/howto-manage-dashboards/dashboard-custom-app.png" alt-text="显示“新建仪表板”按钮的屏幕截图。":::

选择“新建仪表板”打开仪表板编辑器。 在编辑器中，为仪表板命名并从库中选择项。 库包含可用于自定义仪表板的磁贴和仪表板基元：

:::image type="content" source="media/howto-manage-dashboards/dashboard-library.png" alt-text="该屏幕截图显示仪表板库。":::

如果你是管理员，则可以创建个人仪表板或应用程序仪表板。 所有应用程序用户都可以看到管理员创建的应用程序仪表板。 所有用户都可以创建只有他们才能看到的个人仪表板。

输入标题，然后选择要创建的仪表板类型。 [添加磁贴](#add-tiles)以自定义仪表板。

> [!TIP]
> 应用程序中至少需要有一个设备模板，才能添加用于显示设备信息的磁贴。

## <a name="manage-dashboards"></a>管理仪表板

可以创建多个个人仪表板并在它们之间进行切换，或者在多个应用程序仪表板中选择一个：

:::image type="content" source="media/howto-manage-dashboards/switch-dashboards.png" alt-text="该屏幕截图显示如何在仪表板之间切换。":::

可以编辑个人仪表板，并删除不需要的仪表板。 如果你是管理员，还可以编辑和删除应用程序级别的仪表板。

:::image type="content" source="media/howto-manage-dashboards/delete-dashboards.png" alt-text="显示如何删除仪表板的屏幕截图。":::

## <a name="add-tiles"></a>添加磁贴

以下屏幕截图显示从“自定义应用程序”模板创建的应用程序中的仪表板。 若要自定义当前仪表板，请选择“编辑”。 若要添加个人仪表板或应用程序仪表板，请选择“新建仪表板”：

:::image type="content" source="media/howto-manage-dashboards/dashboard-sample-contoso.png" alt-text="显示基于自定义应用程序模板的应用程序仪表板的屏幕截图。":::

选择“编辑”或“新建仪表板”后，仪表板处于“编辑”模式。 可以使用“编辑仪表板”面板中的工具将磁贴添加到仪表板。 可以自定义和删除仪表板上的磁贴。 例如，若要添加折线图磁贴来跟踪一个或多个设备在一段时间内报告的遥测值，请执行以下操作：

1. 选择“从视觉对象开始”、“折线图”，然后选择“添加磁贴”，或直接将磁贴拖动到画布上。
 
1. 若要配置磁贴，请选择其对应的“齿轮”按钮。 输入“标题”并选择“设备组”。 在“设备”列表中，选择要显示在磁贴上的设备。

   :::image type="content" source="media/howto-manage-dashboards/device-details.png" alt-text="显示向仪表板添加磁贴的屏幕截图。":::

1. 选择要显示在磁贴上的所有设备后，选择“更新”。

1. 在仪表板上完成添加和自定义磁贴后，选择“保存”。 这样做会退出编辑模式。

## <a name="customize-tiles"></a>自定义磁贴

若要编辑磁贴，需要处于编辑模式。 不同的“[磁贴类型](#tile-types)”具有不同的自定义选项：

* 磁贴上的“标尺”按钮可以用来更改可视化效果。 可视化效果包括折线图、条形图、饼图、上一个已知值 (LKV)、关键绩效指标 (KPI)、热度地图和地图。

* “方形”按钮用于调整磁贴大小。

* “齿轮”按钮用于配置可视化效果。 例如，对于折线图，可以选择显示图例和轴，并选择要绘制的时间范围。

## <a name="tile-types"></a>磁贴类型

下表描述了可以添加到仪表板的磁贴类型：

| 磁贴             | 说明 |
| ---------------- | ----------- |
| Markdown         | Markdown 磁贴是显示 Markdown 格式的标题和描述文本的可单击磁贴。 URL 可以是指向应用程序中其他页面的相对链接，也可以是指向外部站点的绝对链接。|
| 映像            | 图像磁贴显示自定义图像，可以单击。 URL 可以是指向应用程序中其他页面的相对链接，也可以是指向外部站点的绝对链接。|
| Label            | 标签磁贴在仪表板上显示自定义文本。 你可以选择文本的大小。 使用标签磁贴可向仪表板添加相关信息，例如说明、联系人详细信息或帮助。|
| 计数            | 计数磁贴显示设备组中的设备数。|
| 地图（遥测）              | 映射磁贴显示一个或多个设备在地图上的位置。 你还可以显示设备位置历史记录的 100 个点。 例如，可以显示设备过去一周所在的位置的采样路线。|
| 地图（属性）              | 映射磁贴显示一个或多个设备在地图上的位置。|
| KPI              |  KPI 磁贴显示一段时间内一个或多个设备的聚合遥测值。 例如，可以用它们显示一个或多个设备在过去一小时中达到的最高温度和最大压力。|
| 折线图       | 折线图磁贴绘制一段时间内一个或多个设备的一个或多个聚合遥测值。 例如，可以显示绘制一个或多个设备在过去一小时中的平均温度和压力的折线图。|
| 条形图        | 条形图磁贴绘制一段时间内一个或多个设备的一个或多个聚合遥测值。 例如，可以显示条形图来展示一个或多个设备在过去一小时中的平均温度和压力。|
| 饼图        | 饼图磁贴显示一段时间内一个或多个设备的一个或多个聚合遥测值。|
| 热度地图         | 热度地图磁贴显示一个或多个设备的相关信息，并用不同颜色加以展示。|
| 上一个已知值 | 上一个已知值磁贴显示一个或多个设备的最新遥测值。 例如，可以使用此磁贴显示一个或多个设备的最新温度、压力和湿度值。 |
| 事件历史记录    | 事件历史记录磁贴显示设备在一段时间内的事件。 例如，可以使用它们来显示过去一小时中一个或多个设备的所有阀打开和阀关闭事件。|
| 属性         |  属性磁贴显示一个或多个设备的属性和云属性的当前值。 例如，可以使用此磁贴显示设备属性，如制造商或固件版本。 |
| 状态图表         |  状态图表磁贴绘制一个或多个设备在一段时间内的变化。 例如，可以使用此磁贴显示设备温度变化等属性。 |
| 事件图表         |  事件图表磁贴显示一个或多个设备在一段时间内的遥测事件。 例如，可以使用此磁贴显示设备温度变化等属性。 |
| 状态历史记录         |  状态历史记录磁贴列出并显示状态遥测的状态变化。|
| 外部内容         |  外部内容磁贴允许从外部源加载内容。 |

目前，最多可以向支持多个设备的磁贴添加 10 个设备。

### <a name="customize-visualizations"></a>自定义可视化效果

默认情况下，折线图显示一段时间范围内的数据。 所选时间范围分为 50 个大小相等的分区。 然后按分区聚合设备数据，在所选时间范围内提供 50 个数据点。 若要查看原始数据，可以更改所选内容来查看最后 100 个值。 若要更改时间范围或选择原始数据可视化效果，请使用“配置图表”面板中的“显示范围”下拉列表：

:::image type="content" source="media/howto-manage-dashboards/display-range.png" alt-text="显示“显示范围”下拉列表的屏幕截图。":::

对于显示聚合值的磁贴，请选择“配置图表”面板中遥测类型旁边的“齿轮”按钮来选择聚合。 可以选择平均值、总数、最大值、最小值或计数。

对于折线图、条形图和饼图，可以自定义各种遥测值的颜色。 选择要自定义的遥测旁边的“调色板”按钮：

:::image type="content" source="media/howto-manage-dashboards/color-customization.png" alt-text="显示“调色板”按钮的屏幕截图。":::

对于显示字符串属性或遥测值的磁贴，可以选择文本的显示方式。 例如，如果设备将 URL 存储在字符串属性中，则可以将其显示为可单击的链接。 如果 URL 引用图像，则可以在上一个已知值或属性磁贴中呈现图像。 若要更改字符串的显示方式，请在磁贴配置中选择遥测类型或属性旁边的“齿轮”按钮。

:::image type="content" source="media/howto-manage-dashboards/string-customization.png" alt-text="显示如何更改字符串在磁贴上的显示方式的屏幕截图。":::

对于数值 KPI、LKV 和属性磁贴，可以使用条件格式根据磁贴的值自定义该磁贴的颜色。 若要添加条件格式，请在磁贴上选择“配置”，然后选择要自定义的值旁边的“条件格式”按钮：

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-1.png" alt-text="显示“条件格式”按钮的屏幕截图。":::

接下来，添加条件格式规则：

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-2.png" alt-text="显示可用内存的条件格式规则的屏幕截图。存在小于、大于以及大于或等于的规则。":::

以下屏幕截图显示了这些条件格式规则的效果：

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-3.png" alt-text="显示可用内存磁贴上的紫色背景色的屏幕截图。":::

### <a name="tile-formatting"></a>磁贴格式设置

KPI、LKV 和属性磁贴上提供此功能。 此功能可以让用户调整字体大小，选择小数精度，使用缩写数值（例如将 1,700 写为 1.7K 的格式），或在其磁贴中将字符串值换行。

:::image type="content" source="media/howto-manage-dashboards/tile-format.png" alt-text="显示磁贴格式对话框的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

现在，你已了解如何创建和管理个人仪表板，接下来可以[了解如何管理应用程序首选项](howto-manage-preferences.md)。
