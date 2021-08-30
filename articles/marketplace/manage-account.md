---
title: 如何在 Microsoft 合作伙伴中心管理商业市场帐户 - Azure 市场
description: 了解如何在 Microsoft 合作伙伴中心管理商业市场帐户。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: 2bd5ae6696c5ce1c2d4ea3be974245fde1053b71
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416283"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>在合作伙伴中心管理商业市场帐户

**相应的角色**

- 所有者
- Manager

[创建合作伙伴中心帐户](./create-account.md)后，就可以使用[商业市场仪表板](https://go.microsoft.com/fwlink/?linkid=2166002)管理帐户和产品/服务。

## <a name="access-your-account-settings"></a>访问你的帐户设置

如果尚未执行此操作，你（或组织的管理员）应访问合作伙伴中心帐户的[帐户设置](https://go.microsoft.com/fwlink/?linkid=2165291)。

1. 使用要访问的帐户登录到合作伙伴中心内的[商业市场仪表板](https://go.microsoft.com/fwlink/?linkid=2165290)。 如果你具有多个帐户并且已使用其他帐户登录，则可以[切换帐户](switch-accounts.md)。
1. 在右上角选择“设置”（齿轮图标），然后选择“帐户设置”。 

    [ ![合作伙伴中心内的帐户设置菜单的屏幕截图。](./media/manage-accounts/settings-account.png) ](./media/manage-accounts/settings-account.png#lightbox)

1. 在“帐户设置”下选择“法律” 。 然后选择“开发人员”选项卡，查看与商业市场帐户相关的详细信息。

    [ ![“帐户设置”中法律页上的开发人员选项卡屏幕截图。](./media/manage-accounts/developer-tab.png) ](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>“帐户设置”页

选择“设置”并展开“帐户设置”时，默认视图为“法律信息”  。 此页最多可以有三个选项卡，具体取决于你注册的计划：“合作伙伴”、“经销商”和“开发人员”  。

“合作伙伴”选项卡（适用于在 MPN 中注册的合作伙伴）包含：

- 所有法律企业信息，如公司的注册法律名称和地址
- 主要联系人
- 企业位置。

“经销商”选项卡（适用于开展 CSP 业务的合作伙伴）包含：

- 主要联系人信息
- 客户支持配置文件
- 计划信息

“开发人员”（适用于在任何开发人员计划中注册的合作伙伴）选项卡包含以下信息：

- 帐户详细信息：帐户类型和帐户状态
- 发布者 ID：卖家 ID、用户 ID、发布者 ID、Azure AD 租户等
- 联系人信息：发布者显示名称、卖方联系人（姓名、电子邮件、电话和地址）和公司审批者（姓名、电子邮件、电话）

### <a name="account-settings---developer-tab"></a>帐户设置 -“开发人员”选项卡

以下信息介绍了“开发人员”选项卡上的信息。

#### <a name="account-details"></a>帐户详细信息

在“开发人员”选项卡的“帐户详细信息”部分中，可以查看基本信息，如“帐户类型”（公司或个人）和帐户的“验证状态”  。 在帐户验证过程中，这些设置将显示所需的每个步骤，包括电子邮件验证、雇用验证和业务验证。

#### <a name="publisher-ids"></a>发布者 ID

在“发布者 ID”部分中，可以看到“Symantec ID”（如果可用）、“卖家 ID”、“用户 ID”、“MPN ID”和“Azure AD 租户”    。 这些值由 Microsoft 分配，用于唯一标识你的开发人员帐户，但不能进行编辑。

如果没有 Symantec ID，则可以选择用于请求一个 ID 的链接。

### <a name="contact-info"></a>联系信息

在“联系人信息”部分中，可以看到“发布者显示名称”、“卖家联系人信息”（公司卖家的联系人姓名、电子邮件、电话号码和地址）和“公司审批者”（有权审批公司决策的个人的姓名、电子邮件地址和电话号码）  。

还可以选择“更新”链接以更改联系人信息，如发布者显示名称和电子邮件地址。

### <a name="account-settings-identifiers"></a>帐户设置标识符

在“帐户设置” > “组织配置文件”下，选择“标识符”以查看以下信息：

- MPN ID：与帐户关联的任何 MPN ID
- CSP：此帐户与 CSP 计划关联的 MPN ID。
- 发布者：与帐户关联的任何卖家 ID
- 跟踪 GUID：与你的帐户关联的任何跟踪 GUID

#### <a name="tracking-guids"></a>跟踪 GUID

全局唯一标识符 (GUID) 是可用于跟踪 Azure 使用情况的唯一参考编号（包含 32 个十六进制数字）。

若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://aka.ms/StoragePartners)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。

建议为每个产品/服务和每个产品的分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用单个 GUID。

如果使用模板部署产品并且产品在 Azure 市场和 GitHub 中都提供，则可以创建并注册两个不同的 GUID：

- Azure 市场中的产品 A
- GitHub 中的产品 A

报告根据合作伙伴值（Microsoft 合作伙伴 ID）和 GUID 来完成。 还可以在更为精细的级别跟踪 GUID，从而使产品/服务中的每个计划一致。

有关详细信息，请参阅[使用 GUID 跟踪 Azure 客户使用情况常见问题解答](azure-partner-customer-usage-attribution.md#faq)。

### <a name="agreements"></a>协议

通过“协议”页可以查看已授权的发布协议列表。 这些协议根据名称和版本号列出，包括接受协议的日期和接受协议的用户的名称。

若要访问“协议”页，请执行以下操作：

1. 登录[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165507)。
1. 在右上方，选择“设置” > “帐户设置” 。
1. 在“帐户设置”下选择“协议” 。

如果有需要注意的协议更新，此页顶部可能会显示所需的操作。 若要接受更新的协议，请先阅读链接的协议版本，然后选择“接受协议”。

## <a name="set-up-a-payout-profile"></a>设置付款配置文件

付款配置文件是将销售收款发送到的银行帐户。 此银行帐户必须位于注册合作伙伴中心帐户所在的国家或地区。 有关付款配置文件的详细信息，请参阅[在合作伙伴中心创建和管理付款和税务配置文件](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles)和[设置奖励付款帐户和纳税表单](/partner-center/set-up-your-payout-account)。

若要设置付款配置文件，请执行以下操作：

1. 转到合作伙伴中心内的[商业市场概述](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)页。
2. 在“配置文件”部分的“付款配置文件”旁边，选择“更新”  。
3. 选择付款方式：银行帐户或 PayPal。
4. 添加付款信息：这可能包括选择帐户类型（支票或储蓄）、输入帐户持有者姓名、帐号和银行代号、帐单邮寄地址、电话号码或 PayPal 电子邮件地址。 有关使用 PayPal 作为帐户付款方式，以及是否在你的市场或区域中支持该付款方式的详细信息，请参阅 [PayPal 信息](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)。

> [!IMPORTANT]
> 更改付款帐户可能会推迟付款（最多一个付款周期）。 出现此推迟的原因是我们需要验证帐户更改，正如首次设置付款帐户时我们会进行验证。 帐户通过验证后，仍会收到全额款项；当前付款周期应得的任何付款将添加到下一个付款周期。  

## <a name="tax-profile"></a>税务配置文件

查看当前的税务个人资料状态，确认是否显示正确的实体类型和税务证书信息 。 选择“编辑”以更新或填写任何所需的表单。

若要建立税务状态，必须指定你的居住地和国籍所属的国家或地区，并填写与你所在国家或地区关联的相应税务表单。

不管你的居住地或国籍属于哪个国家或地区，都必须填写美国税务表单才能通过 Microsoft 销售任何产品或服务。 满足特定美国居留要求的合作伙伴必须填写 IRS W-9 表单。 美国之外的其他合作伙伴必须填写 IRS W-8 表单。 完成你的税务个人资料后，即可在线填写这些表单。

无需美国个人纳税人标识号（即 ITIN），即可从 Microsoft 接收付款或申报税务协定权益。

可以在合作伙伴中心以电子方式完成和提交你的税务表单；在大多数情况下，无需打印和邮寄任何表单。

不同的国家和地区具有不同的税务要求。 应付税款的确切金额取决于销售产品/服务的国家和地区。 在某些国家或地区中，Microsoft 代表你缴纳销售和使用税。 这些国家和地区将在列出产品/服务的过程中确定。 在其他国家和地区中，根据你的注册位置，可能需要对销售的产品/服务缴纳销售和使用税，并直接支付给本地税收机构。 此外，你收到的销售收款还可以作为收入进行纳税。 我们强烈建议联系你所在国家或地区最能帮助你确定 Microsoft 销售交易的正确税务信息的相关机构。

有关税务配置文件的详细信息，请参阅[在合作伙伴中心创建和管理付款和税务配置文件](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles)和[设置奖励付款帐户和纳税表单](/partner-center/set-up-your-payout-account)。

### <a name="withholding-rates"></a>预扣税率

在纳税表单中提交的信息可确定相应的预扣税率。 预扣税率仅适用于在美国进行的销售；在非美国位置进行的销售不受预扣税率的限制。 预扣税率变化多端，但对于大多数在美国之外注册的开发人员来说，默认预扣税率为 30%。 如果你所在的国家或地区与美国达成所得税协定，则可以选择降低此预扣税率。

### <a name="tax-treaty-benefits"></a>税务协定权益

如果你不在美国，则可以利用税务协定权益。 这些权益因国家/地区而异，并且可让你减少 Microsoft 预扣的税金。 可以通过填写 W-8BEN 表单的第 II 部分来申报税务协定权益。 建议你与你所在国家或地区的相应资源通信，以确定这些权益是否适合你。

[详细了解 Windows 应用/游戏开发人员和 Azure 市场发布者的税务详细信息](/windows/uwp/publish/tax-details-for-paid-apps)。

### <a name="payout-hold-status"></a>付款暂停状态

默认情况下，Microsoft 每月发送付款。 但是，可以选择暂停付款，这将阻止向你的帐户发送付款。 如果选择暂停付款，我们将继续记录你所获得的任何收入，并提供“付款摘要”中的详细信息。 但是，在删除暂停状态之前，我们不会向你的帐户发送任何付款。

暂停付款：

1. 转到“帐户设置”。 
1. 在左侧导航中，展开“付款和税务”，然后选择“付款和税务配置文件” 。
1. 选择要为其暂停付款的计划，然后选中“暂停我的付款”复选框。

可以随时更改付款暂停状态，但请注意，你的决策将影响下一次每月付款。 例如，如果要暂停四月付款，请确保在三月底之前将付款暂停状态设置为“打开”。

将付款暂停状态设置为“打开”后，所有付款都将处于暂停状态，直到将滑块切换回“关闭” 。 执行此操作时，将在下一次每月付款周期中生效（前提是已满足任何适用的付款阈值）。 例如，如果已暂停付款，但想要在六月生成付款，请确保在五月底之前将付款暂停状态切换为“关闭”。

> [!NOTE]
> “付款暂停状态”选择适用于通过 Microsoft 合作伙伴中心付费的所有收入来源，包括 Azure 市场、Microsoft AppSource、Microsoft Store、广告等） 。 无法为每个收入来源选择不同的暂停状态。

## <a name="devices"></a>设备

设备管理设置仅适用于通用 Windows 平台 (UWP) 发布。 [了解详细信息](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)。

## <a name="create-a-billing-profile"></a>创建计费对象信息

如果要发布 [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) 或 [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md) 产品/服务，则需要填写计费对象信息。

帐单邮寄地址是通过你的法律实体预填充的，可以稍后更新此地址。 税务和增值税 ID 字段对于某些国家/地区是必填字段，对于其他国家/地区是可选字段。 无法编辑国家/地区名称和公司名称。

1. 转到“帐户设置”。
1. 然后在左侧导航中，展开“组织配置文件”，然后选择“计费配置文件” 。


## <a name="multi-user-account-management"></a>多用户帐户管理

合作伙伴中心使用 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 进行多用户帐户访问和管理。 组织的 Azure AD 会在注册过程中自动与合作伙伴中心帐户关联。

## <a name="next-steps"></a>后续步骤

- [添加和管理用户](add-manage-users.md)