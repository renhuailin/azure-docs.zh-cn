---
title: 在 Azure 市场中为托管服务产品/服务创建计划
description: 在 Azure 市场中为托管服务产品/服务创建计划。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/12/2021
ms.openlocfilehash: 4443492d19c09100433bf326f197c9405fa11d8e
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204458"
---
# <a name="create-plans-for-a-managed-service-offer"></a>为托管服务产品/服务创建计划

通过 Microsoft 商业市场销售的托管服务产品/服务必须至少有一种计划。 您可以使用相同的产品/服务创建具有不同选项的各种计划。 这些计划（有时称为 SKU）在版本、盈利或服务层级方面可能有所不同。 有关计划的详细指南，请参阅[商用市场产品/服务的计划和定价](./plans-pricing.md)。

## <a name="create-a-plan"></a>创建计划

1. 在合作伙伴中心中，在你的产品/服务的“计划概述”标签上，选择“+ 创建新计划”。
2. 在出现的对话框的“计划 ID”下面，输入唯一计划 ID。 最大长度为 50，可以使用小写字母数字字符、短划线或下划线。 选择“创建”后，不能修改计划 ID。 你的客户可以看到该 ID。
3. 在“计划名称”框中，输入此计划的名称。 最可以使用 50 个字符。 你的客户可以看到该名称。
4. 选择“创建”  。

## <a name="define-the-plan-listing"></a>定义计划列表

在“计划列表”选项卡上，定义你希望显示在商业市场中的计划名称和描述。

1. “计划名称”框显示你之前为此计划提供的名称。 可以随时更改该名称。 该名称在商业市场中显示为产品/服务计划的标题。
2. 在“计划摘要”框中，提供你的计划的简短描述，在市场搜索结果中可能会用到这些描述。
3. 在“计划描述”框中，解释该计划的独特之处以及你与产品/服务中的其他计划的差异。
4. 选择“保存草稿”，然后转到下一选项卡。

## <a name="define-pricing-and-availability"></a>定义定价和可用性

唯一可用于托管服务产品/服务的定价模型是 **自带许可 (BYOL)** 。 这意味着你将直接向客户收取与此产品/服务相关的费用，而 Microsoft 不向你收取任何费用。

可以将每个计划配置为对所有人可见（公共）或仅特定受众可见（私有）。

> [!NOTE]
> 通过云解决方案提供商 (CSP) 计划的分销商计划建立的订阅不支持私有计划。

> [!IMPORTANT]
> 一旦某个计划已发布为公用，就无法将其更改为专用。 若要控制哪些客户可以接受产品/服务并委派资源，请使用专用计划。 使用公用计划时，不能将其可用性限制为特定客户，甚至不能限制特定数量的客户（不过，如果选择这样做，可完全停止销售计划）。 仅当在发布产品/服务时，包含将“角色定义”设置为托管服务注册分配删除角色的“授权”的情况下，才能在客户接受产品/服务后删除对委派的访问权限。 你还可以与客户联系，要求他们删除你的访问权限。

## <a name="make-your-plan-public"></a>公开计划

1. 在“计划可见性”下面，选择“公共”。 
2. 选择“保存草稿”。 要返回“计划概览”选项卡，请选择左上方的“计划概览”。
3. 要为此产品/服务创建另一个计划，请在“计划概述”选项卡中选择“+ 创建新计划” 。

## <a name="make-your-plan-private"></a>使计划私有

可以使用 Azure 订阅 ID 授予对私有计划的访问权限。 最多可以手动添加 10 个订阅 ID，使用 .CSV 文件最多添加 10,000 个订阅 ID。

要手动添加 10 个订阅 ID，请执行以下操作：

1. 在“计划可见性”下面，选择“私有”。 
2. 输入要向其授予访问权限的访问群体的 Azure 订阅 ID。
3. （可选）在“描述”框中输入该访问群体的描述。
4. 要添加另一个 ID，请选择“添加 ID（最多 10 个）”。
5. 完成添加 ID 后，选择“保存”。

要使用 .CSV 文件最多添加 10,000 个订阅 ID，请执行以下操作：

1. 在“计划可见性”下面，选择“私有”。 
2. 选择“导出访问群体 (csv)”链接。 这将下载一个 CSV 文件。
3. 打开 .CSV 文件。 在“Id”列中，输入要授予访问权限的 Azure 订阅 ID。
4. 在“描述” ****  列中，可以选择为每个条目添加描述。
5. 在“类型” ****  列中，将“SubscriptionId” ****  添加到具有 ID 的每一行。
6. 将文件另存为 .CSV 文件。
7. 在合作伙伴中心中，选择“导入访问群体 (csv)”链接。
8. 在“确认” ****  对话框中，选择“是” **** ，然后上传 .CSV 文件。
9. 选择“保存草稿” **** 。

## <a name="technical-configuration"></a>技术配置

这一部分创建带有授权信息的清单，用于管理客户资源。 若要启用 [Azure 委派资源管理](../lighthouse/concepts/architecture.md)，必须使用此信息。

请查看 [Azure Lighthouse 方案中的租户、角色和用户](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以了解支持的角色以及定义授权的最佳做法。

> [!NOTE]
> “授权”条目中的用户和角色将应用于每一位激活了计划的客户。 如果想要限制特定客户的访问权限，需发布专用计划供其专用。

### <a name="manifest"></a>清单

1. 在“清单”下，提供清单的“版本”。  使用 n.n.n 格式（例如 1.2.5）。
2. 输入“租户 ID”。 这是与组织的 Azure Active Directory (Azure AD) 租户 ID 关联的 GUID；即你将在其中访问客户资源的管理租户。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它。

如果发布产品/服务的新版本并需要创建更新的清单，请选择“+ 新建清单”。 确保增加之前清单版本的版本号。

### <a name="authorizations"></a>授权

授权定义了管理租户中的哪些实体可访问购买计划的客户的资源和订阅。 其中每个实体都分配有一个授予特定访问级别的内置角色。

最多可以为每个计划创建 20 个授权。

> [!TIP]
> 在大多数情况下，需要将角色分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。 将角色分配给 Azure AD 组时，[组类型](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)应为 Security，而非 Office 365。 有关其他建议，请参阅 [Azure Lighthouse 方案中的租户、角色和用户](../lighthouse/concepts/tenants-users-roles.md)。

为每个授权提供以下信息。 根据需要选择“+ 添加授权”，以添加更多用户和角色定义。

- 显示名称：一个易记名称可帮助客户理解此授权的目的。 在委派资源时，客户将看到此名称。
- 主体 ID：用户、用户组或服务主体的 Azure AD 标识符，这些标识符将被授予对客户资源的特定权限（由指定的角色定义）。
- **访问类型**：
  - 主动授权始终具有分配给该角色的特权。 每个计划必须有至少一项主动授权。
  - 符合条件的授权有时间限制，需要用户激活。  若选择“符合条件”，则必须选择最大持续时间，以定义用户在角色激活后拥有该符合条件角色的总时间长度。 最小值为 30 分钟，最大值为 8 小时。 还可选择是否需要多重身份验证以激活角色。 注意，符合条件的授权目前为公共预览版，并有特定的许可要求。 有关详细信息，请参阅[创建合格授权](../lighthouse/how-to/create-eligible-authorizations.md)。
- 角色：从列表中选择一个可用的 Azure AD 内置角色。 此角色将确定“主体 ID”字段中的用户对客户资源拥有的权限。 有关这些角色的说明，请参阅[内置角色](../role-based-access-control/built-in-roles.md)和 [Azure Lighthouse 的角色支持](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)。
  > [!NOTE]
  > 将适用的新内置角色添加到 Azure 后，它们将在此处可用，但在它们显示前可能会有一些延迟。
- **可分配角色**：仅当在“角色定义”中为此授权选择了“用户访问管理员”时，此选项才会显示。 如果是这样，则必须在此处添加一个或多个可分配角色。 “Azure AD 对象 ID”字段中的用户能够将这些角色分配给[托管标识](../active-directory/managed-identities-azure-resources/overview.md)，这是[部署可修正的策略](../lighthouse/how-to/deploy-policy-remediation.md)所必需的。 通常与用户访问管理员角色关联的其他权限均不适用于此用户。
- 审批者：仅在访问类型设置为“符合条件”时才会出现此选项。 如果出现，可选择指定最多 10 个用户或用户组的列表，这些用户或用户组可[批准或拒绝用户激活符合条件角色的请求](../lighthouse/how-to/create-eligible-authorizations.md#approvers)。 请求接受审批并获批后，审批者将收到通知。 若未提供任何内容，则授权将自动激活。

> [!TIP]
> 若要确保可以根据需要[删除对委派的访问权限](../lighthouse/how-to/remove-delegation.md)，请包括“角色定义”设置为[托管服务注册分配删除角色](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的“授权”。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

完成计划的所有部分后，可选择“+ 创建新计划”来创建其他计划。 完成后，选择“保存草稿”。 完成计划创建后，在窗口顶部的痕迹导航跟踪中选择“计划”，返回到该产品/服务的左侧导航菜单。

## <a name="updating-an-offer"></a>更新产品/服务

发布产品/服务后，可随时[发布产品/服务的更新版本](update-existing-offer.md)。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序](../lighthouse/how-to/view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都将能够查看更改并决定是否要更新到新版本。

## <a name="next-steps"></a>后续步骤

- 退出计划设置，然后继续执行（可选）[与 Microsoft 联合销售](./co-sell-overview.md)或者
- [查看并发布产品/服务](review-publish-offer.md)
