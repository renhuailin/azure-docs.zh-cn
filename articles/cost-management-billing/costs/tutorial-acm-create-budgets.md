---
title: 教程 - 创建和管理 Azure 预算
description: 本教程介绍如何对所使用的 Azure 服务进行成本计划和核算。
author: bandersmsft
ms.author: banders
ms.date: 06/17/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1531b6bf591d2fb859dbd680c41d51a5835347a1
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112320723"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教程：创建和管理 Azure 预算

可以通过成本管理中的预算来计划并推动组织责任制。 可以通过预算将他人的支出通知给本人，方便他们对成本进行前摄性管理，并且可以监视一段时间的支出情况。 你可以根据实际成本或预测成本来配置警报，以确保你的支出在组织支出限制内。 超出所创建的预算阈值时，只会触发通知， 不会影响资源，也不会停止你对资源的使用。 可以使用预算来比较和跟踪支出，就像分析成本一样。

成本和使用数据通常在 8-24 小时内可用，每 24 小时对照这些成本评估一次预算。 请务必熟悉[成本和使用情况数据更新](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention)的具体细节。 满足预算限额时，通常会在一小时评估范围内发送电子邮件通知。

在未来选择过期日期时，预算会在某个期间末（月末、季末或年末）自动重置为相同的预算金额。 由于预算是使用相同的预算金额重置的，因此如果未来时段的预算货币金额不同于现在，则需创建单独的预算。 预算到期后，会被自动删除。

本教程中的示例演示了如何针对 Azure 企业协议 (EA) 订阅创建和编辑预算。

请观看[通过 Azure 门户将预算应用于订阅](https://www.youtube.com/watch?v=UrkHiUx19Po)视频，了解如何在 Azure 中创建预算来监视支出。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 通过 PowerShell 创建和编辑预算
> * 使用 Azure 资源管理器模板创建预算

## <a name="prerequisites"></a>先决条件

以下 Azure 帐户类型和范围都支持预算：

- Azure 基于角色的访问控制 (Azure RBAC) 范围
    - 管理组
    - 订阅
- 企业协议范围
    - 计费帐户
    - 部门
    - 注册帐户
- 个人协议
    - 计费帐户
- Microsoft 客户协议范围
    - 计费帐户
    - 计费配置文件
    - 发票科目
    - 客户
- AWS 范围
    - 外部帐户
    - 外部订阅

若要查看预算，你至少需要对 Azure 帐户具有读取访问权限。

如果你有新订阅，则无法立即创建预算或使用其他成本管理功能。 最多可能需要 48 小时才能使用所有成本管理功能。

对于 Azure EA 订阅，必须拥有读取访问权限才能查看预算。 若要创建和管理预算，必须具有参与者权限。

每个订阅支持以下 Azure 权限或范围，以便按用户和组进行预算。

- 所有者 - 可以为订阅创建、修改或删除预算。
- 参与者和成本管理参与者 - 可以创建、修改或删除自己的预算。 可以修改其他人创建的预算的预算金额。
- 读者和成本管理读者 - 可以查看他们有权访问的预算。

**有关范围的详细信息，包括为企业协议和 Microsoft 客户协议范围配置导出所需的访问权限，请参阅 [了解和使用范围](understand-work-scopes.md)** 。 若要详细了解如何分配对成本管理数据的权限，请参阅[分配对成本管理数据的访问权限](./assign-access-acm-data.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

- 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 门户中创建预算

可以按月、按季或按年创建 Azure 订阅预算。

若要创建或查看预算，请在 Azure 门户中打开范围，然后在菜单中选择“预算”。 例如，导航到“订阅”，从列表中选择订阅，然后在菜单中选择“预算” 。 在“预算”中，请使用“范围”框切换到另一范围，如某个管理组。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

创建预算后，会显示一个简单的视图，其中说明了当前支出与预算的对比情况。

选择 **添加** 。

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="显示已创建的预算列表的屏幕截图。" lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

在“创建预算”窗口中，确保显示的范围正确无误。 选择想要添加的任何筛选器。 使用筛选器可以创建特定成本的预算，例如订阅中的资源组或虚拟机之类的服务。 可以在成本分析中使用的任何筛选器也可以应用于预算。

确定范围和筛选器后，请键入预算名称。 然后，选择月度、季度或年度预算重置周期。 重置周期决定了预算分析的时间窗口。 在每个新期限开始时，按预算估算的成本从零开始。 创建季度预算时，其操作方式与每月预算相同。 不同之处在于，季度预算的预算金额是在该季度的三个月中平均分配的。 年度预算金额在日历年的所有 12 个月中平均分配。

如果有即用即付、MSDN 或 Visual Studio 订阅，则发票计费周期可能与日历月不一致。 对于这些订阅类型和资源组，可以创建与发票周期或日历月一致的预算。 若要创建与发票周期相符的预算，请选择“计费月”、“计费季”或“计费年”作为重置期限。 若要创建与日历月相符的预算，请选择“每月”、“每季”或“每年”作为重置期限。

接下来，确定预算失效并停止成本估算的到期日期。

系统会显示一个图表，方便你选择用于预算的阈值，具体取决于目前在预算中选择的字段。 建议的预算基于你将来可能会产生的最高预测成本。 你可以更改预算金额。

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="显示通过每月成本数据创建预算的屏幕截图。" lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

配置预算金额后，选择“下一步”以配置实际成本和预测预算警报的预算警报。

## <a name="configure-actual-costs-budget-alerts"></a>配置实际成本预算警报

预算需要至少一个成本阈值（预算百分比）和相应的电子邮件地址。 可以选择在单个预算中包括多达五个阈值和五个电子邮件地址。 满足预算限额时，通常会在一小时评估范围内发送电子邮件通知。 实际成本预算警报是根据与配置的预算限额相关的实际累计成本所生成。

## <a name="configure-forecasted-budget-alerts"></a>配置预测的预算警报

预测警报会预先通知支出趋势可能超出预算。 这些警报使用[预测的成本预测](quick-acm-cost-analysis.md#understand-forecast)。 预测的成本预测超出设定限额时，将生成警报。 你可以配置预测的限额（预算的 %）。 达到预测的预算限额时，通常在评估后一小时内发送通知。

若要在配置实际与预测成本警报之间进行切换，请在配置警报时使用 `Type` 字段，如下图所示。

如果要接收电子邮件，请将 azure-noreply@microsoft.com 添加到已批准的发件人列表，使电子邮件不会被发送到垃圾电子邮件文件夹。 有关通知的详细信息，请参阅[使用成本警报](./cost-mgt-alerts-monitor-usage-spending.md)。

在下面的示例中，达到预算的 90% 时，会生成电子邮件警报。 如果使用预算 API 创建预算，也可将角色分配给人员来接收警报。 不支持在 Azure 门户中向人员分配角色。 有关 Azure 预算 API 的详细信息，请参阅[预算 API](/rest/api/consumption/budgets)。 如果希望以其他语言发送电子邮件警报，请参阅[预算警报电子邮件支持的区域设置](manage-automation.md#supported-locales-for-budget-alert-emails)。

警报限制支持所提供的预算限额的 0.01% 到 1000% 范围。

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="显示警报条件的屏幕截图。" lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

创建预算后，它会显示在成本分析中。 开始[分析成本和支出](./quick-acm-cost-analysis.md)时，首先要执行的步骤之一是查看预算与对应的支出趋势。

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="显示示例预算的屏幕截图，其中支出显示在成本分析中。" lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

在上面的示例中，你为订阅创建了一个预算。 还可以为资源组创建预算。 如需为资源组创建预算，请导航到“成本管理 + 计费”&gt;“订阅”，选择一个订阅，然后选择“资源组”，接着选择一个资源组，单击“预算”，然后单击“添加”以添加预算。

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>为合并的 Azure 和 AWS 成本创建预算

可以通过将管理组分配给连接器及其合并和链接帐户，将 Azure 和 AWS 成本组合在一起。 将 Azure 订阅分配到相同的管理组。 然后为合并成本创建预算。

1. 在“成本管理”中选择“预算”。
1. 选择 **添加** 。
1. 选择“更改范围”，然后选择相应管理组。
1. 继续创建预算，直到完成。

## <a name="costs-in-budget-evaluations"></a>预算估算中的成本

预算成本估算现在包括预留实例和购买数据。 如果费用适用于你，则当费用合并到估算中时，你可能会收到警报。 若考虑到新的成本，可登录到 [Azure 门户](https://portal.azure.com)以验证是否正确配置了预算限额。 你的 Azure 计费费用不变。 现在，预算会针对一组更完整的成本进行评估。 如果费用不适用于你，则你的预算行为保持不变。

如果要筛选新成本，以便仅针对第一方 Azure 消耗费用来估算预算，请将以下筛选器添加到预算：

- 发布服务器类型：Azure
- 费用类型：使用情况

预算成本估算基于实际成本。 它们不包括摊销。 有关预算中可用的筛选选项的详细信息，请参阅[了解分组和筛选选项](group-filter.md)。

## <a name="trigger-an-action-group"></a>触发操作组

在为订阅或资源组范围创建或编辑预算时，可以将其配置为调用操作组。 达到预算阈值时，操作组可以执行各种不同的操作。 

目前只支持将操作组用于订阅和资源组范围。 有关创建操作组的详细信息，请参阅[配置基本操作组设置](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings)。 

若要详细了解如何将基于预算的自动化与操作组配合使用，请参阅[使用 Azure 预算管理成本](../manage/cost-management-budget-scenario.md)。

若要创建或更新操作组，请在创建或编辑预算时选择“管理操作组”。

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="显示了创建预算以显示“管理操作组”的示例的屏幕截图。" lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

接下来，选择“添加操作组”并创建操作组。

预算与操作组的集成仅适用于禁用常见警报架构的操作组。 若要详细了解如何禁用该架构，请参阅[如何启用常见警报架构？](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>通过 PowerShell 创建和编辑预算

如果你是 EA 客户，则可以使用 Azure PowerShell 模块以编程方式创建和编辑预算。 

>[!Note]
>使用 Microsoft 客户协议的客户应使用[预算 REST API](/rest/api/consumption/budgets/create-or-update) 以编程方式创建预算，因为尚不支持 PowerShell 和 CLI。

若要下载最新版本的 Azure PowerShell，请运行以下命令：

```azurepowershell-interactive
install-module -name Az
```

下面的示例命令将创建一个预算。

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

Get-AzContext
New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建预算

可以使用 Azure 资源管理器模板创建预算。 若要使用模板，请参阅[使用 Azure 资源管理器模板创建预算](quick-create-budget-template.md)。

## <a name="clean-up-resources"></a>清理资源

如果创建了预算而不再需要它，请查看其详细信息并将其删除。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 通过 PowerShell 创建和编辑预算
> * 使用 Azure 资源管理器模板创建预算

请继续学习下一教程，了解如何针对成本管理数据创建定期导出。

> [!div class="nextstepaction"]
> [创建和管理导出的数据](tutorial-export-acm-data.md)
