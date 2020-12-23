---
title: 如何：开发自定义命令应用程序-语音服务
titleSuffix: Azure Cognitive Services
description: 在本操作方法指南中，你将了解如何开发和自定义自定义命令应用程序。 通过自定义命令，可以轻松地构建针对语音首次交互体验进行了优化的丰富语音命令应用，最适合用于任务完成或命令和控制方案，尤其适合物联网 (IoT) 设备、环境和无外设设备。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632637"
---
# <a name="develop-custom-commands-applications"></a>开发自定义命令应用程序

在本操作方法指南中，你将了解如何开发和配置自定义命令应用程序。 通过自定义命令，可以轻松地构建针对语音首次交互体验进行了优化的丰富语音命令应用，最适合用于任务完成或命令和控制方案，尤其适合物联网 (IoT) 设备、环境和无外设设备。

在本文中，你将创建一个应用程序，用于打开和关闭电视，设置温度，以及设置警报。 创建这些基本命令后，将介绍以下用于自定义命令的选项：

* 向命令添加参数
* 将配置添加到命令参数
* 构建交互规则
* 为语音响应创建语言生成模板
* 使用自定义语音 

## <a name="create-application-with-simple-commands"></a>通过简单的命令创建应用程序

首先，请首先创建一个空的自定义命令应用程序。 有关详细信息，请参阅 [快速入门](quickstart-custom-commands-application.md)。 此时，不会导入项目，而是创建一个空项目。

1. 在 " **名称** " 框中，输入 "项目名称" 作为 `Smart-Room-Lite` (或您选择的其他内容) 。
1. 在 " **语言** " 列表中，选择 " **英语 (美国)**"。
1. 选择或创建所选的 LUIS 资源。

   > [!div class="mx-imgBorder"]
   > ![创建项目](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a> (可选) 更新 LUIS 资源

您可以更新在 " **新建项目** " 窗口中选择的创作资源，并设置一个预测资源。 发布自定义命令应用程序时，预测资源用于识别。 在开发和测试阶段，无需预测资源。

### <a name="add-turnon-command"></a>添加 TurnOn 命令

在刚创建的空的 **智能空间精简** 自定义命令应用程序中，添加一个简单的命令，用于处理查询文本， `turn on the tv` 并使用消息进行响应 `Ok, turning the tv on` 。

1. 通过选择左侧窗格顶部的 " **新建命令** " 来创建新命令。 此时将打开 **新的命令** 窗口。
1. 为 " **名称** " 字段提供值作为 **TurnOn**。
1. 选择“创建”。

中间窗格列出了命令的不同属性。 配置命令的以下属性。 有关命令的所有配置属性的说明，请参阅 " [引用](./custom-commands-references.md)"。

| 配置            | 说明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **示例句子** | 示例最谈话用户可以说触发此命令                                                                 |
| **参数**       | 完成命令所需的信息                                                                                |
| **完成规则** | 要执行以执行命令的操作。 例如，要响应用户或与另一个 web 服务进行通信。 |
| **交互规则**   | 用于处理更为具体或复杂的情况的其他规则                                                              |


> [!div class="mx-imgBorder"]
> ![创建命令](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>添加示例句子

让我们从 " **示例句子** " 部分开始，并提供用户可以说出的示例。

1. 在中间窗格中选择 " **示例句子** " 部分。
1. 在最右侧的窗格中，添加示例：

    ```
    turn on the tv
    ```

1.  选择窗格顶部的 " **保存** "。

目前，我们没有参数，因此可以移动到 **完成规则** 部分。

#### <a name="add-a-completion-rule"></a>添加完成规则

接下来，该命令需要具有完成规则。 此规则告知用户正在执行执行操作。 若要阅读有关规则和完成规则的详细信息，请参阅 " [参考](./custom-commands-references.md)"。

1. 选择 "完成" "默认完成规则" 并按如下所示对其 **进行** 编辑：

    
    | 设置    | 建议的值                          | 说明                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **名称**       | ConfirmationResponse                  | 描述规则用途的名称          |
    | **条件** | 无                                     | 确定何时可以运行规则的条件    |
    | **操作**    | > 简单编辑器 > 第一种变体发送语音响应 > `Ok, turning the tv on` | 规则条件为 true 时要执行的操作 |

   > [!div class="mx-imgBorder"]
   > ![创建语音响应](media/custom-commands/create-speech-response-action.png)

1. 选择 " **保存** " 以保存操作。
1. 返回 " **完成规则** " 部分，选择 " **保存** " 以保存所有更改。 

    > [!NOTE]
    > 不需要使用命令附带的默认完成规则。 如果需要，可以删除现有的默认完成规则并添加自己的规则。

### <a name="add-settemperature-command"></a>添加 SetTemperature 命令

现在，再添加一个命令 **SetTemperature** ，它将采用单个查询文本， `set the temperature to 40 degrees` 并使用消息进行响应 `Ok, setting temperature to 40 degrees` 。

按照 **TurnOn** 命令中所示的步骤，使用示例句子 "**将温度设置为40度**" 来创建新命令。

然后，按如下所示 **编辑现有完成** 规则：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| 名称  | ConfirmationResponse                  |
| 条件 | 无                                     |
| Actions    | > 简单编辑器 > 第一种变体发送语音响应 > `Ok, setting temperature to 40 degrees` |

选择 " **保存** " 以保存对命令所做的所有更改。

### <a name="add-setalarm-command"></a>添加 SetAlarm 命令

使用示例句子创建新的命令 **SetAlarm** ，"**为明天的上午9点设置警报**"。 然后，按如下所示 **编辑现有完成** 规则：

| 设置    | 建议的值                          |
| ---------- | ---------------------------------------- |
| 规则名称  | ConfirmationResponse                  |
| 条件 | 无                                     |
| Actions    | > 简单编辑器 > 第一种变体发送语音响应 >`Ok, setting an alarm for 9 am tomorrow` |

选择 " **保存** " 以保存对命令所做的所有更改。

### <a name="try-it-out"></a>试试看

使用 "测试聊天" 面板测试行为。 选择位于右窗格顶部的 " **定型** " 图标。 训练完成后，选择 " **测试**"。 通过语音或文本试用以下查询文本示例：

- 键入：将温度设置为40度
- 预期响应：正常，将温度设置为40度
- 键入：打开电视
- 预期响应： Ok，打开电视
- 键入：将警报设置为明天上午9点
- 预期响应：好，将警报设置为明天上午9点

> [!div class="mx-imgBorder"]
> ![测试 web 聊天](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> 在 "测试" 面板中，可以选择 " **打开详细** 信息" 以获取有关如何处理此语音/文本输入的信息。

## <a name="add-parameters-to-commands"></a>向命令添加参数

本部分介绍如何将参数添加到命令中。 参数是命令完成任务所需的信息。 在复杂的方案中，还可以使用参数定义触发自定义操作的条件。

### <a name="configure-parameters-for-turnon-command"></a>为 TurnOn 命令配置参数

首先编辑现有的 **TurnOn** 命令以打开和关闭多个设备。

1. 现在，命令将同时处理 on 和 off 方案，将命令重命名为 **TurnOnOff**。
   1. 在左窗格中，选择 " **TurnOn** " 命令，然后选择窗格顶部的 " **新建命令** " 旁的省略号 ( ... ") 。
   
   1. 选择 " **重命名**"。 在 " **重命名" 命令** 窗口中，将 " **名称** " 更改为 **TurnOnOff**。

1. 接下来，向此命令添加一个新参数，该参数表示用户是要打开还是关闭设备。
   1. 选择中间窗格顶部的 "  **添加** "。 从下拉选择 " **参数**"。
   1. 在右窗格的 " **参数** " 部分中，在 " **名称** " 框中添加 " **麦克风**" 值。
   1. 选择“必需”。 在 " **为所需参数添加响应** " 窗口中，选择 " **简单编辑器**"。 在 **第一** 种情况下，添加
        ```
        On or Off?
        ```
   1. 选择“更新”。

       > [!div class="mx-imgBorder"]
       > ![创建所需的参数响应](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 现在，我们配置参数属性。 有关命令的所有配置属性的说明，请参阅 " [引用](./custom-commands-references.md)"。 配置参数的属性，如下所示：
      

       | 配置      | 建议的值     | 说明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名称               | `OnOff`           | 参数的描述性名称                                                                           |
       | 为全局          | unchecked       | 指示此参数的值是否全局应用于应用程序中所有命令的复选框|
       | 必需           | checked         | 一个复选框，该复选框指示在完成命令之前是否需要此参数的值 |
       | 响应所需的参数      |简单编辑器 > `On or Off?`      | 在未知情况下询问此参数值的提示 |
       | 类型               | 字符串          | 参数的类型，例如 Number、String、Date Time 或 Geography   |
       | 配置      | 接受来自内部目录的预定义输入值 | 对于字符串，这会将输入限制为一组可能值 |
       | 预定义的输入值     | `on`, `off`           | 可能的值及其别名集         |
       
        
   1. 要添加预定义的输入值，请选择 " **添加预定义的输入** "，并在 " **新建项**  " 窗口中键入上表中提供的 **名称** 。 在这种情况下，我们不使用别名，因此可将其留空。
   
      > [!div class="mx-imgBorder"]
      > ![Create 参数](media/custom-commands/create-on-off-parameter.png)

   1. 选择 " **保存** " 以保存参数的所有配置。
 
#### <a name="add-subjectdevice-parameter"></a>添加 SubjectDevice 参数 

1. 接下来，再次选择 " **添加** " 以添加一个参数，用于表示可使用此命令控制的设备的名称。 使用以下配置。


    | 设置            | 建议的值       |
    | ------------------ | --------------------- |
    | 名称               | `SubjectDevice`         |
    | 为全局          | unchecked             |
    | 必需           | checked               |
    | 响应所需的参数     | 简单编辑器 > `Which device do you want to control?`    | 
    | 类型               | 字符串                |          |
    | 配置      | 接受来自内部目录的预定义输入值 | 
    | 预定义的输入值 | `tv`, `fan`               |
    | 别名 (`tv`)       | `television`, `telly`     |

1. 选择“保存”

#### <a name="modify-example-sentences"></a>修改示例句子

对于带有参数的命令，添加涵盖所有可能组合的示例句子很有用。 例如：

* 完整的参数信息- `turn {OnOff} the {SubjectDevice}`
* 部分参数信息- `turn it {OnOff}`
* 无参数信息- `turn something`

具有不同信息度的示例句子允许自定义命令应用程序解析包含部分信息的一次性解析和多轮解析。

考虑到这一点，按如下所示编辑示例句子以使用参数：

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

选择“保存”。

> [!TIP]
> 示例句子编辑器使用大括号来引用参数。 - `turn {OnOff} the {SubjectDevice}` 使用选项卡自动完成由以前创建的参数支持的功能。

#### <a name="modify-completion-rules-to-include-parameters"></a>修改完成规则以包含参数

修改现有完成规则 **ConfirmationResponse**。

1. 在 " **条件** " 部分中，选择 " **添加条件**"。
1. 在 " **新建条件** " 窗口的 " **类型** " 列表中，选择 " **必需参数**"。 在下面的检查列表中，选中 " **麦克风** " 和 " **SubjectDevice**"。
1. 将 **IsGlobal** 保留为未选中状态。
1. 选择“创建”。
1. 在 " **操作** " 部分中，通过将鼠标悬停在操作上并选择 "编辑" 按钮来编辑现有的 " **发送语音响应** " 操作。 这一次，将使用新创建的 **麦克风** 和 **SubjectDevice** 参数

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. 选择“保存”。

通过选择右窗格顶部的 " **训练** " 图标来尝试更改。 训练完成后，选择 " **测试**"。 **您的应用程序** 窗口将出现。 尝试下列交互。

- 输入：关闭电视
- 输出：确定，关闭电视
- 输入：关闭电视
- 输出：确定，关闭电视
- 输入：将其关闭
- 输出：要控制哪个设备？
- 输入：电视
- 输出：确定，关闭电视

### <a name="configure-parameters-for-settemperature-command"></a>为 SetTemperature 命令配置参数

修改 **SetTemperature** 命令，使其能够按用户的指示设置温度。

用以下配置添加新的参数 **温度**

| 配置      | 建议的值     |
| ------------------ | ----------------|
| 名称               | `Temperature`           |
| 必选           | checked         |
| 响应所需的参数      | 简单编辑器 > `What temperature would you like?`
| 类型               | 数字          |


将示例最谈话编辑为以下值。

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

按照以下配置编辑现有的完成规则。

| 配置      | 建议的值     |
| ------------------ | ----------------|
| 条件         | 要求的参数 > 温度           |
| Actions           | 发送语音响应 > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>配置 SetAlarm 命令的参数

使用以下配置添加一个名为 **DateTime** 的参数。

   | 设置                           | 建议的值                     | 
   | --------------------------------- | ----------------------------------------|
   | 名称                              | `DateTime`                               |
   | 必选                          | checked                                 |
   | 响应所需的参数   | 简单编辑器 > `For what time?`            | 
   | 类型                              | DateTime                                |
   | 日期默认值                     | 如果当前缺少日期，请使用            |
   | 时间默认值                     | 如果缺少时间，则使用 "日开始"     |


> [!NOTE]
> 本文主要介绍了如何使用字符串、数字和日期时间参数类型。 对于所有受支持的参数类型及其属性的列表，请参阅 " [引用](./custom-commands-references.md)"。


将最谈话示例编辑为以下值。

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

按照以下配置编辑现有的完成规则。

   | 设置    | 建议的值                               |
   | ---------- | ------------------------------------------------------- |
   | Actions    | 发送语音响应- `Ok, alarm set for {DateTime}`  |

使用与不同命令相关的最谈话，同时测试所有三个命令。 请注意，你可以在不同的命令之间切换。

- 输入：设置警报
- 输出：什么时间？
- 输入：打开电视
- 输出：好，打开电视
- 输入：设置警报
- 输出：什么时间？
- 输入：下午
- 输出：正常，为 2020-05-01 17:00:00 设置的警报

## <a name="add-configurations-to-commands-parameters"></a>向命令参数添加配置

在本部分中，将详细了解高级参数配置，包括：

 - 如何将参数值归属于在外部定义为自定义命令应用程序的集
 - 如何将验证子句添加到参数的值上

### <a name="configure-parameter-as-external-catalog-entity"></a>将参数配置为外部目录实体

自定义命令允许您将字符串类型参数配置为引用通过 web 终结点承载的外部目录。 这使你无需对自定义命令应用程序进行编辑即可单独更新外部目录。 此方法在目录条目数量很大的情况下非常有用。

使用 **TurnOnOff** 命令中的 **SubjectDevice** 参数。 此参数的当前配置 **接受来自内部目录的预定义输入**。 这是指参数配置中定义的设备的静态列表。 我们想要将此内容移到可以单独更新的外部数据源。

为此，请首先添加新的 web 终结点。 在左侧窗格中转到 " **web 终结点** " 部分，并添加具有以下配置的新 Web 终结点。

| 设置 | 建议的值 |
|----|----|
| 名称 | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| 方法 | GET |


如果 URL 的建议值对你不起作用，则需要配置并托管一个简单的 web 终结点，该终结点将返回一个 json，其中包含可控制的设备列表。 Web 终结点应返回格式为的 json，如下所示：
    
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

接下来，转到 " **SubjectDevice** 参数设置" 页，将属性更改为以下项。

| 设置 | 建议的值 |
| ----| ---- |
| 配置 | 接受来自外部目录的预定义输入 |                               
| 目录终结点 | getDevices |
| 方法 | GET |

然后选择“保存”。

> [!IMPORTANT]
> 除非在左窗格的 " **web 终结点** " 部分中设置了 web 终结点，否则不会显示用于将参数配置为接受来自外部目录的输入的选项。

通过选择 " **训练** " 并等待训练完成来尝试一下。 训练完成后，请选择 " **测试** "，然后尝试几个交互。

* 输入：开启
* 输出：要控制哪个设备？
* 输入：光源
* 输出：好，打开灯

> [!NOTE]
> 请注意，现在可以如何控制在 web 终结点上托管的所有设备。 你仍需要对应用程序进行定型，以便测试新的更改并重新发布应用程序。

### <a name="add-validation-to-parameters"></a>向参数添加验证

**验证** 是适用于某些参数类型的构造，这些参数类型允许你对参数的值配置约束，并且如果值不在约束范围内，则提示进行更正。 有关扩展验证构造的参数类型的完整列表，请参阅 [引用](./custom-commands-references.md)

使用 **SetTemperature** 命令测试验证。 使用以下步骤为 **温度** 参数添加验证。

1. 在左侧窗格中选择 " **SetTemperature** " 命令。
1. 在中间窗格中选择 "  **温度** "。
1. 在右窗格中选择 " **添加验证** "。
1. 在 " **新建验证** " 窗口中，按如下所示配置验证，然后选择 " **创建**"。


    | 参数配置 | 建议的值 | 说明 |
    | ---- | ---- | ---- |
    | 最小值 | `60` | 对于 Number 参数，此参数可以采用的最小值 |
    | 最大值 | `80` | 对于 Number 参数，此参数可以采用的最大值 |
    | 故障响应 |  简单编辑器 > 第一变化 > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | 验证失败时提示要求输入新值 |

    > [!div class="mx-imgBorder"]
    > ![添加范围验证](media/custom-commands/add-validations-temperature.png)

通过选择右侧窗格上的 " **定型** " 图标来尝试一下。 训练完成后，请选择 " **测试** "，并尝试一些交互：

- 输入：将温度设置为72度
- 输出：正常，将温度设置为72度
- 输入：将温度设置为45度
- 输出：抱歉，只能设置60到80度之间的温度
- 输入：改为72度
- 输出：正常，将温度设置为72度

## <a name="add-interaction-rules"></a>添加交互规则

交互规则是用于处理特定或复杂情况的 *其他规则* 。 虽然你可以自由创作你自己的自定义交互规则，但在此示例中，你将对以下目标方案使用交互规则：

* 确认命令
* 向命令添加单步更正

若要了解有关交互规则的详细信息，请参阅 [参考](./custom-commands-references.md) 部分。

### <a name="add-confirmations-to-a-command"></a>向命令添加确认

若要添加确认，请使用 **SetTemperature** 命令。 若要完成确认，请使用以下步骤创建交互规则。

1. 在左窗格中选择 " **SetTemperature** " 命令。
1. 通过在中间窗格中选择 " **添加** " 来添加交互规则。 然后选择 "**交互规则**" "  >  **确认" 命令**。

    此操作将添加三个交互规则，这些规则将要求用户确认警报的日期和时间，并在下一轮需要确认 (是/否) 。

    1. 按照以下配置修改 **Confirm 命令** 交互规则：
        1. 重命名 **名称** 以 **确认温度**。
        1. 添加新条件作为 **必需参数**  >  **温度**。
        1. 将新操作添加为 **类型**  >  **发送语音响应**  >  **是否确实要将温度设置为 {温度} 度？**
        1. 保留 "**预期**" 部分中的 "期望 **用户确认**" 的默认值。
      
         > [!div class="mx-imgBorder"]
         > ![创建所需的参数响应](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. 修改 **确认成功** 交互规则以处理成功的确认 (用户说 "是") 。
      
          1. 将 **名称** 修改为 **确认温度已成功**。
          1. 保持已有的 **确认已成功** 。
          1. 添加新条件作为 **类型**  >  **所需参数**  >  **温度**。
          1. 将 **执行后状态** 的默认值保留为 " **执行完成规则**"。

    1. 修改 **确认拒绝** 交互规则以在确认被拒绝时处理方案， (用户未提到) 。

          1. 将 **名称** 修改为 **确认温度被拒绝**。
          1. 保留 "已有 **确认已拒绝** " 条件。
          1. 添加新条件作为 **类型**  >  **所需参数**  >  **温度**。
          1. 将新操作添加为"  >  **发送语音响应**  >  **无问题" 类型。什么是温度？**
          1. 将 **执行后状态** 的默认值保留为 " **等待用户输入**"。

> [!IMPORTANT]
> 本文使用内置确认功能。 还可以手动逐个添加交互规则。
   
尝试更改，方法是选择 " **训练**"，等待训练完成，然后选择 " **测试**"。

- **输入**：将温度设置为80度
- **输出**：是否确实要将温度设置为80度？
- **输入**：否
- **输出**：没有问题。 什么是温度？
- **输入**：72度
- **输出**：是否确实要将温度设置为72度？
- **输入**：是
- **输出**：正常，将温度设置为83度

### <a name="implement-corrections-in-a-command"></a>在命令中实现更正

在本部分中，你将配置一个单步更正，该更正将在执行完成操作后使用。 还会看到一个示例，说明如何默认启用更正，以防该命令尚未完成。 若要在命令未完成时添加更正，请添加新参数 **AlarmTone**。

从左窗格中选择 " **SetAlarm** " 命令，然后添加新参数 " **AlarmTone**"。
        
- **名称**  > **AlarmTone**
- **类型**  > **字符串**
- **默认值**  > **Chimes**
- **配置**  > **接受来自内部目录的预定义输入值**
- **预定义的输入值**  > 将 **Chimes**、**曲调** 和 **Echo** 作为单个预定义的输入


接下来，将 **DateTime** 参数的响应更新为 "就绪"， **以将音调设置为 {AlarmTone}。什么时间？**。 然后按如下所示修改完成规则：

1. 选择现有完成规则 " **ConfirmationResponse**"。
1. 在右侧窗格中，将鼠标悬停在现有操作上，然后选择 " **编辑**"。
1. 将语音响应更新为 **"正常"，为 {DateTime} 设置警报。警报音为 {AlarmTone}。**

> [!IMPORTANT]
> 如果正在执行的命令中没有任何显式配置，例如，命令尚未完成，则可以更改警报音。 *默认情况下，将对所有命令参数启用更正，而不考虑是否要实现该命令的 turn 号。*

#### <a name="correction-when-command-is-completed"></a>命令完成时进行更正

自定义命令平台还提供了一种单步更正功能，即使该命令已完成也是如此。 默认情况下不启用此功能。 必须显式配置此设置。 使用以下步骤配置单步更正。

1. 在 **SetAlarm** 命令中，添加类型 " **更新上一个" 命令** 的交互规则以更新以前设置的警报。 重命名交互规则的默认 **名称** ，以 **更新以前的警报**。
1. 保留默认条件 " **上一命令需要** 按原样更新"。
1. 将新条件添加为  >  **所需的类型参数**  >  **DateTime**。
1. 将新操作添加为 **类型**  >  **发送语音响应**  >  **简单编辑器**  >  **正在将以前的警报时间更新为 {DateTime}。**
1. 将 **执行后状态** 的默认值保留为 " **已完成**"。

尝试更改，方法是选择 " **训练**"，等待训练完成，然后选择 " **测试**"。

- **输入**：设置警报。
- **输出**：准备好将音频设置为 Chimes。 什么时间？
- **输入**：将音频设置为曲调，将其设置为明天上午9点。
- **输出**：正常，为 2020-05-21 09:00:00 设置的警报。 警报音为曲调。
- **输入**：否，上午8点。
- **输出**：将以前的警报时间更新为 2020-05-29 08:00。

> [!NOTE]
> 在实际应用程序中，在此更正规则的 " **操作** " 部分中，还需要将活动发送回客户端或调用 HTTP 终结点，以更新系统中的警报时间。 此操作应独自负责更新警报时间，而不是任何其他命令属性。 在本例中，这将是警报音。

## <a name="add-language-generation-templates-for-speech-responses"></a>为语音响应添加语言生成模板

语言生成模板允许自定义发送到客户端的响应，并在响应中引入变体。 语言生成自定义可通过以下方式实现：

* 语言生成模板的使用
* 使用自适应表达式

自定义命令模板基于 BotFramework 的 [LG 模板](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。 由于自定义命令会在需要时创建新的 LG 模板 (即，对于参数或操作中的语音响应) 无需指定 LG 模板的名称。 因此，请不要将模板定义为：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

只需定义不带名称的模板正文，如下所示。

> [!div class="mx-imgBorder"]
> ![模板编辑器示例](./media/custom-commands/template-editor-example.png)


此更改会为发送到客户端的语音响应引入变体。 因此，对于同一查询文本，相应的语音响应会随机选取提供的选项。

利用 LG 模板，还可以使用自适应表达式为命令定义复杂的语音响应。 有关更多详细信息，可以参阅 [LG 模板格式](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) 。 默认情况下，自定义命令支持所有功能，但有以下细微差异：

* 在 LG 模板中，实体表示为 $ {entityName}。 在自定义命令中，我们不使用实体，但参数可用作以下表示形式之一： $ {parameterName} 或 {parameterName}
* 自定义命令不支持模板组合和扩展。 这是因为，你永远不会 `.lg` 直接编辑文件，只是自动创建的模板的响应。
* 自定义命令不支持 LG 插入的自定义函数。 仍支持预定义函数。
* 自定义命令不支持 (strict、replaceNull & lineBreakStyle) 的选项。

### <a name="add-template-responses-to-turnonoff-command"></a>向 TurnOnOff 命令添加模板响应

修改 **TurnOnOff** 命令以添加具有以下配置的新参数：

| 设置            | 建议的值       | 
| ------------------ | --------------------- | 
| 名称               | `SubjectContext`         | 
| 为全局          | unchecked             | 
| 必需           | unchecked               | 
| 类型               | 字符串                |
| 默认值      | `all` |
| 配置      | 接受来自内部目录的预定义输入值 | 
| 预定义的输入值 | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>修改完成规则

编辑现有完成规则 **ConfirmationResponse** 的 "**操作**" 部分。 在 " **编辑操作** " 弹出窗口中，切换到 **模板编辑器** 并将文本替换为以下示例。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

按如下所示 **训练** 并 **测试** 应用程序。 请注意，由于使用了模板值的多个替代项，并使用了自适应表达式，因此响应变体。

* 输入：打开电视
* 输出：好，打开电视
* 输入：打开电视
* 输出：已完成，已打开电视
* 输入：关闭光源
* 输出：确定，关闭所有灯光
* 输入：关闭房间光源
* 输出：确定，关闭房间灯光

## <a name="use-custom-voice"></a>使用自定义语音

自定义自定义命令响应的另一种方法是选择自定义输出声音。 使用以下步骤将默认语音切换为自定义语音。

1. 在自定义命令应用程序的左窗格中，选择 " **设置** "。
1. 从中间窗格中选择 " **自定义语音** "。
1. 从表中选择所需的自定义或公共语音。
1. 选择“保存”。

> [!div class="mx-imgBorder"]
> ![带参数的示例句子](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - 对于 **公共语音**， **神经类型** 仅适用于特定区域。 若要查看可用性，请参阅 [按区域/终结点的标准和神经声音](./regions.md#standard-and-neural-voices)。
> - 对于 **自定义** 语音，可以从自定义语音项目页创建。 请参阅 [自定义语音入门](./how-to-custom-voice.md)。

现在，应用程序将在选定的语音中做出响应，而不是默认的语音。

## <a name="next-steps"></a>后续步骤

* 了解如何使用语音 SDK 将 [自定义命令应用程序](how-to-custom-commands-setup-speech-sdk.md) 与客户端应用程序集成。
* 通过 Azure DevOps 为自定义命令应用程序[设置持续部署](how-to-custom-commands-deploy-cicd.md)。 
                      