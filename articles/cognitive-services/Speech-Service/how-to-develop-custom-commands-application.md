---
title: 如何：开发自定义命令应用程序-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何开发和自定义命令应用程序。 这些语音命令应用最适用于任务完成或命令和控制方案。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: b3e9c1e8ad23ea0ebf540eddbd6d4a03b8a72fe2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835068"
---
# <a name="develop-custom-commands-applications"></a>开发自定义命令应用程序

本操作指南文章介绍如何开发和配置自定义命令应用程序。 自定义命令功能有助于构建丰富的语音命令应用，这些应用已针对语音第一次交互体验进行了优化。 此功能最适合任务完成或命令和控制方案。 它特别适用于物联网 (IoT) 设备以及环境和无外设设备。

在本文中，你将创建一个应用程序，用于打开和关闭电视，设置温度，以及设置警报。 创建这些基本命令后，你将了解用于自定义命令的以下选项：

* 向命令添加参数
* 将配置添加到命令参数
* 构建交互规则
* 为语音响应创建语言生成模板
* 使用自定义语音工具

## <a name="create-an-application-by-using-simple-commands"></a>使用简单命令创建应用程序

首先创建一个空的自定义命令应用程序。 有关详细信息，请参阅 [快速入门](quickstart-custom-commands-application.md)。 在此应用程序中，创建一个空白项目，而不是导入项目。

1. 在 " **名称** " 框中，输入项目名称 " *智能会议室-Lite* (或您选择的其他名称) 。
1. 在 " **语言** " 列表中，选择 " **英语 (美国)**"。
1. 选择或创建 LUIS 资源。

   > [!div class="mx-imgBorder"]
   > ![显示 "新建项目" 窗口的屏幕截图。](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a> (可选) 更新 LUIS 资源

您可以更新在 " **新建项目** " 窗口中选择的创作资源。 还可以设置预测资源。 

发布自定义命令应用程序时，预测资源用于识别。 在开发和测试阶段，无需预测资源。

### <a name="add-a-turnon-command"></a>添加 TurnOn 命令

在创建的空智能空间精简自定义命令应用程序中，添加命令。 命令将处理查询文本 `Turn on the tv` 。 它将响应消息 `Ok, turning the tv on` 。

1. 通过选择左侧窗格顶部的 " **新建命令** " 来创建新命令。 此时将打开 **新的命令** 窗口。
1. 对于 " **名称** " 字段，请提供值 `TurnOn` 。
1. 选择“创建”  。

中间窗格列出该命令的属性。 

下表说明了命令的配置属性。 有关详细信息，请参阅 [自定义命令的概念和定义](./custom-commands-references.md)。

| 配置            | 说明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 示例句子 | 示例最谈话用户可以说触发此命令。                                                                 |
| 参数       | 完成命令所需的信息。                                                                                |
| 完成规则 | 要执行以执行命令的操作。 示例：响应用户或与 web 服务进行通信。 |
| 交互规则   | 用于处理更为具体或复杂的情况的其他规则。                                                              |


> [!div class="mx-imgBorder"]
> ![显示命令创建位置的屏幕截图。](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>添加示例句子

在 " **示例句子** " 部分中，提供了用户可以使用的示例。

1. 在中间窗格中，选择 " **示例句子**"。
1. 在右侧窗格中，添加示例：

    ```
    Turn on the tv
    ```

1.  在窗格顶部，选择“保存”。

你还没有参数，因此可以移动到 **完成规则** 部分。

#### <a name="add-a-completion-rule"></a>添加完成规则

接下来，该命令需要完成规则。 此规则告知用户正在执行执行操作。 

有关规则和完成规则的详细信息，请参阅 [自定义命令的概念和定义](./custom-commands-references.md)。

1. 选择已 **完成** 的默认完成规则。 然后按如下所示对其进行编辑：

    
    | 设置    | 建议的值                          | 说明                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **名称**       | `ConfirmationResponse`                  | 描述规则用途的名称          |
    | **条件** | 无                                     | 确定何时可以运行规则的条件    |
    | **操作**    | **发送语音响应**  > **简单编辑器**  > **第一变体** > `Ok, turning the tv on` | 规则条件为 true 时要执行的操作 |

   > [!div class="mx-imgBorder"]
   > ![显示在何处创建语音响应的屏幕截图。](media/custom-commands/create-speech-response-action.png)

1. 选择 " **保存** " 以保存操作。
1. 返回 " **完成规则** " 部分，选择 " **保存** " 以保存所有更改。 

    > [!NOTE]
    > 无需使用命令附带的默认完成规则。 您可以删除默认完成规则并添加您自己的规则。

### <a name="add-a-settemperature-command"></a>添加 SetTemperature 命令

现在再添加一个命令， `SetTemperature` 。 此命令将使用单个查询文本， `Set the temperature to 40 degrees` 并使用消息进行响应 `Ok, setting temperature to 40 degrees` 。

若要创建新命令，请执行命令所用的步骤 `TurnOn` ，但使用示例句子 `Set the temperature to 40 degrees` 。

然后编辑现有完成 **后** 的规则，如下所示：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| **名称**  | `ConfirmationResponse`                  |
| **条件** | 无                                     |
| **操作**    | **发送语音响应**  > **简单编辑器**  > **第一变体** > `Ok, setting temperature to 40 degrees` |

选择 " **保存** " 以保存对命令所做的所有更改。

### <a name="add-a-setalarm-command"></a>添加 SetAlarm 命令

创建新 `SetAlarm` 命令。 使用示例句子 `Set an alarm for 9 am tomorrow` 。 然后编辑现有完成 **后** 的规则，如下所示：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| **名称**  | `ConfirmationResponse`                  |
| **条件** | 无                                     |
| **操作**    | **发送语音响应**  > **简单编辑器**  > **第一变体** > `Ok, setting an alarm for 9 am tomorrow` |

选择 " **保存** " 以保存对命令所做的所有更改。

### <a name="try-it-out"></a>试试看

使用 "测试" 窗格测试应用程序的行为： 

1. 在窗格的右上角，选择 " **训练** " 图标。 
1. 训练完成后，选择 " **测试**"。 

使用语音或文本试用以下查询文本示例：

- 键入： *将温度设置为40度*
- 预期响应：正常，将温度设置为40度
- 键入： *打开电视*
- 预期响应： Ok，打开电视
- 键入：*将警报设置为明天上午 9* 点
- 预期响应：好，将警报设置为明天上午9点

> [!div class="mx-imgBorder"]
> ![在 web 聊天界面中显示测试的屏幕截图。](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> 在 "测试" 窗格中，可以选择 " **打开详细** 信息" 以获取有关如何处理此语音输入或文本输入的信息。

## <a name="add-parameters-to-commands"></a>向命令添加参数

本部分介绍如何将参数添加到命令中。 命令需要参数来完成任务。 在复杂的方案中，参数可用于定义触发自定义操作的条件。

### <a name="configure-parameters-for-a-turnon-command"></a>为 TurnOn 命令配置参数

首先编辑现有 `TurnOn` 命令以打开和关闭多个设备。

1. 现在，此命令将处理 on 和 off 方案，请将命令重命名为 *TurnOnOff*。
   1. 在左侧窗格中，选择 " **TurnOn** " 命令。 然后单击窗格顶部的 " **新建命令** " 旁的省略号 (**...**) "按钮。
   
   1. 选择 " **重命名**"。 在 " **重命名" 命令** 窗口中，将名称更改为 *TurnOnOff*。

1. 将新参数添加到命令。 参数表示用户是要打开还是关闭设备。
   1. 在中间窗格的顶部，选择 "  **添加**"。 从下拉菜单中选择 " **参数**"。
   1. 在右侧窗格的 " **参数** " 部分的 " **名称** " 框中，添加 `OnOff` 。
   1. 选择“必需”。 在 " **为所需参数添加响应** " 窗口中，选择 " **简单编辑器**"。 在 **第一个变体** 字段中，增加 *或关闭？*。
   1. 选择“更新”。

       > [!div class="mx-imgBorder"]
       > ![显示在何处创建必需参数响应的屏幕截图。](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 使用下表配置参数的属性。 有关命令的所有配置属性的信息，请参阅 [自定义命令的概念和定义](./custom-commands-references.md)。
      

       | 配置      | 建议的值     | 说明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **名称**               | `OnOff`           | 参数的描述性名称                                                                           |
       | **为全局**          | 未选定       | 指示此参数的值是否全局应用于应用程序中所有命令的复选框。|
       | **必需**           | 选定         | 指示在命令完成之前是否需要此参数的值的复选框。 |
       | **响应所需的参数**      |**简单编辑器** > `On or Off?`      | 询问此参数值未知的提示。 |
       | 类型               | **字符串**          | 参数类型，如 Number、String、Date Time 或 Geography。   |
       | **配置**      | **接受来自内部目录的预定义输入值** | 对于字符串，此设置将输入限制为一组可能值。 |
       | **预定义的输入值**     | `on`, `off`           | 一组可能的值及其别名。         |
       
        
   1. 若要添加预定义的输入值，请选择 " **添加预定义的输入**"。 在 " **新建项目**  " 窗口中，键入 " *名称* "，如前面的表中所示。 在这种情况下，不使用别名，因此可以将此字段留空。
   
      > [!div class="mx-imgBorder"]
      > ![显示如何创建参数的屏幕截图。](media/custom-commands/create-on-off-parameter.png)

   1. 选择 " **保存** " 以保存参数的所有配置。
 
#### <a name="add-a-subjectdevice-parameter"></a>添加 SubjectDevice 参数

1. 若要添加另一个参数以表示可使用此命令控制的设备的名称，请选择 " **添加**"。 使用以下配置。


    | 设置            | 建议的值       |
    | ------------------ | --------------------- |
    | **名称**               | `SubjectDevice`         |
    | **为全局**          | 未选定             |
    | **必需**           | 选定               |
    | **响应所需的参数**     | **简单编辑器** > `Which device do you want to control?`    | 
    | 类型               | **字符串**                |          |
    | **配置**      | **接受来自内部目录的预定义输入值** | 
    | **预定义的输入值** | `tv`, `fan`               |
    | **别名** (`tv`)       | `television`, `telly`     |

1. 选择“保存”。

#### <a name="modify-example-sentences"></a>修改示例句子

对于使用参数的命令，添加涵盖所有可能组合的示例句子很有用。 例如：

* 完整参数信息： `turn {OnOff} the {SubjectDevice}`
* 部分参数信息： `turn it {OnOff}`
* 无参数信息： `turn something`

使用不同度信息的示例句子允许自定义命令应用程序使用部分信息解析一种单步解析和多轮解析。

了解该信息后，编辑示例句子以使用这些建议的参数：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

选择“保存”。

> [!TIP]
> 在示例-句子编辑器中，使用大括号来引用参数。 例如，`turn {OnOff} the {SubjectDevice}`。
> 使用选项卡自动完成以前创建的参数。

#### <a name="modify-completion-rules-to-include-parameters"></a>修改完成规则以包含参数

修改现有的完成规则 `ConfirmationResponse` 。

1. 在 " **条件** " 部分中，选择 " **添加条件**"。
1. 在 " **新建条件** " 窗口的 " **类型** " 列表中，选择 " **必需参数**"。 在下面的列表中，选择 " **麦克风** " 和 " **SubjectDevice**"。
1. 使 **IsGlobal** 保持未选中状态。
1. 选择“创建”  。
1. 在 " **操作** " 部分，通过将鼠标悬停在 " **发送语音响应** " 操作并选择 "编辑" 按钮，对其进行编辑。 这一次，请使用新创建的 `OnOff` 和 `SubjectDevice` 参数：

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 选择“保存”。

通过选择右侧窗格顶部的 " **训练** " 图标来尝试更改。 

训练完成后，选择 " **测试**"。 此时将显示 " **测试应用程序** " 窗口。 尝试下列交互：

- 输入： *关闭电视*
- 输出：确定，关闭电视
- 输入： *关闭电视*
- 输出：确定，关闭电视
- 输入：*将其* 关闭
- 输出：要控制哪个设备？
- 输入： *电视*
- 输出：确定，关闭电视

### <a name="configure-parameters-for-a-settemperature-command"></a>为 SetTemperature 命令配置参数

修改 `SetTemperature` 命令，使其能够在用户指示时设置温度。

添加一个 `Temperature` 参数。 使用以下配置：

| 配置      | 建议的值     |
| ------------------ | ----------------|
| **名称**               | `Temperature`           |
| **必需**           | 选定         |
| **响应所需的参数**      | **简单编辑器** > `What temperature would you like?`
| 类型               | `Number`          |


编辑示例最谈话以使用以下值。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

编辑现有的完成规则。 使用以下配置。

| 配置      | 建议的值     |
| ------------------ | ----------------|
| **条件**         | **必需的参数**  > **温度**           |
| **操作**           | **发送语音响应** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>为 SetAlarm 命令配置参数

添加一个名为的参数 `DateTime` 。 使用以下配置。

   | 设置                           | 建议的值                     | 
   | --------------------------------- | ----------------------------------------|
   | **名称**                              | `DateTime`                               |
   | **必需**                          | 选定                                 |
   | **响应所需的参数**   | **简单编辑器** > `For what time?`            | 
   | 类型                              | **DateTime**                                |
   | **日期默认值**                     | 如果缺少日期，请使用 today。            |
   | **时间默认值**                     | 如果缺少该时间，则使用当天的开始时间。     |


> [!NOTE]
> 本文主要使用字符串、数字和日期时间参数类型。 有关所有受支持的参数类型及其属性的列表，请参阅 [自定义命令的概念和定义](./custom-commands-references.md)。


编辑示例最谈话。 请使用以下值。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

编辑现有的完成规则。 使用以下配置。

   | 设置    | 建议的值                               |
   | ---------- | ------------------------------------------------------- |
   | **操作**    | **发送语音响应** > `Ok, alarm set for {DateTime}`  |

使用与不同命令相关的最谈话测试这三个命令。  (可以在不同的命令之间切换。 ) 

- 输入： *设置警报*
- 输出：什么时间？
- 输入： *打开电视*
- 输出：好，打开电视
- 输入： *设置警报*
- 输出：什么时间？
- 输入： *下午 5*
- 输出：正常，为 2020-05-01 17:00:00 设置的警报

## <a name="add-configurations-to-command-parameters"></a>将配置添加到命令参数

在本部分中，将详细了解高级参数配置，包括：

 - 参数值如何属于自定义命令应用程序外定义的集。
 - 如何对参数值添加验证子句。

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>将参数配置为外部目录实体

使用自定义命令功能，您可以将字符串类型参数配置为引用通过 web 终结点承载的外部目录。 因此，无需编辑自定义命令应用程序即可单独更新外部目录。 此方法在目录条目很多的情况下很有用。

重新使用 `SubjectDevice` 命令中的参数 `TurnOnOff` 。 此参数的当前配置 **接受来自内部目录的预定义输入**。 此配置引用参数配置中设备的静态列表。 将此内容移到可以单独更新的外部数据源。

若要移动内容，请首先添加新的 web 终结点。 在左侧窗格中，请参阅 " **Web 终结点** " 部分。 在该处添加新的 web 终结点。 使用以下配置。

| 设置 | 建议的值 |
|----|----|
| **名称** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **方法** | **GET** |


如果 URL 的建议值对你不起作用，则配置并托管一个 web 终结点，该终结点将返回一个 JSON 文件，该文件包含可控制的设备列表。 Web 终结点应返回格式如下的 JSON 文件：
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

接下来，请执行 " **SubjectDevice** 参数设置" 页。 设置以下属性。

| 设置 | 建议的值 |
| ----| ---- |
| **配置** | **接受来自外部目录的预定义输入** |                               
| **目录终结点** | `getDevices` |
| **方法** | **GET** |

再选择“保存”。

> [!IMPORTANT]
> 除非在左侧窗格的 " **web 终结点** " 部分中设置了 web 终结点，否则不会显示用于将参数配置为接受来自外部目录的输入的选项。

选择 " **训练**"，尝试一下。 训练完成后，选择 " **测试** "，并尝试几个交互。

* 输入： *开启*
* 输出：要控制哪个设备？
* 输入： *光源*
* 输出：好，打开灯

> [!NOTE]
> 你现在可以控制在 web 终结点上承载的所有设备。 但仍需要训练应用程序来测试新的更改，然后重新发布应用程序。

### <a name="add-validation-to-parameters"></a>向参数添加验证

*验证* 是应用于某些参数类型的构造，这些参数类型允许你配置对参数值的约束。 如果值不在约束范围内，则会提示您进行更正。 有关扩展验证构造的参数类型的列表，请参阅 [自定义命令的概念和定义](./custom-commands-references.md)。

使用命令测试验证 `SetTemperature` 。 使用以下步骤添加参数的验证 `Temperature` 。

1. 在左侧窗格中，选择 " **SetTemperature** " 命令。
1. 在中间窗格中，选择 " **温度**"。
1. 在右侧窗格中，选择 " **添加验证**"。
1. 在 " **新建验证** " 窗口中，按下表所示配置验证。 然后选择“创建”。


    | 参数配置 | 建议的值 | 说明 |
    | ---- | ---- | ---- |
    | **最小值** | `60` | 对于 Number 参数，此参数可以采用的最小值 |
    | **最大值** | `80` | 对于 Number 参数，此参数可以采用的最大值 |
    | **故障响应** |  **简单编辑器**  > **第一变体** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 如果验证失败，则提示要求输入新值 |

    > [!div class="mx-imgBorder"]
    > ![显示如何添加范围验证的屏幕截图。](media/custom-commands/add-validations-temperature.png)

通过选择右侧窗格顶部的 " **训练** " 图标来尝试一下。 训练完成后，选择 " **测试**"。 尝试几个交互：

- 输入： *将温度设置为72度*
- 输出：正常，将温度设置为72度
- 输入： *将温度设置为45度*
- 输出：抱歉，只能设置60到80度之间的温度
- 输入： *改为72度*
- 输出：正常，将温度设置为72度

## <a name="add-interaction-rules"></a>添加交互规则

交互规则是处理特定或复杂情况的 *其他* 规则。 尽管你可以随意创作自己的交互规则，但在此示例中，你将在以下情况下使用交互规则：

* 确认命令
* 向命令添加单步更正

有关交互规则的详细信息，请参阅 [自定义命令的概念和定义](./custom-commands-references.md)。

### <a name="add-confirmations-to-a-command"></a>向命令添加确认

若要添加确认，请使用 `SetTemperature` 命令。 若要获得确认，请使用以下步骤创建交互规则：

1. 在左侧窗格中，选择 " **SetTemperature** " 命令。
1. 在中间窗格中，通过选择 " **添加**" 来添加交互规则。 然后选择 "**交互规则**" "  >  **确认" 命令**。

    此操作将添加三个交互规则。 规则要求用户确认警报的日期和时间。 它们需要确认 ("是" 或 "否") 。

    1. 使用以下配置修改 **Confirm 命令** 交互规则：
        1. 更改名称以 **确认温度**。
        1. 添加新条件：**所需参数**  >  **温度**。
        1. 添加新操作：**键入**  >  **发送语音响应**  >  **是否确实要将温度设置为 {温度} 度？**
        1. 在 " **预期** " 部分中，保留 " **要求用户确认**" 的默认值。
      
         > [!div class="mx-imgBorder"]
         > ![显示如何创建必需参数响应的屏幕截图。](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 修改 **确认成功** 交互规则以处理成功的确认 (用户是否) 。
      
          1. 将名称更改为 **确认温度已成功**。
          1. 使现有 **确认为成功** 状态。
          1. 添加新条件：**键入**  >  **所需的参数**  >  **温度**。
          1. 保留 "**执行完成规则**" 的默认 **执行后状态** 值。

    1. 修改 **确认拒绝** 交互规则以在拒绝确认时处理方案， (用户未提到) 。

          1. 将名称更改为 " **确认温度已拒绝**"。
          1. 保留现有 **确认拒绝** 条件。
          1. 添加新条件：**键入**  >  **所需的参数**  >  **温度**。
          1. 添加新操作：**键入**"  >  **发送语音响应**  >  **无问题"。什么是温度？**。
          1. 更改默认的 **执行后状态** 值，以 **等待用户的输入**。

> [!IMPORTANT]
> 本文介绍如何使用内置确认功能。 还可以手动逐个添加交互规则。
   
通过选择 **定型** 来尝试更改。 训练完成后，选择 " **测试**"。

- **输入**： *将温度设置为80度*
- **输出**：是否确实要将温度设置为80度？
- **输入**： *否*
- **输出**：没有问题。 什么是温度？
- **输入**： *72 度*
- **输出**：是否确实要将温度设置为72度？
- **输入**： *是*
- **输出**：正常，将温度设置为72度

### <a name="implement-corrections-in-a-command"></a>在命令中实现更正

在本部分中，你将配置一个单步更正。 此更正在执行完成操作后使用。 如果命令尚未完成，还会看到一个示例，说明如何默认启用更正。 若要在命令未完成时添加更正，请添加新参数 `AlarmTone` 。

在左窗格中，选择 " **SetAlarm** " 命令。 然后添加新参数 **AlarmTone**。
        
- **路径名** > `AlarmTone`
- **类型**  > **字符串**
- **默认值**  > **Chimes**
- **配置**  > **接受来自内部目录的预定义输入值**
- **预定义的输入值**  > **Chimes**、**曲调** 和 **回显** (这些值是单个预定义的输入。 ) 


接下来，将 **DateTime** 参数的响应更新为 "就绪"， **以将音调设置为 {AlarmTone}。什么时间？**。 然后按如下所示修改完成规则：

1. 选择现有完成规则 " **ConfirmationResponse**"。
1. 在右侧窗格中，将鼠标悬停在现有操作上，然后选择 " **编辑**"。
1. 将语音响应更新为 `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` 。

> [!IMPORTANT]
> 在执行命令时，无需任何显式配置即可更改警报音。 例如，在命令尚未完成时，它可能会更改。 *默认情况* 下，将对所有命令参数启用更正，而不管 turn 号如何，如果该命令尚未完成。

#### <a name="implement-a-correction-when-a-command-is-finished"></a>完成命令后，实现更正

自定义命令平台允许进行单步更正，即使命令已完成。 默认情况下不启用此功能。 必须显式配置此设置。 

使用以下步骤配置单步更正：

1. 在 **SetAlarm** 命令中，添加类型 " **更新上一个" 命令** 的交互规则以更新以前设置的警报。 将交互规则重命名为 " **更新以前的警报**"。
1. 保留默认条件： **需要更新上一个命令**。
1. 添加新条件：**键入**  >  **必需的参数**  >  **日期时间**。
1. 添加新操作：**键入**  >  **发送语音响应**  >  **简单编辑器**  >  **正在将以前的警报时间更新为 {DateTime}**。
1. 在 **命令完成** 后保留默认的 **执行后状态** 值。

通过选择 **定型** 来尝试更改。 等待训练完成，然后选择 " **测试**"。

- **输入**： *设置警报。*
- **输出**：准备好将音频设置为 Chimes。 什么时间？
- **输入**： *将音频设置为曲调，将其设置为明天上午9点。*
- **输出**：正常，为 2020-05-21 09:00:00 设置的警报。 警报音为曲调。
- **输入**： *否，上午8点。*
- **输出**：将以前的警报时间更新为 2020-05-29 08:00。

> [!NOTE]
> 在实际应用程序中，在此更正规则的 " **操作** " 部分中，还需要将活动发送回客户端或调用 HTTP 终结点，以更新系统中的警报时间。 此操作应独自负责更新警报时间。 它不应负责命令的任何其他属性。 在这种情况下，该属性将为警报音。

## <a name="add-language-generation-templates-for-speech-responses"></a>为语音响应添加语言生成模板

语言生成 (LG) 模板使你可以自定义发送到客户端的响应。 它们会在响应中引入变体。 可以通过使用来实现语言生成：

* 语言生成模板。
* 自适应表达式。

自定义命令模板基于机器人框架的 [LG 模板](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。 由于自定义命令功能会在 () 参数或操作中的语音响应时创建新的 LG 模板，因此无需指定 LG 模板的名称。 

因此，你无需定义模板，如下所示：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

您可以改为定义不带名称的模板正文，如下所示：

> [!div class="mx-imgBorder"]
> ![显示模板编辑器示例的屏幕截图。](./media/custom-commands/template-editor-example.png)


此更改引入了发送到客户端的语音响应的变化形式。 对于查询文本，会从提供的选项中随机选取相应的语音响应。

通过利用 LG 模板，你还可以使用自适应表达式为命令定义复杂的语音响应。 有关详细信息，请参阅 [LG 模板格式](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。 

默认情况下，自定义命令功能支持所有功能，但有以下细微差异：

* 在 LG 模板中，实体表示为 `${entityName}` 。 自定义命令功能不使用实体。 但是，可以将参数用作 `${parameterName}` 表示形式或表示形式的变量 `{parameterName}` 。
* 自定义命令功能不支持模板撰写和扩展，因为您永远不会直接编辑 *lg* 文件。 仅编辑自动创建的模板的响应。
* 自定义命令功能不支持 LG 注入的自定义函数。 支持预定义函数。
* 自定义命令功能不支持选项，例如 `strict` 、 `replaceNull` 和 `lineBreakStyle` 。

### <a name="add-template-responses-to-a-turnonoff-command"></a>向 TurnOnOff 命令添加模板响应

修改 `TurnOnOff` 命令以添加新参数。 使用以下配置。

| 设置            | 建议的值       | 
| ------------------ | --------------------- | 
| **名称**               | `SubjectContext`         | 
| **为全局**          | 未选定             | 
| **必需**           | 未选定               | 
| 类型               | **字符串**                |
| **默认值**      | `all` |
| **配置**      | **接受来自内部目录的预定义输入值** | 
| **预定义的输入值** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>修改完成规则

编辑现有完成规则 **ConfirmationResponse** 的 "**操作**" 部分。 在 " **编辑操作** " 窗口中，切换到 " **模板编辑器**"。 然后，将文本替换为以下示例。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

使用以下输入和输出定型和测试应用程序。 请注意响应的变体。 变体由模板值的多个替代项创建，也通过使用自适应表达式来创建。

* 输入： *打开电视*
* 输出：好，打开电视
* 输入： *打开电视*
* 输出：已完成，已打开电视
* 输入： *关闭光源*
* 输出：确定，关闭所有灯光
* 输入： *关闭房间光源*
* 输出：确定，关闭房间灯光

## <a name="use-a-custom-voice"></a>使用自定义语音

自定义自定义命令响应的另一种方法是选择输出语音。 使用以下步骤将默认语音切换为自定义语音：

1. 在自定义命令应用程序的左侧窗格中，选择 " **设置**"。
1. 在中间窗格中，选择 " **自定义语音**"。
1. 在表中，选择自定义语音或公共语音。
1. 选择“保存”。

> [!div class="mx-imgBorder"]
> ![显示示例句子和参数的屏幕截图。](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> 对于公共语音，神经类型仅适用于特定区域。 有关详细信息，请参阅 [语音服务支持的区域](./regions.md#standard-and-neural-voices)。
>
> 您可以在 " **自定义语音** 项目" 页面上创建自定义语音。 有关详细信息，请参阅 [自定义语音入门](./how-to-custom-voice.md)。

现在，应用程序将在选定的语音中做出响应，而不是默认的语音。

## <a name="next-steps"></a>后续步骤

* 了解如何使用语音 SDK 将 [自定义命令应用程序](how-to-custom-commands-setup-speech-sdk.md) 与客户端应用程序集成。
* 使用 Azure DevOps 为自定义命令应用程序[设置持续部署](how-to-custom-commands-deploy-cicd.md)。 
                      
