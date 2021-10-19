---
title: 开始使用更新的 Azure 计费帐户
description: 开始使用更新的 Azure 计费帐户，了解新的计费和成本管理体验中的更改
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 8e58a31d37e4b6aece877e8a2d920e7abc91d8f8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710948"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>开始使用更新的 Azure 计费帐户

管理成本和发票是云体验的关键组成部分之一。 它可帮助你控制和了解云上的支出。 为了使你更轻松地管理成本和发票，Microsoft 正在更新 Azure 计费帐户，以加入增强的成本管理和计费功能。 本文介绍了对计费帐户的更新，并介绍了一些新功能。

> [!IMPORTANT]
> 当你收到 Microsoft 发出的电子邮件，通知你有关帐户更新的信息时，你的帐户将获得更新。 通知将在你的帐户更新前 60 天发送。

## <a name="more-flexibility-with-your-new-billing-account"></a>新计费帐户具有更大的灵活性

下图比较了新旧计费帐户：

:::image type="content" source="./media/mosp-new-customer-experience/comparison-old-new-account.png" alt-text="该图显示了新旧帐户中计费层次结构之间的比较。" border="false" lightbox="./media/mosp-new-customer-experience/comparison-old-new-account.png":::

新的计费帐户包含一个或多个计费对象信息，可用于管理发票和付款方式。 每个计费配置文件包含一个或多个发票科目，用于在计费配置文件的发票上组织成本。

:::image type="content" source="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png" alt-text="该图显示了新的计费层次结构。" border="false" lightbox="./media/mosp-new-customer-experience/new-billing-account-hierarchy.png":::

计费帐户上的角色具有最高级别的权限。 这些角色应分配给那些需要查看发票并跟踪整个帐户的成本的用户，例如组织中的财务或 IT 经理或注册帐户的个人。 有关详细信息，请参阅[计费帐户角色和任务](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)。 帐户更新后，在旧计费帐户中具有帐户管理员角色的用户将在新帐户中被赋予所有者角色。

## <a name="billing-profiles"></a>计费配置文件

计费配置文件用于管理发票和付款方式。 每月发票是在当月开始时针对帐户中的每项计费配置文件生成的。 发票包含上月与计费配置文件相关联的所有订阅的相应费用。

帐户更新后，将自动为每个订阅创建一个计费配置文件。 订阅费用将按其各自的计费配置文件计费，并显示在其发票上。

计费配置文件中的角色有权查看和管理发票和付款方式。 这些角色应分配给为发票付款的用户，例如组织中会计团队的成员。 有关详细信息，请参阅[计费配置文件角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

帐户更新后，对于你已授予其他人[查看发票](download-azure-invoice.md#allow-others-to-download-your-subscription-invoice)权限的每个订阅，具有所有者、参与者、读者或计费读者 Azure 角色的用户将在相应的计费对象信息中被赋予读者角色。

## <a name="invoice-sections"></a>发票科目

发票科目用于组织发票中的成本。 例如，你可能需要单张发票，但同时需要按部门、团队或项目组织成本。 对于这种情况，可以设置单项计费配置文件，在其中为每个部门、团队或项目创建发票科目。

帐户更新后，将为每个计费配置文件创建一个发票科目，并将相关订阅分配给该发票科目。 添加更多订阅后，可以创建更多的科目并将订阅分配给发票科目。 你将看到此计费配置文件发票上的这些科目反映了你分配给它们的每个订阅的使用情况。

发票科目的角色有权控制谁来创建 Azure 订阅。 这些角色应分配给在组织中为团队设置 Azure 环境的用户，例如工程主管和技术架构师。 有关详细信息，请参阅[发票科目角色和任务](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="enhanced-features-in-your-new-experience"></a>新体验中的增强功能

你的新体验包括以下成本管理和计费功能，这些功能使你可以轻松管理成本和发票：

#### <a name="invoice-management"></a>发票管理

**每月计费周期更容易预测** - 在新帐户中，无论你何时注册使用 Azure，计费周期都从该月的第一天开始，到该月的最后一天结束。 发票将在每月月初生成，其中包含上个月的所有费用。

**为多个订阅获取一张月度账单** - 在现有帐户中，可为每个 Azure 订阅获取一张账单。 更新帐户时，将保留现有行为，但你可以灵活地将订阅的费用合并到一张账单中。 更新帐户后，请按照以下步骤将费用合并到一张账单上：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 搜索“成本管理 + 计费”。  
   ![显示在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图。](./media/mosp-new-customer-experience/billing-search-cost-management-billing.png)
3. 在屏幕左侧选择“Azure 订阅”。 
4. 该表列出了你要支付其费用的 Azure 订阅。 在“计费对象信息”列中，可找到计收订阅费用的计费对象信息。 订阅费用显示在计费对象信息的账单上。 若要将所有订阅的费用合并一张账单上，需要将所有订阅关联到一个计费对象信息项中。  
    :::image type="content" source="./media/mosp-new-customer-experience/list-azure-subscriptions.png" alt-text="显示 Azure 订阅列表的屏幕截图。" lightbox="./media/mosp-new-customer-experience/list-azure-subscriptions.png" :::
5. 选择要使用的计费对象信息。 
6. 选择未关联到你在步骤 5 中选择的计费对象信息的订阅。 单击该订阅对应的省略号（三个点）。 选择“更改发票科目”。  
    :::image type="content" source="./media/mosp-new-customer-experience/select-change-invoice-section.png" alt-text="屏幕截图显示了在何处可以找到更改账单科目的选项。" lightbox="./media/mosp-new-customer-experience/select-change-invoice-section-zoomed-in.png" :::
7. 选择你在步骤 #5 中选择的计费对象信息。  
    :::image type="content" source="./media/mosp-new-customer-experience/change-invoice-section.png" alt-text="屏幕截图显示了如何更改账单科目。" lightbox="./media/mosp-new-customer-experience/change-invoice-section-zoomed-in.png" :::
8. 选择“更改”。
9. 对于所有其他订阅，重复步骤 6-8。 

**收到包含 Azure 订阅、支持计划和 Azure 市场产品的单张月度发票** - 你将获得一张包含所有费用（包括 Azure 订阅的使用费用、支持计划费用和 Azure 市场购买费用）的月度发票。

**根据你的需求对发票上的成本进行分组** - 你可以根据需求（按部门、项目或团队）对发票上的费用进行分组。

**在发票上设置可选的采购订单号** - 为了将发票与内部财务系统相关联，请设置采购订单号。 在 Azure 门户中可以随时进行管理和更新。

#### <a name="payment-management"></a>付款管理

**使用信用卡立即支付发票** - 无需等待从你的信用卡中自动扣除费用。 你可以在 Azure 门户中使用任意信用卡支付到期发票或逾期未付发票。

**跟踪应用于该帐户的所有付款** - 在 Azure 门户中可以查看应用于你的帐户的所有付款，包括使用的付款方式、已付款的金额和付款日期。

#### <a name="cost-management"></a>成本管理

**计划每月将使用情况数据导出到存储帐户** - 每天、每周或每月将你的成本和使用情况数据自动发布到存储帐户一次。

#### <a name="account-and-subscription-management"></a>帐户和订阅管理

**分配多个管理员执行计费操作** - 将计费权限分配给多个用户以管理帐户的帐单。 通过向其他人提供读取和/或写入权限来获得灵活性。

**在 Azure 门户中直接创建更多订阅** - 只需在 Azure 门户中单次选择即可创建所有订阅。

#### <a name="api-support"></a>API 支持

**通过 API、SDK 和 PowerShell 执行计费和成本管理操作** - 使用成本管理、计费和消耗 API 将计费和成本数据提取到你首选的数据分析工具中。

**通过 API、SDK 和 PowerShell 执行所有订阅操作** - 使用 Azure 订阅 API 来自动管理 Azure 订阅，包括创建、重命名和取消订阅。

## <a name="get-prepared-for-your-new-experience"></a>为新体验做好准备

为使你能够为新体验做好准备，我们提出了以下建议：

**月度计费周期和不同的发票日期**

在新体验中，发票将在每个月的第 9 天左右生成，其中包含上个月的所有费用。 此日期可能与在旧帐户中生成发票的日期不同。 如果你与其他人共享你的发票，请通知他们有关此日期更改的事宜。


**迁移后首月的账单**

在你更新帐户的那一天，将最终确定现有的未结费用，你将在平时收到账单的那一天收到这些费用的账单。 例如，John 有两个 Azure 订阅：Azure 订阅 1 的计费周期是从当月 5 日到下月 4 日，Azure 订阅 2 的计费周期是从当月 10 日到下月 9 日。 John 一般在每月 5 日收到这两个 Azure 订阅的账单。 现在，如果 John 的帐户在 4 月 4 日进行了更新，则 Azure 帐户 1 的费用将为 3 月 5 日至 4 月 4 日的费用，Azure 订阅 2 的费用将为 3 月 10 日到 4 月 4 日的费用。 John 将在每月 5 日收到两张账单，每个订阅各一张。 帐户更新后，John 的计费周期将基于日历月，并将包含从日历月开始到该日历月结束时产生的所有费用。 每月头几日可查看有关上一个日历月的费用账单。 因此，在上例中，John 将在 5 月初收到另一张帐单，计费周期为 4 月 5 日至 4 月 30 日。


**新的计费和成本管理 API**

如果你正使用成本管理或计费 API 来查询和更新计费或成本数据，则必须使用新 API。 下表列出了不能用于新计费帐户的 API，以及你需要在新计费帐户中进行的更改。

|API | 更改  |
|---------|---------|
|[计费帐户 - 列表](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | 在计费帐户 - 列表 API 中，你的旧计费帐户具有 agreementType“MicrosoftOnlineServiceProgram”，而你的新计费帐户具有 agreementType“MicrosoftCustomerAgreement” 。 如果你依赖于 agreementType，请进行更新。 |
|[发票 - 按计费订阅列出](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | 此 API 将仅返回在更新帐户之前生成的发票。 必须使用[发票 - 按计费订阅列出](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) API 才能获取在新计费帐户中生成的发票。 |


## <a name="cost-management-updates-after-account-update"></a>帐户更新后的成本管理更新

更新后的 Microsoft 客户协议 Azure 计费帐户可以让你在 Azure 门户中访问新的和扩展的成本管理经验，这是你的即用即付帐户所没有的。

### <a name="new-capabilities"></a>新功能

Azure 计费帐户提供以下新功能。

#### <a name="new-billing-scopes"></a>新的计费范围

作为已更新帐户的一部分，你在成本管理 + 计费方面有了新的范围。 除了有助于分层组织和开票，它们还是查看多个基础订阅的组合费用的一种方式。 有关计费范围的详细信息，请参阅 [Microsoft 客户协议范围](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes)。

你还可以访问成本管理 API 以获得更高范围的组合成本视图。 所有使用订阅范围的成本管理 API 仍然可用，只是在架构中进行了一些细微更改。 有关 API 的详细信息，请参阅[成本管理 API](/rest/api/cost-management/) 和 [Azure 消耗 API](/rest/api/consumption/)。

#### <a name="cost-allocation"></a>成本分配

使用更新的帐户，你可以使用成本分配功能来分摊组织中共享服务的成本。 有关分配成本的详细信息，请参阅[创建和管理 Azure 成本分配规则](../costs/allocate-costs.md)。

#### <a name="power-bi"></a>Power BI

适用于 Power BI Desktop 的成本管理连接器可帮助你构建有关 Azure 使用情况和支出的自定义可视化效果和报表。 连接到更新的帐户后，你可以访问成本和使用情况数据。 有关适用于 Power BI Desktop 的成本管理连接器的详细信息，请参阅[在 Power BI Desktop 中使用成本管理连接器创建视觉对象和报表](/power-bi/connect-data/desktop-connect-azure-cost-management)。

### <a name="updated-capabilities"></a>更新的功能

Azure 计费帐户提供以下更新的功能。

#### <a name="cost-analysis"></a>成本分析

你可以继续查看和跟踪每月的消耗成本，现在你可以在成本分析中查看预留和市场购买成本。

使用更新的帐户，你将收到适用于所有 Azure 费用的单个发票。 现在，你还拥有一个简化的单个月历视图，可以替换以前的“计费周期”视图。

例如，如果你的旧帐户的计费周期为 11 月 24 日至 12 月 23 日，则升级后的周期将变为 11 月 1 日至 11 月 30 日、12 月 1 日至 12 月 31 日，依此类推。

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="屏幕截图显示了新旧计费周期之间的比较。" lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>预算

现在，你可以为计费帐户创建预算，从而可以跨订阅跟踪成本。 也可使用预算来控制购买费用。 有关预算的详细信息，请参阅[创建和管理 Azure 预算](../costs/tutorial-acm-create-budgets.md)。

#### <a name="exports"></a>导出

新的计费帐户提供了改进的导出功能。 例如，可以为包括购买或摊销成本（整个购买周期的预留购买成本）的实际成本创建导出。 还可以为计费帐户创建导出，以获取计费帐户中所有订阅的使用情况和费用数据。 若要了解有关导出的详细信息，请参阅[创建和管理导出的数据](../costs/tutorial-export-acm-data.md)。

> [!NOTE]
> 使用“每月导出上月成本”类型更新帐户之前创建导出，将导出上一个日历月的数据，而不是上一个计费周期的数据。

例如，对于从 12 月 23 日到 1 月 22 日的计费周期，导出的 CSV 文件将包含该周期的成本和使用情况数据。 更新后，导出将包含该日历月的数据。 例如，1 月 1 日至 1 月 31 日，以此类推。

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="屏幕截图显示了新旧导出详细信息之间的比较。" lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>其他信息

以下部分提供有关新体验的其他信息。

**无服务故障时间** 订阅中的 Azure 服务将保持正常运行，而不会中断。 仅对你的计费体验进行更新。 这不会对现有的资源、资源组或管理组产生影响。

**不会更改 Azure 资源** 对使用 Azure 基于角色的访问控制 (Azure RBAC) 设置的 Azure 资源的访问权限不受此更新的影响。

**过去的发票在新体验中可用** 更新帐户前生成的发票在 Azure 门户中仍然可用。

**月中更新帐户的发票** 如果你的帐户在月中更新，则你将收到一张显示累计到帐户更新当天为止的费用的发票。 你将收到另一张发票，其中包含该月的剩余时间内产生的费用。 例如，你的帐户有一个订阅，该订阅在 9 月 15 日更新。 你将收到一张显示累计到 9 月 15 日为止的费用的发票。 你将在 9 月 15 日到 9 月 30 日之间收到另一张发票。 9 月份以后，你每月均会收到一张发票。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关计费帐户的详细信息。

- [了解新计费帐户的管理角色](../manage/understand-mca-roles.md)
- [为新计费帐户创建其他 Azure 订阅](../manage/create-subscription.md)
- [在发票上创建科目以管理成本](../manage/mca-section-invoice.md)