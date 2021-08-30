---
title: Azure EA 门户管理
description: 本文介绍管理员可在 Azure EA 门户中完成的常见任务。
author: bandersmsft
ms.author: banders
ms.date: 06/22/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: a973fc9dc9cf4b4fe514ddf987fd2052c46a6e6b
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112519740"
---
# <a name="azure-ea-portal-administration"></a>Azure EA 门户管理

本文介绍管理员可在 Azure EA 门户 (https://ea.azure.com) 中完成的常见任务。 Azure EA 门户是一个在线管理门户，可帮助客户管理其 Azure EA 服务的成本。 有关 Azure EA 门户的简介信息，请参阅 [Azure EA 门户入门](ea-portal-get-started.md)一文。

## <a name="activate-your-enrollment"></a>激活注册

若要激活服务，最初的企业管理员需打开 [Azure Enterprise 门户](https://ea.azure.com)，并使用邀请电子邮件中的电子邮件地址登录。

如果你已被设置为企业管理员，则无需接收激活电子邮件。 转到 [Azure Enterprise 门户](https://ea.azure.com)，并使用工作、学校帐户或 Microsoft 帐户电子邮件地址和密码登录。

如果你有多个注册，请选择要激活的注册。 默认只会显示活动的注册。 若要查看注册历史记录，请清除 Azure Enterprise 门户右上角的“活动”选项。

在“注册”下，状态将显示为“活动”。 

![显示活动注册的示例](./media/ea-portal-administration/ea-enrollment-status.png)

只有现有的 Azure 企业管理员才能创建其他企业管理员。

### <a name="create-another-enterprise-administrator"></a>创建另一个企业管理员

根据你的情况，使用以下选项之一。

#### <a name="if-youre-already-an-enterprise-administrator"></a>如果你已经是一名企业管理员

1. 登录到 [Azure Enterprise 门户](https://ea.azure.com)。
1. 转到“管理” > “注册详细信息”。 
1. 选择右上角的“+ 添加管理员”。

确保已获取用户的电子邮件地址和首选的身份验证方法，例如工作、学校帐户或 Microsoft 帐户。

#### <a name="if-youre-not-an-enterprise-administrator"></a>如果你不是企业管理员

如果你不是企业管理员，请联系企业管理员，让他们将你添加到注册。 企业管理员使用上述步骤将你添加为企业管理员。 被添加到注册后，你会收到一封激活电子邮件。

#### <a name="if-your-enterprise-administrator-cant-help-you"></a>如果企业管理员无法为你提供帮助

如果企业管理员无法为你提供帮助，请创建 [Azure 支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 提供以下信息：

- 注册编号
- 要添加的电子邮件地址和身份验证类型（工作、学校帐户或 Microsoft 帐户）
- 现有企业管理员做出的电子邮件批准
  - 如果无法联系到现有的企业管理员，请联系合作伙伴或软件顾问，让他们通过批量许可服务中心 (VLSC) 工具更改联系人详细信息。

## <a name="create-an-azure-enterprise-department"></a>创建 Azure Enterprise 部门

企业管理员及部门管理员可以使用部门来按部门和成本中心对企业 Azure 服务与使用情况进行组织和报告。 企业管理员可以：

- 添加或删除部门。
- 将帐户关联到部门。
- 创建部门管理员。
- 允许部门管理员查看价格和成本。

部门管理员可将新帐户添加到其部门。 他们可以从其部门中删除帐户，但不能从注册中删除帐户。

若要添加部门：

1. 登录到 Azure Enterprise 门户。
1. 在左侧窗格中选择“管理”。
1. 选择“部门”选项卡，然后选择“+ 添加部门”。 
1. 输入信息。
   只有部门名称是必填字段。 该名称必须至少包含三个字符。
1. 完成后，选择“添加”。

## <a name="add-a-department-administrator"></a>添加部门管理员

创建部门后，企业管理员可以添加部门管理员并将每个管理员关联到部门。 部门管理员可针对其部门执行以下操作：

- 创建其他部门管理员
- 查看和编辑部门属性，例如名称或成本中心
- 添加帐户
- 删除帐户
- 下载使用情况详细信息
- 查看每月使用情况和费用 <sup>1</sup>

> <sup>1</sup> 企业管理员必须授予这些权限。 如果你有权查看部门每月使用情况和费用，但却看不到这些信息，请与合作伙伴联系。

### <a name="to-add-a-department-administrator"></a>添加部门管理员

以企业管理员的身份：

1. 登录到 Azure Enterprise 门户。
1. 在左侧窗格中选择“管理”。
1. 选择“部门”选项卡，然后选择部门。
1. 选择“+ 添加管理员”并添加所需的信息。
1. 若要授予只读访问权限，请将“只读”选项设置为“是”，然后选择“添加”。  

![显示“添加部门管理员”对话框的示例](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>设置只读访问权限

可向部门管理员授予只读访问权限。

- 创建新的部门管理员时，请将只读选项设置为“是”。

- 若要编辑现有的部门管理员：
   1. 选择部门，然后选择要编辑的“部门管理员”旁边的铅笔符号。
   1. 将只读选项设置为“是”，然后选择“保存”。 

企业管理员将自动获得部门管理员权限。

## <a name="add-an-account"></a>添加帐户

帐户和订阅的结构会影响其管理方式及其在发票和报表中的显示方式。 典型的组织结构示例包括按业务部门、职能团队和地理位置。

若要添加帐户：

1. 在 Azure Enterprise 门户的左侧导航区域中，选择“管理”。
1. 选择“帐户”选项卡。在“帐户”页上，选择“+添加帐户”。 
1. 选择部门或将其保留为未分配，然后选择所需的身份验证类型。
1. 输入一个易记名称用于标识报表中的帐户。
1. 输入要与新帐户关联的 **帐户所有者电子邮件地址**。
1. 确认该电子邮件地址，然后选择“添加”。

![显示帐户列表和“+ 添加帐户”选项的示例](./media/ea-portal-administration/create-ea-add-an-account.png)

若要添加另一个帐户，请选择“添加另一个帐户”，或者选择左侧工具栏右下角的“添加”。 

若要确认帐户所有权：

1. 登录到 Azure Enterprise 门户。
1. 查看状态。

   状态应从“挂起”更改为“开始/结束日期”。  开始/结束日期分别是用户首次登录的日期以及协议的结束日期。
1. 首次登录 Azure Enterprise 门户时，如果弹出“警告”消息，帐户所有者需要选择“继续”以激活帐户。 

## <a name="change-azure-subscription-or-account-ownership"></a>更改 Azure 订阅或帐户所有权

本部分仅适用于更改订阅所有者的情况。 更改订阅所有权不需要 Azure 支持工单。 企业管理员可以使用 Azure Enterprise 门户来转移注册中的所选订阅或所有订阅的帐户所有权。 他们还可以选择更改订阅目录（租户）。 

但是，EA 管理员无法将帐户从一个注册转移到另一个注册。 若要将帐户从一个注册转移到另一个注册，需要支持请求。 有关将帐户从一个注册转移到另一个注册的信息，请参阅[将企业帐户转移到新注册](ea-transfers.md#transfer-an-enterprise-account-to-a-new-enrollment)。

完成订阅或帐户所有权转移后，Microsoft 会更新帐户所有者。

执行所有权转移之前，请先了解以下 Azure 基于角色的访问控制 (Azure RBAC) 策略：

- 在同一租户中的两个组织 ID 之间执行订阅或帐户所有权转移时，会保留 Azure RBAC 策略、现有的服务管理员和共同管理员角色。
- 跨租户订阅或帐户所有权转移会导致 Azure RBAC 策略和角色分配丢失。
- 策略和管理员角色不会在不同的目录之间转移。 服务管理员将更新为目标帐户的所有者。
- 若要避免在租户之间转移订阅时丢失 Azure RBAC 策略和角色分配，请确保“将订阅移动到接收方的 Azure AD 租户”复选框保留为未选中状态 。 这将保留当前 Azure AD 租户上的服务、Azure 角色和策略，并仅转移帐户的计费所有权。  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="显示“将订阅移动到 Azure AD 租户”复选框未选中的图像" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


在更改帐户所有者之前：

1. 在 Azure Enterprise 门户中，查看“帐户”选项卡并找到源帐户。 源帐户必须处于活动状态。
1. 找到目标帐户，并确保它处于活动状态。

若要转移所有订阅的帐户所有权：

1. 登录到 Azure Enterprise 门户。
1. 在左侧导航区域中，选择“管理”。
1. 选择“帐户”选项卡，将鼠标悬停在某个帐户上。
1. 选择右侧的“更改帐户所有者”图标。 这是一个人形图标。  
    ![显示“更改帐户所有者”符号的插图](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. 选择要转移到的目标帐户，然后选择“下一步”。
1. 如果要在 Azure AD 租户之间转移帐户所有权，请选择“将订阅移动到接收方的 Azure AD 租户”复选框。  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="显示“将订阅移动到 Azure AD 租户”复选框已选中的图像" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. 确认转移并选择“提交”。

若要转移单个订阅的帐户所有权：

1. 登录到 Azure Enterprise 门户。
1. 在左侧导航区域中，选择“管理”。
1. 选择“帐户”选项卡，将鼠标悬停在某个帐户上。
1. 选择右侧的“转移订阅”图标。 该图标类似于一个页面。  
    ![显示“转移订阅”符号的插图](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. 选择要转移订阅的目标帐户，然后选择“下一步”。
1. 如果要在 Azure AD 租户之间转移订阅所有权，请选择“将订阅移动到接收方的 Azure AD 租户”复选框。  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="显示“将订阅移动到 Azure AD 租户”复选框已选中的图像" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. 确认转移并选择“提交”。


## <a name="associate-an-account-to-a-department"></a>将帐户关联到部门

企业管理员可以将现有帐户与合约下的部门关联。

### <a name="to-associate-an-account-to-a-department"></a>将帐户关联到部门

1. 以企业管理员身份登录到 Azure EA 门户。
1. 在左侧导航区域中选择“管理”。
1. 选择“部门”。
1. 将鼠标悬停在包含帐户的行上，然后选择右侧的铅笔图标。
1. 从下拉菜单中选择部门。
1. 选择“保存”。

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>将现有帐户关联到即用即付订阅

如果你在 Azure 门户中已有一个 Microsoft Azure 帐户，请输入关联的工作、学校帐户或 Microsoft 帐户，以将其关联到企业协议注册。

### <a name="associate-an-existing-account"></a>关联现有帐户

1. 在 Azure Enterprise 门户中选择“管理”。
1. 选择“帐户”选项卡。
1. 选择“+添加帐户”。
1. 输入与现有 Azure 帐户关联的工作、学校帐户或 Microsoft 帐户。
1. 确认该帐户已与现有 Azure 帐户相关联。
1. 提供用于在报表中标识此帐户的名称。
1. 选择 **添加** 。
1. 若要添加更多的帐户，可以再次选择“+添加帐户”选项，或选择“管理”按钮返回主页。 
1. 查看“帐户”页时，新添加的帐户将显示为“挂起”状态。 

### <a name="confirm-account-ownership"></a>确认帐户所有权

1. 登录到与提供的工作、学校帐户或 Microsoft 帐户关联的电子邮件帐户。
1. 打开标题为“邀请通过 Microsoft 批量许可激活 Microsoft Azure Service 帐户”的电子邮件通知。
1. 选择该邀请邮件中的“登录到 Microsoft Azure Enterprise 门户”链接。
1. 选择“登录”。
1. 输入工作、学校帐户或 Microsoft 帐户和密码，以登录并确认帐户所有权。

### <a name="azure-marketplace"></a>Azure 市场

尽管大部分订阅都可以从即用即付环境转换为 Azure 企业协议，但 Azure 市场服务不支持这样做。 为了能够在一个视图中查看所有订阅和费用，我们建议将 Azure 市场服务添加到 Azure Enterprise 门户。

1. 登录到 Azure Enterprise 门户。
1. 在左侧导航区域中选择“管理”。
1. 选择“注册”选项卡。
1. 查看“注册详细信息”部分。
1. 在“Azure 市场”字段的右侧，选择铅笔图标来启用此功能。 选择“保存”。

现在，帐户所有者可以购买以前在即用即付订阅中拥有的任何 Azure 市场服务。

在 Azure EA 注册中激活新的 Azure 市场订阅后，请取消在即用即付环境中创建的 Azure 市场服务。 此步骤非常重要，它的意义在于：即用即付这种付款方式失效后，Azure 市场订阅不会处于错误状态。

### <a name="msdn"></a>MSDN

MSDN 订阅自动转换为 MSDN 开发/测试套餐，Azure EA 套餐所有现有的货币额度均失效。

### <a name="azure-in-open"></a>Azure 开放许可

如果将 Azure 开放许可订阅关联到企业协议，任何未使用的 Azure 开放许可额度将会作废。 因此，在将帐户添加到企业协议之前，我们建议用掉 Azure 开放许可订阅的所有额度。  

### <a name="accounts-with-support-subscriptions"></a>具有支持订阅的帐户

如果企业协议没有支持订阅，而你将具有支持订阅的现有帐户添加到 Azure Enterprise 门户，则 MOSA 支持订阅不会自动转换。 需要在宽限期（截至后一个月的月末）内在 Azure EA 中重新购买支持订阅。

## <a name="department-spending-quotas"></a>部门支出配额

EA 客户可为注册中的每个部门设置或更改支出配额。 支出配额金额是针对当前的预付款期设置的。 在当前预付款期结束时，系统会将现有支出配额扩展到下一个预付款期，除非已更新了配额值。

部门管理员可以查看支出配额，但只有企业管理员才能更新配额。 在配额达到 50%、75%、90% 和 100% 时，企业管理员和部门管理员都会收到通知。

### <a name="enterprise-administrator-to-set-the-quota"></a>企业管理员设置配额：

 1. 打开 Azure EA 门户。
 1. 在左侧导航区域中选择“管理”。
 1. 选择“部门”选项卡。
 1. 选择“部门”。
 1. 选择“部门详细信息”部分中的铅笔符号或者选择“+ 添加部门”符号，以添加支出配额和新部门。
 1. 在“部门详细信息”下的“支出配额 $”框中，输入以注册所用货币为单位的支出配额的金额（必须大于 0）。
    - 还可以在此时编辑部门名称和成本中心。
 1. 选择“保存”。

现在，可在“部门”选项卡下的“部门列表”视图中看到部门支出配额。当前预付款期结束时，Azure EA 门户会为下一个预付款期继续使用该支出配额。

部门配额的金额与当前 Azure 预付款无关，配额的金额和警报仅适用于第一方使用。 部门支出配额仅用于提供信息，不强制实施支出限制。

### <a name="department-administrator-to-view-the-quota"></a>部门管理员查看配额：

1. 打开 Azure EA 门户。
1. 在左侧导航区域中选择“管理”。
1. 选择“部门”选项卡，查看带有支出配额的“部门列表”视图。

如果你是间接客户，必须由渠道合作伙伴来为你启用成本功能。

## <a name="enterprise-user-roles"></a>企业用户角色

Azure EA 门户可帮助你管理 Azure EA 的成本和使用情况。 Azure EA 门户中有三个主要角色：

- EA 管理员
- 部门管理员
- 帐户所有者

每个角色具有不同的访问权限和权力级别。

有关用户角色的详细信息，请参阅[企业用户角色](./understand-ea-roles.md#enterprise-user-roles)。

## <a name="add-an-azure-ea-account"></a>添加 Azure EA 帐户

Azure EA 帐户是 Azure EA 门户中的一个组织单位。 它用于管理订阅，此外还用于报告。 若要访问和使用 Azure 服务，需要创建一个帐户，或使用已创建的帐户。

有关 Azure 帐户的详细信息，请参阅[添加帐户](#add-an-account)。

## <a name="enterprise-devtest-offer"></a>Enterprise 开发/测试套餐

Azure 企业管理员可以使自己组织中的帐户所有者能够基于 EA 开发/测试套餐创建订阅。 为此，请在 Azure EA 门户中选中帐户所有者对应的“开发/测试”框。

选中“开发/测试”框后告知帐户所有者，这样他们就可以设置开发/测试订阅者团队需要的 EA 开发/测试订阅。

该套餐使 Visual Studio 有效订阅者能够按开发/测试费率在 Azure 上运行开发和测试工作负荷。 使用该套餐可以访问完整的开发/测试映像库（包括 Windows 8.1 和 Windows 10）。

### <a name="to-set-up-the-enterprise-devtest-offer"></a>设置 Enterprise 开发/测试套餐：

1. 以企业管理员的身份登录。
1. 在左侧导航区域中选择“管理”。
1. 选择“帐户”选项卡。
1. 选择要启用开发/测试访问权限的帐户所在的行。
1. 选择该行右侧的铅笔符号。
1. 选中“开发/测试”复选框。
1. 选择“保存”。

通过 Azure EA 门户将用户添加为帐户所有者后，与基于 Visual Studio 订阅者的 PAYG 开发/测试套餐或月额度套餐的帐户所有者关联的任何 Azure 订阅都将被转换为 EA 开发/测试套餐。 基于与帐户所有者关联的其他套餐类型（例如 PAYG）的订阅则将被转换为 Microsoft Azure Enterprise 套餐。

开发/测试套餐目前不适用于 Azure 政府版客户。

## <a name="create-a-subscription"></a>创建订阅

帐户所有者可以查看和管理订阅。 你可以使用订阅为组织中的团队授予对开发环境和项目的访问权限。 例如测试、生产、开发和过渡环境。

为每个应用程序环境创建不同的订阅可以帮助保护每个环境的安全。

- 还可以针对每个订阅分配不同的服务管理员帐户。
- 可将订阅关联到任意数量的服务。
- 帐户所有者可以创建订阅，并将服务管理员帐户分配到其帐户中的每个订阅。

### <a name="add-a-subscription"></a>添加订阅

使用以下信息添加订阅。

首次在帐户中添加订阅时，系统会要求你接受 Microsoft 在线订阅协议 (MOSA) 和费率计划。 尽管这些条款并不适用于企业协议客户，但必须接受 MOSA 和费率计划才能创建订阅。 Microsoft Azure 企业协议注册修正条款取代了上述条款，但你的契约关系并无变化。 出现提示时，请选中表示接受条款的框。

创建的订阅使用默认订阅名称 _Microsoft Azure Enterprise_。 可以更改该名称，使之与注册中的其他订阅相区分，并确保该名称在企业级别的报表中易于识别。

若要添加订阅：

1. 在 Azure Enterprise 门户中登录到帐户。
1. 选择“管理”选项卡，然后选择页面顶部的“订阅”。 
1. 确认你是否以帐户所有者的身份登录。
1. 依次选择“+添加订阅”、“购买”。 

   首次将订阅添加到帐户时，必须提供联系人信息。 添加更多的订阅时，系统会自动添加联系人信息。

1. 选择“订阅”，然后选择创建的订阅。
1. 选择“编辑订阅详细信息”。
1. 编辑“订阅名称”和“服务管理员”，然后选择勾选标记。 

   订阅名称将显示在报表中。 它是与开发门户中的订阅关联的项目的名称。

新的订阅最长可能需要在 24 小时后才会显示在订阅列表中。 创建订阅后，可以：

- [编辑订阅详细信息](https://portal.azure.com)
- [管理订阅服务](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>删除订阅

删除你是帐户所有者的订阅：

1. 使用与你的帐户关联的凭据登录 Azure 门户。
1. 在“中心”菜单上，选择“订阅”。
1. 在页面左上角的订阅选项卡中，选择要取消的订阅，然后选择“取消订阅”以启动“取消”选项卡。
1. 输入订阅名称并选择取消原因，然后选择“取消订阅”按钮。

只有帐户管理员可以取消订阅。

有关详细信息，请参阅[取消订阅之后会发生什么情况？](cancel-azure-subscription.md#what-happens-after-subscription-cancellation)。

## <a name="delete-an-account"></a>删除帐户

只能为没有有效订阅的有效帐户完成帐户删除操作。

1. 在 Enterprise 门户的左侧导航区域中，选择“管理”。
1. 选择“帐户”选项卡。
1. 从“帐户”表中选择要删除的帐户。
1. 选择“帐户”行右侧的“X”符号。
1. 帐户下没有有效订阅后，在“帐户”行下选择“是”，以确认删除帐户。

## <a name="update-notification-settings"></a>更新通知设置

企业管理员会自动注册接收与其注册相关的使用情况通知。 每个企业管理员可以更改每项通知的发送间隔，或者完全禁用通知。

通知联系人显示在 Azure EA 门户中的“通知联系人”部分。 管理通知联系人可确保由组织中的适当人员接收 Azure EA 通知。

若要查看当前通知设置：

1. 在 Azure EA 门户中，导航到“管理” > “通知联系人”。 
2. 电子邮件地址 – 与接收通知的企业管理员的 Microsoft 帐户、工作或学校帐户关联的电子邮件地址。
3. 未开票余额通知频率 – 设置向每个企业管理员发送通知的频率。

若要添加联系人：

1. 选择“+添加联系人”。
2. 输入电子邮件地址，然后确认。
3. 选择“保存”。

新的通知联系人将显示在“通知联系人”部分。 若要更改通知频率，请选择通知联系人，然后选择所选行右侧的铅笔符号。 将频率设置为“每日”、“每周”、“每月”或“无”。   

可以取消“即将达到计费周期结束日期”和“即将达到禁用和取消预配日期”生命周期通知。  禁用生命周期通知会取消有关计费周期和协议结束日期的通知。

## <a name="azure-sponsorship-offer"></a>Azure 赞助套餐

Azure 赞助套餐是一个受限的 Microsoft Azure 赞助帐户。 它是 Microsoft 通过电子邮件邀请提供给有限的客户使用的套餐。 如果你符合 Microsoft Azure 赞助套餐的条件，你的帐户 ID 会收到一封邀请电子邮件。

如需详细信息，请创建一个[激活赞助支持请求](https://aka.ms/azrsponsorship)。

## <a name="conversion-to-work-or-school-account-authentication"></a>转换为工作或学校帐户身份验证

Azure Enterprise 用户的身份验证类型可以从 Microsoft 帐户（MSA 或 Live ID）转换为工作或学校帐户（使用 Azure Active Directory）。

开始时，请执行以下操作：

1. 使用所需的角色将工作或学校帐户添加到 Azure EA 门户。
1. 如果出现错误，则表明该帐户在 Active Directory 中可能无效。  Azure 使用用户主体名称 (UPN)，该名称并非始终与电子邮件地址相同。
1. 在 Azure EA 门户使用工作或学校帐户进行身份验证。

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>将订阅由使用 Microsoft 帐户转换为使用工作或学校帐户：

1. 使用拥有订阅的 Microsoft 帐户登录到管理门户。
1. 使用帐户所有权转移来转移到新帐户。
1. 现在，Microsoft 帐户应该没有任何可用订阅了，可将其删除。
1. 任何已删除的帐户仍然以停用状态显示在门户的视图中，以供查阅帐单历史记录。  勾选表示只显示有效帐户的复选框，将已删除的帐户从视图中筛选掉。

## <a name="azure-ea-term-glossary"></a>Azure EA 术语表

- **帐户**：Azure Enterprise 门户中的一个组织单位。 它用于管理订阅和报表。
- **帐户所有者**：管理 Azure 上的订阅和服务管理员的人员。 他们可以查看此帐户及其关联订阅的使用情况数据。
- **修订订阅**：注册修正条款下的一年期订阅或连年订阅。
- **预付款**：在此预付款模式下，Azure 服务的年货币额预付款按折扣预付款费率为使用量付款。
- **部门管理员**：管理部门、新建帐户和帐户所有者、查看所管理部门的使用情况详细信息并可在获得授权时查看费用的用户。
- **合约编号**：Microsoft 提供的唯一标识符，用于标识与企业协议关联的特定注册。
- **企业管理员**：管理 Azure 上的部门、部门所有者、帐户和帐户所有者的用户。 他们能够管理企业管理员，查看使用情况数据、已计费数量以及与企业合约相关的所有帐户和订阅中的未计费费用。
- **企业协议**：一种 Microsoft 许可协议，适用于采用集中购买模式、希望通过 Microsoft 技术实现整个组织的规范化并基于 Microsoft 软件标准维护信息技术基础结构的客户。
- **企业协议合约**：企业协议计划中的注册，以折扣费率批量提供 Microsoft 产品。
- **Microsoft 帐户**：一个基于 Web 的服务，使参与的站点能够使用一组凭据对用户进行身份验证。
- **Microsoft Azure Enterprise 注册修正条款（注册修正条款）** ：企业签署的修正条款，是企业注册的一部分，为其提供对 Azure 的访问权限。
- **Azure Enterprise 门户**：供企业客户用来管理 Azure 帐户及其相关订阅的门户。
- **已使用的资源数量**：一个月内使用的单个 Azure 服务的数量。
- **服务管理员**：可以在 Azure Enterprise 门户中访问和管理订阅与开发项目的人员。
- 订阅：表示 Azure Enterprise 门户订阅，是同一个服务管理员所管理的 Azure 服务的容器。
- **工作或学校帐户**：适用于已设置与云联合的 Azure Active Directory 且所有帐户都在单个租户上的组织。

### <a name="enrollment-statuses"></a>注册状态

- **新建**：此状态会分配给在 24 小时内创建的注册，并将在 24 小时内更新为“挂起”状态。
- **Pending**：注册管理员需要登录到 Azure Enterprise 门户。 登录后，注册将切换为“活动”状态。
- **活动**：注册处于“活动”状态，可以在 Azure Enterprise 门户中创建帐户和订阅。 在企业协议结束日期之前，该注册将保持活动状态。
- **无限延期**：在企业协议结束日期后，会出现无限延期状态。 它使选择延期的 Azure EA 客户能够在企业协议结束后继续无限期地使用 Azure 服务。

   在 Azure EA 注册达到企业协议结束日期之前，注册管理员应决定是否要采取以下做法：

  - 通过添加更多 Azure 预付款来续订注册。
  - 转移到新注册。
  - 迁移到 Microsoft 在线订阅计划 (MOSP)。
  - 确认禁用与注册关联的所有服务。
- **已过期**：Azure EA 客户选择不延期，且 Azure EA 注册已达到企业协议结束日期。 注册将会过期，并且所有关联的服务将被禁用。
- **已转移**：所有关联的帐户和服务均已转移到新注册的注册显示为已转移状态。
  >[!NOTE]
  > 如果新注册编号是在续订时生成的，则注册不会自动转移。 若要方便自动转移，必须将以前的注册编号包含在续订文书中。

## <a name="next-steps"></a>后续步骤

- 了解[虚拟机预留项](ea-portal-vm-reservations.md)如何帮助节省资金。
- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](ea-portal-troubleshoot.md)。
- 阅读[成本管理 + 计费常见问题解答](../cost-management-billing-faq.yml)，了解关于 EA 订阅所有权的问题。