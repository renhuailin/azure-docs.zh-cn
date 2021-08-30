---
title: 通过 URL ping 测试监视可用性 - Azure Monitor
description: 在 Application Insights 中设置 ping 测试。 当网站不可用或响应速度缓慢时接收警报。
ms.topic: conceptual
ms.date: 07/13/2021
ms.reviewer: sdash
ms.openlocfilehash: 55e396f7d1af6bc05d722e90d7dad99b2ac8b2d7
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271646"
---
# <a name="monitor-availability-with-url-ping-tests"></a>通过 URL ping 测试监视可用性

“URL ping 测试”这个名称有点用词不当。 这些测试不使用 Internet 控制消息协议 (ICMP) 来检查站点的可用性。 而是使用更高级的 HTTP 请求功能来验证终结点是否响应。 这些测试会测量与该响应相关的性能。 它们还添加设置自定义成功标准的功能，并提供更多高级功能，例如分析从属请求、允许重试。

为了创建可用性测试，需要使用现有的 Application insights 资源或[创建 Application Insights 资源](create-new-resource.md)。

> [!NOTE]
> URL ping 测试被归类为经典测试。 可在“可用性”窗格的“添加经典测试”下找到此类测试 。 如需更高级的功能，请参阅[标准测试（预览版）](availability-standard-tests.md)。
 
## <a name="create-a-test"></a>创建测试

若要创建你的第一个可用性请求：
1. 在 Application Insights 资源中，打开“可用性”窗格，并选择“添加经典测试”  **** 。

    :::image type="content" source="./media/monitor-web-app-availability/create-test.png" alt-text="显示“可用性”窗格和经典测试添加按钮的屏幕截图。" lightbox ="./media/monitor-web-app-availability/create-test.png":::
1. 为测试命名，然后在“SKU”处选择“URL ping” 。
1. 输入要测试的 URL。
1. 根据需要调整下表所述的设置，然后选择“创建”。

   |设置| 说明 |
   |----|----|
   |**URL** |  URL 可以是要测试的任何网页，但必须在公共 Internet 中可见。 该 URL 可以包括查询字符串。 例如，可稍微练习一下数据库。 如果 URL 解析为重定向，则最多可跟踪 10 个重定向。|
   |**分析从属请求**| 测试会请求图像、脚本、样式文件以及其他属于受测网页的文件。 记录的响应时间包括获取这些文件所耗费的时间。 如果无法在超时期限内为整个测试成功下载所有这些资源，测试会失败。 如果不启用此选项，则测试只请求你指定的 URL 处的文件。 启用此选项会导致更严格的检查。 对于手动浏览站点时无法注意到的情况，测试可能会失败。
   |**启用重试**|测试失败时，会在短时间后重试。 仅当连续三次尝试失败时，才报告失败。 然后，将按照一般的测试频率执行后续测试。 重试会暂停，直到下次成功为止。 可在每个测试位置单独应用此规则。 *建议使用此选项*。 平均大约有 80% 的失败可在重试后消除。|
   |**测试频率**| 该设置决定的是从每个测试位置运行测试的频率。 如果有 5 个测试位置，且默认频率为 5 分钟，则平均每一分钟测试一次站点。|
   |**测试位置**| 此设置的值是服务器从其向 URL 发送 Web 请求的位置。 建议至少有 5 个测试位置，以确保可将网站中的问题与网络问题区分开。 最多可以选择 16 个位置。

如果 URL 在公共 Internet 中不可见，可选择性地打开防火墙，只允许测试事务通过。 若要详细了解可用性测试代理的防火墙例外，请参阅 [IP 地址指南](./ip-addresses.md#availability-tests)。

> [!NOTE]
> 强烈建议至少在 5 个地点进行测试。 此方法有助于防止可能由特定位置的暂时性问题导致的误报。 此外，我们还发现最佳配置是使测试位置的数目等于警报位置阈值 + 2。

## <a name="success-criteria"></a>成功标准

|设置| 说明 |
|----|----|
| **测试超时** |减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源  。|
| **HTTP 响应** | 视为成功的返回状态代码。 200 这一代码指示返回了正常网页。|
| **内容匹配** | 我们会测试每个响应中是否出现某个字符串的精确匹配项（区分大小写）。 它必须是不带通配符的纯字符串（例如“Welcome!”）。 别忘了，如果页面内容更改，可能需要更新。 *内容匹配仅支持英文字符。* |

## <a name="alerts"></a>警报

|设置| 说明 |
|----|----|
|**近实时（预览）** | 建议使用近实时的警报。 创建可用性测试后，请配置此类型的警报。  |
|**警报位置阈值**| 警报位置阈值和测试位置数目之间的最佳关系是，警报位置阈值 = 测试位置数 - 2，至少有 5 个测试位置。|

## <a name="location-population-tags"></a>位置填充标记

使用 Azure 资源管理器部署可用性 URL ping 测试时，可将以下填充标记用于地理位置属性。

### <a name="azure-government"></a>Azure Government

| 显示名称   | 填充名称     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| US Gov 亚利桑那州  | usgov-phx-azr       |
| US Gov 德克萨斯州    | usgov-tx-azr        |
| USDoD 东部     | usgov-ddeast-azr    |
| USDoD 中部  | usgov-ddcentral-azr |

### <a name="azure-china"></a>Azure 中国

| 显示名称   | 填充名称     |
|----------------|---------------------|
| 中国东部     | mc-cne-azr          |
| 中国东部 2   | mc-cne2-azr         |
| 中国北部    | mc-cnn-azr          |
| 中国北部 2  | mc-cnn2-azr         |

### <a name="azure"></a>Azure

| 显示名称                           | 填充名称   |
|----------------------------------------|-------------------|
| 澳大利亚东部                         | emea-au-syd-edge  |
| 巴西南部                           | latam-br-gru-edge |
| Central US                             | us-fl-mia-edge    |
| 东亚                              | apac-hk-hkn-azr   |
| 美国东部                                | us-va-ash-azr     |
| 法国南部（以前为法国中部） | emea-ch-zrh-edge  |
| 法国中部                         | emea-fr-pra-edge  |
| 日本东部                             | apac-jp-kaw-edge  |
| 北欧                           | emea-gb-db3-azr   |
| 美国中北部                       | us-il-ch1-azr     |
| 美国中南部                       | us-tx-sn1-azr     |
| 东南亚                         | apac-sg-sin-azr   |
| 英国西部                                | emea-se-sto-edge  |
| 西欧                            | emea-nl-ams-azr   |
| 美国西部                                | us-ca-sjc-azr     |
| 英国南部                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>查看可用性测试结果

可使用折线图和散点图直观呈现可用性测试结果。

几分钟后，选择“刷新”即可查看测试结果。

:::image type="content" source="./media/monitor-web-app-availability/refresh.png" alt-text="“可用性”页面的屏幕截图，其中突出显示了“刷新”按钮。" lightbox="./media/monitor-web-app-availability/refresh.png":::

散点图显示其中有诊断测试步骤详细信息的测试结果示例。 测试引擎存储已失败的测试的诊断详细信息。 对于成功的测试，将存储执行子集的诊断详细信息。 将鼠标悬停在任何绿点/红点上可查看测试名称和位置。

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="显示折线图的屏幕截图。" border="false":::

选择特定测试/位置或缩短时间段，以查看感兴趣的时间段的更多结果。 使用搜索资源管理器以查看所有执行的结果，或者使用分析查询来针对此数据运行自定义报告。

## <a name="inspect-and-edit-tests"></a>检查和编辑测试

若要编辑、临时禁用或删除某个测试，请选择测试名称旁边的省略号 (...)。 进行更改后，最多可能需要 20 分钟就能将配置更改传播到所有测试代码。

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="显示测试详细信息的屏幕截图，其中包含用于编辑和禁用测试的选项。" border="false":::

对服务执行维护时，可能需要禁用可用性测试或与这些测试关联的警报规则。

## <a name="actions-if-you-see-failures"></a>出现失败时应执行的操作

选择红点。

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="端到端事务详细信息选项卡的屏幕截图。" border="false":::

从可用性测试结果中，可以看到所有组件的事务详细信息。 然后，可以：

* 查看故障排除报表，来确定导致测试失败但应用程序仍然可用的原因。
* 检查从服务器收到的响应。
* 使用在处理失败的可用性测试时收集的相关服务器端遥测数据进行故障诊断。
* 在 Git 或 Azure Boards 中记录问题或工作项以跟踪问题。 Bug 中将包含转至此事件的链接。
* 在 Visual Studio 中打开 Web 测试结果。

若要详细了解端到端事务诊断，请参阅[事务诊断文档](./transaction-diagnostics.md)。

选择异常行可查看导致综合可用性测试失败的服务器端异常的详细信息。 还可获取[调试快照](./snapshot-debugger.md)，进行更丰富的代码级诊断。

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="显示服务器端诊断的屏幕截图。":::

除了原始结果外，还可在[指标资源管理器](../essentials/metrics-getting-started.md)查看两个关键的可用性指标：

- **可用性**：已成功的测试占执行的所有测试的百分比。
- **测试持续时间**：执行的所有测试的平均测试持续时间。

## <a name="automation"></a>自动化

* [使用 PowerShell 脚本自动设置可用性测试](./powershell.md#add-an-availability-test)。
* 设置在引发警报时调用的 [Webhook](../alerts/alerts-webhooks.md)。


## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [多步骤 Web 测试](availability-multistep.md)
* [故障排除](troubleshoot-availability.md)
* [Web 测试 Azure 资源管理器模板](/azure/templates/microsoft.insights/webtests?tabs=json)
