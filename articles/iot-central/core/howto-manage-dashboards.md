---
title: 创建和管理 Azure IoT Central 仪表板 | Microsoft Docs
description: 了解如何创建和管理应用程序仪表板与个人仪表板。
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6c14cc00843a998990c89e7892a793d10cfdbbdf
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113650898"
---
# <a name="create-and-manage-dashboards"></a>创建和管理仪表板

默认的应用程序仪表板是首次导航到应用程序时加载的页面。 管理员最多可以创建 10 个对所有应用程序用户可见的应用程序仪表板。 只有管理员能够创建、编辑和删除应用程序级别的仪表板。

所有用户可以创建自己的个人仪表板。 用户可以在应用程序仪表板与个人仪表板之间切换。

## <a name="create-dashboard"></a>创建仪表板

以下屏幕截图显示从“自定义应用程序”模板创建的应用程序中的仪表板。 可将默认的应用程序仪表板替换为个人仪表板，如果你是管理员，则还可以将其替换为应用程序级别的其他仪表板。 为此，请选择页面左上方“+ 新建仪表板”：

:::image type="content" source="media/howto-manage-dashboards/dashboard-custom-app.png" alt-text="基于自定义应用程序模板的应用程序仪表板的屏幕截图。":::

选择“+ 新建仪表板”打开仪表板编辑器。 在编辑器中，为仪表板命名并从库中选择项。 库包含可用于自定义仪表板的磁贴和仪表板基元：

:::image type="content" source="media/howto-manage-dashboards/dashboard-library.png" alt-text="该屏幕截图显示仪表板库。":::

如果你是管理员，则可以创建个人仪表板或应用程序仪表板。 所有应用程序用户都可以看到管理员创建的应用程序仪表板。 所有用户可以创建只有他们才能看到的个人仪表板。

输入标题，然后选择要创建的仪表板类型。 [添加磁贴](#add-tiles)以自定义仪表板。

> [!TIP]
> 应用程序中至少需要包含一个设备模板，才能添加用于显示设备信息的磁贴。

## <a name="manage-dashboards"></a>管理仪表板

可以创建多个个人仪表板并在它们之间进行切换，或者在多个应用程序仪表板中选择一个：

:::image type="content" source="media/howto-manage-dashboards/switch-dashboards.png" alt-text="该屏幕截图显示如何在仪表板之间切换。":::

可以编辑个人仪表板，并删除任何不再需要的仪表板。 如果你是管理员，还可以编辑或删除应用程序级别的仪表板。

:::image type="content" source="media/howto-manage-dashboards/delete-dashboards.png" alt-text="该屏幕截图显示如何删除仪表板。":::

## <a name="add-tiles"></a>添加磁贴

以下屏幕截图显示从“自定义应用程序”模板创建的应用程序中的仪表板。 若要自定义当前仪表板，请选择“编辑”。 若要添加个人仪表板或应用程序仪表板，请选择“新建”：

:::image type="content" source="media/howto-manage-dashboards/dashboard-sample-contoso.png" alt-text="基于自定义应用程序模板的应用程序仪表板":::

选择“编辑”或“新建”后，仪表板处于“编辑”模式。 可以使用“编辑仪表板”面板中的工具将磁贴添加到仪表板，并在仪表板上自定义和删除磁贴。 例如，若要添加“折线图”磁贴来跟踪一个或多个设备在一段时间内报告的遥测值，请执行以下操作：

1. 依次选择“从视觉对象开始”、“折线图”、“添加磁贴”，或将“折线图”磁贴拖放到画布中  。
 
1. 若要配置磁贴，请选择其对应的齿轮图标。 输入标题并选择一个设备组，然后在“设备”下拉列表中选择要在磁贴上显示的设备  。

:::image type="content" source="media/howto-manage-dashboards/device-details.png" alt-text="将温度遥测磁贴添加到仪表板":::

选择要在磁贴上显示的所有值后，单击“更新”

在仪表板上添加并自定义磁贴后，选择“保存”来将更改保存到仪表板，这时会退出编辑模式。

## <a name="customize-tiles"></a>自定义磁贴

若要编辑磁贴，必须处于编辑模式。 可用的自定义选项取决于[磁贴类型](#tile-types)：

* 磁贴上的标尺图标可以用来更改可视化效果。 可视化效果包括折线图、条形图、饼图、上一个已知值 (LKV)、关键绩效指标 (KPI)、热度地图和地图。

* 方形图标用于调整磁贴大小。

* 齿轮图标用于配置可视化效果。 例如，对于折线图，可以选择显示图例和轴，并选择要绘制的时间范围。

## <a name="tile-types"></a>磁贴类型

下表描述了可以添加到仪表板的不同类型的磁贴：

| 磁贴             | 说明 |
| ---------------- | ----------- |
| Markdown         | Markdown 磁贴是显示markdown 格式的标题和描述文本的可单击磁贴。 URL 可以是指向应用程序中其他页面的相对链接，也可以是指向外部站点的绝对链接。|
| 映像            | 图像磁贴显示自定义图像，可以单击。 URL 可以是指向应用程序中其他页面的相对链接，也可以是指向外部站点的绝对链接。|
| Label            | 标签磁贴在仪表板上显示自定义文本。 你可以选择文本的大小。 使用标签磁贴向仪表板添加相关信息，例如说明、联系人详细信息或帮助。|
| 计数            | 计数磁贴显示设备组中的设备数。|
| 地图(遥测)              | 映射磁贴显示一个或多个设备在地图上的位置。 你还可以显示设备位置历史记录的 100 个点。 例如，可以显示设备过去一周所在的位置的采样路线。|
| 地图(属性)              | 映射磁贴显示一个或多个设备在地图上的位置。|
| KPI              |  KPI 磁贴显示一段时间内一个或多个设备的聚合遥测值。 例如，可以用它显示一个或多个设备在过去一小时内达到的最高温度和最大压力。|
| 折线图       | 折线图磁贴绘制一段时间内一个或多个设备的一个或多个聚合遥测值。 例如，可以显示绘制一个或多个设备在过去一小时内的平均温度和压力的折线图。|
| 条形图        | 条形图磁贴绘制一段时间内一个或多个设备的一个或多个聚合遥测值。 例如，可以显示绘制条形图来展示一个或多个设备在过去一小时内的平均温度和压力。|
| 饼图        | 饼图磁贴显示一段时间内一个或多个设备的一个或多个聚合遥测值。|
| 热度地图         | 热度地图磁贴显示一个或多个设备的相关信息，并用不同颜色加以展示。|
| 上一个已知值 | 上一个已知值磁贴显示一个或多个设备的最新遥测值。 例如，可以使用此磁贴显示一个或多个设备的最新温度、压力和湿度值。 |
| 事件历史记录    | “事件历史记录”磁贴显示设备在一段时间内的事件。 例如，可以使用它来显示最近一小时内一个或多个设备的所有阀打开和关闭事件。|
| 属性         |  属性磁贴显示一个或多个设备的属性和云属性的当前值。 例如，可以使用此磁贴显示设备属性，如设备的制造商或固件版本。 |
| 状态图表         |  状态图表绘制一个或多个设备在所设置时间范围内发生的变化。 例如，可以使用此磁贴显示设备温度变化等设备属性。 |
| 事件图表         |  事件图表显示一个或多个设备在所设置时间范围内的遥测事件。 例如，可以使用此磁贴显示设备温度变化等属性。 |
| 状态历史记录         |  状态历史记录列出并显示状态遥测的状态变化。|
| 外部内容         |  使用“外部内容”磁贴可以从外部源加载外部内容。 |

目前，最多可以向支持多个设备的磁贴添加 10 个设备。

### <a name="customizing-visualizations"></a>自定义可视化效果

默认情况下，折线图显示一段时间范围内的数据。 所选时间范围拆分为 50 个大小相同的桶，然后设备数据聚合每个桶来在所选时间范围内给出 50 个数据点。 若想查看原始数据，可以更改所选内容来查看最后 100 个值。 若要更改时间范围或选择原始数据可视化效果，请使用“配置图表”面板中的显示范围下拉列表。

:::image type="content" source="media/howto-manage-dashboards/display-range.png" alt-text="更改折线图的显示范围":::

对于显示聚合值的磁贴，请选择“配置图表”面板中遥测类型旁边的齿轮图标来选择聚合。 可以在平均值、总数、最大值、最小值和计数之间选择。

对于折线图、条形图和饼图，可以自定义不同遥测值的颜色。 选择要自定义的遥测旁边的调色板图标：

:::image type="content" source="media/howto-manage-dashboards/color-customization.png" alt-text="更改遥测值的颜色":::

对于显示字符串属性或遥测值的磁贴，可以选择文本的显示方式。 例如，如果设备将 URL 存储在字符串属性中，则可以将其显示为可单击的链接。 如果 URL 引用图像，则可以在上一个已知值或属性磁贴中呈现图像。 若要更改字符串的显示方式，请在磁贴配置中选择遥测类型或属性旁边的齿轮图标:

:::image type="content" source="media/howto-manage-dashboards/string-customization.png" alt-text="更改字符串在磁贴上的显示方式":::

对于数值 KPI、LKV 和属性磁贴，可以使用条件格式根据磁贴的当前值自定义该磁贴的颜色。 若要添加条件格式，请在磁贴上选择“配置”，然后选择要自定义的值旁边的“条件格式”图标：

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-1.png" alt-text="显示如何查找磁贴的配置选项及条件格式图标的屏幕截图":::

添加条件格式规则：

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-2.png" alt-text="显示平均流的条件格式规则的屏幕截图。有三条规则 - 小于 20 为绿色、小于 50 为黄色、超过 50 则为红色":::

以下屏幕截图显示了条件格式规则的效果：

:::image type="content" source="media/howto-manage-dashboards/conditional-formatting-3.png" alt-text="显示平均水流磁贴上红色背景色的屏幕截图。磁贴上的数字为 50.54":::

### <a name="tile-formatting"></a>磁贴格式设置

此功能在 KPI、LKV 和属性磁贴中可用，可以让用户调整字体大小，选择小数精度，使用缩写数值（例如将 1700 写为 1.7 K 的格式），或在其磁贴中将字符串值换行。

:::image type="content" source="media/howto-manage-dashboards/tile-format.png" alt-text="磁贴格式":::

## <a name="next-steps"></a>后续步骤

现在，你已了解如何创建和管理个人仪表板，接下来可以[了解如何管理应用程序首选项](howto-manage-preferences.md)。
