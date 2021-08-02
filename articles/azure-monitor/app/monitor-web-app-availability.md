---
title: 通过 URL ping 测试监视可用性 - Azure Monitor
description: 在 Application Insights 中设置 ping 测试。 当网站不可用或响应速度缓慢时接收警报。
ms.topic: conceptual
ms.date: 05/25/2021
ms.reviewer: sdash
ms.openlocfilehash: e7e52cb5db6db5fbd1e73713d3d3558be9e859f0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968404"
---
# <a name="monitor-availability-with-url-ping-tests"></a>通过 URL ping 测试监视可用性

“URL ping 测试”这个名称有点用词不当。 澄清一下，这些测试不使用 ICMP（Internet 控制消息协议）来检查站点的可用性， 而是使用更高级的 HTTP 请求功能来验证终结点是否响应。 它们还测量与该响应相关联的性能，并添加设置自定义成功标准的功能，以及更多高级功能，例如分析从属请求、允许重试。

为了创建可用性测试，需要使用现有的 Application insights 资源或[创建 Application Insights 资源](create-new-resource.md)。

  **** 若要创建第一个可用性请求，请打开“可用性”窗格并选择“创建测试”。

:::image type="content" source="./media/monitor-web-app-availability/availability-create-test-001.png" alt-text="创建测试的屏幕截图。":::

## <a name="create-a-test"></a>创建测试

|设置| 说明
|----|----|----|
|**URL** |  URL 可以是要测试的任何网页，但必须在公共 Internet 中可见。 该 URL 可以包括查询字符串。 因此，例如，可以稍微训练一下数据库。 如果 URL 解析为重定向，最多可以跟踪 10 个重定向。|
|**分析从属请求**| 测试会请求图像、脚本、样式文件以及其他属于受测网页的文件。 记录的响应时间包括获取这些文件所耗费的时间。 如果无法在超时期限内为整个测试成功下载这些资源中的任一个，则表明测试失败。 如果不选中此选项，则测试只请求指定 URL 的文件。 启用此选项会导致更严格的检查。 测试有时候可能会失败，在手动浏览站点时，可能注意不到这一点。
|**启用重试**|测试失败时，会在短时间后重试。 仅当连续三次尝试失败时，才报告失败。 然后，将按照一般的测试频率执行后续测试。 重试会暂停，直到下次成功为止。 可在每个测试位置单独应用此规则。 **建议使用此选项**。 平均大约有 80% 的失败可在重试后消除。|
|**测试频率**| 设置从每个测试位置运行测试的频率。 如果有五个测试位置，且默认频率为五分钟，则平均每隔一分钟测试站点一次。|
|**测试位置**| 是服务器从其将 Web 请求发送到 URL 的位置。 **建议最低测试位置数目为 5**，以确保可以将网站中的问题与网络问题区分开来。 最多可以选择 16 个位置。

**如果 URL 在公共 Internet 中不可见，可以选择性地打开防火墙，只允许测试事务通过**。 若要详细了解可用性测试代理的防火墙例外，请参阅 [IP 地址指南](./ip-addresses.md#availability-tests)。

> [!NOTE]
> 强烈建议从多个位置进行测试，**至少为 5 个位置**。 这是为了防止可能由特定位置的暂时性问题导致的虚假警报。 此外，我们发现最佳配置是使 **测试位置的数目等于警报位置阈值 + 2**。

## <a name="success-criteria"></a>成功标准

|设置| 说明
|----|----|----|
| **测试超时** |减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源  。|
| **HTTP 响应** | 视为成功的返回状态代码。 代码 200 指示返回了正常网页。|
| **内容匹配** | 类似于“欢迎!”的字符串。 我们测试区分大小写的匹配项是否出现在每个响应中。 它必须是不带通配符的纯字符串。 别忘了，如果页面内容更改，可能需要更新。 **内容匹配仅支持英文字符** |

## <a name="alerts"></a>警报

|设置| 说明
|----|----|----|
|**近实时（预览）** | 我们建议使用近实时警报。 在创建可用性测试后会配置此类警报。  |
|**警报位置阈值**|建议最少 3/5 个位置。 警报位置阈值和测试位置数目之间的最佳关系是警报位置阈值  =  测试位置数 - 2，至少有 5 个测试位置   。|

## <a name="location-population-tags"></a>位置填充标记

使用 Azure 资源管理器部署可用性 URL ping 测试时，可以将以下填充标记用于地理位置属性。

### <a name="azure-gov"></a>Azure 政府

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

#### <a name="azure"></a>Azure

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

可用性测试结果可以使用折线图和散点图的视图进行可视化。

几分钟之后，单击“刷新”  即可查看测试结果。

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="屏幕截图显示突出显示了“刷新”按钮的“可用性”页。":::

散点图视图显示其中有诊断测试步骤详细信息的测试结果示例。 测试引擎存储已失败的测试的诊断详细信息。 对于成功的测试，将存储执行子集的诊断详细信息。 将鼠标悬停在任何绿点/红点上，可查看测试、测试名称和位置。

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="行视图。" border="false":::

选择特定测试、位置或减少时间段，可查看围绕感兴趣的时间段的更多结果。 使用搜索资源管理器以查看所有执行结果，或者使用分析查询以针对此数据运行自定义报告。

## <a name="inspect-and-edit-tests"></a>检查和编辑测试

若要编辑、临时禁用或删除某个测试，请单击测试名称旁边的省略号。 进行更改后，将配置更改传播到所有测试代码最多可能需要 20 分钟。

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="查看测试详细信息。编辑以及禁用 Web 测试。" border="false":::

对服务执行维护时，可能需要禁用可用性测试或与这些测试关联的警报规则。

## <a name="if-you-see-failures"></a>如果看到失败

选择红点。

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="端到端事务详细信息选项卡的屏幕截图。" border="false":::

从可用性测试结果中，可以看到所有组件的事务详细信息。 在此门户中，可以：

* 查看故障排除报表以确定导致测试失败的原因，但应用程序仍然可用。
* 检查从服务器收到的响应。
* 使用在处理失败的可用性测试时收集的相关服务器端遥测数据进行故障诊断。
* 在 Git 或 Azure Boards 中记录问题或工作项以跟踪问题。 Bug 中将包含转至此事件的链接。
* 在 Visual Studio 中打开 Web 测试结果。

若要详细了解端到端事务诊断体验，请访问[事务诊断文档](./transaction-diagnostics.md)。

单击异常行可查看导致综合可用性测试失败的服务器端异常的详细信息。 还可以获取[调试快照](./snapshot-debugger.md)，进行更丰富的代码级诊断。

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="服务器端诊断。":::

除了原始结果外，还可以在[指标资源管理器](../essentials/metrics-getting-started.md)中查看两个关键的可用性指标：

1. 可用性：已成功的测试占执行的所有测试的百分比。
2. 测试持续时间：执行的所有测试的平均测试持续时间。

## <a name="automation"></a>自动化

* [使用 PowerShell 脚本自动设置可用性测试](./powershell.md#add-an-availability-test)。
* 设置在引发警报时调用的 [webhook](../alerts/alerts-webhooks.md) 。


## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [多步骤 Web 测试](availability-multistep.md)
* [故障排除](troubleshoot-availability.md)
* [Web 测试 Azure 资源管理器模板](/azure/templates/microsoft.insights/webtests?tabs=json)