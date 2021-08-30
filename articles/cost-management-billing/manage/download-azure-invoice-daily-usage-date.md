---
title: 下载 Azure 账单发票
description: 介绍如何下载或查看 Azure 账单发票。
keywords: 账单发票,发票下载,azure 发票
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/28/2021
ms.author: banders
ms.openlocfilehash: 14183a46085ca0da60f1abb5f6ba390b9d4c334a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745414"
---
# <a name="download-or-view-your-azure-billing-invoice"></a>下载或查看 Azure 账单发票

对于大多数订阅，你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票，也可通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。

只有某些角色（例如帐户管理员或企业管理员）有权获取账单发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](manage-billing-access.md)。

如果你签订了 Microsoft 客户协议，则必须是计费对象信息所有者、参与者、读取者或发票管理者，才能查看计费信息。 若要详细了解 Microsoft 客户协议的计费角色，请参阅[计费对象信息角色和任务](understand-mca-roles.md#billing-profile-roles-and-tasks)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>下载 Azure 发票 (.pdf)

对于大多数订阅，可以从 Azure 门户下载发票。 如果你签订了 Microsoft 客户协议，请参阅“下载适用于计费对象信息的发票”。

### <a name="download-invoices-for-an-individual-subscription"></a>下载个人订阅的发票

1. [作为有权访问发票的用户](manage-billing-access.md)，从 Azure 门户中的[“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择订阅。

2. 选择“发票”  。

    ![屏幕快照，显示“计费和使用情况”选项](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. 单击“下载”按钮下载 PDF 发票的副本，然后选择“下载发票”  。 如果它显示“不可用”  ，请参阅[为何看不到最后一个计费周期的发票？](#noinvoice)

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. 还可以通过单击“下载 csv”  下载已耗用量和估计费用的每日明细。

    ![显示“下载发票和使用情况”页的屏幕截图](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](../understand/review-individual-bill.md)。 若要在成本管理方面需要帮助，请参阅[分析意外费用](../understand/analyze-unexpected-charges.md)。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>下载适用于 Microsoft 客户协议的发票

将为 Microsoft 客户协议中的每条[计费对象信息](../understand/mca-overview.md#billing-profiles)生成发票。 必须是计费对象信息所有者、参与者、读者或发票管理者才能从 Azure 门户下载发票。

1. 搜索“成本管理 + 计费”。 
2. 选择计费对象信息。
3. 选择“发票”  。
4. 在发票网格中，找到要下载的发票行。
5. 单击行末尾的“下载”按钮。
6. 在下载上下文菜单中，选择“发票”  。

如果看不到上一个计费周期的发票，请参阅“其他信息”  。 <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> 为何看不到最后一个计费周期的发票？

看不到发票的几个可能原因：

- 从订阅 Azure 开始算还不到 30 天。

- 尚未生成发票。 请等到计费周期结束。

- 你无权查看发票。 如果你签订了 Microsoft 客户协议，则必须是计费对象信息所有者、参与者、读者或发票管理者，才能查看发票。 对于其他订阅，如果不是帐户管理员，则可能看不到旧发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](manage-billing-access.md)。

- 如果使用免费试用版，或者尚未超过订阅的每月信用额度，则不会收到发票，除非你有 Microsoft 客户协议。

## <a name="get-your-invoice-in-email-pdf"></a>通过电子邮件获取发票 (.pdf)

可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 此功能可能不适用于某些订阅，例如支持套餐、企业协议或 Azure 开放许可。 如果你签订了 Microsoft 客户协议，请参阅[通过电子邮件获取计费对象信息发票](../understand/download-azure-invoice.md#get-your-billing-profiles-invoice-in-email)。

### <a name="get-your-subscriptions-invoices-in-email"></a>通过电子邮件获取订阅的发票

1. 从[订阅页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择你的订阅。 为拥有的每个订阅执行选择加入操作。 依次单击“发票”  、“通过电子邮件发送我的发票”  。

    ![显示了“选择加入”流的屏幕截图](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. 单击“选择加入”  并接受条款。

    ![显示了“选择加入”流步骤 2 的屏幕截图](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. 接受协议后，即可以配置其他收件人。 删除收件人时，不再存储电子邮件地址。 如果改变主意，则需要重新添加。

    ![显示了“选择加入”流步骤 3 的屏幕截图](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

如果执行步骤后未收到电子邮件，请确保你在 Microsoft 帐户中心内的[个人资料通讯首选项](https://account.microsoft.com/profile)中的电子邮件地址正确。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>选择不通过电子邮件获取订阅的发票

若要选择不通过电子邮件获取发票，可以按照上面的步骤操作，并单击“选择不通过电子邮件获取发票”  。 此选项将删除任何设置用来通过电子邮件接收发票的电子邮件地址。 如果再次选择接收，则可以重新配置收件人。

 ![显示了“选择退出”流的屏幕截图](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>通过电子邮件获取 Microsoft 客户协议发票

如果签订了 Microsoft 客户协议，可以选择通过电子邮件获取发票。 所有计费对象信息所有者、参与者、读者和发票经理都将通过电子邮件获取发票。 读者无法更新电子邮件发票首选项。

1. 搜索“成本管理 + 计费”。 
1. 选择计费对象信息。
1. 在“设置”下，选择“属性”   。
1. 在“电子邮件发票”  下，选择“更新电子邮件发票首选项”  。
1. 选择“选择加入”  。
1. 单击“更新”  。

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>选择不通过电子邮件获取计费对象信息发票

若要选择不通过电子邮件获取发票，可以按照上面的步骤操作，并单击“选择退出”  。所有所有者、参与者、读者和发票管理者也会选择不通过电子邮件获取发票。 如果你是读者，则无法更改电子邮件发票首选项。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和费用，请参阅：

- [了解 Microsoft Azure 账单](../understand/review-individual-bill.md)
- [了解有关 Azure 发票的术语](../understand/understand-invoice.md)
- [了解 Microsoft Azure 详细使用条款](../understand/understand-usage.md)
- [查看组织的 Azure 定价](ea-pricing.md)

如果你签订了 Microsoft 客户协议，请参阅：

- [了解计费对象信息发票上的费用](../understand/review-customer-agreement-bill.md)
- [了解计费对象信息发票上的术语](../understand/mca-understand-your-invoice.md)
- [了解计费对象信息的 Azure 使用情况和费用文件](../understand/mca-understand-your-usage.md)
- [查看和下载计费对象信息的税单](../understand/mca-download-tax-document.md)
- [查看组织的 Azure 定价](ea-pricing.md)
