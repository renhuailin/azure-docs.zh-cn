---
title: 排查无法在 Azure 门户中查看发票的问题
description: 解决尝试在 Azure 门户中查看发票时出现的问题。
services: cost-management-billing
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: banders
ms.openlocfilehash: aac769894a85db94c68bc4c73971809775ea4da4
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112971316"
---
# <a name="troubleshoot-issues-while-trying-to-view-invoice-in-the-azure-portal"></a>排查尝试在 Azure 门户中查看发票时出现的问题

尝试在 Azure 门户中查看发票时，可能会遇到问题。 本简短指南将讨论一些常见问题。
 
## <a name="common-issues-and-solutions"></a>常见问题和解决方案

#### <a name="you-see-the-message-we-cant-display-the-invoices-for-your-subscription-this-typically-happens-when-you-sign-in-with-an-email-which-doesnt-have-access-to-view-invoices-check-youve-signed-in-with-the-correct-email-address-if-you-are-still-seeing-the-error-see-why-you-might-not-see-an-invoice"></a><a name="subnotfound"></a> 看到消息“无法显示订阅的发票。 如果你使用无权查看发票的电子邮件地址登录，则通常会发生这种情况。 请检查是否使用了正确的电子邮件地址登录。 如果仍然看到该错误，请参阅‘为何看不到发票’。”

如果用于登录的标识无权访问订阅，则会发生这种情况。

若要解决此问题，请尝试采取以下做法之一： 

验证是否使用了正确的电子邮件地址登录：

只有具有订阅帐户管理员角色的电子邮件地址能够查看其发票。 验证是否使用了正确的电子邮件地址登录。 生成发票时向你发送的电子邮件中显示了该电子邮件地址。  

  ![该屏幕截图显示发票电子邮件](./media/troubleshoot-cant-find-invoice/invoice-email.png)

验证是否使用了正确的帐户登录：

某些客户使用同一电子邮件地址注册了两个帐户 - 工作或学校帐户和个人帐户。 通常，其中只有一个帐户有权查看发票。 你可能使用电子邮件地址注册了两个帐户。 如果使用无相应权限的帐户登录，将看不到发票。 若要确定你是否有多个帐户并使用不同的帐户，请执行以下步骤：

1. 在 InPrivate/Incognito 窗口中，登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你使用同一电子邮件地址注册了多个帐户，系统会提示你选择“工作或学校帐户”或“个人帐户” 。 选择其中一个帐户，然后按照[此处有关查看发票的说明](../understand/download-azure-invoice.md#download-your-mosp-azure-subscription-invoice)进行操作。  

    ![该屏幕截图显示选择帐户的操作](./media/troubleshoot-cant-find-invoice/two-accounts.png)

1. 如果仍然无法在 Azure 门户中查看发票，请尝试使用另一个帐户。

验证是否已登录到正确的 Azure Active Directory (AAD) 租户：

计费帐户和订阅与 AAD 租户相关联。 如果登录到了错误的租户，将看不到订阅的发票。 尝试执行以下步骤在 Azure 门户中切换租户：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在页面右上方选择你的电子邮件地址。
1. 选择“切换目录”。  

    ![该屏幕截图显示选择“切换目录”的操作](./media/troubleshoot-cant-find-invoice/select-switch-tenant.png)

1. 在“所有目录”部分选择一个租户。 如果未看到“所有目录”部分，则表示你无权访问多个租户。  

    ![该屏幕截图显示选择另一目录的操作](./media/troubleshoot-cant-find-invoice/select-another-tenant.png)

#### <a name="you-couldnt-find-the-invoice-that-you-see-on-your-credit-card-statement"></a><a name="cantsearchinvoice"></a>找不到在信用卡对帐单中显示的发票

你在信用卡对帐单中看到一笔“Microsoft Gxxxxxxxxx”费用。 可以在门户中找到所有其他发票，但找不到“Gxxxxxxxxx”。 如果发票属于其他订阅或计费配置文件，则会发生这种情况。 按照以下步骤查看发票。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户搜索栏中搜索发票编号。
1. 选择“查看发票”。  

    ![该屏幕截图显示搜索发票的操作](./media/troubleshoot-cant-find-invoice/search-invoice.png)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- [查看和下载 Azure 发票](../understand/download-azure-invoice.md)
- [查看和下载 Azure 使用情况与费用](../understand/download-azure-daily-usage.md)
- [Azure 门户发生“找不到任何订阅”登录错误](no-subscriptions-found.md)