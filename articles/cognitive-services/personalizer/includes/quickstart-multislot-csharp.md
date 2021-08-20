---
title: 包含文件
description: 包含文件
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: e21dab310c41cf6aae0e201d7d1b8e78a8540a30
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255325"
---
[参考文档](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer) | [多槽概念](..\concept-multi-slot-personalization.md) | [示例](https://aka.ms/personalizer/ms-dotnet)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="创建个性化体验创建服务资源"  target="_blank">创建个性化体验创建服务资源</a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到个性化体验创建服务 API。 稍后在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

[!INCLUDE [Upgrade Personalizer instance to multi-slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。

在控制台窗口（例如 CMD、PowerShell 或 Bash）中，使用 dotnet `new` 命令创建名为 `personalizer-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：`Program.cs`。

```console
dotnet new console -n personalizer-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

在偏好的编辑器或 IDE 中打开项目目录中的 `Program.cs` 文件。 添加以下 using 指令：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Text;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
```

## <a name="object-model"></a>对象模型

若要请求每个槽的单个最佳内容项，请创建一个 MultiSlotRankRequest，然后将 post 请求发送到 [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank)。 然后，响应会被分析为 MultiSlotRankResponse。

若要将奖励分数发送到个性化体验创建服务，请创建一个 MultiSlotReward，并向 [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward) 发送 post 请求。

在本快速入门中，可以很容易地确定奖励评分。 在生产系统中，确定哪些因素会影响[奖励评分](../concept-rewards.md)以及影响程度可能是一个复杂的过程，你的判断可能会随时改变。 此设计决策应是个性化体验创建服务体系结构中的主要决策之一。

## <a name="code-examples"></a>代码示例

这些代码片段演示了如何通过发送适用于 .NET 的 HTTP 请求执行以下任务：

* [创建基 URL](#create-base-urls)
* [多槽排名 API](#request-the-best-action)
* [多槽奖励 API](#send-a-reward)


## <a name="create-base-urls"></a>创建基 URL

在本部分中，你将执行以下两项操作：
* 指定密钥和终结点
* 构造排名和奖励 URL

首先，将以下行添加到 Program 类。 请确保从个性化体验创建服务资源中添加密钥和终结点。

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
//Replace 'PersonalizationBaseUrl' and 'ResourceKey' with your valid endpoint values.
private const string PersonalizationBaseUrl = "<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>";
private const string ResourceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";
```

接下来，构造排名和奖励 URL。

```csharp
private static string MultiSlotRankUrl = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/rank");
private static string MultiSlotRewardUrlBase = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/events/");
```

## <a name="get-content-choices-represented-as-actions"></a>获取以操作形式表示的内容选项

操作表示你希望个性化体验创建服务从中选择最佳内容项的内容选择。 将以下方法添加到 Program 类，以表示操作及其特征的集合。 

```csharp
private static IList<Action> GetActions()
{
    IList<Action> actions = new List<Action>
    {
        new Action
        {
            Id = "Red-Polo-Shirt-432",
            Features =
            new List<object>() { new { onSale = "true", price = "20", category = "Clothing" } }
        },
        new Action
        {
            Id = "Tennis-Racket-133",
            Features =
            new List<object>() { new { onSale = "false", price = "70", category = "Sports" } }
        },
        new Action
        {
            Id = "31-Inch-Monitor-771",
            Features =
            new List<object>() { new { onSale = "true", price = "200", category = "Electronics" } }
        },
        new Action
        {
            Id = "XBox-Series X-117",
            Features =
            new List<object>() { new { onSale = "false", price = "499", category = "Electronics" } }
        }
    };
    return actions;
}
```

## <a name="get-slots"></a>获取槽

槽构成了用户将与之交互的页面。 个性化体验创建服务将决定要在定义的每个槽中显示哪个操作。 可以从特定槽中排除操作（显示为 `ExcludeActions`）。 `BaselineAction` 是在不使用个性化体验创建服务的情况下会显示的槽的默认操作。


本快速入门提供了简单的槽功能。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[特征](../concepts-features.md)可能是一件非常重要的事情。

```csharp
private static IList<Slot> GetSlots()
{
    IList<Slot> slots = new List<Slot>
    {
        new Slot
        {
            Id = "BigHeroPosition",
            Features = new List<object>() { new { size = "large", position = "left" } },
            ExcludedActions = new List<string>() { "31-Inch-Monitor-771" },
            BaselineAction = "Red-Polo-Shirt-432"

        },
        new Slot
        {
            Id = "SmallSidebar",
            Features = new List<object>() { new { size = "small", position = "right" } },
            ExcludedActions = new List<string>() { "Tennis-Racket-133" },
            BaselineAction = "XBox-Series X-117"
        },
    };

    return slots;
}
```

## <a name="classes-for-constructing-rankreward-requests-and-responses"></a>用于构造排名/奖励请求和响应的类

添加以下用于构造排名/奖励请求并分析其响应的嵌套类。

```csharp
private class Action
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class Slot
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public List<object> Features { get; set; }

    [JsonPropertyName("excludedActions")]
    public List<string> ExcludedActions { get; set; }

    [JsonPropertyName("baselineAction")]
    public string BaselineAction { get; set; }
}
```

```csharp
private class Context
{
    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class MultiSlotRankRequest
{
    [JsonPropertyName("contextFeatures")]
    public IList<Context> ContextFeatures { get; set; }

    [JsonPropertyName("actions")]
    public IList<Action> Actions { get; set; }

    [JsonPropertyName("slots")]
    public IList<Slot> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }

    [JsonPropertyName("deferActivation")]
    public bool DeferActivation { get; set; }
}
```

```csharp
private class MultiSlotRankResponse
{
    [JsonPropertyName("slots")]
    public IList<SlotResponse> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }
}
```

```csharp
private class SlotResponse
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("rewardActionId")]
    public string RewardActionId { get; set; }
}
```

```csharp
private class MultiSlotReward
{
    [JsonPropertyName("reward")]
    public List<SlotReward> Reward { get; set; }
}
```

```csharp
private class SlotReward
{
    [JsonPropertyName("slotId")]
    public string SlotId { get; set; }

    [JsonPropertyName("value")]
    public float Value { get; set; }
}
```

## <a name="get-user-preferences-for-context"></a>获取上下文的用户首选项

将以下方法添加到 Program 类，以从命令行获取用户的日期时间输入及用户所在设备的类型。 它们将用作上下文特征。

```csharp
static string GetTimeOfDayForContext()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetDeviceForContext()
{
    string[] deviceFeatures = new string[] { "mobile", "tablet", "desktop" };

    Console.WriteLine("\nWhat is the device type (enter number)? 1. Mobile 2. Tablet 3. Desktop");
    if (!int.TryParse(GetKey(), out int deviceIndex) || deviceIndex < 1 || deviceIndex > deviceFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + deviceFeatures[0] + ".");
        deviceIndex = 1;
    }

    return deviceFeatures[deviceIndex - 1];
}
```

这两个方法都使用 `GetKey` 方法从命令行读取用户的选择。

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

```csharp
private static IList<Context> GetContext(string time, string device)
{
    IList<Context> context = new List<Context>
    {
        new Context
        {
            Features = new {timeOfDay = time, device = device }
        }
    };

    return context;
}
```

## <a name="make-http-requests"></a>发出 HTTP 请求

添加这些功能，以向个性化体验创建服务终结点发送 post 请求，以进行多槽排名和奖励调用。

```csharp
private static async Task<MultiSlotRankResponse> SendMultiSlotRank(HttpClient client, string rankRequestBody, string rankUrl)
{
    try
    {
    var rankBuilder = new UriBuilder(new Uri(rankUrl));
    HttpRequestMessage rankRequest = new HttpRequestMessage(HttpMethod.Post, rankBuilder.Uri);
    rankRequest.Content = new StringContent(rankRequestBody, Encoding.UTF8, "application/json");
    HttpResponseMessage response = await client.SendAsync(rankRequest);
    response.EnsureSuccessStatusCode();
    MultiSlotRankResponse rankResponse = JsonSerializer.Deserialize<MultiSlotRankResponse>(await response.Content.ReadAsByteArrayAsync());
    return rankResponse;
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message);
        Console.WriteLine("Please make sure multi-slot feature is enabled. To do so, follow multi-slot Personalizer documentation to update your loop settings to enable multi-slot functionality.");
        throw;
    }
}
```

```csharp
private static async Task SendMultiSlotReward(HttpClient client, string rewardRequestBody, string rewardUrlBase, string eventId)
{
    string rewardUrl = String.Concat(rewardUrlBase, eventId, "/reward");
    var rewardBuilder = new UriBuilder(new Uri(rewardUrl));
    HttpRequestMessage rewardRequest = new HttpRequestMessage(HttpMethod.Post, rewardBuilder.Uri);
    rewardRequest.Content = new StringContent(rewardRequestBody, Encoding.UTF8, "application/json");

    await client.SendAsync(rewardRequest);
}
```

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个包含 [MultiSlotRank](#request-the-best-action) 和 [MultiSlotReward](#send-a-reward) 调用的循环。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道该服务的表现如何。

以下代码会循环调用这样一个循环：通过命令行询问用户的首选项，将该信息发送给个性化体验创建服务来为每个槽选择最佳操作，向客户显示所选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励评分，指出服务在做选择时的表现如何。

```csharp
static async Task Main(string[] args)
{
    Console.WriteLine($"Welcome to this Personalizer Quickstart!\n" +
            $"This code will help you understand how to use the Personalizer APIs (multislot rank and multislot reward).\n" +
            $"Each iteration represents a user interaction and will demonstrate how context, actions, slots, and rewards work.\n" +
            $"Note: Personalizer AI models learn from a large number of user interactions:\n" +
            $"You won't be able to tell the difference in what Personalizer returns by simulating a few events by hand.\n" +
            $"If you want a sample that focuses on seeing how Personalizer learns, see the Python Notebook sample.");

    IList<Action> actions = GetActions();
    IList<Slot> slots = GetSlots();

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", ResourceKey);
        int iteration = 1;
        bool runLoop = true;
        do
        {
            Console.WriteLine($"\nIteration: {iteration++}");
            string timeOfDayFeature = GetTimeOfDayForContext();
            string deviceFeature = GetDeviceForContext();

            IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

            string eventId = Guid.NewGuid().ToString();

            string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
            {
                ContextFeatures = context,
                Actions = actions,
                Slots = slots,
                EventId = eventId,
                DeferActivation = false
            });

            //Ask Personalizer what action to show for each slot
            MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);

            MultiSlotReward multiSlotRewards = new MultiSlotReward()
            {
                Reward = new List<SlotReward>()
            };

            for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
            {
                Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
                SlotReward reward = new SlotReward()
                {
                    SlotId = multiSlotRankResponse.Slots[i].Id
                };

                string answer = GetKey();

                if (answer == "Y")
                {
                    reward.Value = 1;
                    Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
                }
                else if (answer == "N")
                {
                    reward.Value = 0;
                    Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
                }
                else
                {
                    reward.Value = 0;
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
                }
                multiSlotRewards.Reward.Add(reward);
            }

            string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

            // Send the reward for the action based on user response for each slot.
            await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);

            Console.WriteLine("\nPress q to break, any other key to continue:");
            runLoop = !(GetKey() == "Q");
        } while (runLoop);
    }
}
```
在接下来的部分中，让我们仔细看看排名和奖励调用。
添加以下方法，这些方法在运行代码文件之前[获取内容选项](#get-content-choices-represented-as-actions)、[获取槽](#get-slots)，以及[发送多槽排名和奖励请求](#make-http-requests)：

* `GetActions`
* `GetSlots`
* `GetTimeOfDayForContext`
* `GetTimeOfDayForContext`
* `GetKey`
* `GetContext`
* `SendMultiSlotRank`
* `SendMultiSlotReward`

添加以下类，这些类在运行代码文件之前[构造排名/奖励请求的主体并分析其响应](#classes-for-constructing-rankreward-requests-and-responses)：

* `Action`
* `Slot`
* `Context`
* `MultiSlotRankRequest`
* `MultiSlotRankResponse`
* `SlotResponse`
* `MultiSlotReward`
* `SlotReward`

## <a name="request-the-best-action"></a>请求最佳操作

为了完成排名请求，程序将要求用户指定偏好，以创建内容选项的 `context`。 请求正文包含上下文、操作和槽及其各自的功能。 `SendMultiSlotRank` 方法需要 HTTP 客户端、请求正文和 URL 来发送请求。

本快速入门使用简单的日期时间和用户设备上下文特征。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[操作与特征](../concepts-features.md)可能是一件非常重要的事情。

```csharp
string timeOfDayFeature = GetTimeOfDayForContext();
string deviceFeature = GetDeviceForContext();

IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

string eventId = Guid.NewGuid().ToString();

string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
{
    ContextFeatures = context,
    Actions = actions,
    Slots = slots,
    EventId = eventId,
    DeferActivation = false
});

//Ask Personalizer what action to show for each slot
MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);
```

## <a name="send-a-reward"></a>发送奖励

为了获取可用于奖励请求的奖励评分，程序会通过命令行获取用户针对每个槽所做的选择，为该选择分配一个数值（奖励得分），然后将每个槽的唯一事件 ID、槽 ID 和奖励评分作为数值发送到奖励 API。 无需为每个槽定义奖励。

本快速入门分配一个简单的数字（0 或 1）作为奖励评分。 在生产系统中，确定何时向[奖励](../concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。

```csharp
MultiSlotReward multiSlotRewards = new MultiSlotReward()
{
    Reward = new List<SlotReward>()
};

for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
{
    Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
    SlotReward reward = new SlotReward()
    {
        SlotId = multiSlotRankResponse.Slots[i].Id
    };

    string answer = GetKey();

    if (answer == "Y")
    {
        reward.Value = 1;
        Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
    }
    else if (answer == "N")
    {
        reward.Value = 0;
        Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
    }
    else
    {
        reward.Value = 0;
        Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
    }
    multiSlotRewards.Reward.Add(reward);
}

string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

// Send the reward for the action based on user response for each slot.
await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);
```

## <a name="run-the-program"></a>运行程序

从应用程序目录，使用 dotnet `run` 命令运行应用程序。

```console
dotnet run
```

![快速入门程序会提出一些问题来收集用户的偏好（称为“特征”），然后提供排名最高的操作。](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


其中还有[本快速入门的源代码](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer/multislot-quickstart)。
