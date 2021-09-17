---
title: 使用 Power BI 应用分析 Azure 成本
description: 本文介绍如何安装和使用 Azure 成本管理 Power BI 应用。
author: bandersmsft
ms.author: banders
ms.date: 08/19/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: b500bd2b97c262739902c5e1b8af51b013ad4ecb
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515061"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>使用适用于企业协议 (EA) 的 Azure 成本管理 Power BI 应用分析成本

本文介绍如何安装和使用 Azure 成本管理 Power BI 应用。 该应用可帮助你在 Power BI 中分析和管理 Azure 成本。 可以使用该应用来监视成本和使用趋势，并确定可用于减少支出的成本优化选项。

Azure 成本管理 Power BI 应用目前仅支持已签署[企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)的客户。

该应用在可自定义方面存在限制。 如果要修改和扩展默认的筛选器、视图和可视化效果来根据需要进行自定义，请改用 [Power BI Desktop 中的 Azure 成本管理连接器](/power-bi/connect-data/desktop-connect-azure-cost-management)。 借助 Azure 成本管理连接器，可联接来自其他源的其他数据以创建自定义报表，从而全面了解整体业务成本。 该连接器还支持 Microsoft 客户协议。

> [!NOTE]
> Power BI 模板应用不支持下载 PBIX 文件。

## <a name="prerequisites"></a>先决条件

- 需要一个 [Power BI Pro 许可证](/power-bi/service-self-service-signup-for-power-bi)才能安装和使用该应用。
- 若要连接数据，必须使用[企业管理员](../manage/understand-ea-roles.md)帐户。 支持企业管理员（只读）角色。

## <a name="installation-steps"></a>安装步骤

若要安装应用：

1. 打开 [Azure 成本管理 Power BI 应用](https://aka.ms/costmgmt/ACMApp)。
1. 在 Power BI 的“AppSource”页上，选择“立即获取”。
1. 选择“继续”以同意使用条款和隐私策略。
1. 在“安装此 Power BI 应用”框中，选择“安装”。
1. 如果需要，请创建一个工作区并选择“继续”。
1. 安装完成后会显示通知，指出新应用已准备就绪。
1. 选择安装的应用。
1. 在“开始”页上，选择“连接数据”。
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" alt-text="突出显示“连接数据”链接的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" :::
1. 在出现的对话框中，为“BillingProfileIdOrEnrollmentNumber”输入 EA 注册编号。 指定要获取的数据的月数。 保留“注册编号”的默认“范围”值，然后选择“下一步”。  
    >[!NOTE]
    > 范围的默认值为 `Enrollment Number`。 请勿更改此值，否则初始数据连接将失败。  

    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" alt-text="显示 EA 注册信息输入位置的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" :::
1. 下一个安装步骤是连接到 EA 注册，并要求使用[企业管理员](../manage/understand-ea-roles.md)帐户。 保留所有默认值。 选择“登录并继续”。  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" alt-text="显示“连接到 Azure 成本管理应用”对话框以及用于连接的默认值的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" :::
1. 最后一个对话框将连接到 Azure 并获取数据。 保留配置的默认值，然后选择“登录并继续”。  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" alt-text="显示“连接到 Azure 成本管理应用”对话框及其默认值的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" :::
1. 系统会提示你使用EA 注册进行身份验证。 请使用 Power BI 进行身份验证。 完成身份验证后，将开始刷新 Power BI 数据。
    > [!NOTE]
    > 数据刷新过程可能需要相当长的时间才能完成。 具体的时长取决于指定的月数以及需要同步的数据量。

完成数据刷新后，选择 Azure 成本管理应用来查看预先创建的报表。

## <a name="reports-available-with-the-app"></a>应用中提供的报表

应用中提供以下报表：

**开始使用** - 提供文档的有用链接，以及用于提交反馈的链接。

**帐户概述** - 该报表显示信息的每月摘要，包括：

- 使用额度支付的费用
- 新购买
- Azure 市场费用
- 超额和总费用

**订阅和资源组的用量** - 提供一段时间内的成本视图和图表，按订阅和资源组显示成本。

**服务的用量** - 按计量类别提供一段时间内的用量视图。 可以跟踪用量数据并深入到任何异常，以了解使用高峰或低谷。

**前 5 个用量促成因素** - 该报表按前 5 个计量类别和相应的计量器名称显示筛选的汇总成本。

**Windows Server AHB 用量** - 该报表显示已启用 Azure 混合权益的虚拟机数目。 它还显示虚拟机使用的核心/vCPU 计数。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" alt-text="显示完整 Azure 混合权益报表的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" :::

该报表还会列出 **已启用** 混合权益，但使用的 vCPU 数少于 8 个的 Windows VM。 它还显示哪些 VM **未启用** 混合权益，且使用的 vCPU 数至少有 8 个。 此信息可帮助你充分利用混合权益。 将权益应用到开销最高的虚拟机能够最大程度地实现潜在成本节省。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" alt-text="显示 Azure 混合权益报表中“少于 8 个 vCPU 和未启用 vCPU”区域的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" :::

**RI 费用分摊** - 该报表帮助你了解在每个区域、订阅、资源组或资源的哪个位置应用预留实例 (RI) 权益以及应用的权益数量。 该报表使用分摊用量数据来显示视图。

可对“费用类型”应用筛选器，以查看 RI 利用不足数据。

有关分摊数据的详细信息，请参阅[获取企业协议预留成本和使用情况](../reservations/understand-reserved-instance-usage-ea.md)。

**RI 节省** - 该报表显示订阅、资源组和资源级别的预留项的累积节省额。 其中显示：

- 预留成本
- 预留不适用于用量时的估算按需成本
- 预留累积产生的成本节省

 该报表从节省总额中减去所有利用不足的预留浪费成本。 如果没有预留，则不存在浪费。

可以使用分摊用量数据作为基础。

<a name="shared-recommendation"></a>
**VM RI 覆盖范围(共享建议)** - 该报表在所选时间段内的按需 VM 用量与 RI VM 用量之间拆分。 它提供有关在共享范围购买的 VM RI 的建议。

若要使用该报表，请选择向下钻取筛选器。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" alt-text="显示“VM RI 覆盖范围”报表中选择向下钻取选项的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" :::

选择要分析的区域。 然后选择实例大小灵活性组，等等。

对于每个向下钻取级别，以下筛选器将应用到报表：

- 右侧的覆盖范围数据是筛选器，显示使用按需费率收费的用量，以及预留所涵盖的用量。
- 此外会筛选建议。

建议表根据所用的 VM 大小提供预留项购买建议。

“规范化大小”和“建议的规范化数量”值可帮助你根据实例大小灵活性组的最小大小规范化购买。 如果你打算只为实例大小灵活性组中的所有大小购买一个预留项，此信息将很有帮助。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" alt-text="显示 RI 建议报表的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" :::

**VM RI 覆盖范围(单个建议)** - 该报表在所选时间段内的按需 VM 用量与 RI VM 用量之间拆分。 它提供有关在订阅范围购买的 VM RI 的建议。

有关如何使用该报表的详细信息，请参阅 [VM RI 覆盖范围(共享建议)](#shared-recommendation) 部分。

**RI 购买内容** - 该报表显示指定时间段内的 RI 购买内容。

**价目表** - 该报表显示特定于计费帐户或 EA 注册的详细价格列表。

## <a name="troubleshoot-problems"></a>排查问题

如果 Power BI 应用出现问题，则可参阅以下故障排除信息。

### <a name="error-processing-the-data-in-the-dataset"></a>处理数据集中的数据时出错

可能会出现一个错误，指出：

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

将显示一个表名称，而不是 `<TableName>`。

#### <a name="cause"></a>原因

与成本管理连接时，默认的“范围”值 `Enrollment Number` 已更改。

#### <a name="solution"></a>解决方案

重新连接到成本管理，并将“范围”值设置为 `Enrollment Number`。 请勿输入组织的注册编号，而应键入与下图所示完全相同的 `Enrollment Number`。

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" alt-text="显示“注册编号不可更改”默认文本的屏幕截图。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" :::

### <a name="budgetamount-error"></a>BudgetAmount 错误

可能会出现一个错误，指出：

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>原因

发生此错误的原因是基础元数据出现 bug。 发生此问题的原因是，在 Azure 门户中的“成本管理”>“预算”下没有提供预算。 Bug 修复正在部署到 Power BI Desktop 和 Power BI 服务。

#### <a name="solution"></a>解决方案

- 在修复 bug 之前，可以在计费帐户/EA 注册级别的 Azure 门户中添加测试性预算来解决问题。 测试性预算会取消阻止通过 Power BI 进行的连接。 若要详细了解如何创建预算，请参阅[教程：创建并管理 Azure 预算](tutorial-acm-create-budgets.md)。

### <a name="invalid-credentials-for-azureblob-error"></a>“AzureBlob 凭据无效”错误

可能会出现一个错误，指出：

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>原因

如果更改数据源连接的身份验证方法，则会发生此错误。

#### <a name="solution"></a>解决方案

1. 连接到数据。
1. 输入 EA 注册和月数后，请务必保留“匿名”作为“身份验证方法”的默认值，并保留“无”作为隐私级别设置。  
  :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" alt-text="屏幕截图显示了已输入“匿名”和“无”这两个值的“连接到 Azure 成本管理应用”对话框。" lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" :::
1. 在下一页上，设置 **OAuth2** 作为“身份验证方法”，设置“无”作为“隐私级别”。 然后登录，以便对注册进行身份验证。 此步骤还启动 Power BI 数据刷新。

## <a name="data-reference"></a>数据参考

以下信息汇总了通过应用提供的数据。 此外，还提供了 API 的链接，这些链接深入介绍了数据字段和值。

| **表参考** | **说明** |
| --- | --- |
| **AutoFitComboMeter** | 应用中包含的数据，用于根据实例系列组中的最小大小规范化 RI 建议和用量。 |
| [**余额摘要**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | 企业协议余额的摘要。 |
| [**预算**](/rest/api/consumption/budgets/get#definitions) | 预算详细信息，用于查看实际成本或用量，并将其与现有预算目标进行比较。 |
| [**价目表**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | 所提供计费配置文件或 EA 注册的适用计量费率。 |
| [**RI 费用**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | 与过去 24 个月的预留实例相关的费用。 |
| [**RI 建议(共享)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | 根据过去 7 天的所有订阅使用趋势提供的预留实例购买建议。 |
| [**RI 建议(单个)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | 根据过去 7 天的单个订阅使用趋势提供的预留实例购买建议。 |
| [**RI 使用情况详细信息**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | 现有预留实例在过去一个月的消耗详细信息。 |
| [**RI 使用情况摘要**](/rest/api/consumption/reservationssummaries/list) | 每日 Azure 预留使用百分比。 |
| [**使用情况详细信息**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 注册中给定计费配置文件的已用数量和估算费用的细目。 |
| [**分摊使用情况详细信息**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 注册中给定计费配置文件的已用数量和估算的分摊费用的细目。 |

## <a name="next-steps"></a>后续步骤

有关配置数据、刷新、共享报表和其他报表自定义操作的详细信息，请参阅以下文章：

- [配置计划的刷新](/power-bi/refresh-scheduled-refresh)
- [与同事和他人共享 Power BI 仪表板和报表](/power-bi/service-share-dashboards)
- [在 Power BI 服务中为自己和他人订阅报表与仪表板](/power-bi/service-report-subscribe)
- [将报表从 Power BI 服务下载到 Power BI Desktop](/power-bi/service-export-to-pbix)
- [在 Power BI 服务和 Power BI Desktop 中保存报表](/power-bi/service-report-save)
- [通过导入数据集在 Power BI 服务中创建报表](/power-bi/service-report-create-new)