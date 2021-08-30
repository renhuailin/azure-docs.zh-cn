---
title: 使用 Application Insights 数据自动化自定义报表
description: 使用 Azure Monitor Application Insights 数据自动化自定义每日/每周/每月报表
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: 1579874f77a41abbfef6a9ba44f997d1ec06bb76
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195728"
---
# <a name="automate-custom-reports-with-application-insights-data"></a>使用 Application Insights 数据自动化自定义报表

定期报表有助于团队了解其业务关键型服务的状况。 开发人员、DevOps/SRE 团队及其管理人员可以通过自动化报表获得可靠的见解，而无需每个人都登录到门户，从而提高工作效率。 此类报表还有助于识别可能不会触发任何警报规则的延迟、负载或故障率的逐渐增加趋势。

每个企业都有其独特的报告需求，例如：

* 指标的特定百分位数聚合，或报告中的自定义指标。
* 为不同的受众提供不同的每日、每周和每月数据汇总报表。
* 按区域或环境等自定义属性进行分段。
* 将一些 AI 资源集中在一个报告中，即使它们可能位于不同的订阅或资源组等。
* 包含已发送给选择性受众的敏感指标的单独报告。
* 向可能无权访问门户资源的利益干系人提供的报表。

> [!NOTE] 
> 每周 Application Insights 摘要电子邮件不允许任何自定义，并且将基于下面列出的自定义选项中止。 最后一封每周摘要电子邮件将于 2018 年 6 月 11 日发送。 请配置以下选项之一以获取类似的自定义报表（使用下面建议的查询）。

## <a name="to-automate-custom-report-emails"></a>自动化自定义报表电子邮件

可以[采用编程方式查询 Application Insights](https://dev.applicationinsights.io/) 数据，按计划生成自定义报表。 以下选项有助于快速开始：

* [使用 Power Automate 来自动完成报表](../logs/logicapp-flow-connector.md)
* [使用逻辑应用自动化报表](automate-with-logic-apps.md)
* 在监视方案中使用“Application Insights 计划摘要”[Azure Function](../../azure-functions/functions-get-started.md) 模板。 此函数使用 SendGrid 传递电子邮件。 

    ![Azure Function 模板](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>每周摘要电子邮件的示例查询
以下查询显示每周摘要电子邮件（如报表）的跨多个数据集联接情况。 根据需要对其进行自定义，并将其用于上面列出的选项，自动化每周报告。

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Application Insights 计划的摘要报表

1. 创建一个 Azure 函数应用。（仅当要使用 Application Insights 监视新的函数应用时，才需要启用 Application Insights）

   请访问 Azure Functions 文档，了解如何[创建函数应用](../../azure-functions/functions-get-started.md)

2. 在新的函数应用完成部署后，选择“转到资源”。

3. 选择“新建函数”。

   ![“创建新函数”屏幕截图](./media/automate-custom-reports/new-function.png)

4. 选择“Application Insights 计划的摘要模板”。

     > [!NOTE]
     > 默认情况下，使用运行时版本 3.x 创建函数应用。 必须 [以 Azure Functions 运行时版本](../../azure-functions/set-runtime-version.md) **1.x** 为目标才能使用 Application Insights 计划摘要模板。 转到“配置”>“函数运行时设置”以更改运行时版本。 ![运行时屏幕截图](./media/automate-custom-reports/change-runtime-v.png)

   ![“新建函数 Application Insights 模板”屏幕截图](./media/automate-custom-reports/function-app-04.png)

5. 为你的报告输入合适的收件人电子邮件地址并选择“创建”。

   ![“函数设置”屏幕截图](./media/automate-custom-reports/scheduled-digest.png)

6. 选择“函数应用” > “平台功能” > “配置”。

    ![“Azure 函数应用程序设置”屏幕截图](./media/automate-custom-reports/config.png)

7. 使用合适的对应值 ``AI_APP_ID``、``AI_APP_KEY`` 和 ``SendGridAPI`` 创建三个新的应用程序设置。 选择“保存” 。

     ![“函数集成接口”屏幕截图](./media/automate-custom-reports/app-settings.png)
    
    （可以在要报告的 Application Insights 资源的 API 访问权限下找到 AI_ 值。 如果你没有 Application Insights API 密钥，可以使用“创建 API 密钥”选项。）
    
   * AI_APP_ID = Application ID
   * AI_APP_KEY = API Key
   * SendGridAPI =SendGrid API Key

     > [!NOTE]
     > 如果没有 SendGrid 帐户，可以创建一个。 [此处](../../azure-functions/functions-bindings-sendgrid.md)提供了适用于 Azure 函数的 SendGrid 文档。 如果只需要有关如何设置 SendGrid 的简单解释并生成一个 API 密钥，则本文末尾提供了一个。 

8. 选择“集成”并在“输出”下单击“SendGrid ($return)”。

     ![“输出”屏幕截图](./media/automate-custom-reports/integrate.png)

9. 在“SendGridAPI 密钥应用设置”下，对于“SendGridAPI”，选择你新创建的应用设置。

     ![“运行函数应用”屏幕截图](./media/automate-custom-reports/sendgrid-output.png)

10. 运行并测试你的函数应用。

     ![“测试”屏幕截图](./media/automate-custom-reports/function-app-11.png)

11. 检查你的电子邮件以确认是否成功发送/接收了邮件。

     ![“电子邮件主题行”屏幕截图](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>通过 Azure 使用 SendGrid

只有尚未配置 SendGrid 帐户时，这些步骤才适用。

1. 在 Azure 门户中，选择“创建资源”> 搜索“SendGrid 电子邮件传递”> 单击“创建”> 填写 SendGrid 特定的创建说明。

     ![“创建 SendGrid 资源”屏幕截图](./media/automate-custom-reports/sendgrid.png)

2. 在创建后，在“SendGrid 帐户”下选择“管理”。

     ![“设置 > API 密钥”屏幕截图](./media/automate-custom-reports/sendgrid-manage.png)

3. 这将启动 SendGrid 的站点。 选择“设置” > “API 密钥”。 

     ![“创建并查看 API 密钥应用”屏幕截图](./media/automate-custom-reports/function-app-15.png)

4. 创建一个 API 密钥 > 选择“创建并查看”。 （请查看有关受限访问的 SendGrid 文档来确定什么级别的权限适合你的 API 密钥。 此处选择“完全访问权限”只是为了举例说明。）

   ![“完全访问权限”屏幕截图](./media/automate-custom-reports/function-app-16.png)

5. 复制整个密钥，此值是在函数应用设置中需要用作 SendGridAPI 值的值

   ![“复制 API 密钥”屏幕截图](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>后续步骤

* 详细了解如何创建 [Analytics 查询](../logs/get-started-queries.md)。
* 详细了解[采用编程方式查询 Application Insights 数据](https://dev.applicationinsights.io/)
* 了解有关[逻辑应用](../../logic-apps/logic-apps-overview.md)的详细信息。
* 详细了解 [Microsoft Power Automate](https://ms.flow.microsoft.com)。
