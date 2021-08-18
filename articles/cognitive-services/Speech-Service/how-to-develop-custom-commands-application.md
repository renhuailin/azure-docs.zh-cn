---
title: 如何：开发自定义命令应用程序 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何开发自定义命令应用程序并对其进行自定义。 这些语音命令应用最适合用于“任务完成”或“指挥控制”方案。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: lajanuar
ms.openlocfilehash: cb97e41740997e35445cdf6dfb7281e54c3a5c16
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122069020"
---
# <a name="develop-custom-commands-applications"></a>开发自定义命令应用程序

本操作指南文章介绍如何开发和配置自定义命令应用程序。 自定义命令功能可帮助你生成针对语音优先交互式体验进行优化的丰富语音命令应用。 该功能最适合用于“任务完成”或“指挥控制”方案。 它特别适合用于物联网 (IoT) 设备以及环境监控设备和无外设设备。

在本文中，你将创建一个可以打开和关闭电视、设置温度以及设置闹钟的应用程序。 创建这些基本命令后，你将了解用来对命令进行自定义的以下选项：

* 将参数添加到命令
* 将配置添加到命令参数
* 生成交互规则
* 创建语言生成模板以生成语音响应
* 使用自定义语音工具

## <a name="create-an-application-by-using-simple-commands"></a>使用简单的命令创建应用程序

首先创建一个空的自定义命令应用程序。 有关详细信息，请参阅[快速入门](quickstart-custom-commands-application.md)。 在此应用程序中创建一个空白项目，而不要导入项目。

1. 在“名称”框中，输入项目名称 *Smart-Room-Lite*（或你所选的其他名称）。
1. 在“语言”列表中选择“英语(美国)”。 
1. 选择或创建 LUIS 资源。

   > [!div class="mx-imgBorder"]
   > ![显示“新建项目”窗口的屏幕截图。](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>更新 LUIS 资源（可选）

可以更新你在“新建项目”窗口中选择的创作资源。 还可以设置预测资源。

发布自定义命令应用程序时，一个预测资源将会用于识别。 在开发和测试阶段无需使用预测资源。

### <a name="add-a-turnon-command"></a>添加 TurnOn 命令

在创建的空 Smart-Room-Lite 自定义命令应用程序中添加一个命令。 该命令将处理言语 `Turn on the tv`。 它使用消息 `Ok, turning the tv on` 做出响应。

1. 在左侧窗格的顶部选择“新建命令”以创建一条新命令。 此时会打开“新建命令”窗口。
1. 在“名称”字段中，提供值 `TurnOn`。
1. 选择“创建”。

中间窗格将列出命令的属性。

下表解释了命令的配置属性。 有关详细信息，请参阅[自定义命令的概念和定义](./custom-commands-references.md)。

| 配置            | 说明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 示例句子 | 用户可以说出的用于触发此命令的示例言语。                                                                 |
| 参数       | 完成该命令而需要的信息。                                                                                |
| 完成规则 | 为履行该命令而要执行的操作。 示例：向用户做出响应，或者与 Web 服务通信。 |
| 交互规则   | 用于处理更具体或更复杂情形的其他规则。                                                              |


> [!div class="mx-imgBorder"]
> ![显示命令创建位置的屏幕截图。](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>添加示例句子

在“示例句子”部分，提供用户可以说出的示例句子。

1. 在中间窗格中，选择“示例句子”。
1. 在右侧窗格中添加示例：

    ```
    Turn on the tv
    ```

1.  在窗格顶部，选择“保存”。

目前尚未指定参数，因此可以转到“完成规则”部分。

#### <a name="add-a-completion-rule"></a>添加完成规则

接下来，该命令需要一个完成规则。 此规则让用户知道正在执行一项履行操作。

有关规则和完成规则的详细信息，请参阅[自定义命令的概念和定义](./custom-commands-references.md)。

1. 选择默认的完成规则“完成”。 然后按如下所示对其进行编辑：


    | 设置    | 建议的值                          | 说明                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **名称**       | `ConfirmationResponse`                  | 用于描述规则用途的名称          |
    | **条件** | 无                                     | 用于确定规则何时可以运行的条件    |
    | **操作**    | **发送语音响应** > **简单编辑器** > **第一个变体** > `Ok, turning the tv on` | 当规则条件求值为 true 时要执行的操作 |

   > [!div class="mx-imgBorder"]
   > ![显示要在何处创建语音响应的屏幕截图。](media/custom-commands/create-speech-response-action.png)

1. 选择“保存”以保存操作。
1. 返回“完成规则”部分，选择“保存”以保存所有更改。 

    > [!NOTE]
    > 并非一定要使用命令随附的默认完成规则。 可以删除默认的完成规则，并添加自己的规则。

### <a name="add-a-settemperature-command"></a>添加 SetTemperature 命令

现在再添加一个命令：`SetTemperature`。 此命令将提取单个言语 `Set the temperature to 40 degrees`，并使用消息 `Ok, setting temperature to 40 degrees` 做出响应。

若要创建新命令，请遵循你对 `TurnOn` 命令所用的步骤，但要使用示例句子 `Set the temperature to 40 degrees`。

然后按如下所示编辑现有的完成规则“完成”：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| **名称**  | `ConfirmationResponse`                  |
| **条件** | 无                                     |
| **操作**    | **发送语音响应** > **简单编辑器** > **第一个变体** > `Ok, setting temperature to 40 degrees` |

选择“保存”以保存对命令所做的全部更改。

### <a name="add-a-setalarm-command"></a>添加 SetAlarm 命令

创建新的 `SetAlarm` 命令。 使用示例句子 `Set an alarm for 9 am tomorrow`。 然后按如下所示编辑现有的完成规则“完成”：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| **名称**  | `ConfirmationResponse`                  |
| **条件** | 无                                     |
| **操作**    | **发送语音响应** > **简单编辑器** > **第一个变体** > `Ok, setting an alarm for 9 am tomorrow` |

选择“保存”以保存对命令所做的全部更改。

### <a name="try-it-out"></a>试试看

使用测试窗格测试应用程序的行为：

1. 在窗格的右上角，选择“训练”图标。
1. 训练完成后，选择“测试”。

使用语音或文本尝试以下言语示例：

- 键入：“set the temperature to 40 degrees”
- 预期响应：“Ok, setting temperature to 40 degrees”
- 键入：“turn on the tv”
- 预期响应：“Ok, turning the tv on”
- 键入：“set an alarm for 9 am tomorrow”
- 预期响应：“Ok, setting an alarm for 9 am tomorrow”

> [!div class="mx-imgBorder"]
> ![显示在网络聊天界面中测试的屏幕截图。](media/custom-commands/create-basic-test-chat-no-mic.png)

> [!TIP]
> 在测试窗格中，可以选择“轮次详细信息”，获取有关此语音输入或文本输入的处理状况的信息。

## <a name="add-parameters-to-commands"></a>向命令添加参数

本部分介绍如何将参数添加到命令。 命令需要使用参数来完成任务。 在复杂的方案中，可以使用参数来定义触发自定义操作的条件。

### <a name="configure-parameters-for-a-turnon-command"></a>为 TurnOn 命令配置参数

首先编辑现有的 `TurnOn` 命令以打开和关闭多个设备。

1. 既然该命令要处理打开和关闭设备的方案，因此请将其重命名为 *TurnOnOff*。
   1. 在左侧窗格中，选择“TurnOn”命令。 然后，在窗格顶部的“新建命令”旁边选择省略号 ( **...** ) 按钮。

   1. 选择“重命名”。 在“重命名命令”窗口中，将名称更改为 *TurnOnOff*。

1. 将一个新参数添加到命令。 该参数表示用户是要打开还是关闭设备。
   1. 在中间窗格的顶部，选择“添加”。 在下拉菜单中，选择“参数”。
   1. 在右侧窗格的“参数”部分的“名称”框中，添加 `OnOff`。 
   1. 选择“必需”。 在“为必需的参数添加响应”窗口中，选择“简单编辑器”。  在“第一个变体”字段中，添加“On or Off?”。
   1. 选择“更新”。

       > [!div class="mx-imgBorder"]
       > ![显示“为必需的参数添加响应”部分的屏幕截图，其中已选择“简单编辑器”选项卡。](media/custom-commands/add-required-on-off-parameter-response.png)

   1. 使用下表配置参数的属性。 有关命令的所有配置属性的信息，请参阅[自定义命令的概念和定义](./custom-commands-references.md)。


       | 配置      | 建议的值     | 说明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **名称**               | `OnOff`           | 参数的描述性名称                                                                           |
       | **是全局性的**          | 未选定       | 该复选框指示此参数的某个值是否全局应用于应用程序中的所有命令。|
       | **必需**           | 选定         | 该复选框指示在命令完成之前，此参数的某个值是否是必需的。 |
       | **必需参数的响应**      |**简单编辑器** > `On or Off?`      | 一条提示，请求提供此参数的值（如果未知）。 |
       | 类型               | **字符串**          | 参数类型，例如“数字”、“字符串”、“日期时间”或“地理位置”。   |
       | **配置**      | **接受来自内部目录的预定义输入值** | 对于字符串，此设置将输入限制为一组可能的值。 |
       | **预定义的输入值**     | `on`, `off`           | 一组可能的值及其别名。         |


   1. 若要添加预定义的输入值，请选择“添加预定义的输入”。 在“新建项”窗口中，按上表中所示键入一个 *名称*。 在本例中你不使用别名，因此可将此字段留空。

      > [!div class="mx-imgBorder"]
      > ![显示如何创建参数的屏幕截图。](media/custom-commands/create-on-off-parameter.png)

   1. 选择“保存”以保存参数的所有配置。

#### <a name="add-a-subjectdevice-parameter"></a>添加 SubjectDevice 参数

1. 若要添加另一个参数来表示可使用此命令控制的设备的名称，请选择“添加”。 使用以下配置。


    | 设置            | 建议的值       |
    | ------------------ | --------------------- |
    | **名称**               | `SubjectDevice`         |
    | **是全局性的**          | 未选定             |
    | **必需**           | 选定               |
    | **必需参数的响应**     | **简单编辑器** > `Which device do you want to control?`    | 
    | 类型               | **字符串**                |     
    | **配置**      | **接受来自内部目录的预定义输入值** | 
    | **预定义的输入值** | `tv`, `fan`               |
    | **别名** (`tv`)      | `television`, `telly`     |

1. 选择“保存”。

#### <a name="modify-example-sentences"></a>修改示例句子

对于使用参数的命令，添加涵盖所有可能组合的示例句子会很有帮助。 例如：

* 完整参数信息：`turn {OnOff} the {SubjectDevice}`
* 部分参数信息：`turn it {OnOff}`
* 无参数信息：`turn something`

有了信息丰富程度不同的多个示例句子，自定义命令应用程序可以使用部分信息得出单轮次解决方案和多轮次解决方案。

考虑到这些信息，请编辑示例句子以使用这些建议的参数：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

选择“保存”。

> [!TIP]
> 在示例句子编辑器中，使用大括号来引用参数。 例如，`turn {OnOff} the {SubjectDevice}`。
> 将一个选项卡用于前面创建的参数所支持的自动完成。

#### <a name="modify-completion-rules-to-include-parameters"></a>修改完成规则以包含参数

修改现有的完成规则 `ConfirmationResponse`。

1. 在“条件”部分，选择“添加条件”。 
1. 在“新建条件”窗口中的“类型”列表内，选择“必需参数”。   在随后出现的列表中，选择“OnOff”和“SubjectDevice”。 
1. 选择“创建”。
1. 在“操作”部分，通过将鼠标悬停在“发送语音响应”操作上并选择编辑按钮，对该操作进行编辑。  这一次请使用新建的 `OnOff` 和 `SubjectDevice` 参数：

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 选择“保存”。

选择右侧窗格顶部的“训练”图标，尝试使用所做的更改进行训练。

训练完成后，选择“测试”。 此时将显示“测试应用程序”窗口。 尝试以下交互：

- 输入：“turn off the tv”
- 输出：“Ok, turning off the tv”
- 输入：“turn off the television”
- 输出：“Ok, turning off the tv”
- 输入：“turn it off”
- 输出：“Which device do you want to control?”
- 输入：“the tv”
- 输出：“Ok, turning off the tv”

### <a name="configure-parameters-for-a-settemperature-command"></a>配置 SetTemperature 命令的参数

修改 `SetTemperature` 命令，使其能够根据用户的指示设置温度。

添加 `Temperature` 参数。 使用以下配置：

| 配置      | 建议的值     |
| ------------------ | ----------------|
| **名称**               | `Temperature`           |
| **必需**           | 选定         |
| **必需参数的响应**      | **简单编辑器** > `What temperature would you like?`
| 类型               | `Number`          |


编辑示例言语以使用以下值。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

编辑现有的完成规则。 使用以下配置。

| 配置      | 建议的值     |
| ------------------ | ----------------|
| **条件**         | **必需参数** > **Temperature**           |
| **操作**           | **发送语音响应** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>配置 SetAlarm 命令的参数

添加名为 `DateTime` 的参数。 使用以下配置。

   | 设置                           | 建议的值                     |
   | --------------------------------- | ----------------------------------------|
   | **名称**                              | `DateTime`                               |
   | **必需**                          | 选定                                 |
   | **必需参数的响应**   | **简单编辑器** > `For what time?`            |
   | 类型                              | **DateTime**                                |
   | **日期默认值**                     | 如果缺少日期，则使用当天的日期。            |
   | **时间默认值**                     | 如果缺少时间，则使用当天的起始时间。     |


> [!NOTE]
> 本文主要使用“字符串”、“数字”和“日期时间”参数类型。 有关所有受支持参数类型及其属性的列表，请参阅[自定义命令的概念和定义](./custom-commands-references.md)。


编辑示例言语。 请使用以下值。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

编辑现有的完成规则。 使用以下配置。

   | 设置    | 建议的值                               |
   | ---------- | ------------------------------------------------------- |
   | **操作**    | **发送语音响应** > `Ok, alarm set for {DateTime}`  |

使用与不同命令相关的言语对这三个命令一起进行测试。 （可以在不同的命令之间切换。）

- 输入：“Set an alarm”
- 输出：“For what time?”
- 输入：“Turn on the tv”
- 输出：“Ok, turning the tv on”
- 输入：“Set an alarm”
- 输出：“For what time?”
- 输入：“5 pm”
- 输出：“Ok, alarm set for 2020-05-01 17:00:00”

## <a name="add-configurations-to-command-parameters"></a>将配置添加到命令参数

本部分详细介绍高级参数配置，包括：

 - 参数值如何属于自定义命令应用程序外部定义的某个集。
 - 如何针对参数值添加验证子句。

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>将参数配置为外部目录实体

使用自定义命令功能可将字符串类型的参数配置为引用通过 Web 终结点托管的外部目录。 因此，无需编辑自定义命令应用程序，就能单独更新外部目录。 存在大量目录项时，此方法很有用。

重新使用 `TurnOnOff` 命令中的 `SubjectDevice` 参数。 此参数的当前配置为“接受来自内部目录的预定义输入”。 此配置引用参数配置中设备的静态列表。 将此内容移出到可单独更新的外部数据源。

若要移动内容，请先添加一个新的 Web 终结点。 在左侧窗格中，转到“Web 终结点”部分。 在该部分添加新的 Web 终结点。 使用以下配置。

| 设置 | 建议的值 |
|----|----|
| **名称** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **方法** | **GET** |


如果建议的 URL 值不适合你，请配置并托管一个可返回 JSON 文件（其中包含可控制的设备的列表）的 Web 终结点。 该 Web 终结点应返回如下格式的 JSON 文件：

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

接下来，转到“SubjectDevice”参数设置页。 设置以下属性。

| 设置 | 建议的值 |
| ----| ---- |
| **配置** | **接受来自外部目录的预定义输入** |                               
| **目录终结点** | `getDevices` |
| **方法** | **GET** |

再选择“保存”。

> [!IMPORTANT]
> 除非在左侧窗格中的“Web 终结点”部分设置了 Web 终结点，否则不会显示用于将参数配置为接受来自外部目录的输入的选项。

选择“训练”以尝试该配置。 训练完成后，选择“测试”并尝试交互几次。

* 输入：“turn on”
* 输出：“Which device do you want to control?”
* 输入：“lights”
* 输出：“Ok, turning the lights on”

> [!NOTE]
> 现在，可以控制托管在 Web 终结点上的所有设备。 但仍需训练应用程序以测试新的更改，然后重新发布应用程序。

### <a name="add-validation-to-parameters"></a>添加对参数的验证

“验证”是应用于某些参数类型的构造，可让你针对参数的值配置约束。 如果值不在约束范围内，则验证会提示你予以更正。 有关扩展验证构造的参数类型的列表，请参阅[自定义命令的概念和定义](./custom-commands-references.md)。

使用 `SetTemperature` 命令测试验证。 使用以下步骤添加针对 `Temperature` 参数的验证。

1. 在左侧窗格中，选择“SetTemperature”命令。
1. 在中间窗格中，选择“Temperature”。
1. 在右侧窗格中，选择“添加验证”。
1. 在“新建验证”窗口中，按下表所示配置验证。 然后选择“创建”。


    | 参数配置 | 建议的值 | 说明 |
    | ---- | ---- | ---- |
    | **最小值** | `60` | 适用于数字参数，表示此参数可以采用的最小值 |
    | **最大值** | `80` | 适用于数字参数，表示此参数可以采用的最大值 |
    | **失败响应** |  **简单编辑器** > **第一个变体** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 验证失败时请求提供新值的提示 |

    > [!div class="mx-imgBorder"]
    > ![展示如何添加范围验证的屏幕截图。](media/custom-commands/add-validations-temperature.png)

选择右侧窗格顶部的“训练”图标来尝试使用此配置。 训练完成后，选择“测试”。 尝试交互几次：

- 输入：“Set the temperature to 72 degrees”
- 输出：“Ok, setting temperature to 72 degrees”
- 输入：“Set the temperature to 45 degrees”
- 输出：“Sorry, I can only set temperature between 60 and 80 degrees”
- 输入：“make it 72 degrees instead”
- 输出：“Ok, setting temperature to 72 degrees”

## <a name="add-interaction-rules"></a>添加交互规则

交互规则是用于处理具体或复杂情形的补充规则。 尽管你可以随意创作自己的交互规则，但本示例将使用适用于以下方案的交互规则：

* 确认命令
* 将一步式更正添加到命令

有关交互规则的详细信息，请参阅[自定义命令的概念和定义](./custom-commands-references.md)。

### <a name="add-confirmations-to-a-command"></a>在命令中添加确认

若要添加确认，请使用 `SetTemperature` 命令。 若要实现确认，请使用以下步骤创建交互规则：

1. 在左侧窗格中，选择“SetTemperature”命令。
1. 在中间窗格中，选择“添加”以添加交互规则。 然后选择“交互规则” > “确认命令”。 

    此操作将添加三个交互规则。 这些规则请求用户确认闹钟的日期和时间。 它们预期用户对下一轮次做出确认（是或否）。

    1. 使用以下配置修改“确认命令”交互规则：
        1. 将名称更改为“确认温度”。
        1. 添加新条件：“必需参数” > “Temperature”。
        1. 添加新操作：“类型” > “发送语音响应” > “Are you sure you want to set the temperature as {Temperature} degrees?”
        1. 在“预期”部分，保留默认值“预期收到用户确认”。 

         > [!div class="mx-imgBorder"]
         > ![展示如何创建必需参数响应的屏幕截图。](media/custom-speech-commands/add-validation-set-temperature.png)


    1. 修改“确认成功”交互规则，以处理成功确认（用户说“yes”）。

          1. 将名称更改为“确认温度成功”。
          1. 保留现有的“确认成功”条件。
          1. 添加新条件：“类型” > “必需参数” > “Temperature”。
          1. 将默认的“执行后状态”值保留为“执行完成规则”。 

    1. 修改“拒绝确认”交互规则，以处理拒绝确认（用户说“no”）时的情况。

          1. 将名称更改为“拒绝确认温度”。
          1. 保留现有的“拒绝确认”条件。
          1. 添加新条件：“类型” > “必需参数” > “Temperature”。
          1. 添加新操作：“类型” > “发送语音响应” > “No problem.What temperature then?”。
          1. 将默认的“执行后状态”值更改为“等待用户输入”。 

> [!IMPORTANT]
> 在本文中，你将使用内置确认功能。 也可以手动逐个添加交互规则。

选择“训练”来尝试使用所做的更改。 训练完成后，选择“测试”。

- **输入**：“Set temperature to 80 degrees”
- **输出**：“are you sure you want to set the temperature as 80 degrees?”
- **输入**：“No”
- **输出**：“No problem. What temperature then?”
- **输入**：“72 degrees”
- **输出**：“are you sure you want to set the temperature as 72 degrees?”
- **输入**：“Yes”
- **输出**：“OK, setting temperature to 72 degrees”

### <a name="implement-corrections-in-a-command"></a>在命令中实现更正

在本部分，你将配置一步式更正。 履行操作运行后将使用此项更正。 本部分还会演示一个示例，说明如何在命令尚未履行的情况下默认启用更正。 若要在命令尚未完成时添加更正，请添加新参数 `AlarmTone`。

在左侧窗格中，选择“SetAlarm”命令。 然后添加新参数 **AlarmTone**。

- **名称** > `AlarmTone`
- “类型” > “字符串”
- “默认值” > “Chimes”
- “配置” > “接受来自内部目录的预定义输入值”
- “预定义输入值” > “Chimes”、“Jingle”和“Echo”（这些值是单独的预定义输入。）


接下来，将 **DateTime** 参数的响应更新为“Ready to set alarm with tone as {AlarmTone}.For what time?”。 然后按如下所示修改完成规则：

1. 选择现有的完成规则“ConfirmationResponse”。
1. 在右侧窗格中，将鼠标悬停在现有操作上，并选择“编辑”。
1. 将语音响应更新为 `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}`。

> [!IMPORTANT]
> 无需在使用中的命令中指定任何显式配置即可更改闹钟铃声。 例如，在命令尚未完成时可以更改铃声。 默认情况下会为所有命令参数启用更正，而不管轮次编号是什么（如果命令尚待履行）。

#### <a name="implement-a-correction-when-a-command-is-finished"></a>完成命令后实现更正

即使命令已完成，自定义命令平台也允许执行一步式更正。 此功能默认未启用。 必须显式对其进行配置。

使用以下步骤配置一步式更正：

1. 在 **SetAlarm** 命令中，添加“更新上一个命令”类型的交互规则，以更新先前设置的闹钟。 将交互规则重命名为“更新前一个闹钟”。
1. 保留默认条件：“需要更新上一个命令”。
1. 添加新条件：“类型” > “必需参数” > “DateTime”。
1. 添加新操作：“类型” > “发送语音响应” > “简单编辑器” > “Updating previous alarm time to {DateTime}”。
1. 将默认的“执行后状态”值保留为“命令已完成”。 

选择“训练”来尝试使用所做的更改。 等待训练完成，然后选择“测试”。

- **输入**：“Set an alarm.”
- **输出**：“Ready to set alarm with tone as Chimes. For what time?”
- **输入**：“Set an alarm with the tone as Jingle for 9 am tomorrow.”
- **输出**：“OK, alarm set for 2020-05-21 09:00:00. The alarm tone is Jingle.”
- **输入**：“No, 8 am.”
- **输出**：“Updating previous alarm time to 2020-05-29 08:00.”

> [!NOTE]
> 在真实应用程序中，还需要在此更正规则的“操作”部分，向客户端发回一个活动或调用 HTTP 终结点来更新系统中的闹钟时间。 此操作应该专门负责更新闹钟时间。 它不应负责命令的任何其他特性。 在本例中，该特性是闹钟铃声。

## <a name="add-language-generation-templates-for-speech-responses"></a>添加语言生成模板以生成语音响应

使用语言生成 (LG) 模板可以自定义要发送到客户端的响应。 它们会在响应中引入变体。 可通过以下方式实现语言生成：

* 语言生成模板。
* 自适应表达式。

自定义命令模板基于 Bot Framework 的 [LG 模板](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。 由于自定义命令功能可根据需要创建新的 LG 模板（用于在参数或操作中生成语音响应），因此你无需指定 LG 模板的名称。

因此，无需按如下所示定义模板：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

而可以定义不带名称的模板的正文，如下所示：

> [!div class="mx-imgBorder"]
> ![显示模板编辑器示例的屏幕截图。](./media/custom-commands/template-editor-example.png)


此项更改将在发送到客户端的语音响应中引入变体。 对于言语，相应的语音响应是从提供的选项中随机选出的。

利用 LG 模板还可以使用自适应表达式来为命令定义复杂的语音响应。 有关详细信息，请参阅 [LG 模板格式](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。

默认情况下，自定义命令功能支持所有功能，只是存在以下细微差别：

* 在 LG 模板中，实体以 `${entityName}` 形式表示。 自定义命令功能不使用实体。 但是，可以使用 `${parameterName}` 或 `{parameterName}` 表示形式的参数作为变量。
* 自定义命令功能不支持模板撰写和扩展，因为你永远不会直接编辑 *.lg* 文件， 而只会编辑自动创建的模板的响应。
* 自定义命令功能不支持 LG 注入的自定义函数。 支持预定义的函数。
* 自定义命令功能不支持 `strict`、`replaceNull` 和 `lineBreakStyle` 等选项。

### <a name="add-template-responses-to-a-turnonoff-command"></a>将模板响应添加到 TurnOnOff 命令

修改 `TurnOnOff` 命令以添加一个新参数。 使用以下配置。

| 设置            | 建议的值       |
| ------------------ | --------------------- |
| **名称**               | `SubjectContext`         |
| **是全局性的**          | 未选定             |
| **必需**           | 未选定               |
| 类型               | **字符串**                |
| **默认值**      | `all` |
| **配置**      | **接受来自内部目录的预定义输入值** |
| **预定义的输入值** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>修改完成规则

编辑现有完成规则“ConfirmationResponse”的“操作”部分。  在“编辑操作”窗口中，切换到“模板编辑器”。  然后将文本替换为以下示例。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

使用以下输入和输出来训练并测试应用程序。 请注意响应的变体。 变体是使用模板值的多个替代项以及自适应表达式创建的。

* 输入：“turn on the tv”
* 输出：“Ok, turning the tv on”
* 输入：“turn on the tv”
* 输出：“Done, turned on the tv”
* 输入：“turn off the lights”
* 输出：“Ok, turning all the lights off”
* 输入：“turn off room lights”
* 输出：“Ok, turning off the room lights”

## <a name="use-a-custom-voice"></a>使用自定义语音

自定义命令响应的另一种自定义方法是选择一种输出语音。 使用以下步骤将默认语音切换为自定义语音：

1. 在自定义命令应用程序的左侧窗格中，选择“设置”。
1. 在中间窗格中，选择“自定义语音”。
1. 在表中，选择一种自定义语音或公共语音。
1. 选择“保存”。

> [!div class="mx-imgBorder"]
> ![显示示例句子和参数的屏幕截图。](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> 对于公共语音，神经类型仅适用于特定的区域。 有关详细信息，请参阅[语音服务支持的区域](./regions.md#neural-and-standard-voices)。
>
> 可以在“自定义语音”项目页面上创建自定义语音。 有关详细信息，请参阅[自定义语音入门](./how-to-custom-voice.md)。

现在，应用程序将以选定的语音而不是默认语音做出响应。

## <a name="next-steps"></a>后续步骤

* 了解如何使用语音 SDK [将自定义命令应用程序与客户端应用相集成](how-to-custom-commands-setup-speech-sdk.md)。
* 使用 Azure DevOps 为自定义命令应用程序[设置持续部署](how-to-custom-commands-deploy-cicd.md)。
