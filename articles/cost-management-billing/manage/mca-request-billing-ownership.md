---
title: 转让 Microsoft 客户协议的 Azure 订阅计费所有权
description: 了解如何转让 Azure 订阅的计费所有权。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/26/2021
ms.author: banders
ms.openlocfilehash: 6654098b7fe2b212ac87d4ae52feaed128579dc5
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114711351"
---
# <a name="transfer-azure-subscription-billing-ownership-for-a-microsoft-customer-agreement"></a>转让 Microsoft 客户协议的 Azure 订阅计费所有权

在以下情况下，需要转移 Azure 订阅的计费所有权：
- 要将订阅的计费责任转移到其他计费所有者。
- 要将 Azure 订阅从一个许可协议转移到另一个许可协议。 例如，从企业协议或 Microsoft 在线订阅协议 (MOSA) 转移到 Microsoft 客户协议。 [检查你是否有权访问 Microsoft 客户协议](#check-for-access)。

你向其请求计费所有权的人员须具有以下角色之一：
- 对于 Microsoft 客户协议，此人必须具有计费帐户或者相关计费配置文件或发票科目的所有者或参与者角色。 有关详细信息，请参阅[计费角色和任务](understand-mca-roles.md#invoice-section-roles-and-tasks)。
- 对于企业协议，此人必须是帐户所有者。
- 对于 Microsoft 在线订阅协议，此人必须是帐户管理员。

## <a name="request-billing-ownership"></a>请求计费所有权

1. 以 Microsoft 客户协议计费帐户的发票科目所有者或参与者身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. 在计费范围页中，选择“计费范围”，然后选择用于支付订阅使用费用的计费帐户。 计费帐户的类型应为 **Microsoft 客户协议**。  
    [![显示“成本管理 + 计费”门户搜索的屏幕截图](./media/mca-request-billing-ownership/list-of-scopes.png)](./media/mca-request-billing-ownership/list-of-scopes.png#lightbox)
    > [!NOTE]
    > Azure 门户会记住你访问的最后一个计费范围，并在你下一次转到“成本管理 + 计费”页时显示该范围。 如果你早前访问过“成本管理 + 计费”，则看不到计费范围页。 如果是这样，请检查你是否处于[正确的范围](#check-for-access)。 否则，请[切换范围](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)，选择 Microsoft 客户协议的计费帐户。
1. 在左侧选择“计费对象信息”。  
    [![屏幕截图显示正在选择计费对象信息](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
    > [!Note]
    > 如果看不到“计费对象信息”，则表明你未处于正确的计费范围。 需要为 Microsoft 客户协议选择计费帐户，然后选择“计费对象信息”。 若要了解如何更改范围，请参阅[在 Azure 门户中切换计费范围](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)。
1. 从列表中选择“计费对象信息”。 在你接管订阅所有权后，系统会根据此计费对象信息计收订阅使用费用。
1. 在左侧选择“发票科目”。  
    [![屏幕截图显示正在选择发票部分](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. 从列表中选择一个发票科目。 在你接管订阅的所有权后，系统会将其使用情况分配到计费对象信息的发票的此部分。
1. 选择左下角的“转移请求”，然后选择“添加新请求”。   
    [![显示正在选择转移请求的屏幕截图](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests.png#lightbox)
1. 输入要从其请求计费所有权的用户的电子邮件地址。 选择“发送转移请求”。  
    [![显示正在发送转移请求的屏幕截图](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)](./media/mca-request-billing-ownership/mca-send-transfer-requests.png#lightbox)
1. 用户将收到一封电子邮件，其中包含查看转让请求的说明。  
    ![显示查看转移请求电子邮件的屏幕截图](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)
1. 若要批准转移请求，用户需选择电子邮件中的链接，并按说明操作。
    [![显示评审转移请求的屏幕截图](./media/mca-request-billing-ownership/review-transfer-requests.png)](./media/mca-request-billing-ownership/review-transfer-requests.png#lightbox) 用户可以选择要从中转移 Azure 产品的计费帐户。 选择后，将显示可转移的合格产品。 **注意：** 无法转移禁用的订阅，如果适用，这些订阅将显示在“不可转移的 Azure 产品”列表中。 选择了要转移的 Azure 产品后，请选择“验证”。
1. “转移验证结果”区域将显示要转移的 Azure 产品的影响。 可能的状态有：
    * “通过”- 此 Azure 产品已通过验证，可以进行转移。
    * “警告”- 所选 Azure 产品出现警告。 虽然仍可转移该产品，但这样做会产生一些影响，如果用户希望采取缓解措施，则他们应该了解这些影响。 例如，要转移的 Azure 订阅从 RI 中获益。 转移后，订阅将不再获得该权益。 若要最大程度地节省成本，请确保 RI 关联到可使用其权益的另一订阅。 此外，用户还可以选择返回到“选择”页并取消选择此 Azure 订阅。
    * “失败”- 由于出现错误，无法转移所选的 Azure 产品。 用户需要返回“选择”页并取消选择此产品以转移其他选定的 Azure 产品。  
    ![显示验证体验的屏幕截图](./media/mca-request-billing-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>检查转移请求状态

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. 在计费范围页中，选择已发送其转账请求的计费帐户。
1. 在左侧选择“计费对象信息”。  
    [![屏幕截图显示正在选择计费对象信息](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
1. 选择已发送其转账请求的 **计费对象信息**。
1. 在左侧选择“发票科目”。  
    [![屏幕截图显示正在选择发票部分](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. 从列表中选择已发送其转账请求的发票科目。
1. 选择左下角的“转移请求”。 “转移请求”页将显示以下信息：  
    [![显示转移请求列表的屏幕截图](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png#lightbox)

   |列|定义|
   |---------|---------|
   |请求日期|发送转移请求的日期|
   |Recipient|已向其发送转移计费所有权请求的用户的电子邮件地址|
   |到期日期|请求过期的日期|
   |状态|转移请求的状态|

    转移请求可能处于以下状态之一：

   |状态|定义|
   |---------|---------|
   |正在进行|用户未接受转移请求|
   |Processing|用户已批准转移请求。 用户选择的订阅的计费正在转移到你的发票科目|
   |已完成| 用户选择的订阅的计费已转移到你的发票科目|
   |已完成但出错|请求已完成，但用户选择的某些订阅的计费无法转移|
   |已过期|用户未及时接受请求，请求现已过期|
   |已取消|有权访问转移请求的某人取消了该请求|
   |已拒绝|用户拒绝了转移请求|

1. 选择一个转移请求以查看详细信息。 转移详细信息页将显示以下信息：  
    [![显示已转移订阅列表的屏幕截图](./media/mca-request-billing-ownership/mca-transfer-completed.png)](./media/mca-request-billing-ownership/mca-transfer-completed.png#lightbox)

   |列  |定义|
   |---------|---------|
   |转移请求 ID|转移请求的唯一 ID。 提交支持请求时，请与 Azure 支持人员共享该 ID，以加速你的支持请求的处理|
   |转移请求日期|发送转移请求的日期|
   |转移请求者|发送转移请求的用户的电子邮件地址|
   |转移请求过期日期| 转移请求的过期日期|
   |收件人的电子邮件地址|已向其发送转移计费所有权请求的用户的电子邮件地址|
   |发送给收件人的转移链接|发送给用户的 URL，用于查看转移请求|

## <a name="supported-subscription-types"></a>支持的订阅类型

可以请求下面列出的订阅类型的计费所有权。

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 赞助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 赞助套餐](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft 客户协议](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 订户](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 订户](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 订户](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 订户](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* 转移后，订阅中可用的任何额度在新帐户中将不可用。

\*\* 仅支持在 Azure 网站上注册期间创建的帐户中的订阅。

## <a name="additional-information"></a>其他信息

以下部分提供有关转移订阅的附加信息。

### <a name="cancel-a-prior-support-plan"></a>取消以前的支持计划

如果你有一个 Azure 支持计划并想要将所有 Azure 订阅转移到新协议，则必须取消该支持计划，因为它不会随订阅一起转移。 例如，在将 Microsoft 在线订阅协议（在 Web 上购买的 Azure 订阅）转移到 Microsoft 客户协议时，就需要这样做。 若要取消支持计划，请执行以下操作：

如果旧帐户的帐户管理员凭据不同于用于访问新 Microsoft 客户协议帐户的凭据，则必须使用旧帐户的凭据。

1.  通过 https://portal.azure.com 登录到 Azure 门户。
1.  导航到“成本管理 + 计费”。
1.  在左侧窗格中选择“计费范围”。
1.  选择与你的 Microsoft 支持计划关联的计费帐户。
    - 对于 Microsoft 客户协议：
        - 在左侧窗格中选择“周期性费用”。
        - 在支持计划行项右侧的右侧窗格中，选择省略号图标 (...)，然后选择“关闭自动续订” 。
    - 对于 Microsoft 在线订阅协议 (MOSA)：
        - 在左侧窗格中选择“订阅”。
        - 在右侧窗格中选择支持计划订阅，然后选择“取消”。

### <a name="access-your-historical-invoices"></a>访问历史发票

在将计费所有权转移到新的 Microsoft 客户协议帐户后，你可能想要访问旧 Microsoft 在线订阅协议帐户（在 Web 上购买的 Azure 订阅）的发票。 为此，请按照以下步骤操作：

如果旧帐户的帐户管理员凭据不同于用于访问新 Microsoft 客户协议帐户的凭据，则必须使用旧帐户的凭据。

1.  通过 https://portal.azure.com/ 登录到 Azure 门户。
1.  导航到“成本管理 + 计费”。
1.  在左侧窗格中选择“计费范围”。
1.  选择与你的 Microsoft 在线订阅协议帐户关联的计费帐户。
1.  在左侧窗格中选择“发票”以访问历史发票。

### <a name="no-service-downtime"></a>服务不会中断

订阅中的 Azure 服务将保持正常运行，而不会出现任何中断。 我们只会转换用户选择转移的 Azure 订阅的计费关系。

### <a name="disabled-subscriptions"></a>已禁用的订阅

无法转移已禁用的订阅。 订阅必须处于活动状态才能转移其计费所有权。

### <a name="azure-resources-transfer"></a>Azure 资源转移

订阅中的所有资源（例如 VM、磁盘和网站）都会转移。

### <a name="azure-marketplace-products-transfer"></a>Azure 市场产品转移

Azure 市场产品将连同各自的订阅一起转移。

### <a name="azure-reservations-transfer"></a>Azure 预留项转移

如果正在转移企业协议 (EA) 订阅或 Microsoft 客户协议，则 Azure 预留会自动与订阅一起移动。

### <a name="access-to-azure-services"></a>对 Azure 服务的访问权限

在转换期间，使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 为现有用户、组或服务主体分配的访问权限不受影响。

### <a name="charges-for-transferred-subscription"></a>转移的订阅费用

订阅的原始计费所有者负责支付转移完成之前所报告的所有费用。 你的发票科目负责支付从转移开始时报告的费用。 可能有些费用是在转移之前发生的，但在转移之后才报告。 这些费用将显示在你的发票科目中。

### <a name="cancel-a-transfer-request"></a>取消转移请求

在批准或拒绝转移请求之前，你可以取消该请求。 若要取消转移请求，请转到[转移详细信息页](#check-the-transfer-request-status)，并在页面底部选择“取消”。

### <a name="software-as-a-service-saas-transfer"></a>软件即服务 (SaaS) 转移

SaaS 产品不会随订阅一起转移。 请让用户[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)转移 SaaS 产品的计费所有权。 除了计费所有权以外，用户还可以转移资源所有权。 使用资源所有权可以执行管理操作，例如删除产品，以及查看产品详细信息。 用户必须是 SaaS 产品的资源所有者才能转移资源所有权。

## <a name="check-for-access"></a>检查访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- Azure 订阅的计费所有权现已转移到你的发票科目。 可在 [Azure 门户](https://portal.azure.com)中跟踪这些订阅的费用。
- 为其他人授予查看和管理这些订阅的计费的权限。 有关详细信息，请参阅[发票科目角色和任务](understand-mca-roles.md#invoice-section-roles-and-tasks)。
