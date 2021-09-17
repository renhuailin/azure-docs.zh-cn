---
title: Azure Enterprise 注册发票
description: 本文介绍如何管理和处理 Azure 企业发票。
author: bandersmsft
ms.author: banders
ms.date: 08/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: ruturajd
ms.custom: contperf-fy21q1
ms.openlocfilehash: 77f463d34ebd05e39876933d65631371acff1707
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515718"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure Enterprise 注册发票

本文介绍如何管理和处理 Azure 企业协议 (Azure EA) 发票。 发票代表帐单。 请检查发票以确保其准确。 你还应该熟悉管理发票时可能需要执行的其他任务。

## <a name="view-usage-summary-and-download-reports"></a>查看使用情况摘要和下载报告

企业管理员可以查看对其使用情况数据、使用的 Azure 预付款以及与 Azure Enterprise 门户中其他使用量相关的费用的摘要。 费用以摘要形式显示，适用于所有帐户和订阅。

若要查看特定帐户的详细使用情况，请下载使用情况详细信息报表：

1. 登录到 Azure Enterprise 门户。
1. 选择“报告”。
1. 选择“下载使用情况”选项卡。
1. 在报表列表中，选择要获取的月份报表对应的“下载”。

   > [!NOTE]
   > 使用情况详细信息报表不包含任何适用的税费。
   >
   > 从使用服务开始算起，最长可能需要在延迟八小时之后，其费用才会反映在报表中。

查看使用情况摘要报表和关系图：

1. 登录到 Azure Enterprise 门户。
1. 选择预付款期限。
   若要更改“使用情况摘要”的日期范围，可以在页面右上角从“M”（每月）切换为“C”（自定义），然后输入自定义的开始日期和结束日期。    
   ![在自定义视图中创建和查看使用情况摘要并下载报表](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. 若要查看更多详细信息，可以在图表上选择某个时段或月份。
   - 该图表显示了每月的使用情况，其中细分了使用量、服务额外费用、单独计收的费用和 Azure 市场费用。
   - 对于选定的月份，可以使用图表下面的字段按部门、帐户和订阅进行筛选。
   - 可以在“按服务列出的费用”与“按层次结构列出的费用”之间切换。 
   - 展开相关的部分可以查看“Azure 服务”、“单独计收的费用”和“Azure 市场”中的详细信息。  

观看以下视频来了解如何查看使用情况：

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>下载 CSV 报告

企业管理员可以使用“月份报表下载”页以 CSV 文件形式下载以下报表：

- 余额和费用
- 使用情况详细信息
- Azure 市场费用
- 价目表

若要下载报告：

1. 在 Azure Enterprise 门户中选择“报表”。
2. 选择页面顶部的“下载使用情况”。
3. 选择月份报告旁边的“下载”。

   > [!NOTE]
   > 从使用日期开始算起，最长可能需要延迟 72 小时，其费用才会显示在报告中。
   >
   > 用户在使用 Safari 将 CSV 文件下载到 Excel 时可能会遇到格式错误。 为避免错误，请使用文本编辑器打开文件。

![显示“下载使用情况”页的示例](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

观看以下视频来了解如何下载使用情况信息：

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>高级报表下载

可以使用高级报表下载来获取涵盖特定日期范围或帐户的报表。 输出文件采用 CSV 格式，以容纳较大的记录集。

1. 在 Azure Enterprise 门户中选择“高级报表下载”。
1. 选择适当的日期范围和帐户。
1. 选择“请求使用情况数据”。
1. 选择“刷新”按钮，直到报表状态更新为“下载”。 
1. 下载报表。

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>下载先前注册的使用情况报表和计费信息

发生注册转移后，可以下载先前某个注册的使用情况报表和计费信息。 Azure Enterprise 门户和成本管理中都会提供历史报表。

Azure Enterprise 门户会从视图中筛选出非活动的注册。 需要取消选中“活动”框才能查看非活动的已转移注册。  

![取消选中“活动”框可让用户查看非活动的注册](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>更改即将推出的超额发票的 PO 编号

Azure Enterprise 门户会自动生成默认的采购订单 (PO) 编号，除非企业管理员在开票日期之前设置一个编号。 在收到自动发送的发票通知电子邮件后的最多 7 天内，企业管理员可以更新 PO 编号。 

若要防止每月自动生成 PO 编号，可以锁定 PO 编号。 请参阅[锁定 PO 编号](#lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles)。

### <a name="update-the-azure-services-purchase-order-number"></a>更新 Azure 服务采购订单编号

1. 在 Azure Enterprise 门户中，选择“报表” > “使用情况摘要”。
1. 在右上角选择“编辑 PO 编号”。
1. 选择“Azure 服务”单选按钮。
1. 从日期范围下拉菜单中选择一个“发票期间”。
   在收到发票通知之后、支付发票金额之前的 7 天内可以编辑 PO 编号。
1. 在“PO 编号”字段中输入新的 PO 编号。
1. 选择“保存”以提交更改。

### <a name="update-the-azure-marketplace-purchase-order-number"></a>更新 Azure 市场采购订单编号

1. 在 Azure Enterprise 门户中，选择“报表” > “使用情况摘要”。
1. 在右上角选择“编辑 PO 编号”。
1. 选中“市场”单选按钮。
1. 从日期范围下拉菜单中选择一个“发票期间”。  
    在收到发票通知之后、支付发票金额之前的 7 天内可以编辑 PO 编号。
1. 在“PO 编号”字段中输入新的 PO 编号。
1. 选择“保存”以提交更改。

### <a name="lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles"></a>锁定 PO 编号以防止在即将到来的计费周期中自动更新

锁定 PO 编号后，它将对所有新发票保持锁定，你无需更新 PO 编号。

1.  在 Azure Enterprise 门户中，选择“报表” > “使用情况摘要”。 
2.  在右上角选择“编辑 PO 编号”。
3.  在“PO 编号”字段中键入新的 PO 编号。
4.  选择“锁定 PO 编号”框。
5.  选择“保存”以提交更改。  
    :::image type="content" source="./media/ea-portal-enrollment-invoices/lock-po.png" alt-text="显示“查看/编辑 PO 编号”框的屏幕截图。" lightbox="./media/ea-portal-enrollment-invoices/lock-po.png" :::


## <a name="azure-enterprise-billing-frequency"></a>Azure 企业计费频率

Microsoft 每年在注册生效日期对 Microsoft Azure 服务的任何预付款购买计费。 对于超出预付金额的任何用量，Microsoft 将以欠款的形式计费。

- 预付费用是根据每月费率报出的，每年提前计费。
- 超额费用按月计算，在计费周期结束时以欠款形式计费。

### <a name="billing-intervals"></a>计费时间间隔

计费时间间隔取决于预付款购买的选择方式。 年度预付的日期范围与以下任何一项相一致：

- 注册周年日期
- 一年期修正订阅的生效日期。

收到超额发票的日期取决于注册开始日期和设置：

- **开始日期在 2018 年 5 月 1 日之前的直接合约**：
  - 如果你签署的是直接企业协议 (EA)，则 Azure 服务（不包括 Azure 市场服务）采用每年计费周期。 计费周期基于周年日期：协议生效的日期。
  - 如果超过了 Azure EA 预付款阈值的 150%，则会自动转换为基于周年日期的按季计费周期。 此外，你还会收到 Azure 服务超额发票。
  - 如果未超过 Azure 预付款阈值的 150%，则注册将保持采用按年计费周期。 预付款年份结束时，你将收到超额发票。

- **开始日期在 2018 年 5 月 1 日之后的直接合约**：
  - Azure 使用量和单独计费发票采用每月计费周期。
  - Azure 预付款未涵盖的任何费用计为超额款项。  

- **登记开始日期在 2018 年 5 月 1 日之前的间接合约**：

  如果你是在开始日期 2018 年 5 月 1 日之前签署间接企业协议 (EA) 的客户，则系统已为你设置每季计费周期。 渠道合作伙伴 (CP) 将直接向你开票。  

- **开始日期在 2018 年 5 月 1 日之后的间接合约**：

  采用每月计费周期。  

### <a name="increase-your-azure-prepayment"></a>增加 Azure 预付款

随时可以增加预付款。 将按该年份预付款期限的剩余月数计费。 例如，如果你注册的是一年期的修订订阅并在第六个月增加了预付款，则系统将按当年剩下的六个月对你开票。 还将更新预付款期限后六个月的预付款使用量。 这些新数量用于确定任何超额费用。

### <a name="overage"></a>超额

如果超额，将对计费时期内超出预付款使用量的使用量或预留使用量计费。 如果要查看为各项计算超额用量的明细，请参阅使用情况摘要报表或联系自己的渠道合作伙伴。

对于发票上的每个项，你将看到：

- **应付金额**：总费用
- **预付款使用量**：用于支付费用的预付款金额
- **净额**：超出预付款的费用

仅对超出预付款的净额计算适用税金。

系统自动执行超额开票。 发出通知和开具发票的时间取决于计费周期的结束日期。

- 通常在客户计费结束日期后的 7 日内发送超额通知。
- 在发送通知后的 7 到 9 天内发送超额发票。
- 在发送超额通知之后、开票之前的 7 天内，可以查看费用和更新系统生成的 PO 编号。

### <a name="azure-marketplace"></a>Azure 市场

自 2019 年 4 月计费周期起，客户将开始收到一份 Azure 发票，它将所有 Azure 和 Azure 市场的费用合并到一份发票中（而不是分别使用两份单独的发票）。 此项更改不影响澳大利亚、日本或新加坡的客户。

在向合并发票过渡期间，你将收到一份不完整的 Azure 市场发票。 这份单独的最终发票显示计费更新日期之前产生的 Azure 市场费用。 该日期以后产生的 Azure 市场费用显示在 Azure 发票上。 过渡期结束后，所有 Azure 费用和 Azure 市场费用均显示在合并发票上。  

### <a name="adjust-billing-frequency"></a>调整计费频率

客户的计费频率为每年、每季或每月。 计费周期是客户签署协议时确定的。 每月计费是最短的计费间隔。

- 将直接注册的计费周期从每年更改为每季需要经过企业管理员的 **批准**。 对于间接注册，需要合作伙伴管理员的批准。 更改将在当前计费季度结束时生效。
- 若要将计费周期从每年或每季更改为每月，需要对协议进行 **修正**。  对现有注册计费周期进行任何更改都需要经过企业管理员的批准，或者经过“接收帐单的联系人”的批准。
- 将审批内容 **提交** 给 [Azure Enterprise 门户支持人员](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 选择问题类别：“计费和开票”。

更改将在当前计费季度结束时生效。

如果已签署“协议修订 M503”，则可以将任何协议的任何计费频率改为每月计费。 

### <a name="request-an-invoice-copy"></a>请求发票副本

若要请求发票副本，请与合作伙伴联系。

## <a name="credits-and-adjustments"></a>额度和调整

可以在 [Azure Enterprise 门户](https://ea.azure.com)的“报表”部分查看应用于注册的所有额度或调整。

若要查看额度：

1. 在 [Azure Enterprise 门户](https://ea.azure.com)中选择“报表”部分。
1. 选择“使用情况摘要”。
1. 在右上角将 M 视图更改为 C 视图 。
1. 扩展 Azure 服务预付款表中的调整字段。
1. 你将看到应用于你的注册的额度和简短说明。 例如：服务级别协议额度。

## <a name="pay-your-overage-with-your-azure-prepayment"></a>通过 Azure 预付款支付超额费用

若要向超额费用应用 Azure 预付款，必须满足以下条件：

- 产生的超额费用尚未支付，并且付款期限在计费服务结束日期后的一年之内。
- 可用 Azure 预付款金额涵盖产生的总费用，包括以往未付的所有 Azure 发票。
- 要完成的计费条款必须完全了结。 计费将在每个月的五号之后完全了结。
- 要抵销的计费周期必须完全了结。
- Azure 预付款折扣 (APD) 基于实际的新预付款金额减去根据上次消耗量计划的任何资金。 此项要求仅适用于产生的超额费用。 它只对 Azure 预付款服务有效，因此不适用于支付 Azure 市场费用。 Azure 市场费用单独计收。

若要完成超额抵销，你或帐户团队可以提出支持请求。 需要经过企业管理员或“接收帐单的联系人”的电子邮件批准。

## <a name="move-charges-to-another-enrollment"></a>将费用转移到另一个注册

只有在将转移操作的日期调后时，才会移动使用情况数据。 可通过两个选项将使用情况数据从一个注册移到另一个注册：

- 将帐户从一个注册转移到另一个注册
- 将注册从一个注册转移到另一个注册

使用任一选项都必须向 EA 支持团队提交[支持请求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)以获得帮助。 

## <a name="enterprise-agreement-usage-calculations"></a>企业协议用量计算

请参阅 [Azure 服务](https://azure.microsoft.com/services/)和 [Azure 定价](https://azure.microsoft.com/pricing/)了解各项服务的基本公开定价信息、度量单位、常见问题解答和使用情况报表指导。 可在以下部分找到有关 EA 计算的详细信息。

### <a name="enterprise-agreement-units-of-measure"></a>企业协议度量单位

企业协议的度量单位通常不同于其他计划，例如 Microsoft Online Services 协议计划 (MOSA)。 这种区分意味着，对于许多服务而言，合计度量单位是为了提供规范化的定价。 Azure Enterprise 门户的“使用情况摘要”视图中显示的度量单位始终为 Enterprise 度量。 通过提交[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，可提供每项服务的当前度量单位和换算的完整列表。

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>使用情况详细信息报表与使用情况摘要页之间的转换

在“下载使用情况数据”报表中可以看到，原始资源用量数据最多取六位小数。 但是，Azure Enterprise 门户中显示的使用情况数据舍入预付款单位的四位小数，并将超额单位用量截断为零位小数。 在转换为 Azure 门户中使用的单位之前，原始用量数据首先舍入为四位数。 然后，转换后的 Enterprise 单位也舍入为四位数。 只能在“下载使用情况”报表中看到转换之前实际使用的时数，而在 Azure Enterprise 门户中则看不到。

例如：如果使用情况报表中报告的实际 SQL Server 时数为 694.533404。 这些单位被转换为 6.94533404 个单位的计算时间（每个单位 100 小时），然后舍入为 6.9453 并显示在 Azure Enterprise 门户中。

- 若要确定扩展计费量，所显示的单位将乘以预付款单价，结果将被截断为两位小数。 对日元 (JPY) 和韩元 (KRW)，则将该总额舍入为零位小数（即整数）。
- 对于超额，计费单位将截断为六位数，然后乘以超额单价，得出计费总额。
- 对于托管服务提供商 (MSP) 帐单，在转换为 EA 度量单位后，将与标记为 MSP 的部门相关的所有用量取整（零位小数）。 因此，此用量的总和可能低于 Azure Enterprise 门户中报告的所有用量的总和， 这取决于 MSP 是在 Azure 预付款余额内还是已经超额。

### <a name="graduated-pricing"></a>分级定价

企业协议定价目前不包括分级定价（定价随用量的增加而降低）。 从具有分级定价的 MOSA 计划转移到企业协议时，根据企业协议折扣调整后，定价与服务基本层相当。

### <a name="partial-hour-billing"></a>不足一小时的计费

所有计费用量基于转换为部分小时数的分钟数（而不是按整小时的增量）。 按小时列出的 Enterprise 费率应用于总小时数加上部分小时数。

### <a name="average-daily-consumption"></a>平均日使用量

某些服务按月定价，但用量按日报告。 在这种情况下，将每日计算用量，将其除以 31，再根据该计费月内的天数求和。 因此，对于不足 31 日的月份，费率始终不会高于（而是略低于）任何月份的预期费率。

### <a name="compute-hours-conversion"></a>计算小时数的转换

在 2016 年 1 月 28 日之前，A0、A2、A3 与 A4 标准和基本虚拟机及云服务的用量以 A1 虚拟机计量分钟数发出。 A0 VM 统计为 A1 VM 分钟数的分数，而 A2、A3 和 A4 VM 则转换为倍数。 由于此策略给我们的客户造成了某些困扰，因此我们实行了更改，将每分钟用量分配给专用的 A0、A2、A3 和 A4 计量器。

新计量方式于 2016 年 1 月 27 日到 1 月 28 日之间生效。 在过渡期间，在显示用量的 CSV 下载内容中，可以看到以下两项：

- 在 A1 计量器上发出的用量
- 在与部署大小对应的新专用计量器上发出的用量。

| **部署大小** | **2016 年 1 月 26 日当日，用量按 A1 的倍数发布** | **自 2016 年 1 月 27 日起，按专用计价器发布用量** |
| --- | --- | --- |
| A0 | 0.25 个 A1 小时 | 1 个 A0 小时 |
| A2 | 2 个 A1 小时 | 1 个 A2 小时 |
| A3 | 4 个 A1 小时 | 1 个 A3 小时 |
| A4 | 8 个 A1 小时 | 1 个 A4 小时 |

### <a name="regions"></a>区域

对于定价受区域影响的服务，请参阅下表来了解地理位置和区域之间的映射：

| 地域 | 数据传输区域 | CDN 区域 |
| --- | --- | --- |
| 区域 1 | 北欧 <br> 西欧 <br> 美国西部 <br> 美国东部 <br> 美国中北部 <br> 美国中南部 <br> 美国东部 2 <br> 美国中部 | 北美 <br> 欧洲 |
| 区域 2 | 亚太东部 <br> 亚太东南部 <br> 日本东部 <br> 日本西部 <br> 澳大利亚东部 <br> 澳大利亚东南部 | 亚太区 <br> 日本 <br> 拉丁美洲 <br> 中东/非洲 <br> 澳大利亚东部 <br> 澳大利亚东南部 |
| 区域 3 | 巴西南部 |   |

对于驻留在同一数据中心内的服务之间的数据传出不收取任何费用。 例如 Microsoft 365 和 Azure。

### <a name="azure-prepayment-and-unbilled-usage"></a>Azure 预付款和未开票使用情况

Azure 预付款是提前为 Azure 服务支付的金额。 使用服务时会消耗 Azure 预付款。 第一方 Azure 服务按 Azure 预付款计费。 但是，某些费用是单独计收的，而 Azure 市场服务不消耗 Azure 预付款金额。

### <a name="charges-billed-separately"></a>单独计费

某些从第三方来源提供的产品和服务不使用 Azure 预付款。 将这些项作为标准计费周期的超额发票的一部分单独计费。

我们已将所有 Azure 和 Azure 市场费用合并到一份与注册的计费周期相符的发票中。 合并发票不适用于澳大利亚、日本或新加坡的客户。

合并发票首先显示 Azure 用量，后接任何 Azure 市场费用。 澳大利亚、日本或新加坡的客户可在单独的发票上查看 Azure 市场费用。

如果在标准计费周期结束时没有超额的用量，则对单独计费的费用单独开票。 此过程适用于澳大利亚、日本和新加坡的客户。

请参阅以下单独计费的服务示例。 你可以通过提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)获取完整的单独计费服务列表。

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 注册用户
- Openlogic
- 远程访问权限 XenApp Essentials 已注册的用户
- Ubuntu Advantage
- Visual Studio Enterprise（每月）
- Visual Studio Enterprise（每年）
- Visual Studio Professional（每月）
- Visual Studio Professional（每年）

## <a name="what-to-expect-after-change-of-channel-partner"></a>更改渠道合作伙伴后会发生什么

如果在月中更改了渠道合作伙伴 (COCP)，则客户会收到两份发票，一份对应于上一个关联合作伙伴的使用情况，另一份对应于新合作伙伴的使用情况。

发票将在计费周期结束后的一个月发出。 如果按月计费，则会在 10 月为两个合作伙伴发出 9 月份的发票。 如果计费周期为一季或一年，则客户应收到一份针对上一个关联合作伙伴的使用情况的发票，以及其余针对新合作伙伴使用情况的发票，收到发票的时间取决于计费频率。

## <a name="next-steps"></a>后续步骤

- 有关发票和费用的信息，请参阅[了解 Azure 企业协议帐单](../understand/review-enterprise-agreement-bill.md)。
- 若要开始使用 Azure Enterprise 门户，请参阅 [Azure EA 门户入门](ea-portal-get-started.md)。
