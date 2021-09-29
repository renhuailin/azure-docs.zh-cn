---
title: 查看月评估实验室成本趋势
description: 本文介绍了如何在 Azure 开发测试实验室中跟踪实验室的成本(月评估成本趋势图表)。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2b202479fd7f3b42cb6f4f2dd284ef8981705790
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645030"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>跟踪与 Azure 开发测试实验室中的实验室相关的成本
本文介绍如何跟踪实验室成本。 其中介绍了如何查看实验室当前日历月的评估成本趋势。 本文还介绍了如何查看实验室中每个资源的本月至今累计成本。

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>查看月评估实验室成本趋势 
在本部分中，将了解如何使用“月评估成本趋势”图表，查看当前日历月中截止目前的成本以及当前日历月截止月底的预估成本。 还可以了解如何通过设置支出目标和阈值管理实验室成本，当达到阈值时会触发开发测试实验室向你报告结果。

若要查看月评估成本趋势图表，请按以下步骤操作： 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，并从列表中选择“开发测试实验室”。
3. 在实验室列表中，选择实验室。  
4. 在左侧菜单中选择“配置和策略”。  
4. 在左侧菜单的“成本跟踪”部分中选择“成本趋势”。  以下屏幕截图为成本图表示例。 
   
    ![成本图表](./media/devtest-lab-configure-cost-management/graph.png)

    “评估成本”值为当前日历月中截止目前的成本。 “预计成本”值是当前整个日历月的评估成本，使用前五天的实验室成本计算得出。

    成本金额会进位到下一个证书。 例如： 

   * 5.01 将计为 6 
   * 5.50 将计为 6
   * 5.99 将计为 6

     如以上图表所述，图表中默认看到的成本是使用[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)优惠率的估算成本。 还可以通过[管理实验室的成本目标](#managing-cost-targets-for-your-lab)设置在图表中显示的自身支出目标。

     以下成本“未”计入成本计算：

   * 当前 不支持 CSP 和 Dreamspark 订阅，因为 Azure 开发测试实验室使用不支持 CSP 和 Dreamspark 订阅的 Azure 计费 API。
   * 套餐费率。 目前，无法使用你与 Microsoft 或 Microsoft 合作伙伴协商的套餐费率（显示于订阅下）。 仅能使用即用即付费率。
   * 税额
   * 折扣
   * 计费货币。 目前，实验室成本仅以美元显示。

### <a name="managing-cost-targets-for-your-lab"></a>管理实验室的成本目标
开发测试实验室可让你设置支出目标来更好地管理实验室成本，随后可在每月估计成本趋势图表中查看该目标。 开发测试实验室还可在达到指定的目标支出或阈值时向你发送通知。 

1. 在“成本趋势”页上，选择“管理目标”。 

    ![“管理目标”按钮](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. 在“管理目标”页上，指定支出目标和阈值。 还可以设置是在成本趋势图上还是通过 Webhook 通知报告每个所选阈值。

    ![“管理目标”窗格](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 选择要跟踪成本目标的时间段。
      - **每月**：每月进行成本目标跟踪。
      - “固定”：在开始和结束日期中指定的日期范围内跟踪成本目标。 通常情况下，这些值表示项目计划运行的时长。
   - 指定目标成本。 例如，计划在定义时间段内用于本实验的费用。
   - 选择启用或禁用任何需报告的阈值 - 增量为 25% - 高达指定目标成本的 125%。
      - **通知**：达到阈值时，可从你指定的 Webhook URL 获得通知。
      - “绘制在图表上”：达到此阈值时，结果会绘制在可以查看的成本趋势图上，如查看月预估成本趋势图表所述。
   - 如果选择达到阈值时接收通知，须指定 Webhook URL。 在成本集成区域中，选择“单击此处”可添加集成。 在配置通知窗格中输入“Webhook URL”，然后选择“确定”。 

       ![“配置通知”窗格](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - 如果指定“通知”，须定义 Webhook URL。
     - 同样，如果定义 Webhook URL，则须在“成本阈值”窗格中将“通知”设置为“开”。
     - 须在将 Webhook 输入此处之前创建 Webhook。  

       有关 Webhook 的详细信息，请参阅[创建 Webhook 或 API Azure 函数](../azure-functions/functions-bindings-http-webhook.md)。 

## <a name="view-cost-by-resource"></a>查看按资源划分的成本 
使用实验室中的每月成本趋势功能，可以查看当前日历月的成本。 它还会根据过去7天内的支出，显示到月末结束之前的支出预测。 为了帮助你了解实验室中的支出提前达到阈值的原因，可以使用“按资源划分的成本”功能，该功能用表格显示“每个资源”的本月至今累计成本。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，并从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需的实验室。  
4. 在左侧菜单中选择“配置和策略”。
5. 在左侧菜单的“成本跟踪”部分中选择“按资源划分的成本”。  可以查看与实验室关联的每个资源的相关成本。 

    ![按资源划分的成本](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

此功能有助于轻松地识别成本最高的资源，以便于采取行动来减少实验室支出。 例如，VM 的成本基于 VM 的大小。 VM 越大，成本就越高。 可以轻松地找到 VM 的大小和所有者，以便可以与 VM 所有者对话，了解为何需要这样的 VM 大小，以及是否有可能降低大小。

通过[自动关闭策略](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)，可以在一天中的特定时间关闭实验室 VM，从而降低成本。 不过，实验室用户可以选择退出关闭策略，这会增加运行 VM 的成本。 可以在表中选择一个 VM，以查看其是否已选择退出自动关闭策略。 如果是这种情况，则可以与 VM 所有者联系，以找出 VM 选择退出策略的原因。
 
## <a name="next-steps"></a>后续步骤
以下为后续步骤：

* [定义实验室策略](devtest-lab-set-lab-policy.md) - 了解如何设置各种策略，来管理实验室和其 VM 的使用方式。 
* [创建自定义映像](devtest-lab-create-template.md) - 创建 VM 时，指定一个基本映像，可以是自定义映像或市场映像。 本文演示了如何从 VHD 文件创建自定义映像。
* [配置市场映像](devtest-lab-configure-marketplace-images.md) - 开发测试实验室支持创建基于 Azure 市场映像的 VM。 本文展示了如何指定可用于在实验室中创建 VM 的 Azure 市场映像（如果有）。
* [在实验室中创建 VM](devtest-lab-add-vm.md) - 演示了如何从基本映像（自定义或市场映像）创建 VM，以及如何在 VM 中使用项目。
