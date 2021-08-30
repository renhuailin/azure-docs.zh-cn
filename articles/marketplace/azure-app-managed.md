---
title: 配置托管的应用程序计划
description: 在合作伙伴中心（Azure 市场）为 Azure 应用程序产品/服务配置托管的应用程序计划。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 666dfb22036c3d7f1d88515f4b8118eaf133db8c
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113594943"
---
# <a name="configure-a-managed-application-plan"></a>配置托管的应用程序计划

本文仅适用于 Azure 应用程序产品/服务的托管应用程序计划。 如果要配置解决方案模板计划，请参阅[配置解决方案模板计划](azure-app-solution.md)。

## <a name="re-use-technical-configuration-optional"></a>重复使用技术配置（可选）

如果在产品/服务内创建了多个相同类型的计划，并且它们之间的技术配置相同，则可以重复使用其他计划中的技术配置。 此计划发布之后便无法更改此设置。

若要重复使用技术配置：

1. 选中“此计划重复使用同种类型的其他计划中的技术配置”复选框。
1. 在出现的列表中，选择所需的基本计划。

> [!NOTE]
> 如果计划当前正在重复使用，或已重复使用另一个相同类型的计划的技术配置，请转到该基本计划以查看以前发布的包的历史记录。

## <a name="define-markets-pricing-and-availability"></a>定义市场、定价和可用性

每个计划必须至少在一个市场中提供。 在“定价和可用性”选项卡上，可以配置将提供该计划的市场、价格以及将计划设为对所有人可见还是仅对特定客户可见（也称为专用计划）。

1. 在“市场”下，选择“编辑市场”链接。 
1. 在出现的对话框中，选择要在其中提供计划的市场位置。 必须至少选择 1 个市场，最多可以选择 141 个市场。

    > [!NOTE]
    > 该对话框包括一个搜索框和一个选项，可筛选出“免税”国家/地区（Microsoft 代表你在这些国家/地区减免销售和使用税）。

1. 选择“保存”以关闭该对话框。

## <a name="define-pricing"></a>定义定价

在“定价”框中，提供此计划的每月价格。 此价格不包括在此解决方案部署的资源所产生的任何 Azure 基础结构或基于使用量的成本中。

除了每月价格之外，还可以使用[按流量计费](./azure-app-metered-billing.md)为非标准单位的消耗设置价格。 如果需要，可以将每月价格设置为零，专门使用按流量计费来收费。

以 USD（USD=美元）设置的价格在保存时会使用当前汇率转换为所有选定市场的当地币种。 在发布之前，通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单独市场中设置自定义价格，请修改并导入定价电子表格。

### <a name="add-a-custom-meter-dimension-optional"></a>添加自定义计量维度（可选）

1. 在“市场计量服务维度”下面，选择“添加自定义计量维度（最多 18 个）”链接。 
1. 在“ID”框中，输入在发送使用量事件时引用的不可变标识符。
1. 在“显示名称”框中，输入与维度关联的显示名称。 例如，“短信已发送”。
1. 在“度量单位”框中，输入计费单位的说明。 例如，“每条短信”或“每 100 封电子邮件”。
1. 在“单位价格（美元）”框中，输入一个维度单位的价格。
1. 在“包括在每月中的数量”框中，输入对于按月重复付费的客户，每个月中包含的维度数量（以整数形式）。 要设置无限数量，请改为选中复选框。
1. 要添加另一个自定义计量维度，请重复步骤 1 到 7。

### <a name="set-custom-prices-optional"></a>设置自定义价格（可选）

以 USD（USD = 美元）设置的价格在保存时会使用当前汇率转换为所有选定市场的本地货币。 在发布之前，通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单独市场中设置自定义价格，请修改并导入定价电子表格。

发布之前请仔细查看价格，因为发布计划之后对可以更改的内容有一些限制。

> [!NOTE]
> 发布计划中某个市场的价格后，无法再更改。

要在单独市场中设置自定义价格，请先导出定价电子表格并进行修改，然后再导入。 你负责验证此定价并拥有这些设置。 有关详细信息，请参阅[自定义价格](plans-pricing.md#custom-prices)。

1. 必须先保存定价更改，才能实现定价数据的导出。 在“定价和可用性”选项卡底部附近，选择“保存草稿”。 
1. 在“定价”下面，选择“导出定价数据”链接。 
1. 在 Microsoft Excel 中打开 exportedPrice.xlsx 文件。
1. 在电子表格中，对价格信息进行所需的更新，然后保存文件。

   可能需要先在 Excel 中启用编辑，然后才能更新文件。

1. 在“定价和可用性”选项卡上的“定价”下，选择“导入定价数据”。  
1. 在出现的对话框中，单击“是”。
1. 选择已更新的 exportedPrice.xlsx 文件，然后单击“打开”。

## <a name="choose-who-can-see-your-plan"></a>选择计划开放对象

可以将每个计划配置为对每个人都可见，或者仅对特定受众可见。 你可以通过用于包含所分配的每个订阅 ID 的说明的选项，使用 Azure 订阅 ID 来为私有受众分配访问权限。 最多可以手动添加 10 个订阅 ID，使用 .CSV 文件最多可添加 10,000 个订阅 ID。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

> [!NOTE]
> 如果发布专用计划，以后仍可将其设为公用。 但发布公用计划后无法改设为专用。

在“计划可见性”下面，执行以下任一操作：

- 要将计划设为公共，请选择“公共”选项按钮（也称为单选按钮）。
- 要将计划设为私有，请选择“私有”选项按钮，然后手动或使用 CSV 文件添加 Azure 订阅 ID。

> [!NOTE]
> 专用或受限受众与在“预览”选项卡上定义的预览受众不同。预览受众可以在你的产品/服务尚未在市场中发布之前提前访问。 专用受众选项仅适用于特定计划，而预览受众可以查看所有计划（专用或非专用）以进行验证。

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>手动为私有计划添加 Azure 订阅 ID

1. 在“计划可见性”下，选择“私有”选项按钮。
1. 在出现的“Azure 订阅 ID”框中，输入要授予对该私有计划访问权限的受众的 Azure 订阅 ID。 至少需要一个订阅 ID。
1. （可选）在“描述”框中输入该访问群体的描述。
1. 要添加另一个订阅 ID，请选择“添加 ID（最多 10 个）”链接，然后重复步骤 2和 3。

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>使用 .CSV 文件为私有计划添加 Azure 订阅 ID

1. 在“计划可见性”下，选择“私有”选项按钮。
1. 选择“导出受众 (csv)”链接。
1. 打开 .CSV 文件，然后将要授予私有产品/服务的访问权限的 Azure 订阅 ID 添加到 ID 列中。
1. （可选）在“描述”列中输入每个受众的描述。
1. 对于具有预订 ID 的每一行，在“类型”列中添加“SubscriptionId”。
1. 保存 .CSV 文件。
1. 在“可用性”选项卡上的“计划可见性”下，选择“导入受众 (csv)”链接。
1. 在显示的对话框中，选择“是”。
1. 选择 CSV 文件，然后选择“打开”。 出现一条消息，指示 .CSV 文件已成功导入。

## <a name="define-the-technical-configuration"></a>定义技术配置

在“技术配置”选项卡上，你将上传部署包并提供包的版本号，客户可以通过这个部署包来部署你的计划。 你还将提供其他技术信息。

> [!NOTE]
> 如果已选择在“计划设置”选项卡上重复使用来自另一个计划的包，则此选项卡将不可见。如果是这样，请转到[查看计划](#view-your-plans)。

### <a name="assign-a-version-number-for-the-package"></a>分配包的版本号

在“版本”框中，提供技术配置的当前版本。 每次将更改发布到此页时，都会递增此版本。 版本号必须采用以下格式：整数.整数.整数。 例如，`1.0.2`。

### <a name="upload-a-package-file"></a>上传包文件

在“包文件 (.zip)”下，将包文件拖到灰色框，或选择“浏览文件”链接。

> [!NOTE]
> 如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 `https://upload.xboxlive.com` 服务。

#### <a name="previously-published-packages"></a>以前发布的包

“以前发布的包”子选项卡使你可以查看技术配置的所有已发布版本。

### <a name="enable-just-in-time-jit-access-optional"></a>启用实时 (JIT) 访问（可选）

要为该计划启用 JIT 访问，请选中“启用即时 (JIT) 访问”复选框。 若要要求托管应用程序的使用者向你的帐户授予永久访问权限，请将此选项保留为未选中状态。 要了解有关该选项的详细信息，请参阅[即时 (JIT) 访问](plan-azure-app-managed-app.md#just-in-time-jit-access)。

### <a name="select-a-deployment-mode"></a>选择部署模式

选择“完整”或“增量”部署模式。 

- 在“完整”模式下，若 [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template) 中未定义资源，客户重新部署应用程序会导致托管资源组被删。
- 在“增量模式”下，重新部署应用程序会使现有资源保持不变。

若要详细了解部署模式，请参阅 [Azure 资源管理器部署模式](../azure-resource-manager/templates/deployment-modes.md)。

### <a name="provide-a-notification-endpoint-url"></a>提供通知终结点 URL

在“通知终结点 URL”框中，提供 HTTPS Webhook 终结点以接收有关此计划版本的托管应用程序实例上所有 CRUD 操作的通知。

### <a name="customize-allowed-customer-actions-optional"></a>自定义允许的客户操作（可选）

1. 要指定除了默认提供的“`*/read`”操作外，客户还可以对托管资源执行哪些操作，请选中“自定义允许的客户操作”框。
1. 在出现的框中，提供其他数据操作以及你希望客户能够执行的允许数据操作（用分号分隔）。 例如，要允许使用者重新启动虚拟机，请将 `Microsoft.Compute/virtualMachines/restart/action` 添加到“允许的控制操作”框中。

### <a name="choose-who-can-manage-the-application"></a>选择哪些用户可以管理应用程序

指出哪个用户应该在每个选定的 Azure 区域（_全球 Azure_ 和 _Azure政府云_）中对该管理应用程序具有管理访问权限。 你将使用 Azure AD 身份标识要授予对托管资源组权限的用户、组或应用程序。 有关更多信息，请参阅[为 Azure 应用程序产品/服务创建 Azure 托管应用程序计划](plan-azure-application-offer.md)。

在适用的情况下，针对 Global Azure 和 Azure Government Cloud 完成以下步骤。

1. 在“Azure Active Directory 租户 ID”框中，输入包含要向其授予权限的用户、组合或应用程序标识的 Azure AD 租户 ID （也称作目录 ID）。
1. 在“主体”框中，提供要向其授予托管资源组权限的用户、组或应用程序的 Azure AD 对象 ID。 通过其主体 ID 标识用户，该 ID 可在 [Azure 门户上的 Azure Active Directory 用户边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到。
1. 从“角色定义”列表中，选择一个 Azure AD 内置角色。 选定角色描述主体对客户订阅中的资源拥有的权限。
1. 要添加其他授权，请选择“添加授权（最多 100 个）”链接，然后重复步骤 1 至 3。

### <a name="policy-settings-optional"></a>策略设置（可选）

最多可以配置五个策略，每个策略选项只有一个实例。 某些策略需要附加参数。

1. 在“策略设置”下，选择“+ 添加策略（最多 5 个）”链接。 
1. 在“名称”框中，输入策略分配名称（最多 50 个字符）。
1. 从“策略”列表框中，选择 Azure 策略，该策略将应用于客户订阅中的托管应用程序创建的资源。
1. 在“策略参数”框中，提供要对其应用审核和诊断设置策略的参数。
1. 从“策略 SKU”列表框中，选择策略 SKU 类型。

    > [!NOTE]
    > “标准策略”SKU 对于审核策略是必需的。

## <a name="view-your-plans"></a>查看计划

- 选择“保存草稿”，然后在页面的左上方，选择“计划概述”以返回到“计划概览”页面。

创建一个或多个计划后，你将在“计划概述”选项卡上看到你的计划名称、计划 ID、计划类型、可用性（公共或私有）、当前发布状态以及任何可用的操作。

“计划概述”选项卡的“操作”列中可用的操作根据你的计划状态而有所不同，并且可能包括以下内容：

- 如果计划状态为“草稿”，则“操作”列中的链接将显示“删除草稿”。
- 如果计划状态为“实时”，则“操作”列中的链接将为“停止分发”或“同步私有受众”。 “同步私有受众”链接将仅向你的私有受众发布更改，而不会发布你可能对产品/服务进行的任何其他更新。
- 要为此产品/服务创建另一个计划，请在“计划概述”选项卡顶部，选择“+ 创建新计划”。 然后重复[如何为 Azure 应用程序产品/服务创建计划](azure-app-plans.md)中的步骤。 否则，如果你已完成计划的创建，请参阅下一节：后续步骤。

## <a name="next-steps"></a>后续步骤

- [测试和发布 Azure 应用程序产品/服务](azure-app-test-publish.md)。
- 通过“与 Microsoft 进行联合销售”计划和/或“通过 CSP 进行转售”计划来[销售 Azure 应用程序产品/服务](azure-app-marketing.md)。