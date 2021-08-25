---
title: 为 Microsoft 客户协议设置计费 - Azure
description: 了解如何设置 Microsoft 客户协议的计费帐户。 请参阅设置的先决条件并查看其他可用资源。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/19/2021
ms.author: banders
ms.openlocfilehash: 9b586d453f9ac49dd7bd2894c0003fef521167da
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514943"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>设置 Microsoft 客户协议的计费帐户

如果你的直接企业协议注册已过期或即将过期，可以签署一份 Microsoft 客户协议以续订该注册。 本文介绍在完成设置后如何对现有计费进行更改，并引导你完成新计费帐户的设置。 目前，不能使用 Microsoft 客户协议续订过期的间接企业协议。

续订过程包括以下步骤：

1. 接受新的 Microsoft 客户协议。 咨询 Microsoft 现场代表，以了解详情并接受新协议。
2. 对为新的 Microsoft 客户协议创建的新计费帐户进行设置。

若要设置计费帐户，必须将 Azure 订阅的计费从企业协议注册转换到新帐户。 该项设置不会影响订阅中正在运行的 Azure 服务。 但是，它会改变订阅的计费管理方式。

- 应在 [Azure 门户](https://portal.azure.com)而不是 [EA 门户](https://ea.azure.com)中管理 Azure 服务和计费。
- 你每月会收到一份数字版的费用发票。 可以在“Azure 成本管理 + 计费”页中查看和分析该发票。
- 需使用新帐户中的计费结构和范围，而不是企业协议注册中的部门和帐户，来管理和组织计费。

在开始设置之前，我们建议执行以下操作：

- **了解新的计费帐户**
  - 新帐户将会简化组织的计费。 [获取新计费帐户的简要概述](../understand/mca-overview.md)
- **验证是否有权完成设置**
  - 只有拥有特定管理权限的用户才能完成该设置。 检查你是否拥有[完成设置所需的访问权限](#access-required-to-complete-the-setup)。
- **了解计费层次结构的变化**
  - 新计费帐户的组织方式与企业协议注册不同。 [了解新帐户中计费层次结构的变化](#understand-changes-to-your-billing-hierarchy)。
- **了解计费管理员访问权限的变化**
  - 企业协议注册中的管理员有权访问新帐户中的计费范围。[了解其访问权限的变化](#changes-to-billing-administrator-access)。
- **查看新帐户取代的企业协议功能**
  - 查看企业协议注册中已由新帐户中的功能取代的功能。
- **查看最常见问题的解答**
  - 查看[更多信息](#additional-information)以了解有关设置的详细信息。

## <a name="access-required-to-complete-the-setup"></a>完成设置所需的访问权限

若要完成该设置，需要拥有以下访问权限：

- 签署 Microsoft 客户协议时创建的计费配置文件的所有者。 若要详细了解计费配置文件，请参阅[了解计费配置文件](../understand/mca-overview.md#billing-profiles)。  
&mdash; 和 &mdash;
- 已续订的注册的企业管理员。

### <a name="start-migration-and-get-permission-needed-to-complete-setup"></a>开始迁移并获取完成设置所需的权限

可以使用以下选项启动 EA 注册到 Microsoft 客户协议的迁移体验。


- 使用签署 Microsoft 客户协议时收到的电子邮件中的链接登录到 Azure 门户。

- 如果没有电子邮件，请使用以下链接登录。

  `https://portal.azure.com/#blade/Microsoft_Azure_SubscriptionManagement/TransitionEnrollment`

如果你同时具备企业管理员和计费帐户所有者角色或计费对象信息角色，则会在 Azure 门户中看到以下页面。 你可以继续设置 EA 注册和 Microsoft 客户协议计费帐户以供转换。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page.png" alt-text="显示“设置计费帐户”页的屏幕截图" lightbox="./media/mca-setup-account/setup-billing-account-page.png" :::

如果你没有企业协议的企业管理员角色或 Microsoft 客户协议的计费对象信息所有者角色，则可以使用以下信息获取完成设置所需的访问权限。

#### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>如果你不是该注册的企业管理员

如果你具备计费帐户或计费对象信息所有者角色，但不是企业管理员，则会在 Azure 门户中看到以下页面。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" alt-text="显示“设置计费帐户 - 准备企业协议注册以供转换”页的屏幕截图。" lightbox="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" :::

可以使用两个选项：

- 让注册的企业管理员为你提供企业管理员角色。 有关更多信息，请参阅[创建另一个企业管理员](ea-portal-administration.md#create-another-enterprise-administrator)。
-  你可以为企业管理员提供计费帐户所有者或计费对象信息所有者角色。 有关详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

获得企业管理员角色后，复制“设置计费帐户”页上的链接。 在 Web 浏览器中打开该链接以继续设置 Microsoft 客户协议。 否则，请将其发送给企业管理员。

#### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>如果你不是计费配置文件的所有者

如果你是企业管理员但没有计费帐户，则会在 Azure 门户中看到以下会阻止转换的错误。

如果你认为你具有正确 Microsoft 客户协议的计费对象信息所有者访问权限，并且看到以下消息，请确保你位于组织的正确租户中。 你可能需要更改目录。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" alt-text="显示“设置计费帐户 - Microsoft 客户协议计费帐户”页的屏幕截图。" lightbox="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" :::

可以使用两个选项：

- 让现有计费帐户所有者为你提供计费帐户所有者或计费对象信息所有者角色。 有关详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- 为现有计费帐户所有者提供企业管理员角色。 有关更多信息，请参阅[创建另一个企业管理员](ea-portal-administration.md#create-another-enterprise-administrator)。

获得计费帐户所有者或计费对象信息所有者角色后，复制“设置计费帐户”页上的链接。 在 Web 浏览器中打开该链接以继续设置 Microsoft 客户协议。 否则，请将该链接发送给计费帐户所有者。

#### <a name="prepare-enrollment-for-transition"></a>准备用于转换的注册

在你拥有对 EA 注册和计费对象信息的所有者访问权限后，即可准备它们以进行转换。

打开之前显示的迁移，或打开通过电子邮件发送的链接。 链接为 `https://portal.azure.com/#blade/Microsoft_Azure_SubscriptionManagement/TransitionEnrollment`。

下图显示“准备企业协议注册以进行转换”窗口示例。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition.png" alt-text="显示提供了选项的“设置计费帐户 - 准备企业协议注册以进行转换”页面的屏幕截图。" lightbox="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition.png" :::

接下来，选择要转换的源注册。 然后选择计费帐户和计费对象信息。 如果验证通过且没有出现类似以下屏幕的任何问题，请选择“继续”以继续。

:::image type="content" source="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition-continue.png" alt-text="显示具有有效选项的“设置计费帐户 - 准备企业协议注册以进行转换”页面的屏幕截图。" lightbox="./media/mca-setup-account/setup-billing-account-prepare-enrollment-transition-continue.png" :::

**错误条件**

如果你拥有企业管理员（只读）角色，则会看到以下阻止转换的错误。 必须具有企业管理员角色才能转换注册。

`Select another enrollment. You do not hve Enterprise Administrator write permission to the enrollment.`

如果注册距结束日期超过 60 天，你将看到以下阻止转换的错误。 当前日期必须在注册结束后的 60 天内才能转换注册。

`Select another enrollment. This enrollment has more than 60 days before its end date.`

如果注册仍有额度，你将看到以下会阻止转换的错误。 用完所有额度后才能转换注册。

`Select another enrollment. This enrollment still has credits and can't be transitioned to a billing account.`

如果你没有计费对象信息的所有者权限，你将看到以下会阻止转换的错误。 必须具有计费对象信息所有者角色才能转换注册。

`Select another Billing Profile. You do not have owner permission to this profile.`

如果新的计费对象信息未启用新计划，你将看到以下错误。 启用该计划后才能转换注册。

`Select another Billing Profile. The current selection does not have Azure Plan and Azure dev test plan enabled on it.`

## <a name="understand-changes-to-your-billing-hierarchy"></a>了解计费层次结构的变化

新计费帐户将会简化组织的计费，同时提供增强的计费和成本管理功能。 下图解释了计费在新计费帐户中的组织方式。

![ea-mca-post-transition-hierarchy 插图](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. 使用计费帐户来管理 Microsoft 客户协议的计费。 企业管理员将成为计费帐户的所有者。 若要详细了解计费帐户，请参阅[了解计费帐户](../understand/mca-overview.md#your-billing-account)。
2. 像在企业协议注册中那样，使用计费配置文件来管理组织的计费。 企业管理员将成为计费配置文件的所有者。 若要详细了解计费配置文件，请参阅[了解计费配置文件](../understand/mca-overview.md#billing-profiles)。
3. 可以根据需求使用发票科目来组织成本，类似于在企业协议注册中使用部门。 部门将成为发票科目，部门管理员将成为各自发票科目的所有者。 若要详细了解发票科目，请参阅[了解发票科目](../understand/mca-overview.md#invoice-sections)。
4. 新计费帐户不支持企业协议中创建的帐户。 新帐户的订阅属于其部门的相应发票科目。 帐户所有者可以创建和管理其发票科目的订阅。

## <a name="changes-to-billing-administrator-access"></a>计费管理员访问权限的变化

企业协议注册中的计费管理员可以根据其访问权限访问新帐户中的计费范围。 下表解释了在设置过程中访问权限的变化：

| 现有角色 | 转换后的角色 |
| --- | --- |
| **企业管理员（只读 = 否）** | **- 计费帐户所有者** </br> 管理计费帐户中的所有内容 </br> **- 计费配置文件所有者** </br> 管理计费配置文件中的所有内容 </br> **- 所有发票科目的发票科目所有者** </br> 管理发票科目中的所有内容 |
| **企业管理员（只读 = 是）** | **- 计费帐户读取者** </br> 查看计费帐户中所有内容的只读视图</br> **- 计费配置文件读取者** </br> 查看计费配置文件中所有内容的只读视图</br>**- 所有发票科目的发票科目读取者**</br> 发票科目中所有内容的只读视图|
| **部门管理员（只读 = 否）** |**- 为相应部门创建的发票科目的发票科目所有者** </br>管理发票科目中的所有内容|
| **部门管理员（只读 = 是）**|**- 为相应部门创建的发票科目的发票科目读取者**</br> 查看发票科目中所有内容的只读视图|
| **帐户所有者** | **- 为相应部门创建的发票科目的 Azure 订阅创建者** </br>  为发票科目创建 Azure 订阅|

在接受 Microsoft 客户协议时，为新的计费帐户选择了 Azure Active Directory (AD) 租户。 如果你的组织中没有租户，则系统会创建一个新租户。 该租户在 Azure Active Directory 中代表你的组织。 组织中的全局租户管理员可以使用租户来管理对组织中应用程序和数据的访问。

新帐户仅支持在签署 Microsoft 客户协议时选择的租户中的用户。 如果对企业协议拥有管理权限的用户属于该租户，则他们在设置期间有权访问新的计费帐户。 如果他们不属于该租户，则除非你发出邀请，否则他们无权访问新的计费帐户。

邀请用户时，会将他们作为来宾用户添加到租户，然后他们将有权访问计费帐户。 若要邀请用户，必须为租户启用来宾访问。 有关详细信息，请参阅[在 Azure Active Directory 中控制来宾访问](/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory)。 如果来宾访问已禁用，请联系租户的全局管理员将它启用。 <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>查看已取代的功能

以下企业协议的功能已由 Microsoft 客户协议的计费帐户中的新功能取代。

### <a name="enterprise-agreement-accounts"></a>企业协议帐户

新计费帐户不支持企业协议注册中创建的帐户。 新帐户的订阅属于为相应部门创建的发票科目。 帐户所有者将成为 Azure 订阅创建者，可以创建和管理其发票科目的订阅。

### <a name="notification-contacts"></a>通知联系人

将会向通知联系人发送有关 Azure 企业协议的电子邮件通信。 新计费帐户不支持通知联系人。 有关 Azure 额度和发票的电子邮件将发送到有权访问计费帐户中计费配置文件的用户。

### <a name="spending-quotas"></a>支出配额

为企业协议注册中的部门设置的支出配额已由新计费帐户中的预算取代。 对于为注册中的部门设置的每个支出配额，将会创建一个预算。 有关预算的详细信息，请参阅[教程：创建并管理 Azure 预算](../costs/tutorial-acm-create-budgets.md)。

### <a name="cost-centers"></a>成本中心

针对企业协议注册中的 Azure 订阅设置的成本中心将会沿袭到新计费帐户。 但是，不支持部门和企业协议帐户的成本中心。

## <a name="additional-information"></a>其他信息

以下部分提供了有关设置计费帐户的其他信息。

### <a name="no-service-downtime"></a>服务不会中断

订阅中的 Azure 服务将保持正常运行，而不会中断。 我们只会转换 Azure 订阅的计费关系。 这不会对现有的资源、资源组或管理组产生影响。

### <a name="user-access-to-azure-resources"></a>用户对 Azure 资源的访问权限

在转换期间，使用 Azure 基于角色的访问控制 (Azure RBAC) 设置的 Azure 资源访问权限不受影响。

### <a name="azure-reservations"></a>Azure 预留

企业协议注册中的任何 Azure 预留项将转移到新计费帐户。 在转换过程中，对订阅应用的预留折扣不会有任何变化。

### <a name="azure-marketplace-products"></a>Azure 市场产品

企业协议注册中的任何 Azure 市场产品将随订阅一起移动。 在转换过程中，服务对市场产品的访问权限不会有任何变化。

### <a name="support-plan"></a>支持计划

在转换过程中，支持权益不会转移。 购买新的支持计划可以获得新计费帐户中 Azure 订阅的权益。

### <a name="past-charges-and-balance"></a>既往费用和余额

可以通过 Azure 门户查看企业协议注册中在转换之前的费用和剩余额度。 <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>何时应完成设置？

应在企业协议注册过期之前完成计费帐户的设置。 如果注册已过期，Azure 订阅中的服务将保持运行，而不会中断。 但是，你要按服务的即用即付费率付费。

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>设置后企业协议注册的变化

可以手动将转换后为企业协议注册创建的 Azure 订阅移到新的计费帐户。 有关详细信息，请参阅[从其他用户获取 Azure 订阅的计费所有权](mca-request-billing-ownership.md)。 若要移动转换后购买的 Azure 预留项，请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 也可以在转换后向用户提供对计费帐户的访问权限。 有关详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>还原转换

转换不可还原。 将 Azure 订阅的计费转换到新计费帐户后，无法将其还原为企业协议注册。

### <a name="closing-your-browser-during-setup"></a>在设置期间关闭浏览器

在选择“开始转换”之前，可以关闭浏览器。 可以使用电子邮件中收到的链接返回到设置，并开始转换。 开始转换后，如果关闭浏览器，转换过程仍会继续运行。 返回到转换状态页可以监视转换过程的最新状态。 转换完成后，你会收到一封电子邮件。

## <a name="complete-the-setup-in-the-azure-portal"></a>在 Azure 门户中完成设置

若要完成设置，需要同时有权访问新计费帐户和企业协议注册。 有关详细信息，请参阅[完成计费帐户设置所需的访问权限](#access-required-to-complete-the-setup)。

1. 使用签署 Microsoft 客户协议时收到的电子邮件中的链接登录到 Azure 门户。

2. 如果没有电子邮件，请使用以下链接登录。

   `https://portal.azure.com/#blade/Microsoft_Azure_SubscriptionManagement/TransitionEnrollment`

3. 在设置过程的最后一个步骤中选择“开始转换”。  选择“开始转换”后：

    ![显示设置向导的屏幕截图](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - 将在新计费帐户中创建对应于企业协议层次结构的计费层次结构。 有关详细信息，请参阅[了解计费层次结构的变化](#understand-changes-to-your-billing-hierarchy)。
    - 企业协议注册中的管理员有权访问新计费帐户，因此他们可以继续管理组织的计费。
    - Azure 订阅的计费将转换到新帐户。 在此转换过程中，Azure 服务不会受到任何影响。  它们将保持正常运行，而不会出现任何中断。
    - 如果你购买了 Azure 预留项，它们将移到新的计费帐户，而不会更改权益或条款。

4. 可以在“转换状态”页上监视转换状态。 

   ![显示转换状态的屏幕截图](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>验证计费帐户是否已设置

 验证以下各项，以确保正确设置新的计费帐户：

### <a name="azure-subscriptions"></a>Azure 订阅

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![显示了 Azure 门户搜索的屏幕截图](./media/mca-setup-account/search-cmb.png)

3. 选择计费帐户。 计费帐户的类型为 **Microsoft 客户协议**。

4. 在左侧选择“Azure 订阅”。 

   ![显示订阅列表的屏幕截图](./media/mca-setup-account/mca-subscriptions-post-transition.png)

从企业协议注册转换为新计费帐户的 Azure 订阅将显示在 Azure 订阅页上。 如果你认为缺少了任何订阅，请在 Azure 门户中手动转换订阅的计费。 有关详细信息，请参阅[从其他用户获取 Azure 订阅的计费所有权](mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure 预留项

企业协议注册中的任何 Azure 预留项将移到新的计费帐户，而不会更改权益或条款。 在转换之前完成的事务不会出现在新的计费帐户中。 但是，可以通过访问[“Azure 预留项”页](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)来验证预留项权益是否应用于订阅。

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>企业管理员对计费帐户的访问权限

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![显示了 Azure 门户搜索的屏幕截图](./media/mca-setup-account/search-cmb.png)

3. 选择 **Microsoft 客户协议** 的计费帐户。

4. 在左侧选择“访问控制(IAM)”。 

   ![一张屏幕截图，上面显示了转换后列为计费帐户所有者的企业管理员的访问权限。](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

企业管理员将作为计费帐户所有者列出，而拥有只读权限的企业管理员将作为计费帐户读取者列出。 如果你认为缺少了任何企业管理员的访问权限，可以在 Azure 门户中为其授予访问权限。 有关详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>企业管理员对计费配置文件的访问权限

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![显示了 Azure 门户搜索的屏幕截图](./media/mca-setup-account/search-cmb.png)

3. 选择为注册创建的计费配置文件。 根据你拥有的访问权限，可能需要选择一个计费帐户。 在该计费帐户中，选择“计费配置文件”，然后选择计费配置文件。

4. 在左侧选择“访问控制(IAM)”。 

   ![一张屏幕截图，上面显示了转换后列为计费对象信息所有者的企业管理员的访问权限。](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

企业管理员将作为计费配置文件所有者列出，而拥有只读权限的企业管理员将作为计费配置文件读取者列出。 如果你认为缺少了任何企业管理员的访问权限，可以在 Azure 门户中为其授予访问权限。 有关详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>企业管理员、部门管理员和帐户所有者对发票科目的访问权限

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![显示了 Azure 门户搜索的屏幕截图](./media/mca-setup-account/search-cmb.png).

3. 选择发票科目。 发票科目的名称与其在企业协议注册中的相应部门名称相同。 根据你拥有的访问权限，可能需要选择一个计费帐户。 在计费帐户中，选择“计费配置文件”  ，然后选择“发票科目”  。 从发票科目列表中，选择一个发票科目。

   ![显示转换后的发票科目列表的屏幕截图](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. 在左侧选择“访问控制(IAM)”。 

    ![显示转换后部门和帐户管理员的访问权限的屏幕截图](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

企业管理员和部门管理员将作为发票科目所有者或发票科目读取者列出，而部门中的帐户所有者将作为 Azure 订阅创建者列出。 针对所有发票科目重复该步骤，以检查对企业协议注册中所有部门的访问权限。 不属于任何部门的帐户所有者将会获得对名为“默认发票科目”的发票科目的权限。  如果你认为缺少了任何管理员的访问权限，可以在 Azure 门户中为其授予访问权限。 有关详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [开始使用新计费帐户](../understand/mca-overview.md)

- [完成 Microsoft 客户协议计费帐户中的企业协议任务](mca-enterprise-operations.md)

- [管理对计费帐户的访问权限](understand-mca-roles.md)