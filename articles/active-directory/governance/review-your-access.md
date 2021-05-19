---
title: 在访问评审中评审自己对组和应用的访问权限 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中评审自己对组或应用程序的访问权限。
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5532ec5cb0f619199749621d71f7d2165dfe2776
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109787376"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中评审自己对组或应用程序的访问权限

Azure Active Directory (Azure AD) 借助称为“Azure AD 访问评审”的功能，简化了企业对 Azure AD 及其他 Microsoft Online Services 中的组或应用程序访问权限的管理方式。

本文介绍如何评审自己对组或应用程序的访问权限。

## <a name="review-your-access-using-my-apps"></a>使用“我的应用”评审访问权限

执行访问评审的第一步是找到并打开访问评审。

>[!IMPORTANT]
> 接收电子邮件可能存在延迟，在某些情况下，可能需要长达 24 小时来接收。 将 azure-noreply@microsoft.com 添加到安全收件人列表以确保收到所有电子邮件。

1. 找到要求你执行访问评审的 Microsoft 电子邮件。 以下示例电子邮件要求评审你对某个组的访问权限。

    ![要求评审你对某个组的访问权限的 Microsoft 示例电子邮件](./media/review-your-access/access-review-email.png)

1. 单击“评审访问权限”链接，打开访问评审。

如果未收到该电子邮件，可遵循以下步骤找到待处理的访问评审。

1. 登录到“我的应用”门户 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。

    ![“我的应用”门户，其中列出了你有权访问的应用](./media/review-your-access/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 在页面右侧，单击“访问评审”磁贴来查看待处理的访问评审。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![应用和组的待处理访问评审列表](./media/review-your-access/access-reviews-list.png)

1. 单击你要执行的访问评审对应的“开始评审”链接。

### <a name="perform-the-access-review"></a>执行访问评审

打开访问评审以后，即可看到自己的访问权限。

1. 评审你的访问权限，决定是否仍需要访问权限。

    如果请求是评审他人的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审对组或应用程序的访问权限](perform-access-review.md)。

    ![屏幕截图显示了开放访问评审，其中询问是否仍需访问某个组。](./media/review-your-access/perform-access-review.png)

1. 单击“是”保留访问权限，或者单击“否”删除访问权限。

1. 如果单击“是”，你可能需要在“原因”框中指出原因。 

    ![屏幕截图显示已完成的访问评审，其中系统询问你是否仍需访问某个组，且已选择“是”。](./media/review-your-access/perform-access-review-submit.png)

1. 单击“提交” 。

    此时会提交你所做的选择，并会将你返回到“我的应用”门户。

    若要更改响应，请重新打开“访问评审”页面并更新响应。 在访问评审结束之前，随时可以更改响应。

    > [!NOTE]
    > 如果你指出不再需要访问权限，系统不会立即将你删除。 系统会在评审结束时或者在管理员停止评审时将你删除。

## <a name="review-your-own-access-using-my-access-new"></a>使用“我的访问权限”（新）查看自己的访问权限

可以通过以下几种不同的方式，使用更新的“我的访问权限”用户界面来尝试新的体验：

### <a name="my-apps-portal"></a>“我的应用”门户

1. 登录到“我的应用”门户 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。

    ![“我的应用”门户，其中列出了你有权访问的应用](./media/review-your-access/myapps-access-panel.png)

2. 单击“访问评审”磁贴，查看待处理的访问评审列表。

    > [!NOTE]
    > 如果“访问评审”磁贴不可见，则表明该组织没有要执行的访问评审，此时不需要执行任何操作。

3. 单击页面顶部横幅中的“试用!”， 转到新的“我的访问权限”体验。

    ![应用和组的待处理访问评审列表，其中显示预览版期间“新体验现在可用”横幅](./media/review-your-access/banner-your-access.png)

4. 在“执行访问评审”部分中继续

### <a name="email"></a>电子邮件

>[!IMPORTANT]
> 接收电子邮件可能存在延迟，在某些情况下，可能需要长达 24 小时来接收。 将 azure-noreply@microsoft.com 添加到安全收件人列表以确保收到所有电子邮件。

1. 查找要求你执行访问评审的 Microsoft 电子邮件。 可以看到如下所示的示例电子邮件：

 ![要求评审对某个组的访问权限的 Microsoft 示例电子邮件](./media/review-your-access/access-review-email-preview.png)

2. 单击“评审访问权限”链接，打开访问评审。

3. 在“执行访问评审”部分中继续

>[!NOTE]
>如果单击“开始评审”后转到“我的应用”，请按照上面标题为“我的应用门户”部分列出的步骤进行操作 。

### <a name="directly-at-my-access"></a>直接前往“我的访问权限”

还可使用浏览器打开“我的访问权限”来查看待处理的访问评审。

1. 在 https://myaccess.microsoft.com/ 登录到“我的访问权限”

2. 选择左侧栏菜单中的“访问评审”，查看分配给你的待处理访问评审的列表。

   ![菜单中的访问评审](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>执行访问评审

1. 在“组和应用”下，可以看到：
    
    - 名称：访问评审的名称。
    - 截止日期：评审的截止日期。 在此日期后，可能会从正在评审的组或应用中删除被拒绝的用户。
    - 资源：正在评审的资源的名称。
    - 进度：此访问评审的用户总数中已评审的用户数。
    
2. 单击访问评审的名称即可开始使用。

   ![应用和组的待处理访问评审列表](./media/review-your-access/access-reviews-list-preview.png)

3. 评审你的访问权限，决定是否仍需要访问权限。

    如果请求是评审他人的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审对组或应用程序的访问权限](perform-access-review.md)。

    ![打开访问评审，系统会询问你是否仍需访问某个组](./media/review-your-access/review-access-preview.png)

1. 选择“是”将保留该访问权限，选择“否”将删除该访问权限 。

1. 如果单击“是”，你可能需要在“原因”框中指出原因。 

    ![完成访问评审，系统会询问你是否仍需访问某个组](./media/review-your-access/review-access-yes-preview.png)

1. 单击“提交” 。

    此时会提交你所做的选择，并会将你返回到“我的访问权限”页。

    若要更改响应，请重新打开“访问评审”页面并更新响应。 在访问评审结束之前，随时可以更改响应。

    > [!NOTE]
    > 如果你指出不再需要访问权限，系统不会立即将你删除。 系统会在评审结束时或者在管理员停止评审时将你删除。

## <a name="next-steps"></a>后续步骤

- [完成组或应用程序的访问评审](complete-access-review.md)
