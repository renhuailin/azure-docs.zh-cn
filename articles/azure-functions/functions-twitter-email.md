---
title: 创建与 Azure 逻辑应用集成的函数
description: 创建与 Azure 逻辑应用和 Azure 认知服务集成的函数。 生成的工作流将对推文情绪进行分类并发送电子邮件通知。
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: f4f911feef354e4f2bd1f947bef6418615b74ca2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750232"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>教程：创建与 Azure 逻辑应用集成的函数

Azure Functions 在逻辑应用设计器中与 Azure 逻辑应用集成。 借助这种集成，可以利用 Functions 的计算能力来与其他 Azure 服务和第三方服务进行协调。

本教程演示如何创建工作流来分析 Twitter 活动。 计算推文时，工作流会在检测到积极情绪时发送通知。

在本教程中，学习：

> [!div class="checklist"]
> * 创建认知服务 API 资源。
> * 创建一个用来对推文情感进行分类的函数。
> * 创建连接到 Twitter 的逻辑应用。
> * 将情感检测功能添加到逻辑应用。
> * 将逻辑应用连接到函数。
> * 根据函数的响应发送电子邮件。

## <a name="prerequisites"></a>先决条件

* 有效的 [Twitter](https://twitter.com/) 帐户。
* [Outlook.com](https://outlook.com/) 帐户（用于发送通知）。

> [!NOTE]
> 如果要使用 Gmail 连接器，则只有 G-Suite 商业帐户可以在逻辑应用中不受限制地使用此连接器。 如果有 Gmail 用户帐户，则只能将 Gmail 连接器与 Google 批准的特定应用和服务一起使用，也可以[创建用于通过 Gmail 连接器进行身份验证的 Google 客户端应用](/connectors/gmail/#authentication-and-bring-your-own-application)。 <br><br>有关详细信息，请参阅 [Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

## <a name="create-text-analytics-resource"></a>创建文本分析资源

Azure 中以单个资源的形式提供了认知服务 API。 使用文本分析 API 可检测已发布推文的情绪。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户的左上角选择“创建资源”。

1. 在“类别”下，选择“AI + 机器学习”

1. 在“文本分析”下，选择“创建”。

1. 在“创建文本分析”屏幕中输入以下值。

    | 设置 | 值 | 注解 |
    | ------- | ----- | ------- |
    | 订阅 | Azure 订阅名称 | |
    | 资源组 | 创建名为 tweet-sentiment-tutorial 的新资源组 | 稍后将删除此资源组，以删除在本教程中创建的所有资源。 |
    | 区域 | 选择离你最近的区域 | |
    | 名称 | **TweetSentimentApp** | |
    | 定价层 | 选择“免费 F0” | |

1. 选择“查看 + 创建”。

1. 选择“创建”  。

1. 部署完成后，选择“转到资源”。

## <a name="get-text-analytics-settings"></a>获取文本分析设置

创建文本分析资源后，复制几个设置并保留供以后使用。

1. 选择“密钥和终结点”。

1. 单击“输入”框末尾的图标，复制“密钥 1”。

1. 将该值粘贴到文本编辑器中。

1. 单击“输入”框末尾的图标，复制“终结点”。

1. 将该值粘贴到文本编辑器中。

## <a name="create-the-function-app"></a>创建函数应用

1. 在顶部搜索框中，搜索并选择“函数应用”。

1. 选择“创建”  。

1. 输入以下值。

    | 设置 | 建议的值 | 注解 |
    | ------- | ----- | ------- |
    | 订阅 | Azure 订阅名称 | |
    | 资源组 | **tweet-sentiment-tutorial** | 在本教程中使用相同的资源组名称。 |
    | Function App 名称 | **TweetSentimentAPI** + 唯一后缀 | 函数应用程序名称是全局唯一的。 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。 |
    | 发布 | **代码** | |
    | 运行时堆栈 | **.NET** | 提供的函数代码是用 C# 编写的。 |
    | 版本 | 选择最新版本号 | |
    | 区域 | 选择离你最近的区域 | |

1. 选择“查看 + 创建”。

1. 选择“创建”  。

1. 部署完成后，选择“转到资源”。

## <a name="create-an-http-triggered-function"></a>创建 HTTP 触发的函数  

1. 从“Functions”窗口的左侧菜单中选择“Functions”。

1. 从顶部菜单中选择“添加”并输入以下值。

    | 设置 | 值 | 注解 |
    | ------- | ----- | ------- |
    | 开发环境 | **在门户中开发** | |
    | 模板 | **HTTP 触发器** | |
    | 新建函数 | **TweetSentimentFunction** | 这是函数的名称。 |
    | 授权级别 | **Function** | |

1. 选择“添加”按钮。

1. 选择“代码 + 测试”按钮。

1. 将以下代码粘贴到代码编辑器窗口中。

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    将情绪评分传递到函数，函数将返回此值对应的类别名称。

1. 选择工具栏上的“保存”按钮来保存更改。

    > [!NOTE]
    > 若要测试函数，请从顶部菜单中选择“测试/运行”。 在“输入”选项卡上，在“正文”输入框中输入值 `0.9`，然后选择“运行” 。 验证“输出”部分中“HTTP 响应内容”框中返回的值为“积极”  。

接下来，创建与 Azure Functions、Twitter 和认知服务 API 集成的逻辑应用。

## <a name="create-a-logic-app"></a>创建逻辑应用

1. 在顶部搜索框中，搜索并选择“逻辑应用”。

1. 选择 **添加** 。

1. 选择“消耗”并输入以下值。

    | 设置 | 建议的值 |
    | ------- | --------------- |
    | 订阅 | Azure 订阅名称 |
    | 资源组 | **tweet-sentiment-tutorial** |
    | 逻辑应用名称 | **TweetSentimentApp** |
    | 区域 | 选择离你最近的区域，最好是在前面步骤中选择的同一区域。 |

    接受所有其他设置的默认值。

1. 选择“查看 + 创建”。

1. 选择“创建”  。

1. 部署完成后，选择“转到资源”。

1. 选择“空白逻辑应用”按钮。

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="“空白逻辑应用”按钮":::

1. 选择工具栏上的“保存”按钮来保存进度。

现在，可以使用逻辑应用设计器将服务和触发器添加到应用程序中。

## <a name="connect-to-twitter"></a>连接到 Twitter

创建与 Twitter 的连接，以便应用可以轮询新推文。

1. 在顶部搜索框中搜索“Twitter”。

1. 选择“Twitter”图标。

1. 选择“发布新推文时”触发器。

1. 输入以下值以设置连接。

    | 设置 |  值 |
    | ------- | ---------------- |
    | 连接名称 | **MyTwitterConnection** |
    | 身份验证类型 | 使用默认共享应用程序 |

1. 选择“登录”。

1. 按照弹出窗口中的提示完成 Twitter 登录。

1. 接下来，在“发布新推文时”框中输入以下值。

    | 设置 | 值 |
    | ------- | ----- |
    | 搜索文本 | **#my-twitter-tutorial** |
    | 你想多久检查一次项？ | 文本框中为“1”， <br> 下拉列表中为“小时”。 可以输入不同的值，但请务必查看 Twitter 连接器的当前[限制](/connectors/twitterconnector/#limits)。  |

1. 选择工具栏上的“保存”按钮来保存进度。

接下来，连接到文本分析以检测收集到的推文的情绪。

## <a name="add-text-analytics-sentiment-detection"></a>添加文本分析情绪检测

1. 选择“新建步骤”。

1. 在搜索框中搜索“文本分析”。

1. 选择“文本分析”图标。

1. 选择“检测情绪”并输入以下值。

    | 设置 | 值 |
    | ------- | ----- |
    | 连接名称 | **TextAnalyticsConnection** |
    | 帐户密钥 | 粘贴前面保留的文本分析帐户密钥。 |
    | 站点 URL | 粘贴前面保留的文本分析终结点。 |

1. 选择“创建”  。

1. 单击“添加新参数”框，并选中弹出窗口中显示的“文档”旁边的框。

1. 单击“文档 ID - 1”文本框，打开“动态内容”弹出窗口。

1. 在“动态内容”搜索框中，搜索“ID”，然后单击“推文 ID” 。

1. 单击“文档文本 - 1”文本框，打开“动态内容”弹出窗口。

1. 在“动态内容”搜索框中，搜索“文本”，然后单击“推文文本” 。

1. 在“选择操作”中键入“文本分析”，然后单击“检测情感”操作。  

1. 选择工具栏上的“保存”按钮来保存进度。

“检测情绪”框应类似于以下屏幕截图。

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="“检测情绪”设置":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>将情绪输出连接到函数终结点

1. 选择“新建步骤”。

1. 在搜索框中搜索“Azure Functions”。

1. 选择“Azure Functions”图标。

1. 在搜索框中搜索你的函数名称。 如果遵循上述指导，函数名称将以 TweetSentimentAPI 开头。

1. 选择函数图标。

1. 选择 TweetSentimentFunction 项。

1. 单击“请求正文”框，然后从弹出窗口中选择“检测情绪”的“评分”项 。

1. 选择工具栏上的“保存”按钮来保存进度。

## <a name="add-conditional-step"></a>添加条件步骤

1. 选择“添加操作”按钮。

1. 单击“控制”框，然后在弹出窗口中搜索并选择“控制”。

1. 选择“条件”。

1. 单击“选择值”框，然后从弹出窗口中选择“TweetSentimentFunction”的“正文”项 。

1. 在“选择值”框中输入“积极”。

1. 选择工具栏上的“保存”按钮来保存进度。

## <a name="add-email-notifications"></a>添加电子邮件通知

1. 在“True”框下，选择“添加操作”按钮。

1. 在文本框中搜索并选择“Office 365 Outlook”。

1. 在文本框中搜索“发送”，然后选择“发送电子邮件” 。

1. 选择“登录”按钮。

1. 按照弹出窗口中的提示完成 Office 365 Outlook 登录。

1. 在“收件人”框中输入你的电子邮件地址。

1. 单击“主题”框，然后单击“TweetSentimentFunction”下的“正文”项。 如果列表中未显示“正文”项，请单击“查看更多”链接来展开选项列表。

1. 在“主题”中的“正文”项后，输入文本“发推者:” 。

1. 在“发推者:”文本后，再次单击该框，然后在“发布新推文时”选项列表中选择“用户名”。

1. 单击“正文”框，然后在“发布新推文时”选项列表下方，选择“推文文本”。 如果列表中未显示“推文文本”项，请单击“查看更多”链接来展开选项列表。

1. 选择工具栏上的“保存”按钮来保存进度。

“电子邮件”框现在应类似于此屏幕截图。

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="电子邮件通知":::

## <a name="run-the-workflow"></a>运行工作流

1. 在 Twitter 帐户中，用以下文本发推：I'm enjoying #my-twitter-tutorial。

1. 返回到“逻辑应用设计器”并选择“运行”按钮。

1. 在你的电子邮件中查看工作流发来的邮件。

## <a name="clean-up-resources"></a>清理资源

若要清理在本教程中创建的所有 Azure 服务和帐户，请删除该资源组。

1. 在顶部搜索框中搜索“资源组”。

1. 选择“tweet-sentiment-tutorial”。

1. 选择“删除资源组”

1. 在文本框中输入“tweet-sentiment-tutorial”。

1. 选择“删除”按钮。

或者，你可能想要返回到 Twitter 帐户，并从源中删除任何测试推文。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure Functions 创建无服务器 API](functions-create-serverless-api.md)
