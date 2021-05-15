---
title: 配置 Azure IoT Central 仪表板 | Microsoft Docs
description: 作为构建者，了解如何用磁贴配置默认 Azure IoT Central 应用程序仪表板。
author: philmea
ms.author: philmea
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 8a8ba765a966409c06dbba636932f7777624f6d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864246"
---
# <a name="configure-the-application-dashboard"></a>配置应用程序仪表板

“仪表板”是连接到 IoT Central 应用程序后看到的第一个页面。 如果从其中一个行业聚焦的[应用程序模板](./concepts-app-templates.md)创建应用程序，则此应用程序就有一个可以开始使用的预定义仪表板。 如果从自定义[应用程序模板](./concepts-app-templates.md)创建应用程序，仪表板将显示一些入门提示。

> [!TIP]
> 除了默认应用程序仪表板外，用户还可以[创建多个仪表板](howto-create-personal-dashboards.md)。 这些仪表板可以仅供用户使用，也可以在应用程序的所有用户之间共享。  

## <a name="add-tiles"></a>添加磁贴

以下屏幕截图显示从“自定义应用程序”模板创建的应用程序中的仪表板。 若要自定义当前仪表板，请选择“编辑”，添加自定义个人或共享仪表板，选择“新建”：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="基于自定义应用程序模板的应用程序仪表板":::

选择“编辑”或“新建”后，仪表板处于“编辑”模式。 可以使用“编辑仪表板”面板中的工具将磁贴添加到仪表板，并在仪表板上自定义和删除磁贴。 例如，可以添加“遥测”磁贴来显示由一个或多个设备报告的当前温度：

1. 选择“设备组” ，然后在“设备”下拉列表中选择要在磁贴上显示的设备。 现在可以看到设备的可用遥测、属性和命令。

1. 如果需要，可以使用下拉列表选择要在磁贴上显示的遥测值。 可以通过选择“+ 遥测”、“+ 属性”或“+ 云属性”向磁贴添加更多项。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="将温度遥测磁贴添加到仪表板":::

选择要在磁贴上显示的所有值后，单击“添加磁贴”。 磁贴会显示在仪表板上，可以就磁贴更改可视化效果、调整大小、进行移动和配置。

在仪表板上添加并自定义磁贴后，选择“保存”来将更改保存到仪表板，这时会退出编辑模式。

## <a name="customize-tiles"></a>自定义磁贴

若要编辑磁贴，必须处于编辑模式。  可用的自定义选项取决于[磁贴类型](#tile-types)：

* 磁贴上的标尺图标可以用来更改可视化效果。 可视化效果包括折线图、条形图、饼图、上一个已知值、关键绩效指标（即 KPI）、热图和映射。

* 方形图标用于调整磁贴大小。

* 齿轮图标用于配置可视化效果。 例如，对于折线图可视化效果，可以选择显示图例和轴，并选择要绘制的时间范围。


## <a name="tile-types"></a>磁贴类型

下表描述了可以添加到仪表板的不同类型的磁贴：

| 磁贴             | 说明 |
| ---------------- | ----------- |
| Markdown         | Markdown 磁贴是显示markdown 格式的标题和描述文本的可单击磁贴。 URL 可以是指向应用程序中其他页面的相对链接，也可以是指向外部站点的绝对链接。|
| 映像            | 图像磁贴显示自定义图像，可以单击。 URL 可以是指向应用程序中其他页面的相对链接，也可以是指向外部站点的绝对链接。|
| Label            | 标签磁贴在仪表板上显示自定义文本。 你可以选择文本的大小。 使用标签磁贴向仪表板添加相关信息，例如说明、联系人详细信息或帮助。|
| 计数            | 计数磁贴显示设备组中的设备数。|
| 映射              | 映射磁贴显示一个或多个设备在地图上的位置。 你还可以显示设备位置历史记录的 100 个点。 例如，可以显示设备过去一周所在的位置的采样路线。|
| KPI              |  KPI 磁贴显示一段时间内一个或多个设备的聚合遥测值。 例如，可以用它显示一个或多个设备在过去一小时内达到的最高温度和最大压力。|
| 折线图       | 折线图磁贴绘制一段时间内一个或多个设备的一个或多个聚合遥测值。 例如，可以显示绘制一个或多个设备在过去一小时内的平均温度和压力的折线图。|
| 条形图        | 条形图磁贴绘制一段时间内一个或多个设备的一个或多个聚合遥测值。 例如，可以显示绘制条形图来展示一个或多个设备在过去一小时内的平均温度和压力。|
| 饼图        | 饼图磁贴显示一段时间内一个或多个设备的一个或多个聚合遥测值。|
| 热度地图         | 热度地图磁贴显示一个或多个设备的相关信息，并用不同颜色加以展示。|
| 上一个已知值 | 上一个已知值磁贴显示一个或多个设备的最新遥测值。 例如，可以使用此磁贴显示一个或多个设备的最新温度、压力和湿度值。 |
| 事件历史记录    | “事件历史记录”磁贴显示设备在一段时间内的事件。 例如，可以使用它来显示最近一小时内一个或多个设备的所有阀打开和关闭事件。|
| 属性         |  属性磁贴显示一个或多个设备的属性和云属性的当前值。 例如，可以使用此磁贴显示设备属性，如设备的制造商或固件版本。 |

目前，最多可以向支持多个设备的磁贴添加 10 个设备。

### <a name="customizing-visualizations"></a>自定义可视化效果

默认情况下，折线图显示一段时间范围内的数据。 所选时间范围拆分为 50 个大小相同的桶，然后设备数据聚合每个桶来在所选时间范围内给出 50 个数据点。 若想查看原始数据，可以更改所选内容来查看最后 100 个值。 若要更改时间范围或选择原始数据可视化效果，请使用“配置图表”面板中的显示范围下拉列表。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="更改折线图的显示范围":::

对于显示聚合值的磁贴，请选择“配置图表”面板中遥测类型旁边的齿轮图标来选择聚合。 可以在平均值、总数、最大值、最小值和计数之间选择。

对于折线图、条形图和饼图，可以自定义不同遥测值的颜色。 选择要自定义的遥测旁边的调色板图标：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="更改遥测值的颜色":::

对于显示字符串属性或遥测值的磁贴，可以选择文本的显示方式。 例如，如果设备将 URL 存储在字符串属性中，则可以将其显示为可单击的链接。 如果 URL 引用图像，则可以在上一个已知值或属性磁贴中呈现图像。 若要更改字符串的显示方式，请在磁贴配置中选择遥测类型或属性旁边的齿轮图标:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="更改字符串在磁贴上的显示方式":::

对于数值“KPI”、“上一个已知值”和“属性”磁贴，可以使用条件格式根据其当前值来自定义磁贴颜色。 若要添加条件格式，请在磁贴上选择“配置”，然后选择要自定义的值旁边的“条件格式”图标：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="显示如何查找磁贴的配置选项及条件格式图标的屏幕截图":::

添加条件格式规则：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="显示平均流的条件格式规则的屏幕截图。有三条规则 - 小于 20 为绿色、小于 50 为黄色、超过 50 则为红色":::
   
以下屏幕截图显示了条件格式规则的效果：

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="显示平均水流磁贴上红色背景色的屏幕截图。磁贴上的数字为 50.54":::

### <a name="tile-formatting"></a>磁贴格式化
此功能在 KPI、LKV 和属性磁贴中可用，可以让用户调整字体大小，选择小数精度，使用缩写数值（例如将 1700 写为 1.7 K 的格式），或在其磁贴中将字符串值换行。

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="磁贴格式":::

## <a name="next-steps"></a>后续步骤

现在，你已了解如何配置 Azure IoT Central 默认应用程序仪表板，接下来可以[了解如何创建个人仪表板](howto-create-personal-dashboards.md)。
