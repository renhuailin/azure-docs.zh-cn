---
title: 按发票为 Azure 订阅付款
description: 了解如何按发票为 Azure 订阅付款。 请参阅常见问题解答以查看详细信息。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 06/16/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: b8b59db6b73cdc2ecae3ca60741a892580162602
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112287743"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>按发票为 Azure 订阅付款

本文适用于通过 Azure 网站注册 Azure 且签订了 Microsoft 客户协议 (MCA) 的客户。 [检查对 Microsoft 客户协议的访问权限](#check-access-to-a-microsoft-customer-agreement)。 如果你已通过 Microsoft 代表注册了 Azure，则默认付款方式已设置为“支票或电汇”。

如果切换到按发票付款，这意味着需要在开票日期后的 30 天内以支票/电汇方式支付账单费用。 必须向 Azure 支持部门提交请求，才能按发票支付 Azure 订阅费用。 请求获批后，可以在 Azure 门户中切换到发票付款（支票/电汇）。

> [!IMPORTANT]
> * 发票付款（支票/电汇）仅适用于代表公司使用 Azure 的客户。
> * 在切换到发票付款之前，请支付所有未付费用。
> * 目前，中国的全球 Azure 不支持发票付款。

## <a name="request-to-pay-by-invoice"></a>请求按发票付款

1. 要提交支持请求，请登录到 Azure 门户。 搜索并选择“帮助 + 支持”。  
    :::image type="content" source="./media/pay-by-invoice/search-for-help-and-support.png" alt-text="搜索“帮助和支持”的屏幕截图。" lightbox="./media/pay-by-invoice/search-for-help-and-support.png" :::
1. 选择“新建支持请求”。  
    :::image type="content" source="./media/pay-by-invoice/help-and-support.png" alt-text="“新建支持请求”链接的屏幕截图。" lightbox="./media/pay-by-invoice/help-and-support.png" :::
1. 选择“计费”作为“问题类型”。  问题类型是支持请求类别。 选择要按发票付款的订阅，选择支持计划，然后选择“下一步”。
1. 选择“付款”作为“问题类型”。  问题类型是支持请求子类别。
1. 选择“切换到按发票付款”作为“问题子类型” 。
1. 在“详细信息”框中输入以下信息，并选择“下一步”。
    - 新客户或现有客户：
    - 如果是现有客户，当前付款方式为：
    - 订单 ID（请求发票选项）：
    - 帐户管理员 Live ID（或组织 ID）（应为公司域）：
    - 商务帐户 ID：
    - 公司名称（与在 VAT 或政府网站上注册的相同）：
    - 公司地址（与在 VAT 或政府网站上注册的相同）：
    - 公司网站：
    - 国家/地区：
    - 税务 ID/增值税 ID：
    - 公司成立时间（年）：
    - 以前是否与 Microsoft 有业务往来：
    - 联系人姓名：
    - 联系人电话：
    - 联系人电子邮件：
    - 有关为何需要发票选项而不是信用卡的理由：
    - 对于核心增加情况，请提供以下附加信息：
        - （旧配额）现有核心数：
        - （新配额）请求的核心数：
        - 特定区域和系列的订阅：
    - “公司名称”和“公司地址”应与你为 Azure 帐户提供的信息匹配。  若要查看或更新信息，请参阅[更改 Azure 帐户个人资料信息](change-azure-account-profile.md)。
    - 请在 Azure 门户中添加计费联系人信息，然后额度限制才会获得批准。 联系人详细信息应该与公司的应付账款或财务部门相关。
1. 验证联系人信息和首选联系方法，并选择“创建”。

如果由于所需的信用额度，我们需要运行信用检查，我们会发送给你信用检查应用程序。 我们可能会要求你提供经过审核的公司财务报表。 如果未提供任何财务信息，或者这些信息不足以支持所需的信用额度，我们可能会要求你提供保证金或备用信用证，以批准你的信用检查请求。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>切换到发票付款（支票/电汇）

获准按发票付款后，即可在 Azure 门户中切换到发票付款（支票/电汇）。

如果你有一个 Microsoft Online Services 计划帐户，可将 Azure 订阅切换为支票/电汇付款。 有了 Microsoft 客户协议，即可将计费对象信息切换为“支票/电汇”。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>将 Azure 订阅切换为支票/电汇

执行以下步骤，将 Azure 订阅切换为发票付款（支票/电汇）。 *切换为按发票付款（支票/电汇）后，无法切换回信用卡付款*。

1. 转到 Azure 门户，以帐户管理员身份登录。 搜索并选择“成本管理 + 计费”。  
    :::image type="content" source="./media/pay-by-invoice/search.png" alt-text="显示在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图。" lightbox="./media/pay-by-invoice/search.png" :::
1. 选择要切换为发票付款的订阅。
1. 选择“付款方式”。
1. 在命令栏中，选择“按发票付款”按钮。  
    :::image type="content" source="./media/pay-by-invoice/pay-by-invoice.png" alt-text="显示“按发票付款”的屏幕截图。" lightbox="./media/pay-by-invoice/pay-by-invoice.png" :::

### <a name="switch-billing-profile-to-checkwire-transfer"></a>将计费对象信息切换为支票/电汇

执行以下步骤，将计费对象信息切换为支票/电汇。 只有注册 Azure 的人才能更改计费对象信息的默认付款方式。

1. 转到 Azure 门户，查看计费信息。 搜索并选择“成本管理 + 计费”。
1. 在菜单中，选择“计费对象信息”。  
    :::image type="content" source="./media/pay-by-invoice/billing-profile.png" alt-text="显示“计费对象信息”菜单项的屏幕截图。" lightbox="./media/pay-by-invoice/billing-profile.png" :::
1. 选择计费对象信息。
1. 在“计费对象信息”菜单中，选择“付款方式”。  
   :::image type="content" source="./media/pay-by-invoice/billing-profile-payment-methods.png" alt-text="显示“付款方式”菜单项的屏幕截图。" lightbox="./media/pay-by-invoice/billing-profile-payment-methods.png" :::
1. 在“其他付款方式”标题下，选择省略号 (…)，然后选择“设为默认值”。  
    :::image type="content" source="./media/pay-by-invoice/customer-led-switch-to-invoice.png" alt-text="显示“支票/电汇”省略号和“设为默认值”选项的屏幕截图。" lightbox="./media/pay-by-invoice/customer-led-switch-to-invoice.png" :::

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="frequently-asked-questions"></a>常见问题

为什么我收到了要求我提供法律文件的请求？

如果客户提供的信息不完整或无法查证，Microsoft 有时会要求客户提供法律文档。 示例可能包括：

* 帐户名称与公司名称有何不同
* 名称变更

## <a name="next-steps"></a>后续步骤

* 根据需要在 [Azure 门户](https://portal.azure.com)更新账单联系人信息。