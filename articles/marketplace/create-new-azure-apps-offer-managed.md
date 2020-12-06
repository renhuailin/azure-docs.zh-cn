---
title: 配置托管应用程序计划
description: 了解如何在合作伙伴中心为 Azure 应用程序产品/服务配置托管应用程序计划。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744629"
---
# <a name="configure-a-managed-application-plan"></a>配置托管应用程序计划

本文仅适用于 Azure 应用程序产品/服务的托管应用程序计划。 如果要配置解决方案模板计划，请参阅 [配置解决方案模板计划](create-new-azure-apps-offer-solution.md)。

## <a name="define-markets-pricing-and-availability"></a>定义市场、定价和可用性

每个计划必须至少在一个市场中提供。 在 " **定价和可用性** " 选项卡上，你可以配置此计划将在其中可用的市场、价格，以及是要使计划对每个人可见，还是仅对特定客户可见 (也称为专用计划) 。

1. 在 " **市场**" 下，选择 " **编辑市场** " 链接。
1. 在出现的对话框中，选择要在其中提供计划的市场位置。 必须至少选择1个市场和141最大市场。

    > [!NOTE]
    > 此对话框包含一个搜索框和一个选项，用于仅筛选 "已汇款的" 国家/地区，其中，Microsoft 汇寄的销售和使用条款。

1. 选择 " **保存**" 以关闭对话框。

## <a name="define-pricing"></a>定义定价

在 " **价格** " 框中，提供此计划的每月价格。 此价格是此解决方案部署的资源所产生的任何 Azure 基础结构或基于使用情况的成本的补充。

除了每月价格之外，还可以使用[按流量计费](partner-center-portal/azure-app-metered-billing.md)为非标准单位的消耗设置价格。 如果需要，可以将每月价格设置为零，专门使用按流量计费来收费。

价格设置为 USD (USD = 美国美元) 将在保存时使用当前汇率转换为所有选定市场的本地货币。 在发布之前，通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单独市场中设置自定义价格，请修改并导入定价电子表格。

### <a name="add-a-custom-meter-dimension-optional"></a>添加自定义计量维度 (可选) 

1. 在 " **Marketplace 计量服务维度**" 下，选择 " **添加自定义计量维度 (最大 18)** " 链接。
1. 在 " **ID** " 框中，输入不可变标识符引用，同时发出使用事件。
1. 在 " **显示名称** " 框中，输入与维度关联的显示名称。 例如，"发送短信"。
1. 在 " **度量单位** " 框中，输入计费单位的说明。 例如，"每短信" 或 "每100电子邮件"。
1. 在 " **每单位价格（美元** ）" 框中，输入一个维度单位的价格。
1. 在 "基本" 框中包括的 " **每月数量** " 中，输入 (作为每月支付每月定期费用的客户所包含的维度的整数) 。 若要设置无限数量，请改为选中复选框。
1. 若要添加另一个自定义计量维度，请重复步骤1到7。

### <a name="set-custom-prices-optional"></a> (可选) 设置自定义价格

以 USD（USD = 美元）设置的价格在保存时会使用当前汇率转换为所有选定市场的本地货币。 在发布之前，通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单独市场中设置自定义价格，请修改并导入定价电子表格。

发布之前请仔细查看价格，因为发布计划之后对可以更改的内容有一些限制。

> [!NOTE]
> 发布计划中某个市场的价格后，无法再更改。

若要设置单个市场的自定义价格，请导出、修改并导入定价电子表格。 你负责验证此定价并拥有这些设置。 有关详细信息，请参阅 [自定义价格](plans-pricing.md#custom-prices)。

1. 必须先保存定价更改，才能实现定价数据的导出。 在 " **定价和可用性** " 选项卡的底部附近，选择 " **保存草稿**"。
1. 在 " **定价**" 下，选择 " **导出定价数据** " 链接。
1. 打开 Microsoft Excel 中的 exportedPrice.xlsx 文件。
1. 在电子表格中，将所需的更新作为定价信息，然后保存该文件。

   你可能需要在 Excel 中启用编辑，然后才能更新文件。

1. 在 " **定价和可用性** " 选项卡上的 " **定价**" 下，选择 " **导入定价数据** " 链接。
1. 在出现的对话框中，单击 **"是"**。
1. 选择更新的 exportedPrice.xlsx 文件，然后单击 " **打开**"。

## <a name="choose-who-can-see-your-plan"></a>选择可以查看你的计划的人员

你可以将每个计划配置为对每个用户可见，或仅针对特定受众。 你可以使用 Azure 订阅 Id 向专用用户授予访问权限，其中包含分配给每个订阅 ID 的说明。 你最多可以手动添加10个订阅 Id，或使用最多添加10000个订阅 Id。CSV 文件。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

> [!NOTE]
> 如果发布专用计划，则可以在以后将其可见性更改为 "公共"。 但是，在发布公共计划后，不能将其可见性更改为私有。

在 " **计划可见性**" 下，执行以下操作之一：

- 若要使该计划成为公共计划，请选择 " **公共** " 选项按钮 (也称为 _单选按钮_) 。
- 要使该计划成为私有计划，请选择 " **专用** " 选项按钮，然后手动或使用 CSV 文件添加 Azure 订阅 id。

> [!NOTE]
> 专用或受限制的受众不同于你在 " **预览** " 选项卡上定义的预览观众。预览版观众在 marketplace 发布之前，可以访问产品/服务。 专用受众选项仅适用于特定计划，而预览受众可以查看所有计划（专用或非专用）以进行验证。

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>手动为私有计划添加 Azure 订阅 Id

1. 在 " **计划可见性**" 下，选择 " **专用** " 选项按钮。
1. 在出现的 " **Azure 订阅 id** " 框中，输入想要授予对此私有计划的访问权限的受众的 AZURE 订阅 id。 至少需要一个订阅 ID。
1.  (可选) 在 " **说明** " 框中输入此受众的说明。
1. 若要添加其他订阅 ID，请选择 " **添加 ID (最大 10)** " 链接，并重复步骤2和3。

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>使用。用于为私有计划添加 Azure 订阅 Id 的 CSV 文件

1. 在 " **计划可见性**" 下，选择 " **专用** " 选项按钮。
1. 选择 " **导出访问群体 (csv)** " 链接。
1. 打开。CSV 文件，并添加要向其授予对专用产品/服务的访问 **权限的 Azure** 订阅 id。
1. （可选）在 " **说明** " 列中为每个受众输入描述。
1. 在 " **类型** " 列中，为每个具有订阅 ID 的行添加 "SubscriptionId"。
1. 保存。CSV 文件。
1. 在 " **可用性** " 选项卡上的 " **计划可见性**" 下，选择 " **导入访问群体 (csv)** " 链接。
1. 在出现的对话框中，选择 **"是"**。
1. 选择。CSV 文件，然后选择 " **打开**"。 此时将显示一条消息，指示。已成功导入 CSV 文件。

## <a name="define-the-technical-configuration"></a>定义技术配置

在 " **技术配置** " 选项卡上，将上载部署包，以便客户部署计划并为包提供版本号。 你还将提供其他技术信息。

> [!NOTE]
> 如果在 " **计划设置** " 选项卡上选择重新使用其他计划中的包，则此选项卡将不可见。如果是，请参阅 [查看你的计划](#view-your-plans)。

### <a name="assign-a-version-number-for-the-package"></a>为包分配版本号

在 " **版本** " 框中提供技术配置的当前版本。 每次将更改发布到此页时，都会递增此版本。 版本号的格式必须为：整数。 例如，`1.0.2`。

### <a name="upload-a-package-file"></a>上传包文件

在 " **包文件 ( .zip)** 下，将包文件拖到灰色框，或选择" **浏览文件 (")** 链接。

> [!NOTE]
> 如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 `https://upload.xboxlive.com` 服务。

#### <a name="previously-published-packages"></a>以前发布的包

“以前发布的包”子选项卡使你可以查看技术配置的所有已发布版本。

### <a name="enable-just-in-time-jit-access-optional"></a>启用实时 (JIT) 访问 (可选) 

若要启用此计划的 JIT 访问，请选中 " **启用实时 (JIT) 访问** " 复选框。 若要要求托管应用程序的使用者向你的帐户授予永久访问权限，请将此选项保留为未选中状态。 若要了解有关此选项的详细信息，请参阅实时 [ (JIT) 访问](plan-azure-app-managed-app.md#just-in-time-jit-access)。

### <a name="select-a-deployment-mode"></a>选择部署模式

选择 " **完成** " 或 " **增量** " 部署模式。

- 在 **完整** 模式下，如果未在的 [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)中定义资源，则客户重新部署应用程序将导致删除托管资源组中的资源。
- 在 **增量** 模式下，应用程序的重新部署会使现有资源保持不变。

若要详细了解部署模式，请参阅 [Azure 资源管理器部署模式](../azure-resource-manager/templates/deployment-modes.md)。

### <a name="provide-a-notification-endpoint-url"></a>提供通知终结点 URL

在 " **通知终结点 URL** " 框中，提供 HTTPS Webhook 终结点，以接收有关此计划版本的托管应用程序实例上的所有 CRUD 操作的通知。

### <a name="customize-allowed-customer-actions-optional"></a>自定义允许的客户操作 (可选) 

1. 若要指定客户可以对托管资源执行的操作以及默认可用的 " `*/read` " 操作，请选择 " **自定义允许的客户操作** " 框。
1. 在出现的框中，提供要使客户能够执行的其他控制操作和允许的数据操作（用分号分隔）。 例如，若要允许使用者重新启动虚拟机，请将添加 `Microsoft.Compute/virtualMachines/restart/action` 到 " **允许的控制操作** " 框。

### <a name="choose-who-can-manage-the-application"></a>选择可以管理应用程序的人员

指示应在每个所选 Azure 区域中拥有此托管应用程序的管理访问权限： _全局 azure_ 和 _azure 政府云_。 你将使用 Azure AD 标识来确定你想要授予对托管资源组的权限的用户、组或应用程序。 有关详细信息，请参阅 [规划 Azure 托管应用程序的 Azure 应用程序产品](plan-azure-application-offer.md)。

在适用的情况下，针对全球 Azure 和 Azure 政府云完成以下步骤。

1. 在 " **Azure Active Directory 租户 id** " 框中，输入 "AZURE AD 租户 id" (也称为目录 id) ，其中包含要向其授予权限的用户、组或应用程序的标识。
1. 在 " **主体 ID** " 框中，提供要向其授予对托管资源组的权限的用户、组或应用程序的 AZURE AD 对象 ID。 按用户的主体 ID 标识用户，可在 Azure 门户上的 [Azure Active Directory 用户 "边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 中找到该用户。
1. 从 " **角色定义** " 列表中，选择一个 Azure AD 的内置角色。 你选择的角色将描述主体对客户订阅中的资源所具有的权限。
1. 若要添加另一个授权，请选择 " **添加授权 (最大 100)** " 链接，并重复步骤1到3。

### <a name="policy-settings-optional"></a>策略设置 (可选) 

最多可以配置五个策略，每个策略选项只有一个实例。 某些策略需要附加参数。

1. 在 " **策略设置**" 下，选择 " **+ 添加策略 (max 5)** " 链接。
1. 在 " **名称** " 框中，输入 (限制为50个字符) 的策略分配名称。
1. 从 " **策略** " 列表框中，选择将应用于客户订阅中的托管应用程序创建的资源的 Azure 策略。
1. 在 " **策略参数** " 框中，提供要对其应用审核和诊断设置策略的参数。
1. 从 " **策略 sku** " 列表框中，选择策略 sku 类型。

    > [!NOTE]
    > 审核策略需要 _标准策略_ SKU。

## <a name="view-your-plans"></a>查看计划

- 选择 " **保存草稿**"，然后在页面的左上方，选择 " **计划概述** " 以返回到 " **计划概述** " 页。

创建一个或多个计划后，你将在 " **计划概述** " 选项卡上看到计划名称、计划 ID、计划类型、可用性 (公共或私有) 、当前发布状态和任何可用操作。

"**计划概述**" 选项卡的 "**操作**" 列中的可用操作取决于计划的状态，其中可能包括以下内容：

- 如果计划状态为 " **草稿**"，则 " **操作** " 列中的链接将显示 " **删除草稿**"。
- 如果计划状态为 " **活动**"，则 " **操作** " 列中的链接将是 " **停止销售计划** " 或 " **同步专用受众**"。 " **同步专用用户** " 链接将仅发布对专用用户的更改，而不会发布对产品/服务的任何其他更新。
- 若要为此服务创建另一个计划，请在 " **计划概述** " 选项卡的顶部选择 " **+ 创建新计划**"。 然后，重复 [如何为 Azure 应用程序产品/服务创建计划](create-new-azure-apps-offer-plans.md)中的步骤。 否则，如果你已完成创建计划，请参阅下一节：后续步骤。

## <a name="next-steps"></a>后续步骤

- [如何测试和发布 Azure 应用程序产品/服务](create-new-azure-apps-offer-test-publish.md)。
- 了解如何通过与 Microsoft 共同销售并转售通过 Csp 计划的 [方式销售 Azure 应用程序产品](create-new-azure-apps-offer-marketing.md) 。