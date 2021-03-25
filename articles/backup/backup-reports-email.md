---
title: 通过电子邮件发送 Azure 备份报告
description: 创建自动化任务以通过电子邮件接收定期报告
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 8c18d4c7a3c7a9ba343296961fa9a44614366405
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510357"
---
# <a name="email-azure-backup-reports"></a>通过电子邮件发送 Azure 备份报告

使用备份报告中可用的“电子邮件报告”功能可以创建自动化任务，以通过电子邮件接收定期报告。 此功能的工作方式是在 Azure 环境中部署一个逻辑应用，该逻辑应用根据提供的输入从所选 Log Analytics (LA) 工作区查询数据。 [了解有关逻辑应用及其定价的详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="getting-started"></a>入门

若要通过备份报告配置电子邮件任务，请执行以下步骤：

1.  导航到“备份中心” > “备份报告”，然后单击“电子邮件报告”选项卡  。
2.  通过指定下列信息来创建任务：
    * **任务详细信息** - 要创建的逻辑应用的名称，以及应在其中创建逻辑应用的订阅、资源组和位置。 请注意，逻辑应用可以跨多个订阅、资源组和位置（如“报告筛选器”部分所选）查询数据，但它在单个订阅、资源组和位置的上下文中创建。
    * **要导出的数据** - 要导出的选项卡。 可以通过选择“所有选项卡”选项，为每个选项卡创建单个任务应用，或使用单个任务通过电子邮件发送所有选项卡。
    * **电子邮件选项**：电子邮件频率、收件人电子邮件 ID 和电子邮件主题。

    ![“电子邮件”选项卡](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  单击“提交”并“确认”后，将创建逻辑应用 。 逻辑应用和关联的 API 连接是用标记 UsedByBackupReports: tru 创建的，这样更容易发现。 需要执行一次性授权步骤，逻辑应用才能够成功运行，如下一部分所述。

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>授权到 Azure Monitor 日志和 Office 365 的连接

逻辑应用使用 [azuremonitorlogs](https://docs.microsoft.com/connectors/azuremonitorlogs/) 连接器查询 LA 工作区，使用 [Office365 Outlook](https://docs.microsoft.com/connectors/office365connector/) 连接器发送电子邮件。 需要对这两个连接器执行一次性授权。 
 
若要执行授权，请按照以下步骤进行操作：

1.  在 Azure 门户中导航到“逻辑应用”。
2.  搜索已创建的逻辑应用名称，并导航到该资源。

    ![逻辑应用](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  单击“API 连接”菜单项。

    ![API 连接](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  会显示两个格式为 `<location>-azuremonitorlogs` 和 `<location>-office365` 的连接，即 eastus-azuremonitorlogs 和 eastus-office365 。
5.  导航到每个连接，并选择“编辑 API 连接”菜单项。 在出现的屏幕中，选择“授权”，并在授权完成后保存连接。

    ![授权连接](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  若要在授权后测试逻辑应用是否正常工作，可以导航回逻辑应用，打开“概述”，并在顶部窗格中选择“运行触发器”以测试是否已成功生成电子邮件 。

## <a name="contents-of-the-email"></a>电子邮件的内容

* 门户中显示的所有图表和图都可用作电子邮件中的内联内容。
* 门户中显示的网格可用作电子邮件中的 *.csv 附件。
* 电子邮件中显示的数据会使用用户在创建电子邮件任务时在报表中选择的所有报表级筛选器。
* 不会应用选项卡级筛选器，例如“备份实例名称”、“策略名称”等 。 唯一的例外是“优化”选项卡中的“保留优化”网格，其中应用了“每日”、“每周”、“每月”和“每年”RP 保留的筛选器     。
* （图表的）时间范围和聚合类型基于用户在报告中选择的时间范围。 例如，如果选择的时间范围为最近 60 天（转换为每周聚合类型），且电子邮件频率为每天，则收件人将每天都会收到一封电子邮件，其中包含最近 60 天内所采集数据的图表，并按每周级别聚合数据。

## <a name="troubleshooting-issues"></a>对问题进行故障排除

如果在成功部署逻辑应用后仍未按预期收到电子邮件，可以按照以下步骤对配置进行故障排除：

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>情形 1：既未收到成功的电子邮件，也未收到错误的电子邮件

* 出现此问题的原因可能是 Outlook API 连接器未获得授权。 若要授权连接，请按照上面提供的授权步骤进行操作。

* 如果在创建逻辑应用时指定了错误的电子邮件收件人，也可能出现此问题。 若要验证是否已正确指定电子邮件收件人，可以导航到 Azure 门户中的逻辑应用，打开逻辑应用设计器并选择电子邮件步骤，查看是否使用了正确的电子邮件 ID。

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>情形 2：收到错误电子邮件，指示逻辑应用未能执行至完成

若要排查此问题，请执行以下操作：
1.  导航到 Azure 门户中的逻辑应用。
2.  在“概述”屏幕的底部，会显示“运行历史记录”部分 。 可以打开上一次运行，查看工作流中的哪些步骤失败。 可能的原因包括：
    * **尚未对 Azure Monitor 日志连接器授权**：若要解决此问题，请执行上面提供的授权步骤。
    * **LA 查询出错**：如果已使用自己的查询自定义逻辑应用，则任何 LA 查询中的错误都可能导致逻辑应用失败。 可以选择相关步骤并查看导致查询运行出错的错误。

如果问题持续出现，请联系 Microsoft 支持部门。

## <a name="next-steps"></a>后续步骤
[详细了解备份报告](https://docs.microsoft.com/azure/backup/configure-reports)
