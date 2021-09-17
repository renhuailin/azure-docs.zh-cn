---
title: 在 Azure IoT Central 中定义新的 IoT 设备类型 | Microsoft Docs
description: 本文介绍如何在 Azure IoT Central 应用程序中创建新的 Azure IoT 设备模板。 其中介绍了如何为类型定义遥测、状态、属性和命令。
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 59c95633322f279504ded61bbf02a7415b1337b9
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446289"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中定义新的 IoT 设备类型

设备模板是一个蓝图，用于定义可连接到 [Azure IoT Central 应用程序](concepts-app-templates.md)的某种设备的特征和行为。

本文介绍如何在 IoT Central 中创建设备模板。 例如，可为发送遥测数据（如温度）和属性（如位置）的传感器创建一个设备模板。 在此设备模板中，操作员可以创建和连接真实设备。

以下屏幕截图显示了设备模板的示例：

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="显示设备模板的屏幕截图。":::

设备模板包含以下部分：

- 模型 - 使用模型可以定义设备与 IoT Central 应用程序的交互方式。 每个模型具有唯一的模型 ID，并且定义了设备的功能。 功能分组为接口。 通过接口可以在不同的模型中重复使用组件，或使用继承来扩展功能集。
- 云属性 - 使用云属性可以定义 IoT Central 应用程序存储的设备相关信息。 例如，云属性可记录设备最近一次维修的日期。
- 自定义 - 使用自定义项可以修改功能。 例如，指定某个属性的最小和最大温度值。
- 视图 - 使用视图可以可视化来自设备的数据，使用窗体可以管理和控制设备。

若要了解详细信息，请参阅[什么是设备模板？](concepts-device-templates.md)。

## <a name="create-a-device-template"></a>创建设备模板

可采用多种做法来创建设备模板：

- 在 IoT Central GUI 中设计设备模板。
- 从 [Azure IoT 认证设备目录](https://aka.ms/iotdevcat)中导入设备模板。 （可选）根据你的需求在 IoT Central 中自定义设备模板。
- 当设备连接到 IoT Central 时，让它发送它所实现的模型的模型 ID。 IoT Central 使用模型 ID 来检索模型存储库中的模型，并创建设备模板。 将 IoT Central 应用程序所需的任何云属性、自定义项和视图添加到设备模板中。
- 当设备连接到 IoT Central 时，让 IoT Central 从设备发送的数据[自动生成设备模板](#autogenerate-a-device-template)定义。
- 使用[数字孪生定义语言 (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 创作设备模型。 将设备模型手动导入到 IoT Central 应用程序中。 然后，添加 IoT Central 应用程序所需的云属性、自定义项和视图。
- 你还可以使用 [REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) 或 [CLI](howto-manage-iot-central-from-cli.md) 将设备模板添加到 IoT Central 应用程序。

> [!NOTE]
> 在每种情况下，设备代码都必须实现模型中定义的功能。 设备代码实现不受设备模板的云属性、自定义项和视图部分的影响。

本部分介绍如何从目录中导入设备模板，以及如何使用 IoT Central GUI 对其进行自定义。 此示例使用设备目录中的“ESP32-Azure IoT 工具包”设备模板：

1. 若要添加新的设备模板，请在“设备模板”页上选择“+ 新建” 。
1. 在“选择类型”页上，向下滚动直到在“使用预配置的设备模板”部分中找到“ESP32-Azure IoT 套件”磁贴。  
1. 选择“ESP32-Azure IoT 工具包”磁贴，然后选择“下一页: 查看”。
1. 在“查看”页上，选择“创建”   。
创建的模板的名称为“传感器控制器”。 该模型包含“传感器控制器”、“SensorTemp”和“设备信息接口”等组件  。 组件定义 ESP32 设备的功能。 功能包括遥测、属性和命令。

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="显示传感器控制器设备模板的屏幕截图。":::

## <a name="autogenerate-a-device-template"></a>自动生成设备模板

还可以从尚未分配到设备模板的已连接设备自动创建设备模板。 IoT Central 使用设备发送的遥测值和属性值来推断设备模型。

> [!NOTE]
> 目前，此预览功能不能使用组件的遥测数据和属性。 它只能从根遥测数据和属性生成功能。

以下步骤说明如何使用此功能：

1. 将设备连接到 IoT Central 并开始发送数据。 在“原始数据”视图中看到了数据时，请在“管理模板”下拉菜单中选择“自动创建模板”  ：

    :::image type="content" source="media/howto-set-up-template/infer-model-1.png" alt-text="显示来自未分配设备的原始数据的屏幕截图。":::

1. 在“数据预览”页上，对原始数据进行任何所需的更改，然后选择“创建模板” ：

    :::image type="content" source="media/howto-set-up-template/infer-model-2.png" alt-text="显示在“数据预览”中进行更改的屏幕截图，可通过这种更改编辑由 IoT Central 用来生成设备模板的数据。":::

1. IoT Central 基于“数据预览”页上显示的数据格式生成模板，并将设备分配到该模板。 可以在“设备模板”页上进一步更改设备模板，例如，将其重命名或添加功能：

    :::image type="content" source="media/howto-set-up-template/infer-model-3.png" alt-text="显示如何重命名自动生成的设备模板的屏幕截图。":::

## <a name="manage-a-device-template"></a>管理设备模板

可从模板编辑器页中重命名或删除模板。

定义模板后，即可发布该模板。 在模板发布之前，设备无法连接到该模板，并且该模板不会显示在“设备”页上。

若要详细了解如何修改设备模板以及控制其版本，请参阅[编辑现有设备模板](howto-edit-device-template.md)。

## <a name="models"></a>模型

模型定义设备与 IoT Central 应用程序交互的方式。 可以使用更多功能来自定义模型，添加接口来继承功能，或者添加基于其他接口的新组件。

要创建设备模型，可以：

- 使用 IoT Central 从头开始创建自定义模型。
- 从 JSON 文件导入 DTDL 模型。 设备构建人员可能已使用 Visual Studio Code 来创作应用程序的设备模型。
- 从设备目录中选择一台设备。 此选项将导入制造商已为此设备发布的设备模型。 用该方式导入的设备模型将自动发布。

1. 若要查看模型 ID，请在模型中选择根接口，然后选择“编辑标识”：

    :::image type="content" source="media/howto-set-up-template/view-id.png" alt-text="显示设备模板根接口的模型 ID 的屏幕截图。":::

1. 若要查看组件 ID，请在模型中的任一组件接口上选择“编辑标识”。

有关详细信息，请参阅 [IoT 即插即用建模指南](../../iot-pnp/concepts-modeling-guide.md)。

### <a name="interfaces-and-components"></a>接口和组件

若要查看和管理设备模型中的接口，请执行以下操作：

1. 转到“设备模板”页，然后选择你创建的设备模板。 接口将列在设备模板的“模型”部分。 以下屏幕截图显示了设备模板中的“传感器控制器”根接口示例：

    :::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="显示模型根接口的屏幕截图"::: 

1. 选择省略号图标，将继承的接口或组件添加到根接口。 若要详细了解接口和组件，请参阅建模指南中的[多个组件](../../iot-pnp/concepts-modeling-guide.md#multiple-components)。

    :::image type="content" source="media/howto-set-up-template/add-interface.png" alt-text="如何添加接口或组件":::

1. 若要导出模型或接口，请选择“导出”。

1. 若要查看或者编辑某个接口或功能的 DTDL，请选择“编辑 DTDL”。

### <a name="capabilities"></a>功能

选择“+ 添加功能”可将功能添加到接口或组件。 例如，可将“目标温度”功能添加到“SensorTemp”组件 。

:::image type="content" source="media/howto-set-up-template/add-capability.png" alt-text="如何添加功能":::

#### <a name="telemetry"></a>遥测

遥测是从设备发送的值流，通常来自传感器。 例如，传感器可能会报告环境温度，如下所示：

:::image type="content" source="media/howto-set-up-template/telemetry.png" alt-text="如何添加遥测":::

下表显示了遥测功能的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 视图和窗体上使用的遥测值的显示名称。 |
| 名称 | 遥测消息中字段的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 此字段必须为字母数字。 |
| 功能类型 | 遥测。 |
| 语义类型 | 遥测的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。 |
| 架构 | 遥测数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 架构不可用于事件和状态语义类型。 |
| 严重性 | 仅适用于事件语义类型。 严重性为“错误”、“信息”或“警告”。 |
| 状态值 | 仅适用于状态语义类型。 定义可能的状态值，其中每个状态值都具有显示名称、名称、枚举类型和值。 |
| 计价单位 | 遥测值的单位，如“mph”、“%”或“&deg;C”  。 |
| 显示单位 | 视图和窗体上使用的显示单位。 |
| 注释 | 有关遥测功能的任何注释。 |
| 说明 | 遥测功能的说明。 |

#### <a name="properties"></a>属性

属性表示时间点值。 可从 IoT Central 设置可写属性。
例如，设备可以使用某个可写属性来让操作员设置目标温度，如下所示：

:::image type="content" source="media/howto-set-up-template/property.png" alt-text="如何添加属性":::

下表显示了属性功能的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 视图和窗体上使用的属性值的显示名称。 |
| 名称 | 属性的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 此字段必须为字母数字。 |
| 功能类型 | 属性。 |
| 语义类型 | 属性的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。 |
| 架构 | 属性数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 架构不可用于事件和状态语义类型。 |
| 可写 | 如果属性不是可写的，则设备可向 IoT Central 报告属性值。 如果属性是可写的，则该设备可以向 IoT Central 报告属性值，而 IoT Central 可将属性更新发送到设备。
| 严重性 | 仅适用于事件语义类型。 严重性为“错误”、“信息”或“警告”。 |
| 状态值 | 仅适用于状态语义类型。 定义可能的状态值，其中每个状态值都具有显示名称、名称、枚举类型和值。 |
| 计价单位 | 属性值的单位，如“mph”、“%”或“&deg;C”  。 |
| 显示单位 | 视图和窗体上使用的显示单位。 |
| 评论 | 有关属性功能的任何注释。 |
| 说明 | 属性功能的说明。 |

#### <a name="commands"></a>命令

可从 IoT Central 调用设备命令。 命令可以选择将参数传递给设备，并接收来自设备的响应。 例如，可以调用某个命令以便在 10 秒内重启设备，如下所示：

:::image type="content" source="media/howto-set-up-template/command.png" alt-text="如何添加命令":::

下表显示了命令功能的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 视图和窗体上使用的命令的显示名称。 |
| 名称 | 命令的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 此字段必须为字母数字。 |
| 功能类型 | 命令。 |
| 脱机时排队 | 如果已启用，即使设备处于脱机状态，也可以调用命令。 如果未启用，则只能在设备联机时调用命令。 |
| 评论 | 有关命令功能的任何注释。 |
| 说明 | 命令功能的说明。 |
| 请求 | 如果启用，则请求参数的定义包括：名称、显示名称、架构、单位和显示单位。 |
| 响应 | 如果启用，则命令响应的定义包括：名称、显示名称、架构、单位和显示单位。 |

要了解有关设备如何实现命令的详细信息，请参阅[遥测、属性和命令有效负载 > 命令和长期命令](concepts-telemetry-properties-commands.md#commands)。

#### <a name="offline-commands"></a>脱机命令

如果设备当前脱机，则可通过为设备模板中的命令启用“脱机时排队”选项，选择排队命令。

:::image type="content" source="media/howto-set-up-template/offline-commands.png" alt-text="如何添加脱机命令":::

此选项使用 IoT 中心云到设备消息向设备发送通知。 要了解详细信息，请参阅 IoT 中心文章[发送云到设备消息](../../iot-hub/iot-hub-devguide-messages-c2d.md)。

云到设备消息：

- 是从解决方案发送到设备的单向通知。
- 保证至少一次消息送达。 IoT 中心将云到设备消息保留在每个设备队列中，从而确保根据连接性和设备故障进行复原。
- 要求设备实现消息处理程序以处理云到设备消息。

> [!NOTE]
> 此选项仅在 IoT Central web UI 中可用。 如果从设备模板导出模型或组件，则不包含此设置。

## <a name="cloud-properties"></a>云属性

使用云属性在 IoT Central 中存储设备相关信息。 云属性从不发送至设备。 例如，可以使用云属性来存储已安装设备的客户的名称或设备的最近一次维修日期。

:::image type="content" source="media/howto-set-up-template/cloud-properties.png" alt-text="如何添加云属性"::: 

下表显示了云属性的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 视图和窗体上使用的云属性值的显示名称。 |
| 名称 | 云属性的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 |
| 语义类型 | 属性的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。 |
| 架构 | 云属性数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 |

## <a name="customizations"></a>自定义

需要修改导入的组件或将 IoT Central 特定的功能添加到功能时，可以使用自定义项。 例如，可以更改属性的显示名称和单位，如下所示：

:::image type="content" source="media/howto-set-up-template/customize.png" alt-text="如何进行自定义":::

下表显示了自定义项的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
|显示名称 | 替代模型中的显示名称。 |
|语义类型 | 替代模型中的语义类型。 |
|计价单位 | 替代模型中的单位。 |
|显示单位 | 在模型中替代。 |
|注释 | 在模型中替代。 |
|描述 | 在模型中替代。 |
|颜色 | 特定于 IoT Central 的选项。 |
|最小值 | 设置最小值 - 特定于 IoT Central 的选项。 |
|最大值 | 设置最大值 - 特定于 IoT Central 的选项。 |
|小数位数 | 特定于 IoT Central 的选项。 |
|初始值 | 仅命令特定于 IoT Central 的值 - 默认参数值。 |

## <a name="views"></a>视图

使用视图可以定义视图和窗体来让操作员监视设备并与之交互。 视图使用可视化效果（例如图表）来显示遥测值和属性值。

生成默认视图是可视化重要设备信息的快捷方法。 有以下三个默认视图：

### <a name="default-views"></a>默认视图

- **命令**：包含设备命令的视图，使操作员能够将其调度到你的设备。
- **概述**：包含设备遥测的视图，显示图表和指标。
- **关于**：包含设备信息的视图，显示设备属性。

选择“生成默认视图”后，这些视图会自动添加到设备模板的“视图”部分下 。

### <a name="custom-views"></a>自定义视图

将视图添加到设备模板，使操作员能够使用图表和指标来可视化设备。 你可以将自己的自定义视图添加到设备模板。

将视图添加到设备模板：

1. 转到设备模板并选择“视图”。
1. 选择“可视化设备”。
1. 在“视图名称”中输入视图的名称。
1. 在“添加磁贴”下选择“从某个视觉对象开始”，并选择磁贴的视觉对象类型。 然后选择“添加磁贴”或将视觉对象拖放到画布中。 若要配置磁贴，请选择齿轮图标。

:::image type="content" source="media/howto-set-up-template/start-visual.png" alt-text="如何从视觉对象开始"::: 

:::image type="content" source="media/howto-set-up-template/tile.png" alt-text="配置磁贴"::: 

若要测试视图，请选择“配置预览设备”。 使用此功能，可以让你在视图发布后看到与操作员看到的视图一样的视图。 使用此功能验证视图是否显示正确的数据。 选择从以下选项：

- 无预览设备。
- 为设备模板配置的真实测试设备。
- 应用程序中的现有设备（通过使用设备 ID）。

### <a name="forms"></a>窗体

通过查看和设置属性，将窗体添加到设备模板，使操作员可以管理设备。 操作员只能编辑云属性和可写设备属性。 设备模板可具有多个窗体。

1. 选择“视图”节点，然后选择“编辑设备和云数据”磁贴来添加新视图。

1. 将窗体名称更改为“管理设备”。

1. 依次选择“客户名称”、“上次服务日期”云属性和“目标温度”属性  。 然后选择“添加部分”。

1. 选择“保存”以保存新窗体。

:::image type="content" source="media/howto-set-up-template/form.png" alt-text="配置窗体":::

## <a name="publish-a-device-template"></a>发布设备模板

在连接用于实现设备模型的设备之前，必须发布设备模板。

若要发布设备模板，请转到你的设备模板，然后选择“发布”。

在你发布设备模板后，操作员可以转到“设备”页，并添加使用此设备模板的真实或模拟设备。 你可以在进行更改时继续修改并保存设备模板。 如果要将这些更改推送给操作员，以便在“设备”页下查看，则必须每次都选择“发布”。

## <a name="next-steps"></a>后续步骤

建议下一步查看如何 [对现有设备模板进行更改](howto-edit-device-template.md)。
