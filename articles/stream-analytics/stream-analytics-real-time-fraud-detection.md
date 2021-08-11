---
title: 教程 - 使用 Azure 流分析分析欺诈性呼叫数据，并在 Power BI 仪表板中可视化结果
description: 本教程以端到端方式演示了如何使用 Azure 流分析来分析电话呼叫流中的欺诈性呼叫。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: eb13fa39452cac26fac2476b6f102248478b18a5
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689748"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>教程：使用流分析分析欺诈性呼叫数据，并在 Power BI 仪表板中可视化结果

本教程介绍如何使用 Azure 流分析来分析电话呼叫数据。 由客户端应用程序生成的电话呼叫数据包含欺诈性呼叫，这些呼叫由流分析作业进行筛选。 本教程中的技术也适用于其他类型的欺诈检测，如信用卡欺诈或身份盗用。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 生成示例性的电话呼叫数据并将其发送到 Azure 事件中心。
> * 创建流分析作业。
> * 配置作业输入和输出。
> * 定义用于筛选欺诈性呼叫的查询。
> * 测试和启动作业。
> * 在 Power BI 中可视化结果。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已完成以下步骤：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 请从 Microsoft 下载中心下载电话呼叫事件生成器应用 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)，或者从 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) 获取源代码。
* 你将需要 Power BI 帐户。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-azure-event-hub"></a>创建 Azure 事件中心

需将数据发送到 Azure，然后流分析才能分析欺诈性呼叫数据流。 在本教程中，需使用 [Azure 事件中心](../event-hubs/event-hubs-about.md)将数据发送到 Azure。

请按以下步骤创建一个事件中心，然后向该事件中心发送呼叫数据：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源” > “物联网” > “事件中心”  。

   ![在门户中创建 Azure 事件中心](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. 使用以下值填写“创建命名空间”窗格：

   |**设置**  |**建议的值** |**说明**  |
   |---------|---------|---------|
   |名称     | asaTutorialEventHub        |  用于标识事件中心命名空间的唯一名称。       |
   |订阅     |   \<Your subscription\>      |   选择要在其中创建事件中心的 Azure 订阅。      |
   |资源组     |   MyASADemoRG      |  选择“新建”，然后输入帐户的新资源组名称。       |
   |位置     |   美国西部 2      |    一个位置，可在其中部署事件中心命名空间。     |

4. 对其余设置使用默认选项，然后选择“查看 + 创建”。 选择“创建”，启动部署过程。

   ![在 Azure 门户中创建事件中心命名空间](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. 当命名空间完成部署以后，转到“所有资源”，在 Azure 资源的列表中找到 asaTutorialEventHub。 选择“asaTutorialEventHub”将其打开。

6. 接下来，选择“+ 事件中心”，然后输入事件中心的名称 。 将分区计数设置为 2。  对其余设置使用默认选项，然后选择“创建”。 然后，等待部署成功完成。

   ![Azure 门户中的事件中心配置](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>授予对事件中心的访问权限，并获取连接字符串

在应用程序可以将数据发送到 Azure 事件中心之前，事件中心必须具有允许访问的策略。 访问策略生成包含授权信息的连接字符串。

1. 导航到在上一步中创建的事件中心“MyEventHub”。 在“设置”下选择“共享访问策略”，然后选择“+ 添加”。  

2. 将策略命名为“MyPolicy”，并确保选中“管理”。  然后选择“创建”。

   ![创建事件中心共享访问策略](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. 创建策略后，选择策略名称以打开策略。 找到“连接字符串-主密钥”。 在连接字符串旁边选择复制按钮。

   ![保存共享的访问策略连接字符串](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. 将连接字符串粘贴到文本编辑器中。 需要在下一部分使用此连接字符串。

   连接字符串如下所示：

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   请注意，连接字符串包含多个以分号分隔的键值对：**Endpoint**、**SharedAccessKeyName**、**SharedAccessKey** 和 **EntityPath**。

## <a name="start-the-event-generator-application"></a>启动事件生成器应用程序

在启动 TelcoGenerator 应用之前，应该对其进行配置，以便将数据发送到此前创建的 Azure 事件中心。

1. 提取 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) 文件的内容。
2. 在所选文本编辑器中打开 `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` 文件（该文件夹中有多个 `.config` 文件，因此请确保打开正确的文件。）

3. 使用以下详细信息更新配置文件中的 `<appSettings>` 元素：

   * 将 *EventHubName* 键的值设置为连接字符串中 EntityPath 的值。
   * 将 *Microsoft.ServiceBus.ConnectionString* 键的值设置为没有 EntityPath 值的连接字符串。 别忘了删除 EntityPath 值前面的分号。

4. 保存文件。

5. 接下来打开命令窗口，转到解压缩 TelcoGenerator 应用程序的文件夹。 然后输入以下命令：

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   此命令采用以下参数：
   * 每小时的呼叫数据记录数。
   * 欺诈概率 (%)，即应用模拟欺诈呼叫的频率。 值 0.2 表示大约有 20% 的通话记录似乎具有欺诈性。
   * 持续时间（小时），即应用应运行的小时数。 还可以通过在命令行终止此过程 (**Ctrl+C**) 来随时停止该应用。

   几秒钟后，当应用将电话通话记录发送到事件中心时，应用将开始在屏幕上显示通话记录。 这些电话呼叫数据包含以下字段：

   |**记录**  |**定义**  |
   |---------|---------|
   |CallrecTime    |  呼叫开始时间的时间戳。       |
   |SwitchNum     |  用于连接呼叫的电话交换机。 在此示例中，交换机是表示来源国家/地区（美国、中国、英国、德国或澳大利亚）的字符串。       |
   |CallingNum     |  呼叫方的电话号码。       |
   |CallingIMSI     |  国际移动用户标识 (IMSI)。 它是呼叫方的唯一标识符。       |
   |CalledNum     |   呼叫接收人的电话号码。      |
   |CalledIMSI     |  国际移动用户标识 (IMSI)。 它是呼叫接收人的唯一标识符。       |

## <a name="create-a-stream-analytics-job"></a>创建流分析作业

有了呼叫事件流以后，即可创建流分析作业，以便从事件中心读取数据。

1. 若要创建流分析作业，请导航到 [Azure 门户](https://portal.azure.com/)。

2. 选择“创建资源”并搜索“流分析作业” 。 选择“流分析作业”磁贴并选择“创建” 。

3. 使用以下值填写“新建流分析作业”窗体：

   |**设置**  |**建议的值**  |**说明**  |
   |---------|---------|---------|
   |作业名称     |  ASATutorial       |   用于标识事件中心命名空间的唯一名称。      |
   |订阅    |  \<Your subscription\>   |   选择要在其中创建作业的 Azure 订阅。       |
   |资源组   |   MyASADemoRG      |   选择“使用现有”，然后输入帐户的新资源组名称。      |
   |位置   |    美国西部 2     |      一个位置，可在其中部署作业。 为获得最佳性能，建议将作业和事件中心放置在同一区域，这样在不同区域之间传输数据时就不需要付费。      |
   |宿主环境    | 云        |     流分析作业可以部署到云或边缘设备。 可以通过云部署到 Azure 云，通过“Edge”部署到 IoT Edge 设备。    |
   |流式处理单位     |    1       |      流单元表示执行作业所需的计算资源。 默认情况下，此值设置为 1。 若要了解如何缩放流单元，请参阅[了解和调整流单元](stream-analytics-streaming-unit-consumption.md)一文。      |

4. 对其余设置使用默认选项，然后选择“创建”，等待部署成功完成。

   ![创建 Azure 流分析作业](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>配置作业输入

下一步是使用在上一部分创建的事件中心，为用于读取数据的作业定义输入源。

1. 从 Azure 门户打开“所有资源”页，找到 ASATutorial 流分析作业。

2. 在“流分析作业”的“作业拓扑”部分，选择“输入” 。

3. 选择“+ 添加流输入”和“事件中心”。 在输入窗体中填充以下值：

   |**设置**  |**建议的值**  |**说明**  |
   |---------|---------|---------|
   |输入别名     |  CallStream       |  提供用于标识输入的友好名称。 输入别名只能包含字母数字字符、连字符和下划线，而且长度必须介于 3 到 63 个字符之间。       |
   |订阅    |   \<Your subscription\>      |   选择在其中创建了事件中心的 Azure 订阅。 事件中心可以位于流分析作业所在的订阅中，也可以位于另一订阅中。       |
   |事件中心命名空间    |  asaTutorialEventHub       |  选择在上一部分创建的事件中心命名空间。 当前订阅中所有可用的事件中心命名空间均列在下拉列表中。       |
   |事件中心名称    |   MyEventHub      |  选择在上一部分创建的事件中心。 当前订阅中所有可用的事件中心均列在下拉列表中。       |
   |事件中心策略名称   |  MyPolicy       |  选择在上一部分创建的事件中心共享访问策略。 当前订阅中所有可用的事件中心策略均列在下拉列表中。       |

4. 对其余设置使用默认选项，然后选择“保存”。

   ![配置 Azure 流分析输入](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>配置作业输出

最后一步是定义作业的输出接收器，以便在其中写入转换后的数据。 在本教程中，请使用 Power BI 来输出和可视化数据。

1. 从 Azure 门户打开“所有资源”，然后选择 ASATutorial 流分析作业。

2. 在“流分析作业”的“作业拓扑”部分，选择“输出”选项 。

3. 选择“+ 添加” > “Power BI”。 然后，选择“授权”并按提示向 Power BI 进行身份验证。

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="Power BI 的授权按钮":::

4. 在输出窗体中填写以下详细信息并选择“保存”：

   |**设置**  |**建议的值**  |
   |---------|---------|
   |输出别名  |  MyPBIoutput  |
   |组工作区| 我的工作区 |
   |数据集名称  |   ASAdataset  |
   |表名称 |  ASATable  |
   | 身份验证模式 | 用户令牌 |

   ![配置 Azure 流分析输出](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   本教程采用用户令牌身份验证模式。 若要使用托管标识，请参阅[使用托管标识对 Power BI 的 Azure 流分析作业进行身份验证](powerbi-output-managed-identity.md)。

## <a name="create-queries-to-transform-real-time-data"></a>创建查询以转换实时数据

此时，设置一个流分析作业以读取传入数据流。 接下来创建一个分析实时数据的查询。 这些查询使用类似 SQL 的语言，该语言具有特定于流分析的一些扩展。

本教程的这一部分会创建并测试多个查询，展示可以转换输入流以便进行分析的几种方法。 

此处创建的查询只会在屏幕中显示已转换的数据。 在后面的部分中，你将把转换后的数据写入 Power BI。

若要了解有关语言的详细信息，请参阅 [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)。

### <a name="test-using-a-pass-through-query"></a>使用传递查询进行测试

如果想要将每个事件存档，可使用传递查询读取事件负载中的所有字段。

1. 在 Azure 门户中导航到你的流分析作业，在“作业拓扑”下选择“查询”。 

2. 在查询窗口中输入以下查询：

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >对于 SQL，关键字不区分大小写，空格也不重要。

    在此查询中，`CallStream` 是创建输入时指定的别名。 如果使用了其他别名，请改为使用该名称。

3. 选择“测试查询”。

    流分析作业对来自输入的示例数据运行查询，并在窗口底部显示输出。 这些结果指示已正确配置事件中心和流分析作业。

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="测试查询的示例输出":::

    你看到的确切记录数取决于你在采样中捕获的记录数。

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>减少使用列投影的字段数

在许多情况下，分析并不需要输入流中的所有列。 可以使用查询投影一组返回的字段，这些字段比传递查询中的字段要小。

运行以下查询，并注意输出。

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>按区域计算传入呼叫数：带聚合功能的翻转窗口

假设要计算每个区域的传入呼叫数。 在流数据中，当要执行聚合函数（如计数）时，需要将流划分为临时单位，因为数据流本身实际上是无限的。 使用流分析[开窗函数](stream-analytics-window-functions.md)执行此操作。 然后，可以使用该窗口中的数据作为一个单元。

此转换需要一个不重叠的时间范围序列，每个窗口将具有一组可对其进行分组和聚合的离散数据。 这种类型的窗口称为“翻转窗口”。 在翻转窗口中，可以获得按 `SwitchNum`（它表示发起呼叫的国家/地区）分组的传入呼叫的计数。

1. 将以下查询粘贴到查询编辑器中：

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    此查询在 `FROM` 子句中使用 `Timestamp By` 关键字来指定输入流中要用于定义翻转窗口的时间戳字段。 在这种情况下，窗口按每条记录中的 `CallRecTime` 字段将数据划分为段。 （如果未指定任何字段，开窗操作将使用每个事件到达事件中心的时间。 请参阅[流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)中的“到达时间与应用程序时间”。 

    投影包括 `System.Timestamp`，后者将返回每个窗口结束时的时间戳。 

    若要指定想要使用翻转窗口，请在 `GROUP BY` 子句中使用 [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) 函数。 在函数中，可以指定时间单位（从微秒到一天的任意时间）和窗口大小（单位数）。 在此示例中，翻转窗口由 5 秒时间间隔组成，因此你会收到按国家/地区的每隔 5 秒的呼叫计数。

2. 选择“测试查询”。 在结果中，请注意“WindowEnd”下的时间戳以 5 秒为增量。

### <a name="detect-sim-fraud-using-a-self-join"></a>使用自联接检测 SIM 欺诈

在此示例中，将欺诈使用情况视为来自同一用户的呼叫，但与另一个 5 秒内的呼叫位于不同的位置。 例如，同一用户不能合法地同时从美国和澳大利亚发起呼叫。

若要检查这些情况，可以使用流数据的自联接基于 `CallRecTime` 值将流联接到自身。 然后，可以查找 `CallingIMSI` 值（始发号码）相同，但 `SwitchNum` 值（来源国家/地区）不同的呼叫记录。

当对流数据使用联接时，该联接必须对可以及时分隔匹配行的程度施加一定限制。 如前所述，流数据实际上是无限的。 使用 `DATEDIFF` 函数在联接的 `ON` 子句中指定关系的时间限制。 此示例中联接基于调用数据的 5 秒时间间隔。

1. 将以下查询粘贴到查询编辑器中：

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    除了联接中的 `DATEDIFF` 函数以外，此查询与任何 SQL 联接类似。 此 `DATEDIFF` 版本特定于流分析，它必须显示在 `ON...BETWEEN` 子句中。 参数为时间单位（此示例中为秒）和联接的两个源的别名。 这与标准 SQL `DATEDIFF` 函数不同。

    `WHERE` 子句包含标志欺诈呼叫的条件：始发交换机不同。

2. 选择“测试查询”。 查看输出，然后选择“保存查询”。

## <a name="start-the-job-and-visualize-output"></a>启动作业并可视化输出

1. 若要启动作业，请导航到作业“概述”，然后选择“启动” 。

2. 选择“现在”作为作业输出启动时间，然后选择“启动”。  可以在通知栏中查看作业状态。

3. 作业成功以后，请导航到 [Power BI](https://powerbi.com/)，然后使用工作或学校帐户登录。 如果流分析作业查询输出结果，则创建的 *ASAdataset* 数据集存在于“数据集”选项卡下。

4. 从 Power BI 工作区选择“+ 创建”，创建名为“欺诈性呼叫”的新仪表板。

5. 在窗口顶部，选择“编辑”和“添加磁贴” 。 然后选择“自定义流数据”和“下一步”。  在“你的数据集”下选择“ASAdataset”。  从“可视化效果类型”下拉列表中选择“卡”，然后向“字段”添加“欺诈性呼叫”   。 选择“下一步”，为磁贴输入一个名称，然后选择“应用”，创建该磁贴。

   ![创建 Power BI 仪表板磁贴](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. 使用以下选项再次执行步骤 5：
   * 转到“可视化效果类型”后，选择“折线图”。
   * 添加轴，然后选择“windowend”。
   * 添加值，然后选择“fraudulentcalls”。
   * 对于“要显示的时间窗口”，请选择最近 10 分钟。

7. 添加两个磁贴以后，仪表板应该如以下示例所示。 请注意，如果事件中心发送器应用程序和流分析应用程序正在运行，则 Power BI 仪表板会在新数据到达时定期进行更新。

   ![在 Power BI 仪表板中查看结果](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>将 Power BI 仪表板嵌入到 Web 应用程序中

在本教程的此部分中，请使用 Power BI 团队创建的示例性 [ASP.NET](https://asp.net/) Web 应用程序来嵌入仪表板。 有关如何嵌入仪表板的详细信息，请参阅[使用 Power BI 嵌入](/power-bi/developer/embedding)一文。

若要设置应用程序，请访问 [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub 存储库，然后按照“用户拥有数据”部分的说明操作（请使用 **integrate-web-app** 子部分下的重定向 URL 和主页 URL）。 由于我们使用的是“仪表板”示例，因此请使用 [GitHub 存储库](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/)中的 **integrate-web-app** 示例代码。
在浏览器中运行应用程序以后，请执行以下步骤，将此前创建的仪表板嵌入网页中：

1. 选择“登录到 Power BI”，以便授予应用程序访问 Power BI 帐户中的仪表板的权限。

2. 选择“获取仪表板”按钮，此时会在表中显示帐户的仪表板。 找到此前创建的仪表板的名称 **powerbi-embedded-dashboard**，然后复制相应的 **EmbedUrl**。

3. 最后，将 **EmbedUrl** 粘贴到相应的文本字段中，然后选择“嵌入仪表板”。 现在可以查看嵌入 Web 应用程序中的同一仪表板了。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建简单的流分析作业、如何分析传入数据，以及如何在 Power BI 仪表板中展示结果。 若要详细了解流分析作业，请继续阅读下一教程：

> [!div class="nextstepaction"]
> [在流分析作业中运行 Azure Functions](stream-analytics-with-azure-functions.md)
