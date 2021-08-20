---
title: 查看单独的 Azure 订阅账单
description: 了解如何理解账单和资源使用情况，以及如何验证个人 Azure 订阅的费用，包括即用即付。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: a8eb9ec2b71495011dfa7ebe9dbf1dcf8cd5d19e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449410"
---
# <a name="tutorial-review-your-individual-azure-subscription-bill"></a>教程：查看单独的 Azure 订阅账单

本文有助于你理解和查看即用即付或 Visual Studio Azure 订阅的帐单，包括即用即付和 Visual Studio。 通常情况下，你通过电子邮件接收每个计费周期的发票。 发票代表 Azure 账单。 Azure 门户中提供了发票上的成本信息。 本教程在 Azure 门户中将发票与每日使用情况明细文件和成本分析进行比较。

本教程仅适用于具有个人订阅的 Azure 客户。 常见的个人订阅是直接从 Azure 网站购买的，其费率为即用即付费率。

若想获取相关帮助来理解意外产生的费用，请参阅[分析意外费用](analyze-unexpected-charges.md)。 或者，如果要取消 Azure 订阅，请参阅[取消 Azure 订阅](../manage/cancel-azure-subscription.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 将已开票费用与使用情况文件进行比较
> * 在成本分析中比较费用和使用情况

## <a name="prerequisites"></a>先决条件

必须有付费的 Microsoft Online Services 计划计费帐户。 此帐户在你通过 Azure 网站注册获取 Azure 时创建。 例如，如果你有一个采用即用即付费率的帐户，或者你是 Visual Studio 订阅者。

仅当超过每月信用额度时，才会创建 Azure 免费帐户的发票。

从订阅 Azure 那天算起，必须超过 30 天。 在发票周期结束时，Azure 会向你开具账单。

## <a name="sign-in-to-azure"></a>登录 Azure

- 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="compare-billed-charges-with-your-usage-file"></a>将计费费用与使用情况文件进行比较

<a name="charges"></a>

若要比较使用情况和成本，第一步是下载发票和使用情况文件。 使用情况明细 CSV 文件按计费周期和每日使用情况显示费用信息。 它不包含任何税务信息。 若要下载文件，你必须是帐户管理员或拥有“所有者”角色。

在 Azure 门户的搜索框中键入“订阅”，然后单击“**订阅**”。

[![导航到订阅](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

在订阅列表中，单击订阅。

在“计费”下，单击“发票”。 

在发票列表中，查找要下载的发票，然后单击下载符号。 可能需要更改时间跨度才能查看较旧的发票。 生成使用情况详细信息文件和发票可能需要几分钟的时间。

![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/review-individual-bill/download-invoice.png)

在“下载使用情况 + 费用”窗口中，单击“下载 csv”和“下载发票”。 

![显示“下载发票和使用情况”页的屏幕截图](./media/review-individual-bill/usageandinvoice.png)

如果显示“不可用”，可能有多种原因导致你看不到使用情况详细信息或发票：

- 从订阅 Azure 开始算还不到 30 天。
- 此计费周期没有任何使用量。
- 尚未生成发票。 请等到计费周期结束。
- 你无权查看发票。 除非你是帐户管理员，否则可能看不到旧发票。
- 如果使用免费试用版，或者尚未超过订阅的每月信用额度，则不会收到发票，除非你有 Microsoft 客户协议。

接下来，查看费用。 发票显示税费值和使用费。

![Azure 发票示例](./media/review-individual-bill/invoice-usage-charge.png)

打开已下载的 CSV 使用情况文件。 在文件的末尾，将“成本”列中所有项的值求和。

![已将成本求和的使用情况文件示例](./media/review-individual-bill/usage-file-usage-charges.png)

 求和“成本”值应与发票上的“使用费”成本精确匹配。

在计量级别显示使用费。 下方的术语在发票和使用情况明细文件中表示同一事物。 例如，发票中的计费周期等同于使用情况明细文件中的计费周期。

| 发票（PDF） | 使用情况明细 (CSV)|
| --- | --- |
|计费周期 | BillingPeriodStartDate BillingPeriodEndDate |
|名称 |测定仪类别 |
|类型 |测定仪子类别 |
|资源 |MeterName |
|区域 |MeterRegion |
|已耗用 | 数量 |
|附送 |附送数量 |
|可计费 |超额数量 |
|费率 | EffectivePrice|
| 值 | 成本 |

发票的“使用费”部分显示计费周期内已使用的每种计量的总值（成本）。 例如，下图显示用于“P10 磁盘”资源的 Azure 存储服务的使用费。

![发票使用费](./media/review-individual-bill/invoice-usage-charges.png)

在 CSV 使用情况文件中，按发票上显示的相应资源的 *MeterName* 进行筛选。 然后，对列中项的“成本”值求和。 以下示例重点介绍与发票上的同一行项对应的计量名称（P10 磁盘）。

若要核对预留购买费用，在 CSV 使用情况文件中按 ChargeType“购买”进行筛选，它将显示该月的所有预留购买费用。 通过查看使用情况文件中的 MeterName 和 MeterSubCategory，将这些费用分别与发票中的“资源”和“类型”进行比较。

![MeterName 的使用情况文件求和值](./media/review-individual-bill/usage-file-usage-charge-resource.png)

求和“成本”值应与发票上已收费的单项资源的“使用费”成本精确匹配。

## <a name="compare-billed-charges-and-usage-in-cost-analysis"></a>在成本分析中比较计费费用和使用情况

还可以通过 Azure 门户中的成本分析验证费用。 若要快速了解已开票的使用情况和费用，请从 Azure 门户中的“订阅”页面选择订阅。 接下来单击“成本分析”，然后在“视图”列表中单击“发票详细信息”。

![显示发票详细信息选择的示例](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

接下来，在日期范围列表中选择发票的时间段。 为发票编号添加一个筛选器，然后选择与发票上的 InvoiceNumber 匹配的编号。 成本分析显示已开票项目的成本详细信息。

![显示成本分析中的开票成本详细信息的示例](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

成本分析中显示的成本应与发票上已收费的单项资源的“使用费”成本精确匹配。

![发票使用费](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-marketplace-services"></a>外部市场服务

<a name="external"></a>

外部服务或市场费用针对的是由第三方软件供应商创建的资源。 这些资源可以从 Azure 市场使用。 例如，Barracuda 防火墙是由第三方提供的 Azure 市场资源。 此防火墙的所有费用及其对应的计量指标都将显示为外部服务费用。

外部服务费用显示在单独的发票上。

### <a name="resources-are-billed-by-usage-meters"></a>资源按使用计量计费

Azure 不直接根据资源成本计费。 资源费用按一个或多个计量来计算。 计量用于跟踪资源整个生存期的使用情况。 随后会使用这些计量指标来计算费用。

创建单个 Azure 资源（例如虚拟机）时，会创建一个或多个计量实例。 计量用于跟踪资源在一段时间内的使用情况。 每个计量都会发送使用情况记录，这些记录由 Azure 用来计算账单费用。

例如，如果在 Azure 中创建了一个虚拟机 (VM)，那么可能会创建以下计量指标来跟踪其使用情况：

- 计算小时数
- IP 地址小时数
- 数据传入
- 数据发送
- 标准托管磁盘
- 标准托管磁盘操作数
- 标准 IO 磁盘
- 标准 IO 块 Blob 读取
- 标准 IO 块 Blob 写入
- 标准 IO 块 Blob 删除

创建 VM 后，每个计量器都会开始发出使用情况记录。 此使用情况和计量的价格在 Azure 计量系统中进行跟踪。

可以在使用情况 CSV 文件中查看用于计算账单的计量指标，如前面的示例中所示。

## <a name="pay-your-bill"></a>支付帐单

<a name="payment"></a>

如果已将信用卡设置为付款方式，则会在计费周期结束后的 10 天内自动付款。 在信用卡对帐单上，行项会显示 **MSFT Azure**。

若要更改用于付款的信用卡，请参阅[添加、更新或删除用于 Azure 的信用卡](../manage/change-credit-card.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 将已开票费用与使用情况文件进行比较
> * 在成本分析中比较费用和使用情况

完成本快速入门，开始使用成本分析。

> [!div class="nextstepaction"]
> [开始分析成本](../costs/quick-acm-cost-analysis.md)
