---
title: 从 Application Insights 连续导出遥测数据 | Microsoft Docs
description: 将诊断和使用情况数据导出到 Microsoft Azure 中的存储，然后从中下载这些数据。
ms.topic: conceptual
ms.date: 02/19/2021
ms.custom: references_regions
ms.openlocfilehash: 8a302717ed962971069ee56a07d78747d82b00df
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110455040"
---
# <a name="export-telemetry-from-application-insights"></a>从 Application Insights 导出遥测数据
想要将遥测数据保留超过标准保留期限？ 或者要以某种专业方式处理这些数据？ 连续导出很适合此目的。 可以使用 JSON 格式将 Application Insights 门户中显示的事件导出到 Microsoft Azure 中的存储。 可以从该存储中下载这些数据，并编写所需的代码来处理这些数据。  

> [!IMPORTANT]
> 连续导出已弃用。 [迁移到基于工作区的 Application Insights 资源](convert-classic-resource.md)，以使用[诊断设置](#diagnostic-settings-based-export)导出遥测数据。

> [!NOTE]
> 只有经典 Application Insights 资源支持连续导出。 [基于工作区的 Application Insights 资源](./create-workspace-resource.md)必须使用[诊断设置](./create-workspace-resource.md#export-telemetry)。
>

在设置连续导出之前，请考虑一些备选方法：

* 通过“指标”或“搜索”选项卡顶部的“导出”按钮，可将表格和图表发送到 Excel 电子表格。

* [Analytics](../logs/log-query-overview.md) 提供功能强大的遥测查询语言。 它还可以导出结果。
* 如果想要[在 Power BI 中浏览数据](./export-power-bi.md)，无需使用连续导出也可以做到。
* 使用[数据访问 REST API](https://dev.applicationinsights.io/) 能够以编程方式访问遥测数据。
* 也可以访问“[通过 Powershell 连续导出](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport)”设置。

连续导出将数据复制到存储后（数据可在其中保存任意长的时间），在正常[保留期](./data-retention-privacy.md)内，这些数据仍可在 Application Insights 中使用。

## <a name="supported-regions"></a>支持的区域

以下区域支持连续导出：

* Southeast Asia
* 加拿大中部
* 印度中部
* 北欧
* 英国南部
* 澳大利亚东部
* Japan East
* 韩国中部
* 法国中部
* 东亚
* 美国西部
* 美国中部
* 美国东部 2
* 美国中南部
* 美国西部 2
* 南非北部
* 美国中北部
* Brazil South
* 瑞士北部
* Australia Southeast
* 英国西部
* 德国中西部
* 瑞士西部
* 澳大利亚中部 2
* 阿联酋中部
* 巴西东南部
* 澳大利亚中部
* 阿拉伯联合酋长国北部
* 挪威东部
* 日本西部

> [!NOTE]
> 如果导出是在 2021 年 2 月 23 日之前配置的，连续导出将继续适用于美国东部和欧洲西部的应用程序。  无论应用程序何时创建，都不能在美国东部或欧洲西部的任何应用程序上配置新的连续导出规则。 

## <a name="continuous-export-advanced-storage-configuration"></a>连续导出高级存储配置

连续导出不支持以下 Azure 存储功能/配置：

* 将 [VNET/Azure 存储防火墙](../../storage/common/storage-network-security.md)与 Azure Blob 存储一起使用。

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)。

## <a name="create-a-continuous-export"></a><a name="setup"></a> 创建连续导出

> [!NOTE]
> 应用程序每天导出的数据不能超过 3TB。 如果每天导出的数据超过 3TB，则导出将被禁用。 若要无限制地导出，请使用[基于诊断设置的导出](#diagnostic-settings-based-export)。

1. 在应用左侧配置下的 Application Insights 资源中，打开“连续导出”，并选择“添加”：

2. 选择要导出的遥测数据类型。

3. 创建或选择要用于存储数据的 [Azure 存储帐户](../../storage/common/storage-introduction.md)。 有关存储定价选项的详细信息，请访问[官方定价页面](https://azure.microsoft.com/pricing/details/storage/)。

     单击“添加”、“导出目标”、“存储帐户”，并创建新存储或选择现有存储。

    > [!Warning]
    > 默认情况下，存储位置将设置为与 Application Insights 资源相同的地理区域。 如果存储在不同的区域中，则可能会产生传输费用。

4. 在存储中创建或选择一个容器。

> [!NOTE]
> 创建导出后，新引入的数据将开始流向 Azure Blob 存储。 连续导出只传输在启用连续导出后创建/引入的新遥测数据。 启用连续导出之前存在的任何数据都不会被导出，且不支持使用连续导出以追溯性方式导出先前创建的数据。

数据出现在存储中之前可能有大约一小时的延迟。

第一次导出完成后，你将在 Azure Blob 存储容器中找到类似于以下内容的结构：（这会因你收集的数据而异。）

|名称 | 说明 |
|:----|:------|
| [可用性](export-data-model.md#availability) | 报告[可用性 Web 测试](./monitor-web-app-availability.md)。  |
| [事件](export-data-model.md#events) | [TrackEvent()](./api-custom-events-metrics.md#trackevent) 生成的自定义事件。 
| [异常](export-data-model.md#exceptions) |报告服务器和浏览器中发生的[异常](./asp-net-exceptions.md)。
| [消息](export-data-model.md#trace-messages) | 由 [TrackTrace](./api-custom-events-metrics.md#tracktrace) 和[日志记录适配器](./asp-net-trace-logs.md)发送。
| [指标](export-data-model.md#metrics) | 由指标 API 调用生成。
| [PerformanceCounters](export-data-model.md) | Application Insights 收集到的性能计数器。
| [请求](export-data-model.md#requests)| 由 [TrackRequest](./api-custom-events-metrics.md#trackrequest) 发送。 标准模块使用此属性报告在服务器上测量的服务器响应时间。| 

### <a name="to-edit-continuous-export"></a>编辑连续导出

单击“连续导出”，然后选择要编辑的存储帐户。

### <a name="to-stop-continuous-export"></a>停止连续导出

若要停止导出，请单击“禁用”。 再次单击“启用”时，将使用新数据重新启动导出。 无法获取在禁用导出时传入门户的数据。

要永久停止导出，请将其删除。 这样做不会将数据从存储中删除。

### <a name="cant-add-or-change-an-export"></a>无法添加或更改导出？
* 若要添加或更改导出，你需要具有“所有者”、“参与者”或“Application Insights 参与者”访问权限。 [了解角色][roles]。

## <a name="what-events-do-you-get"></a><a name="analyze"></a> 获取哪些事件？
导出的数据是从应用程序接收的原始遥测数据，只是我们添加了基于客户端 IP 地址计算出的位置数据。

被[采样](./sampling.md)丢弃的数据不会包含在导出的数据中。

不包含其他计算的指标。 例如，不会导出平均 CPU 使用率，但会导出用来计算平均值的原始遥测数据。

该数据还包含已设置的任何[可用性 Web 测试](./monitor-web-app-availability.md)的结果。

> [!NOTE]
> **采样。** 如果应用程序发送大量数据，采样功能可能会运行，并只发送一小部分生成的遥测数据。 [了解有关采样的详细信息。](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> 检查数据
可以直接在门户中检查存储。 单击最左侧菜单中的“主页”，在顶部显示“Azure 服务”处选择“存储帐户”，选择存储帐户名称，在“概述”页的服务下，选择“Blob”，最后选择容器名称。

若要在 Visual Studio 中检查 Azure 存储，请依次打开“视图”、“Cloud Explorer”。 （如果没有此菜单命令，则需要安装 Azure SDK：打开“新建项目”对话框，展开 Visual C#/云/并选择“用于 .NET 的 Microsoft Azure SDK”。）

打开 Blob 存储后，会看到包含一组 Blob 文件的容器。 每个文件的 URI 派生自 Application Insights 的资源名称、其检测密钥、遥测类型/日期/时间。 （资源名称为全小写形式，检测密钥不包含连字符。）

![使用适当的工具检查 Blob 存储](./media/export-telemetry/04-data.png)

日期和时间采用 UTC，表示在存储中存放遥测数据的时间 - 而不是生成遥测数据的时间。 因此，如果编写代码来下载数据，数据的日期和时间可能会线性移动。

路径格式如下：

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

其中

* `blobCreationTimeUtc` 是在内部暂存存储中创建 Blob 的时间
* `blobDeliveryTimeUtc` 是将 Blob 复制到导出目标存储的时间

## <a name="data-format"></a><a name="format"></a> 数据格式
* 每个 Blob 是一个文本文件，其中包含多个以“\n”分隔的行。 它包含大约半分钟时间内处理的遥测数据。
* 每行代表遥测数据点，例如请求或页面视图。
* 每行是未设置格式的 JSON 文档。 如果要查看行，请在 Visual Studio 中打开 blob，然后选择“编辑” > “高级” > “设置文件格式”  ：

   ![使用适当的工具查看遥测数据](./media/export-telemetry/06-json.png)

持续时间以刻度为单位，10000 刻度 = 1 毫秒。 例如，这些值显示从浏览器发送请求用了 1 毫秒时间，接收它用了 3 毫秒，在浏览器中处理页面用了 1.8 秒：

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[属性类型和值的详细数据模型参考。](export-data-model.md)

## <a name="processing-the-data"></a>处理数据
如果数据规模不大，可以编写一些代码来提取数据，在电子表格中阅读这些数据，或执行其他操作。 例如：

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

如需更详细的代码示例，请参阅[使用辅助角色][exportasa]。

## <a name="delete-your-old-data"></a><a name="delete"></a>删除旧数据
要负责管理存储容量，以及在必要时删除旧数据。

## <a name="if-you-regenerate-your-storage-key"></a>如果重新生成存储密钥...
如果更改存储密钥，连续导出将停止运行。 Azure 帐户中会显示通知。

打开“连续导出”选项卡并编辑导出。 编辑“导出目标”，只保留选定的同一存储。 单击“确定”以确认。

连续导出将重新开始。

## <a name="export-samples"></a>导出示例

* [使用流分析导出到 SQL][exportasa]
* [流分析示例 2](export-stream-analytics.md)

如果数据规模较大，可以考虑 [HDInsight](https://azure.microsoft.com/services/hdinsight/) - 云中的 Hadoop 群集。 HDInsight 提供多种技术用于管理和分析大数据。可以使用 HDInsight 来处理从 Application Insights 导出的数据。

## <a name="q--a"></a>问题解答
* *我想要一次性下载某个图表。*  

    没问题，可以这样做。 请在选项卡顶部单击“导出数据”。
* *我设置了导出，但存储中没有数据。*

    自设置导出之后，Application Insights 是否从应用程序收到了任何遥测数据？ 只会收到新数据。
* *尝试设置导出时出现拒绝访问错误*

    如果帐户由组织拥有，则你必须是所有者或参与者组的成员。
* *是否可以直接导出到我自己的本地存储？*

    很抱歉，不可以。 我们的导出引擎目前仅适用于 Azure 存储。  
* *放置在存储中的数据量是否有任何限制？*

    不是。 我们将持续推送数据，直到删除了导出。 如果达到 Blob 存储的外在限制，推送会停止，但那个限制极大。 可以自行控制使用的存储量。  
* *存储中应会出现多少个 Blob？*

  * 对于选择要导出的每种数据类型，将每隔分钟创建一个新 Blob（如果有可用的数据）。
  * 此外，对于高流量应用程序，将分配额外的分区单元。 在此情况下，每个单元会每分钟创建一个 Blob。
* *我为存储重新生成了密钥或更改了容器的名称，但现在导出不能正常进行。*

    请编辑导出并打开导出目标选项卡。像以前一样保留选择相同的存储，并单击“确定”以确认。 导出将重新开始。 如果更改是在最近几天内做出的，则不会丢失数据。
* *是否可以暂停导出？*

    是的。 单击“禁用”即可。

## <a name="code-samples"></a>代码示例

* [流分析示例](export-stream-analytics.md)
* [使用流分析导出到 SQL][exportasa]
* [属性类型和值的详细数据模型参考。](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>基于诊断设置的导出

基于诊断设置的导出使用与连续导出不同的架构。 它还支持连续导出不支持的功能：

* 具有 vnet、防火墙和专用链接的 Azure 存储帐户。
* 导出到事件中心。

若要迁移到基于诊断设置的导出，请执行以下操作：

1. 禁用当前连续导出。
2. [将应用程序迁移到基于工作区](convert-classic-resource.md)。
3. [启用诊断设置导出](create-workspace-resource.md#export-telemetry)。 从 Application Insights 资源中选择“诊断设置”>“添加诊断设置”。

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

