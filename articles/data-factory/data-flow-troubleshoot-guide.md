---
title: 排查映射数据流问题
description: 了解如何排查 Azure 数据工厂中的数据流问题。
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: f41d53207eb00e4044e523e481de444a615a5d88
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389392"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>排查 Azure 数据工厂中的映射数据流问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了排查 Azure 数据工厂中的映射数据流问题的常见方法。  请参阅[常见的数据流错误代码和消息](data-flow-troubleshoot-errors.md)，了解具体的错误消息及其相关的原因和建议。

## <a name="miscellaneous-troubleshooting-tips"></a>其他故障排除提示
- **问题**：发生意外的异常，执行失败。
    - **消息**：在数据流活动执行期间：遇到了意外异常，执行失败。
    - **原因**：此错误为后端服务错误。 重试操作并重启调试会话。
    - **建议**：如果重试和重启不能解决问题，请与客户支持联系。

-  **问题**：在预览调试数据期间未输出有关联接的数据。
    - **消息**：存在大量的 null 值或缺失值，这可能是因为采样的行太少。 尝试更新调试行限制并刷新数据。
    - **原因**：联接条件与任何行都不匹配，或者在预览数据期间导致了大量的 null 值。
    - **建议**：在“调试设置”中增加源行限制中的行数。 确保选择的 Azure IR 的数据流群集足够大，可以处理更多数据。
    
- **问题**：包含多行 CSV 文件的源发生验证错误。 
    - **消息**：可能会出现以下错误消息之一：
        - 最后一列为 NULL 或缺失。
        - 源上的架构验证失败。
        - 架构导入无法在 UX 中正确显示，并且最后一列的名称中包含换行符。
    - **原因**：在映射数据流中，如果使用 \r\n 作为行分隔符，多行 CSV 源文件目前无法正常工作。 有时，在回车符处使用多余的行可能导致错误。 
    - **建议**：使用 \n（而不是 \r\n）作为行分隔符在源中生成文件。 或者使用复制活动将 CSV 文件转换为使用 \n 作为行分隔符。

## <a name="general-troubleshooting-guidance"></a>一般故障排除指南
1. 检查数据集连接状态。 在每个源转换和接收器转换中，转到要使用的每个数据集的链接服务，然后测试连接。
2. 在数据流设计器中，检查文件和表连接的状态。 在调试模式下，在源转换中选择“数据预览”，以确保能够访问数据。
3. 如果数据预览中一切正常，请转到管道设计器，并将数据流置于管道活动中。 调试管道，以进行端到端测试。

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>数据流中 CSV/CDM 格式的改进 

如果在 Azure 数据工厂 V2 中使用分隔文本或 CDM 格式映射数据流，由于从 2021 年 5 月 1 日开始对数据流中的分隔文本/CDM 进行改进，你可能会面临现有管道的行为变更。 

改进之前，可能会遇到以下问题，但改进之后，问题已修复。 阅读以下内容，以确定此改进是否对你有影响。 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>场景 1：遇到意外的行分隔符问题

 如果处于以下情况，则会受到影响：
 - 使用多行设置设置为 True 或将 CDM 设置为源的分隔文本。
 - 第一行超过 128 个字符。 
 - 数据文件中的行分隔符不是 `\n`。

 改进之前，由于在多行设置设置为 True 时，行分隔符设置会失效，而且会基于前 128 个字符自动检测行分隔符，因此，默认的行分隔符 `\n` 可能会意外地用于分析分隔文本文件。 如果无法检测到实际的行分隔符，则会返回到 `\n`。  

 改进后，`\r`、`\n` 和 `\r\n` 这三个行分隔符中的任何一个都应有效。
 
 改进后的一项管道行为变更如下例所示：

 **示例**：<br/>
   对于以下列：<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   改进之前，`\r` 保留在列值中。 分析后的列结果为：<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   改进后，分析得到的列结果应为：<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>场景 2：遇到错误读取包含“\r\n”的列值的问题

 如果处于以下情况，则会受到影响：
 - 使用多行设置设置为 True 或将 CDM 设置为源的分隔文本。 
 - 行分隔符为 `\r\n`。

 改进之前，读取列值时，其中的 `\r\n` 可能会错误地替换为 `\n`。 

 改进之后，列值中的 `\r\n` 将不会替换为 `\n`。

 改进后的一项管道行为变更如下例所示：
 
 **示例**：<br/>
  
 对于以下列：<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 改进之前，分析后的列结果为：<br/>
  **`A\n`**` B C`<br/>

 改进后，分析得到的列结果应为：<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>场景 3：遇到错误地写入包含“\n”的列值的问题

 如果处于以下情况，则会受到影响：
 - 使用分隔文本作为接收器。
 - 列值包含 `\n`。
 - 行分隔符已设为 `\r\n`。
 
 改进之前，写入列值时，其中的 `\n` 可能会错误地替换 `\r\n`。 

 改进之后，列值中的 `\n` 将不会替换为 `\r\n`。
 
 改进后的一项管道行为变更如下例所示：

 **示例**：<br/>

 对于以下列：<br/>
 **`A\n`**` B C`<br/>

 改进之前，CSV 接收器为：<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 改进之后，CSV 接收器应为：<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>场景 4：遇到将空字符串误读为 NULL 的问题
 
 如果处于以下情况，则会受到影响：
 - 使用分隔文本作为源。 
 - NULL 值设置为非空值。 
 - 列值为空字符串，且不带引号。 
 
 改进之前，不带引号的空字符串的列值读取为 NULL。 

 改进之后，空字符串不会被分析为 NULL 值。 
 
 改进后的一项管道行为变更如下例所示：

 **示例**：<br/>

 对于以下列：<br/>
  `A, ,B, `<br/>

 改进之前，分析后的列结果为：<br/>
  `A null B null`<br/>

 改进后，分析得到的列结果应为：<br/>
  `A "" (empty string) B "" (empty string)`<br/>

###  <a name="internal-server-errors"></a>内部服务器错误

可能导致内部服务器错误的特定情况如下所示。

#### <a name="scenario-1-not-choosing-the-appropriate-compute-sizetype-and-other-factors"></a>情况 1：未选择适当的计算大小/类型和其他因素

  数据流的成功执行取决于许多因素，包括计算大小/类型、要处理的源/接收器的数量、分区规范、所涉及的转换、数据集的大小、数据倾斜度等。<br/>
  
  有关更多指导，请参阅 [Integration Runtime 性能](concepts-integration-runtime-performance.md)。

#### <a name="scenario-2-using-debug-sessions-with-parallel-activities"></a>情况 2：结合使用调试会话与并行活动

  使用数据流调试会话通过管道中的 ForEach 之类的构造触发运行时，可能会将多个并行运行提交给同一群集。 由于内存不足等资源问题，在运行时，这种情况可能会导致出现群集故障问题。<br/>
  
  若要在发布更改后，使用在管道活动中定义的适当集成运行时配置提交运行，请选择“立即触发”或“调试” > “使用活动运行时”  。

#### <a name="scenario-3-transient-issues"></a>情况 3：暂时性问题

  执行中涉及的微服务的暂时性问题可能导致运行失败。<br/>
  
  在管道活动中配置重试可解决暂时性问题引起的问题。 有关更多指导，请参阅[活动策略](concepts-pipelines-activities.md#activity-json)。

## <a name="next-steps"></a>后续步骤

在故障排除时如需更多帮助，请参阅以下资源：

- [常见映射数据流错误和消息](data-flow-troubleshoot-errors.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
