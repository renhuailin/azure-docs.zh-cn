---
title: Azure Sentinel 信息模型 (ASIM) 分析程序 | Microsoft Docs
description: 本文介绍如何将 KQL 函数用作实现 Azure Sentinel 信息模型 (ASIM) 的查询时分析程序
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: a08fff90197315edbfb78cb76a1c41bc84f90d69
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538338"
---
# <a name="azure-sentinel-information-model-asim-parsers-public-preview"></a>Azure Sentinel 信息模型 (ASIM) 分析程序（公共预览版）

在 Azure Sentinel 中，查询时需分析和[规范化](normalization.md)数据。 分析器生成为 [KQL 用户定义的函数](/azure/data-explorer/kusto/query/functions/user-defined-functions)，这些函数将现有表（例如 CommonSecurityLog）、自定义日志表或 Syslog 中的数据转换为规范化架构。 将分析程序保存为工作区函数后，可以像使用任何 Azure Sentinel 表一样使用该函数。

> [!IMPORTANT]
> ASIM 当前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="source-agnostic-and-source-specific-parsers"></a>源不可知和源特定分析程序

ASIM 包含两个级别的分析程序：源不可知和源特定分析程序：

### <a name="source-agnostic-parsers"></a>源不可知分析程序

与源无关的分析器将所有已规范化的源组合到同一架构，可用于通过规范化字段查询所有这些源。 与源无关的分析器名称为 `im<schema>`，其中的 `<schema>` 代表它处理的特定架构。

例如，以下查询使用与源无关的 DNS 分析器通过 `ResponseCodeName`、`SrcIpAddr` 和 `TimeGenerated` 规范化字段来查询 DNS 事件：

```kusto
imDns
  | where isnotempty(ResponseCodeName)
  | where ResponseCodeName =~ "NXDOMAIN"
  | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
```

与源无关的分析器可以使用 `union` KQL 运算符来组合多个特定于源的规范化分析器。 特定于源的规范化分析器名称为 `vim<schema><vendor><product>`。 因此，`imDns` 分析器如下所示：

```kusto
union isfuzzy=true
vimDnsEmpty,
vimDnsCiscoUmbrella,
vimDnsInfobloxNIOS,
vimDnsMicrosoftOMS
```

> [!NOTE]
> 在“日志”页面使用 ASIM 源不可知分析程序（以 `im` 开头）时，时间范围选择器设置为 `custom`。 你仍可以自行设置时间范围。 也可使用分析程序参数指定时间范围。
>
> 或者，使用 `ASim` 分析程序，该分析程序不支持参数，默认情况下也不会将时间范围选择器设置为 `custom`。
>

### <a name="source-specific-parsers"></a>源特定分析程序

将特定于源的规范化分析器添加到与源无关的分析器，可以在使用与源无关的分析器的内置查询中包含自定义源。

使用特定于源的分析器可以从内置内容（例如分析、工作簿和自定义数据的见解）中立即获取值。

也可以单独使用特定于源的分析器。 例如，在特定于 Infoblox 的工作簿中使用 `vimDnsInfobloxNIOS` 分析器。

## <a name="optimizing-parsing-using-parameters"></a><a name="optimized-parsers"></a>使用参数优化分析

使用分析程序可能会影响查询性能，主要是因为分析后必须筛选结果。 出于此原因，许多分析程序都具有可选的筛选参数，这使你能够在分析之前进行筛选并提高查询性能。 与查询优化和预筛选工作结合使用时，与根本不使用规范化相比，ASIM 分析程序提供的性能通常更佳。

通过在调用分析程序时添加一个或多个命名参数来使用筛选参数。 例如，以下查询将确保只返回对不存在的域的 DNS 查询：

```kusto
imDns(responsecodename='NXDOMAIN')
```

前面的示例类似于以下查询，但效率更高。

```kusto
imDns | where ResponseCodeName == 'NXDOMAIN'
```

每个架构都具有一组标准的筛选参数，这些参数记录在架构文档中。筛选参数是完全可选的，当前仅在 DNS 架构中完全受支持。 其他架构支持标准筛选参数，无需预筛选优化。

## <a name="writing-source-specific-parsers"></a>编写源特定分析程序

分析器是保存为工作区函数的 KQL 查询。 保存后，可以像使用内置表一样使用该函数。 分析器查询包括以下部分：

筛选器 > 分析 > 准备字段  

### <a name="filtering"></a>筛选

#### <a name="filtering-the-relevant-records"></a>筛选相关记录

在许多情况下，表包含多种类型的事件。 例如：
* Syslog 表包含来自多个源的数据。
* 自定义表可以包含来自单个源的信息，该源提供多种事件类型并可适应各种架构。

因此，解析器首先应该仅筛选与目标架构相关的记录。

KQL 中的筛选是使用 `where` 运算符进行的。 例如，Sysmon 事件 1 报告进程创建，应规范化为 ProcessEvent 架构 。 Sysmon 事件 1 事件是 `Event` 表的一部分，应使用以下筛选器：

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

#### <a name="filtering-based-on-parser-parameters"></a>基于分析程序参数进行筛选

使用[参数化分析程序](#optimized-parsers)时，请确保分析程序接受相关架构的筛选参数，如该架构的参考文章中所述。

每个架构的功能文章都是相同的。 例如，对于 DNS 查询参数化分析程序签名：

```kusto
let DNSQuery_MS=(
    starttime:datetime=datetime(null), 
    endtime:datetime=datetime(null), 
    srcipaddr:string='*', 
    domain_has_any:dynamic=dynamic([]),
    responsecodename:string='*', 
    dnsresponsename:string='*', 
    response_has_any_prefix:dynamic=dynamic([]),
    eventtype:string='lookup'
    )
```

根据参数值添加筛选器。 筛选时，请确保：

- **在使用物理字段分析之前进行筛选**。 如果筛选后的结果不够准确，请在分析后重复该测试以微调结果。 有关更多信息，请参阅[“筛选优化”](#optimization)。
 - **如果参数未定义且仍具有默认值，请不要筛选**。 以下示例显示了如何对字符串参数（默认值通常为“\*”）和列表参数（默认值通常为空列表）实现筛选。

``` kusto
srcipaddr=='*' or ClientIP==srcipaddr
array_length(domain_has_any) == 0 or Name has_any (domain_has_any)
```

> [!TIP]
> 同一类型的现有分析程序非常适合用于实现参数筛选。
>

#### <a name="filtering-optimization"></a><a name="optimization"></a>筛选优化


为了确保分析器保持良好的性能，请注意以下筛选建议：

-   始终根据内置字段而不是分析的字段进行筛选。 虽然使用分析的字段进行筛选有时更方便，但这会对性能造成很大的影响。
-   使用有利于优化性能的运算符。 具体而言，请使用 `==`、`has` 和 `startswith`。 使用 `contains` 或 `matches regex` 之类的运算符也会对性能造成很大的影响。

为保持性能而提供的筛选建议不一定总是很容易遵循。 例如，使用 `has` 不如使用 `contains` 那么准确。 在其他情况下，匹配内置字段（例如 `SyslogMessage`）不如比较提取的字段（例如 `DvcAction`）那么准确。 在这种情况下，建议仍旧使用性能优化的运算符而不是内置字段进行预筛选，并在分析后使用更准确的条件重复筛选。

有关示例，请参阅以下 [Infoblox DNS](https://aka.ms/AzSentinelInfobloxParser) 分析程序代码片段。 该分析器首先检查 SyslogMessage 字段是否 `has`（包含）单词 `client`。 但是，该词语可能使用在消息中的不同位置。 因此，在分析 `Log_Type` 字段后，该分析器会再次检查单词 `client` 是否确实为字段值。

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> 分析程序不应按时间筛选数据，因为使用分析程序的查询已针对时间进行筛选。
>

### <a name="parsing"></a>分析

查询选择相关的记录后，可能需要分析这些记录。 通常，如果单个文本字段中传达了大量事件信息，则需要进行分析。

下面按性能优化顺序列出了执行分析的 KQL 运算符。 第一个运算符提供优化程度最高的性能，最后一个运算符提供优化程度最低的性能。

|运算符  |说明  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    分析由分隔符分隔的值的字符串     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     分析 CSV（逗号分隔值）行格式的值的字符串。    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    使用某种模式（可以是性能较好的简化模式，也可以是正则表达式）分析任意字符串中的多个值。     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | 使用正则表达式分析任意字符串中的单一值。 `extract_all` 的性能类似于 `parse`（如果后者使用正则表达式）。        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    使用正则表达式提取任意字符串中的单个值。 <br><br>如果需要单个值，则使用 `extract` 的性能比使用 `parse` 或 `extract_all` 更好。 但是，对同一源字符串多次激活 `extract` 则不如单次激活 `parse` 或 `extract_all` 高效，因此应避免此操作。      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | 分析 JSON 格式的字符串中的值。 如果只需要 JSON 中的少量几个值，则使用 `parse`、`extract` 或 `extract_all` 可提供更好的性能。        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    分析 XML 格式的字符串中的值。 如果只需要 XML 中的少量几个值，则使用 `parse`、`extract` 或 `extract_all` 可提供更好的性能。     |
| | |

除了分析字符串之外，分析阶段可能还需要对原始值进行其他处理，包括：

- 格式设置和类型转换。 提取源字段后，可能需要设置其格式以适应目标架构字段。 例如，可能需要将表示日期和时间的字符串转换为日期时间字段。     在这种情况下，`todatetime` 和 `tohex` 等函数非常有用。

- 值查找。 提取源字段的值后，可能需要将其映射到为目标架构字段指定的值集。 例如，某些源报告数字 DNS 响应代码，而架构强制要求报告更常见的文本响应代码。 函数 `iff` 和 `case` 有助于映射一些值。

    例如，Microsoft DNS 分析器使用 `iff` 语句基于事件 ID 和响应代码分配 `EventResult` 字段，如下所示：

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    对于多个值，请使用 `datatable` 和 `lookup`，如同一个 DNS 分析器中所示：

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName,
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use',
         'Unassigned')
    ```

> [!NOTE]
> 转换不允许只使用 `lookup`，因为需将多个值映射到 `Reserved for Private Use` 或 `Unassigned`，因此查询会同时使用 lookup 和 case。
> 尽管如此，查询仍比对所有值使用 `case` 要高效得多。
>

### <a name="prepare-fields-in-the-result-set"></a>在结果集中准备字段

分析程序必须准备结果集字段，以确保使用规范化字段。 作为一项准则，不应从结果集中删除未规范化的原始字段，除非有令人信服的理由（例如，这些字段会造成混淆）。

以下 KQL 运算符用于准备字段：

|运算符  | 说明  | 何时在分析器中使用  |
|---------|---------|---------|
|**extend**     | 创建计算字段并将其添加到记录中        |  如果规范化字段是从原始数据分析或转换的，则使用 `Extend`。 有关详细信息，请参阅前面[分析](#parsing)部分中的示例。     |
|**project-rename**     | 重命名字段        |     如果某个字段存在于实际事件中且只需要重命名，请使用 `project-rename`。 <br><br>已重命名的字段的行为仍类似于内置字段，针对该字段的操作的性能要好得多。   |
|**project-away**     |      删除字段。   |对于要从结果集中删除的特定字段，请使用 `project-away`。         |
|**project**     |  选择之前已存在的字段，或者作为语句的一部分创建的字段。 删除所有其他字段。       | 不建议在分析器中使用，因为分析器不应删除未规范化的任何其他字段。 <br><br>如果需要删除特定的字段（例如，在分析过程中使用的临时值），请使用 `project-away` 从结果中将其删除。      |
| | | |

### <a name="handle-parsing-variants"></a>处理分析变体

在许多情况下，事件流中的事件包括需要不同分析逻辑的变体。

从不同的子分析程序生成一个分析程序，并让每个子分析程序处理需要不同分析逻辑的另一个事件变体通常会更方便。 然后，可以使用 `union` 运算符来统一这些子分析器（每个子分析器本身是一个查询）。 这种方法虽然可以带来方便，但不建议采用，因为它会显著影响分析器的性能。

处理变体时，请遵循以下准则：

|场景  |处理  |
|---------|---------|
|不同的变体表示不同的事件类型，通常映射到不同的架构     |  使用单独的分析器       |
|不同的变体表示相同的事件类型，但以不同的方式构建。     |   如果变体是已知的（例如，在分析之前有区分事件的方法时），请使用 `case` 运算符选择要运行的正确 `extract_all` 和字段映射，如 [Infoblox DNS 分析器](https://aka.ms/AzSentinelInfobloxParser)中所示。      |
|如果 `union` 不可避免     |  使用不可避免地需要使用 `union`，请确保遵循以下准则：<br><br>- 在每个子查询中使用内置字段进行预筛选。 <br>- 确保筛选器互斥。 <br>- 考虑不分析较不重要的信息，以减少子查询数量。       |
| | |

## <a name="add-your-parser-to-the-schema-source-agnostic-parser"></a><a name="include"></a>在架构源不可知分析程序中添加自己的分析程序

规范化允许将你自己的内容和内置内容与自定义数据一起使用。

例如，如果你的自定义连接器接收 DNS 查询活动日志，则你可以确保 DNS 查询活动日志利用任何规范化的 DNS 内容。

为此，需修改相关的源不可知分析程序，以包含你创建的源特定分析程序。 例如，更改 `imDns` 源不可知分析程序，使其包含你的分析程序，具体方法是将你的分析程序添加到 `union` 语句的分析程序列表中。 如果分析程序支持参数，请将其包含在参数签名中，如下面的 vimDnsYyyXxx 分析程序。 如果不支持，请不要将其包含在签名中，如下面的 vimDnsWwwZzz 分析程序。

```kusto
let DnsGeneric=(starttime:datetime=datetime(null), endtime:datetime=datetime(null)... ){
  union isfuzzy=true
    vimDnsEmpty, 
    vimDnsCiscoUmbrella (starttime, endtime, srcipaddr...),
    vimDnsInfobloxNIOS (starttime, endtime, srcipaddr...),
    vimDnsMicrosoftOMS (starttime, endtime, srcipaddr...),
    vimDnsYyyXxx (starttime, endtime, srcipaddr...),
    vimDnsWwwZzz
  };
  DnsGeneric( starttime, endtime, srcipaddr...)
```


## <a name="deploy-parsers"></a>部署分析器

通过将分析器复制到 Azure Monitor“日志”页并保存更改来手动部署分析器。 此方法可用于测试。 有关详细信息，请参阅[创建函数](../azure-monitor/logs/functions.md)。

但是，若要部署大量分析器，我们建议使用 ARM 模板。 例如，在部署包含与源无关的分析器以及多个特定于源的分析器的完整规范化解决方案时，或者为源的不同架构部署多个分析器时，可以使用 ARM 模板。

有关详细信息，请参阅[泛型分析器 ARM 模板](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery)。 使用此模板作为起点，在模板部署过程中将分析器粘贴到相关位置来部署分析器。 有关示例，请参阅 [DNS 分析器 ARM 模板](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)。

> [!TIP]
> ARM 模板可以包含不同的资源，因此可将分析器与连接器、分析规则或监视列表以及几个有用选项一起部署。 例如，分析器可以引用与其一起部署的监视列表。
>


## <a name="next-steps"></a>后续步骤

本文介绍 Azure Sentinel 信息模型 (ASIM) 分析程序。

有关详细信息，请参阅：

- [Azure Sentinel 信息模型概述](normalization.md)
- [Azure Sentinel 信息模型架构](normalization-about-schemas.md)
- [Azure Sentinel 信息模型内容](normalization-content.md)
